作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

## 本节目录 ##

 *  1、消息队列负载
    
    2、消息拉取
    
    3、消息顺序消息消费
    
     *  3.1核心属性与构造函数
     *  3.2 `start` 方法
     *  3.3 `submitConsumeRequest`
     *  3.4 `ConsumeMessageOrderlyService`\#`ConsumeRequest`
     *  3.4 消息队列锁实现

  
所谓顺序消费，`rocketmq` 支持同一消费队列上的消息顺序消费。

消息消费涉及3个点：  
1、消息队列重新负载。  
2、消息拉取。  
3、消息消费。

按照消息消费步骤来揭开 `RocketMQ` 顺序消息消费实现原理。

## 1、消息队列负载 ##

`RocketMQ` 在同一个 `JVM` 进程拥有一个 `clientConfigId` (客户端`ID`)，该 `JVM` 进程中不同的消息消费组的消息客户端ID相同，因为在JVM进程中对于每一个 `ClientConfig` 只会实例化一个 `MQClientInstance`。消息消费的第一个步骤是首先要为消费组内的所有消息者分配消息消费队列。`RocetMQ` 中通过 `RebalanceService` 线程实现消费队列负载。

```
RebalanceImpl#updateProcessQueueTableInRebalance
List<PullRequest> pullRequestList = new ArrayList<PullRequest>();
for (MessageQueue mq : mqSet) {
    if (!this.processQueueTable.containsKey(mq)) {
        if (isOrder && !this.lock(mq)) {  // @1
            log.warn("doRebalance, {}, add a new mq failed, {}, because lock failed", consumerGroup, mq);
            continue;
        }
     this.removeDirtyOffset(mq);
     ProcessQueue pq = new ProcessQueue();
     long nextOffset = this.computePullFromWhere(mq);
     if (nextOffset >= 0) {
         ProcessQueue pre = this.processQueueTable.putIfAbsent(mq, pq);
         if (pre != null) {
             log.info("doRebalance, {}, mq already exists, {}", consumerGroup, mq);
          } else {
             log.info("doRebalance, {}, add a new mq, {}", consumerGroup, mq);
             PullRequest pullRequest = new PullRequest();
             pullRequest.setConsumerGroup(consumerGroup);
             pullRequest.setNextOffset(nextOffset);
             pullRequest.setMessageQueue(mq);
             pullRequest.setProcessQueue(pq);
             pullRequestList.add(pullRequest);
             changed = true;
          }
    } else {
         log.warn("doRebalance, {}, add new mq failed, {}", consumerGroup, mq);
    }
  }
}
```

上述的重点是代码@1，顺序消息时，添加该消息队列的拉取任务之前，首先要先尝试锁定消费者(消费组+`CID`)，不同消费组的消费者可以同时锁定同一个消息消费队列，集群模式下同一个消费组内只能被一个消费者锁定，如果锁定成功，则添加到拉取任务中，如果锁定未成功，说明虽然发送了消息队列重新负载，但该消息队列还未被释放，本次负载周期不会进行消息拉取。锁定消息队列的具体实现，下文详细讲解。

## 2、消息拉取 ##

DefaultMQPushConsumerImpl\#pullMessage(final PullRequest pullRequest)

```
if (this.consumeOrderly) {
if (processQueue.isLocked()) {
                if (!pullRequest.isLockedFirst()) {
                    final long offset = this.rebalanceImpl.computePullFromWhere(pullRequest.getMessageQueue());
                    boolean brokerBusy = offset < pullRequest.getNextOffset();
                    log.info("the first time to pull message, so fix offset from broker. pullRequest: {} NewOffset: {} brokerBusy: {}",
                        pullRequest, offset, brokerBusy);
                    if (brokerBusy) {
                        log.info("[NOTIFYME]the first time to pull message, but pull request offset larger than broker consume offset. pullRequest: {} NewOffset:          
                              {}", pullRequest, offset);
                    }
                    pullRequest.setLockedFirst(true);
                    pullRequest.setNextOffset(offset);
                }
      } else {
          this.executePullRequestLater(pullRequest, PULL_TIME_DELAY_MILLS_WHEN_EXCEPTION);
          log.info("pull message later because not locked in broker, {}", pullRequest);
          return;
     }
}
```

根据 `PullRequest` 拉取消息。如果处理队列未被锁定，则延迟拉取消息，也就说消息消费需要在 `ProceeQueue` 队列被自己锁定的情况下才会拉取消息，否则将 `PullRequest` 延迟3s再拉取。并且 `PullRequest` 的初始拉取点在拉取时只在第一次拉取时设置。

## 3、消息顺序消息消费 ##

实现类：`org.apache.rocketmq.client.impl.consumer.ConsumeMessageOrderlyService`

### 3.1核心属性与构造函数 ###

![img\_0914\_01\_1.png][img_0914_01_1.png]

```html
 *  private final static long MAX\_TIME\_CONSUME\_CONTINUOUSLY =  
    Long.parseLong(System.getProperty(“rocketmq.client.maxTimeConsumeContinuously”, “60000”));  
    MAX\_TIME\_CONSUME\_CONTINUOUSLY ：消费任务一次运行的最大时间。可以通过-Drocketmq.client.maxTimeConsumeContinuously来设置，默认为60s。
 *  DefaultMQPushConsumerImpl defaultMQPushConsumerImpl：消息消费者实现类。
 *  DefaultMQPushConsumer defaultMQPushConsumer：消息消费者。
 *  MessageListenerOrderly messageListener：顺序消息消费监听器。
 *  BlockingQueue< Runnable> consumeRequestQueue：消息消费任务。
 *  ThreadPoolExecutor consumeExecutor：消息消费线程池。
 *  String consumerGroup：消息消费组。
 *  MessageQueueLock messageQueueLock：消息消费队列锁，其内如实现为：
```  
    ![img\_0914\_01\_2.png][img_0914_01_2.png]  
    构造方法如下：

```
public ConsumeMessageOrderlyService(DefaultMQPushConsumerImpl defaultMQPushConsumerImpl,
        MessageListenerOrderly messageListener) {
        this.defaultMQPushConsumerImpl = defaultMQPushConsumerImpl;
        this.messageListener = messageListener;

        this.defaultMQPushConsumer = this.defaultMQPushConsumerImpl.getDefaultMQPushConsumer();
        this.consumerGroup = this.defaultMQPushConsumer.getConsumerGroup();
        this.consumeRequestQueue = new LinkedBlockingQueue<Runnable>();  // @1

        this.consumeExecutor = new ThreadPoolExecutor(
            this.defaultMQPushConsumer.get(),
            this.defaultMQPushConsumer.getConsumeThreadMax(),
            1000 * 60,
            TimeUnit.MILLISECONDS,
            this.consumeRequestQueue,ConsumeThreadMin
            new ThreadFactoryImpl("ConsumeMessageThread_"));  // @2

        this.scheduledExecutorService = Executors.newSingleThreadScheduledExecutor(new ThreadFactoryImpl("ConsumeMessageScheduledThread_")); 
                //@3
    }
```

代码@1：创建任务拉取队列，注意，这里使用的是无界队列。  
代码@2：创建消费者消费线程池，注意由于消息任务队列 `consumeRequestQueue` 使用的是无界队列，故线程池中最大线程数量取自 `consumeThreadMin`。  
代码@3：创建调度线程，该线程主要调度定时任务，延迟延迟消费等。

### 3.2 start方法 ###

```
public void start() {
        if (MessageModel.CLUSTERING.equals(ConsumeMessageOrderlyService.this.defaultMQPushConsumerImpl.messageModel())) {
            this.scheduledExecutorService.scheduleAtFixedRate(new Runnable() {
                @Override
                public void run() {
                    ConsumeMessageOrderlyService.this.lockMQPeriodically();
                }
            }, 1000 * 1, ProcessQueue.REBALANCE_LOCK_INTERVAL, TimeUnit.MILLISECONDS);
        }
    }
```

如果消息消费模式为集群模式，启动定时任务，默认每隔20s执行一次锁定分配给自己的消息消费队列。通过 `-Drocketmq.client.rebalance.lockInterval=20000` 设置间隔，该值建议与一次消息负载频率设置相同。该方法最终将调用`RebalanceImpl\#lockAll`方法.

```
public void lockAll() {
        HashMap<String, Set<MessageQueue>> brokerMqs = this.buildProcessQueueTableByBrokerName();  // @1

        Iterator<Entry<String, Set<MessageQueue>>> it = brokerMqs.entrySet().iterator();
        while (it.hasNext()) {
            Entry<String, Set<MessageQueue>> entry = it.next();
            final String brokerName = entry.getKey();
            final Set<MessageQueue> mqs = entry.getValue();

            if (mqs.isEmpty())
                continue;

            FindBrokerResult findBrokerResult = this.mQClientFactory.findBrokerAddressInSubscribe(brokerName, MixAll.MASTER_ID, true); // @2
            if (findBrokerResult != null) {
                LockBatchRequestBody requestBody = new LockBatchRequestBody();
                requestBody.setConsumerGroup(this.consumerGroup);
                requestBody.setClientId(this.mQClientFactory.getClientId());
                requestBody.setMqSet(mqs);

                try {
                    Set<MessageQueue> lockOKMQSet =
                        this.mQClientFactory.getMQClientAPIImpl().lockBatchMQ(findBrokerResult.getBrokerAddr(), requestBody, 1000);  // @3

                    for (MessageQueue mq : lockOKMQSet) {     // @4
                        ProcessQueue processQueue = this.processQueueTable.get(mq);
                        if (processQueue != null) {
                            if (!processQueue.isLocked()) {
                                log.info("the message queue locked OK, Group: {} {}", this.consumerGroup, mq);
                            }

                            processQueue.setLocked(true);                                                                          
                            processQueue.setLastLockTimestamp(System.currentTimeMillis());
                        }
                    }
                    for (MessageQueue mq : mqs) {  // @5
                        if (!lockOKMQSet.contains(mq)) {
                            ProcessQueue processQueue = this.processQueueTable.get(mq);
                            if (processQueue != null) {
                                processQueue.setLocked(false);
                                log.warn("the message queue locked Failed, Group: {} {}", this.consumerGroup, mq);
                            }
                        }
                    }
                } catch (Exception e) {
                    log.error("lockBatchMQ exception, " + mqs, e);
                }
            }
        }
    }
```

代码@1：根据当前负载的消息队列，按照 `Broker` 分类存储在 `Map`。负载的消息队列在 `RebalanceService` 时根据当前消费者数量与消息消费队列按照负载算法进行分配，然后尝试对该消息队列加锁，如果申请锁成功，则加入到待拉取任务中。  
代码@2：根据 `Broker` 获取主节点的地址。  
代码@3：向 `Broker` 发送锁定消息队列请求，该方法会返回本次成功锁定的消息消费队列，关于 `Broker` 端消息队列锁定实现见下文详细分析。  
代码@4：遍历本次成功锁定的队列来更新对应的 `ProcessQueue` 的 `locked` 状态，如果 `locked` 为 `false`,则设置成 `true`,并更新锁定时间。  
代码@5：遍历 `mqs`，如果消息队列未成功锁定，需要将 `ProceeQueue` 的 `locked` 状态为 `false`，在该处理队列未被其他消费者锁定之前，该消息队列将暂停拉取消息。

### 3.3 submitConsumeRequest ###

提交消息消费。

```
public void submitConsumeRequest(
        final List<MessageExt> msgs,
        final ProcessQueue processQueue,
        final MessageQueue messageQueue,
        final boolean dispathToConsume) {
        if (dispathToConsume) {
            ConsumeRequest consumeRequest = new ConsumeRequest(processQueue, messageQueue);
            this.consumeExecutor.submit(consumeRequest);
        }
    }
```

顺序消息消费，不会处理传入的消息，而是从消息队列中顺序去获取。接下来重点探讨`ConsumeMessageOrderlyService\#ConsumeRequest`。

### 3.4 ConsumeMessageOrderlyService\#ConsumeRequest ###

![img\_0914\_01\_3.png][img_0914_01_3.png]

 *  `ProcessQueue processQueue`：消息处理队列。
 *  `MessageQueue` `messageQueue`：消息队列。

这里并没有要处理的消息，而是等下需要从 `ProcessQueue` 中获取消息。

顺序消息消费流程通过 `ConsumeMessageOrderlyService\#ConsumeRequest\#run` 方法来实现。

```
if (this.processQueue.isDropped()) {
    log.warn("run, the message queue not be able to consume, because it's dropped. {}", this.messageQueue);
    return;
 }
```

Step1：如果消息队列状态为 dropped 为true,则停止本次消息消费。

```
final Object objLock = messageQueueLock.fetchLockObject(this.messageQueue);
synchronized (objLock) { ...
```

Step2：获取 `MessageQueue` 对应的锁，在消费某一个消息消费队列时先加锁，意味着一个消费者内消费线程池中的线程并发度是消息消费队列级别，同一个消费队列在同一时刻只会被一个线程消费，其他线程排队消费。

```
if (MessageModel.BROADCASTING.equals(ConsumeMessageOrderlyService.this.defaultMQPushConsumerImpl.messageModel())
                    || (this.processQueue.isLocked() && !this.processQueue.isLockExpired())) {
     //消息消费逻辑
} else {
if (this.processQueue.isDropped()) {
             log.warn("the message queue not be able to consume, because it's dropped. {}", this.messageQueue);
       return;
}
ConsumeMessageOrderlyService.this.tryLockLaterAndReconsume(this.messageQueue, this.processQueue, 100);
}
```

Step3：如果是广播模式的话，直接进入消费，无需锁定处理对列，因为相互直接无竞争，如果是集群模式，能消息消息 的前提条件就是必须 `proceessQueue` 被锁定并且锁未超时。

会不会出现这样一种情况：发生消息队列重新负载时，原先由自己处理的消息队列被另外一个消费者分配，此时如果还未来的及将`ProceeQueue`解除锁定，就被另外一个消费者添加进去，此时会存储多个消息消费者同时消费个消息队列。

答案是不会的，因为当一个新的消费队列分配给消费者时，在添加其拉取任务之前必须先向 `Broker` 发送对该消息队列加锁请求，只有加锁成功后，才能添加拉取消息，否则等到下一次负载后，该消费队列被原先占有的解锁后，才能开始新的拉取任务。集群模式下，如果未锁定处理队列，则延迟该队列的消息消费。

```
final long beginTime = System.currentTimeMillis();
 for (boolean continueConsume = true; continueConsume; ) {
     ... 省略相关代码
    long interval = System.currentTimeMillis() - beginTime;
    if (interval > MAX_TIME_CONSUME_CONTINUOUSLY) {
           ConsumeMessageOrderlyService.this.submitConsumeRequestLater(processQueue, messageQueue, 10);
           break;
    }
}
```

Step4：顺序消息消费处理逻辑，每一个`ConsumeRequest`消费任务不是以消费消息条数来计算，而是根据消费时间，默认当消费时长大于`MAX\_TIME\_CONSUME\_CONTINUOUSLY`，默认60s后，本次消费任务结束，由消费组内其他线程继续消费。

```
if (this.processQueue.isDropped()) {
    log.warn("the message queue not be able to consume, because it's dropped. {}", this.messageQueue);
    break;
 }
if (MessageModel.CLUSTERING.equals(ConsumeMessageOrderlyService.this.defaultMQPushConsumerImpl.messageModel())
         && （ !this.processQueue.isLocked() ||  this.processQueue.isLockExpired()  ）) {
    log.warn("the message queue not locked, so consume later, {}", this.messageQueue);
    ConsumeMessageOrderlyService.this.tryLockLaterAndReconsume(this.messageQueue, this.processQueue, 10);
    break;
}
```

Step5：如果消息消费队列被丢弃，则直接结束本次消息消费。如果是集群模式，消息处理队列未加锁或锁过期，则尝试对消息队列加锁，加锁成功则再提交消费任务，否则延迟3s再提交消费任务。

```
final int consumeBatchSize =
       ConsumeMessageOrderlyService.this.defaultMQPushConsumer.getConsumeMessageBatchMaxSize();
List<MessageExt> msgs = this.processQueue.takeMessags(consumeBatchSize);
```

Step6：每次从处理队列中按顺序取出`consumeBatchSize`消息，如果未取到消息，则设置`continueConsume为false`，本次消费任务结束。  
顺序消息消费时，从`ProceessQueue`中取出的消息，会临时存储在`ProceeQueue的consumingMsgOrderlyTreeMap`属性中。

```
this.lockTreeMap.writeLock().lockInterruptibly();
this.lastConsumeTimestamp = now;
try {
       if (!this.msgTreeMap.isEmpty()) {
            for (int i = 0; i < batchSize; i++) {
                  Map.Entry<Long, MessageExt> entry = this.msgTreeMap.pollFirstEntry();
                  if (entry != null) {
                        result.add(entry.getValue());
                        consumingMsgOrderlyTreeMap.put(entry.getKey(), entry.getValue());
                   } else {
                       break;
                  }
              }
        }
          if (result.isEmpty()) {
            consuming = false;
        }
} finally {
      this.lockTreeMap.writeLock().unlock();
}
```

Step7：执行消息消费钩子函数（消息消费之前before方法），通过  
`DefaultMQPushConsumerImpl\#registerConsumeMessageHook(ConsumeMessageHook consumeMessagehook)`注册消息消费钩子函数，可以注册多个。

```
long beginTimestamp = System.currentTimeMillis();
ConsumeReturnType returnType = ConsumeReturnType.SUCCESS;
boolean hasException = false;
try {
    this.processQueue.getLockConsume().lock();
    if (this.processQueue.isDropped()) {
        log.warn("consumeMessage, the message queue not be able to consume, because it's dropped. {}",
                                        this.messageQueue);
        break;
     }
      status = messageListener.consumeMessage(Collections.unmodifiableList(msgs), context);
 } catch (Throwable e) {
     log.warn("consumeMessage exception: {} Group: {} Msgs: {} MQ: {}",RemotingHelper.exceptionSimpleDesc(e),
     ConsumeMessageOrderlyService.this.consumerGroup,msgs,messageQueue);
     hasException = true;
 } finally {
     this.processQueue.getLockConsume().unlock();
}
```

Step8：调用消息消费监听器供业务程序消息消费，并返回消息消费结果。 返回结果：`ConsumeOrderlyStatus.SUCCESS成功或SUSPEND\_CURRENT\_QUEUE\_A\_MOMENT`挂起，延迟的意思。  
Step9：执行消息消费钩子函数，就算` messageListener.consumeMessage` 抛出异常，钩子函数同样会执行。

Step10：如果消费结果为 `ConsumeOrderlyStatus.SUCCESS`，执行 `ProceeQueue` 的 `commit` 方法，并返回待更新的消息消费进度。

`ProceeQueue\#commit`

```
public long commit() {
        try {
            this.lockTreeMap.writeLock().lockInterruptibly();
            try {
                Long offset = this.consumingMsgOrderlyTreeMap.lastKey();
                msgCount.addAndGet(0 - this.consumingMsgOrderlyTreeMap.size());
                for (MessageExt msg : this.consumingMsgOrderlyTreeMap.values()) {
                    msgSize.addAndGet(0 - msg.getBody().length);
                }
                this.consumingMsgOrderlyTreeMap.clear();
                if (offset != null) {
                    return offset + 1;
                }
            } finally {
                this.lockTreeMap.writeLock().unlock();
            }
        } catch (InterruptedException e) {
            log.error("commit exception", e);
        }

        return -1;
    }
```

首先申请 `lockTreeMap` 写锁，获取 `consumingMsgOrderlyTreeMap` 中最大的消息偏移量 `offset`，`consumingMsgOrderlyTreeMap` 中存放的是本批消费的消息。然后更新 `msgCount`、`msgSize`，并清除 `consumingMsgOrderlyTreeMap`。并返回offset+1消息消费进度，从中可以看出 `offset` 表示消息消费队列的逻辑偏移量，类似于数组下标，然后调用消息进度存储器存储消息消费进度，完成该批消息的消费。

Step11：如果消息消费返回`SUSPEND\_CURRENT\_QUEUE\_A\_MOMENT`，表示需要挂起。

```
if (checkReconsumeTimes(msgs)) {
     consumeRequest.getProcessQueue().makeMessageToCosumeAgain(msgs);
     this.submitConsumeRequestLater(
             consumeRequest.getProcessQueue(),
             consumeRequest.getMessageQueue(),
             context.getSuspendCurrentQueueTimeMillis());
      continueConsume = false;
} else {
      commitOffset = consumeRequest.getProcessQueue().commit();
}
```

 *  检查消息的重试次数。如果消息重试次数大于或等于允许的最大重试次数，将该消息发送到 `Broker` 端，该消息在消息服务端最终会进入到`DLQ`(死信队列)，也就是 `RocketMQ` 不会再次消费，需要人工干预。如果消息成功进入到 `DLQ` 队列，`checkReconsumeTimes` 返回 `false`，该批消息将直接调用 `ProcessQueue\#commit` 提交，表示消息消费成功，如果这批消息中有任意一条消息的重试次数小于允许的最大重试次数，将返回 `true`，执行消息重试。
 *  消息消费重试，先将该批消息重新放入到 `ProcessQueue` 的 `msgTreeMap`，然后清除 `consumingMsgOrderlyTreeMap`，默认延迟1s再加入到消费队列中，并结束此次消息消费。可以通过`DefaultMQPushConsumer\#setSuspendCurrentQueueTimeMillis`设置当前队列重试挂起时间。如果执行消息重试，因为消息消费进度并未向前推进，故本次视为无效消费，将不更新消息消费进度。

顺序消息的整个流程基本分析完成，最后来看一下消息队列锁定的具体实现。

### 3.4 消息队列锁实现 ###

消息消费的各个环节基本都是围绕消息消费队列(`MessageQueue`)与消息处理队列(`ProceeQueue`)展开的。消息消费进度拉取，消息进度消费都要判断 `ProceeQueue的locked` 是否为 `true`,在设置 `ProceeQueue` 为 `true` 的前提条件是消息消费者(`cid`)向 `Broker` 端发送锁定消息队列的请求并返回加锁成功。

服务端关于MessageQueue加锁处理类:`org.apache.rocketmq.broker.client.rebalance.RebalanceLockManager`。类图如下。  
![img\_0914\_01\_4.png][img_0914_01_4.png]  
核心属性：

```html
 *  REBALANCE\_LOCK\_MAX\_LIVE\_TIME ：锁最大存活时间。可以通过-Drocketmq.broker.rebalance.lockMaxLiveTime设置，默认为60s。
 *  mqLockTable： 消息消费队列锁容器，按消费组分组。不同的消费组队同一消息消费队列可以同时加锁，同一个消费组内不同的消费者只允许其中一个队同一消息消费队列加锁成功。
```

核心方法：

 ```html
*  public Set< MessageQueue> tryLockBatch(final String group, final Set< MessageQueue> mqs,final String clientId)  
    申请对mqs消息消费队列集合加锁。String group：消息消费组名；Set< MessageQueue> mqs：待加锁的消息消费队列集合；String clientId：消息消费者（cid)。返回成功加锁的消息队列集合。
 *  public void unlockBatch(final String group, final Set< MessageQueue> mqs, final String clientId)  
    对消息队列集合解锁。String group：消息消费组名；Set< MessageQueue> mqs：待解锁的消息消费队列集合；String clientId：当前消息队列锁拥有者。
```

其实现原理比较简单，主要就是维护 `mqLockTable`，比较简单，就不细入分析了。

顺序消息消费的实现原理一句话就是对消息队列做什么事情之前，先申请该消息队列的锁。无论是创建消息队列拉取任务、拉取消息、消息消费无不如此。

--------------------

备注：本文是《RocketMQ技术内幕》的前期素材，建议关注笔者的书籍：《RocketMQ技术内幕》。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/16/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/16/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/16/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/16/img_0914_01_4.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")