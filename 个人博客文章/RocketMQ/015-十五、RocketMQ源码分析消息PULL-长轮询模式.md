作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

### 本节目录 ###

 *  1、长轮询、短轮询概述
    
    2、`RocketMQ` 拉轮询拉取任务创建
    
    3、源码分析 `PullRequestHoldService` 线程
    
     *  3.1 `PullRequestHoldService\#suspendPullRequest`
     *  3.2 `run` 方法详解
    
    4、源码分析`DefaultMessageStore\#ReputMessageService`
    
     *  4.1 `run` 方法
     *  4.2 `doReput`

## 1、长轮询、短轮询概述 ##

消息拉取为了提高网络性能，在消息服务端根据拉取偏移量去物理文件查找消息时没有找到，并不立即返回消息未找到，而是会将该线程挂起一段时间，然后再次重试，直到重试。挂起分为长轮询或短轮询，在broker 端可以通过 `longPollingEnable=true` 来开启长轮询。

短轮询：`longPollingEnable=false`，第一次未拉取到消息后等待 `shortPollingTimeMills` 时间后再试。`shortPollingTimeMills` 默认为1S。

长轮询：`longPollingEnable=true`，会根据消费者端设置的挂起超时时间，受 `DefaultMQPullConsumer` 的`brokerSuspendMaxTimeMillis`控制，默认`20s`,（`brokerSuspendMaxTimeMillis`），长轮询有两个线程来相互实现。

 *  PullRequestHoldService：每隔5s重试一次。
 *  DefaultMessageStore\#ReputMessageService，每当有消息到达后，转发消息，然后调用PullRequestHoldService 线程中的拉取任务，尝试拉取，每处理一次，Thread.sleep(1), 继续下一次检查。

## 2、RocketMQ拉轮询拉取任务创建 ##

`org.apache.rocketmq.broker.processor.PullMessageProcessor\#processRequest`

首先看一下 `processRequest` 方法参数：

```
private RemotingCommand processRequest(
                   final Channel channel, 
                   RemotingCommand request, 
                   boolean brokerAllowSuspend)
```

```html
 *  Channel channel：网络通道
 *  RemotingCommand request：消息拉取请求
 *  brokerAllowSuspend：是否允许挂起，也就是是否允许在未找到消息时暂时挂起线程。第一次调用时默认为true。
```

```
case ResponseCode.PULL_NOT_FOUND:
    if (brokerAllowSuspend && hasSuspendFlag) {   // @1
         long pollingTimeMills = suspendTimeoutMillisLong;  // @2
         if (!this.brokerController.getBrokerConfig().isLongPollingEnable()) { // @3
                pollingTimeMills = this.brokerController.getBrokerConfig().getShortPollingTimeMills();
          }
         String topic = requestHeader.getTopic();
         long offset = requestHeader.getQueueOffset();
         int queueId = requestHeader.getQueueId();
         PullRequest pullRequest = new PullRequest(request, channel, pollingTimeMills,
               this.brokerController.getMessageStore().now(), offset, subscriptionData, messageFilter);  // @4
        this.brokerController.getPullRequestHoldService().suspendPullRequest(topic, queueId, pullRequest);  
        response = null;  // @5
        break;
     }
```

代码@1：`hasSuspendFlag`, 构建消息拉取时的拉取标记，默认为true。  
代码@2：`suspendTimeoutMillisLong`：取自 `DefaultMQPullConsumer` 的 `brokerSuspendMaxTimeMillis` 属性。  
代码@3：如果不支持长轮询，则忽略 `brokerSuspendMaxTimeMillis` 属性，使用 `shortPollingTimeMills`，默认为 `1000ms` 作为下一次拉取消息的等待时间。  
代码@4：创建 `PullRequest`, 然后提交给 `PullRequestHoldService` 线程去调度，触发消息拉取。  
代码@5：关键，设置 `response=null`，则此时此次调用不会向客户端输出任何字节，客户端网络请求客户端的读事件不会触发，不会触发对响应结果的处理，处于等待状态。

在拉取消息时，如果拉取结果为 `PULL\_NOT\_FOUND`，在服务端默认会挂起线程，然后根据是否启用长轮询机制，延迟一定时间后再次重试根据偏移量查找消息。

再研究轮询查找消息之前先看一下 `PullRequest` 对象的核心属性：  
![img\_0914\_01\_1.png][img_0914_01_1.png]

```html
 *  requestCommand：请求命令
 *  clientChannel：网络连接，通过该通道向客户端返回响应结果。
 *  timeoutMills：超时时长
 *  suspendTimestamp：挂起开始时间戳,如果当前系统>=(timeoutMills+suspendTimestamp)表示已超时。
 *  pullFromThisOffset：带拉取消息队列偏移量
 *  subscriptionData：订阅信息。
 *  messageFilter：消息过滤器
```

RocketMQ轮询机制由两个线程共同来完成：

```html
 *  PullRequestHoldService：每隔5S重试一次。
 *  DefaultMessageStore\#ReputMessageService，每处理一次重新拉取，Thread.sleep(1),继续下一次检查。
```

## 3、源码分析PullRequestHoldService线程 ##

### 3.1 PullRequestHoldService\#suspendPullRequest ###

```
public void suspendPullRequest(final String topic, final int queueId, final PullRequest pullRequest) {
        String key = this.buildKey(topic, queueId);
        ManyPullRequest mpr = this.pullRequestTable.get(key);
        if (null == mpr) {
            mpr = new ManyPullRequest();
            ManyPullRequest prev = this.pullRequestTable.putIfAbsent(key, mpr);
            if (prev != null) {
                mpr = prev;
            }
        }
        mpr.addPullRequest(pullRequest);
    }
```

根据主题名称 + 队列id, 获取 ManyPullRequest, 对于同一个 topic + 队列的拉取请求用 ManyPullRequest包装，然后将 pullRequest 添加到 ManyPullRequest 中。

### 3.2 run方法详解 ###

PullRequestHoldService\#run

```
public void run() {
        log.info("{} service started", this.getServiceName());
        while (!this.isStopped()) {
            try {
                if (this.brokerController.getBrokerConfig().isLongPollingEnable()) {   
                    this.waitForRunning(5 * 1000);      // @1
                } else {
                    this.waitForRunning(this.brokerController.getBrokerConfig().getShortPollingTimeMills());  //@2
                }

                long beginLockTimestamp = this.systemClock.now();
                this.checkHoldRequest();     // @3
                long costTime = this.systemClock.now() - beginLockTimestamp;
                if (costTime > 5 * 1000) {
                    log.info("[NOTIFYME] check hold request cost {} ms.", costTime);
                }
            } catch (Throwable e) {
                log.warn(this.getServiceName() + " service has exception. ", e);
            }
        }

        log.info("{} service end", this.getServiceName());
    }
```

代码@1:如果开启了长轮询模式，则每次只挂起 5s，然后就去尝试拉取。  
代码@2:如果不开启长轮询模式，则只挂起一次，挂起时间为 `shortPollingTimeMills`，然后去尝试查找消息。  
代码@3:遍历 `pullRequestTable`，如果拉取任务的待拉取偏移量小于当前队列的最大偏移量时执行拉取，否则如果没有超过最大等待时间则等待，否则返回未拉取到消息，返回给消息拉取客户端。具体请看：  
`PullRequestHoldService\#checkHoldRequest。`

```
private void checkHoldRequest() {
        for (String key : this.pullRequestTable.keySet()) {
            String[] kArray = key.split(TOPIC_QUEUEID_SEPARATOR);
            if (2 == kArray.length) {
                String topic = kArray[0];
                int queueId = Integer.parseInt(kArray[1]);
                final long offset = this.brokerController.getMessageStore().getMaxOffsetInQueue(topic, queueId);  // @1
                try {
                    this.notifyMessageArriving(topic, queueId, offset);  // @2
                } catch (Throwable e) {
                    log.error("check hold request failed. topic={}, queueId={}", topic, queueId, e);
                }
            }
        }
    }
```

代码@1：根据主题，消费队列ID查找队列的最大偏移量。  
代码@2：根据该offset，判断是否有新的消息达到。

`PullRequestHoldService\#notifyMessageArriving`

```
public void notifyMessageArriving(final String topic, final int queueId, final long maxOffset, final Long tagsCode,
        long msgStoreTime, byte[] filterBitMap, Map<String, String> properties) { // @1
        String key = this.buildKey(topic, queueId);
        ManyPullRequest mpr = this.pullRequestTable.get(key);    
        if (mpr != null) {
            List<PullRequest> requestList = mpr.cloneListAndClear(); // @2
            if (requestList != null) {
                List<PullRequest> replayList = new ArrayList<PullRequest>();

                for (PullRequest request : requestList) {
                    long newestOffset = maxOffset;
                    if (newestOffset <= request.getPullFromThisOffset()) {   // @3
                        newestOffset = this.brokerController.getMessageStore().getMaxOffsetInQueue(topic, queueId);
                    }

                    if (newestOffset > request.getPullFromThisOffset()) {  // @4
                        boolean match = request.getMessageFilter().isMatchedByConsumeQueue(tagsCode,
                            new ConsumeQueueExt.CqExtUnit(tagsCode, msgStoreTime, filterBitMap));
                        // match by bit map, need eval again when properties is not null.
                        if (match && properties != null) {   
                            match = request.getMessageFilter().isMatchedByCommitLog(null, properties);
                        }

                        if (match) { 
                            try {
                                this.brokerController.getPullMessageProcessor().executeRequestWhenWakeup(request.getClientChannel(),
                                    request.getRequestCommand()); 
                            } catch (Throwable e) {
                                log.error("execute request when wakeup failed.", e);
                            }
                            continue;
                        }
                    }

                    if (System.currentTimeMillis() >= (request.getSuspendTimestamp() + request.getTimeoutMillis())) {  // @5
                        try {
                            this.brokerController.getPullMessageProcessor().executeRequestWhenWakeup(request.getClientChannel(),
                                request.getRequestCommand());
                        } catch (Throwable e) {
                            log.error("execute request when wakeup failed.", e);
                        }
                        continue;
                    }

                    replayList.add(request);
                }

                if (!replayList.isEmpty()) {
                    mpr.addPullRequest(replayList);  // @6
                }
            }
        }
    }
```

代码@1：参数详解：

```html
 *  String topic：主题名称。
 *  int queueId：队列id。
 *  long maxOffset：消费队列当前最大偏移量。
 *  Long tagsCode：消息tag hashcode //基于tag消息过滤。
 *  long msgStoreTime：消息存储时间。
 *  byte\[\] filterBitMap：过滤位图。
 *  Map<String, String> properties：消息属性,基于属性的消息过滤。
```

代码@2：获取主题与队列的所有 `PullRequest` 并清除内部 `pullRequest` 集合，避免重复拉取。  
代码@3：如果待拉取偏移量(`pullFromThisOffset`)大于消息队列的最大有效偏移量，则再次获取消息队列的最大有效偏移量，再给一次机会。  
代码@4：如果队列最大偏移量大于 `pullFromThisOffset` 说明有新的消息到达，先简单对消息根据 tag,属性进行一次消息过滤，如果 tag,属性为空，则消息过滤器会返回true,然后 `executeRequestWhenWakeup` 进行消息拉取，结束长轮询。  
代码@5：如果挂起时间超过 `suspendTimeoutMillisLong`，则超时，结束长轮询，调用 `executeRequestWhenWakeup` 进行消息拉取，并返回结果到客户端。  
代码@6：如果待拉取偏移量大于消息消费队列最大偏移量，并且未超时，调用 `mpr.addPullRequest(replayList)` 将拉取任务重新放入，待下一次检测。

`ManyPullRequest\#addPullRequest`

```
public synchronized void addPullRequest(final List<PullRequest> many) {
        this.pullRequestList.addAll(many);
}
```

这个方法值得注意一下，为什么 `addPullRequest` 方法会加锁呢？原因是 `ReputMessageService` 内部其实会持有 `PullRequestHoldService` 的引用，也就是在运行过程中，对于拉取任务, `ReputMessageService`、`PullRequestHoldService` 处理的任务是同一个集合。

继续代码@5：重点跟进一下 `executeRequestWhenWakeup` 方法。

`PullMessageProcessor\#executeRequestWhenWakeup`

```
public void executeRequestWhenWakeup(final Channel channel,
        final RemotingCommand request) throws RemotingCommandException {
        Runnable run = new Runnable() {
            @Override
            public void run() {
                try {
                    final RemotingCommand response = PullMessageProcessor.this.processRequest(channel, request, false);

                    if (response != null) {
                        response.setOpaque(request.getOpaque());
                        response.markResponseType();
                        try {
                            channel.writeAndFlush(response).addListener(new ChannelFutureListener() {
                                @Override
                                public void operationComplete(ChannelFuture future) throws Exception {
                                    if (!future.isSuccess()) {
                                        log.error("processRequestWrapper response to {} failed",
                                            future.channel().remoteAddress(), future.cause());
                                        log.error(request.toString());
                                        log.error(response.toString());
                                    }
                                }
                            });
                        } catch (Throwable e) {
                            log.error("processRequestWrapper process request over, but response failed", e);
                            log.error(request.toString());
                            log.error(response.toString());
                        }
                    }
                } catch (RemotingCommandException e1) {
                    log.error("excuteRequestWhenWakeup run", e1);
                }
            }
        };
        this.brokerController.getPullMessageExecutor().submit(new RequestTask(run, channel, request));
    }
```

这里的核心亮点是：在调用 `PullMessageProcessor.this.processRequest(channel, request, false)` 方法是，最后一个参数是 `false`,表示 `broker` 端不支持挂起，这样在 `PullMessageProcessor` 方法中，如果没有查找消息，也不会继续再挂起了，因为进入这个方法时，拉取的偏移量是小于队列的最大偏移量，正常情况下是可以拉取到消息的。

## 4、源码分析DefaultMessageStore\#ReputMessageService ##

ReputMessageService启动入口：DefaultMessageStore\#satart方法：

```
if (this.getMessageStoreConfig().isDuplicationEnable()) {
    this.reputMessageService.setReputFromOffset(this.commitLog.getConfirmOffset());
 } else {
    this.reputMessageService.setReputFromOffset(this.commitLog.getMaxOffset());
 }
 this.reputMessageService.start();
```

首先，设置 `reputFromOffset` 偏移量，如果允许重复，初始偏移量为 `confirmOffset`，否则设置为 `commitLog` 当前最大偏移量。

### 4.1 run方法 ###

```
public void run() {
    DefaultMessageStore.log.info(this.getServiceName() + " service started");
    while (!this.isStopped()) {
         try {
                 Thread.sleep(1);
                  this.doReput();
          } catch (Exception e) {
                  DefaultMessageStore.log.warn(this.getServiceName() + " service has exception. ", e);
          }
      }
      DefaultMessageStore.log.info(this.getServiceName() + " service end");
}
```

从 `run` 方法可以看出 `ReputMessageService` 线程是一个“拼命三郎”，每执行完一次业务方法 `doReput`，休息1毫秒，就继续抢占 `CPU`，再次执行 `doReput` 方法。

### 4.2 doReput ###

```
private void doReput() {
for (boolean doNext = true; this.isCommitLogAvailable() && doNext; ) {
      	if (DefaultMessageStore.this.getMessageStoreConfig().isDuplicationEnable()
                    && this.reputFromOffset >= DefaultMessageStore.this.getConfirmOffset()) {
             break;
         }
         SelectMappedBufferResult result = DefaultMessageStore.this.commitLog.getData(reputFromOffset);   // @1
         if (result != null) {
         	try {
                        this.reputFromOffset = result.getStartOffset();
                        for (int readSize = 0; readSize < result.getSize() && doNext; ) {  //@2
                            DispatchRequest dispatchRequest =
                                DefaultMessageStore.this.commitLog.checkMessageAndReturnSize(result.getByteBuffer(), false, false); //@3
                            int size = dispatchRequest.getMsgSize();

                            if (dispatchRequest.isSuccess()) {
                                if (size > 0) {
                                    DefaultMessageStore.this.doDispatch(dispatchRequest);  // @4

                                    if (BrokerRole.SLAVE != DefaultMessageStore.this.getMessageStoreConfig().getBrokerRole()
                                        && DefaultMessageStore.this.brokerConfig.isLongPollingEnable()) {                                              // @5
                                        DefaultMessageStore.this.messageArrivingListener.arriving(dispatchRequest.getTopic(),
                                            dispatchRequest.getQueueId(), dispatchRequest.getConsumeQueueOffset() + 1,
                                            dispatchRequest.getTagsCode(), dispatchRequest.getStoreTimestamp(),
                                            dispatchRequest.getBitMap(), dispatchRequest.getPropertiesMap());
                                    }

                                    this.reputFromOffset += size;
                                    readSize += size;
                                    if (DefaultMessageStore.this.getMessageStoreConfig().getBrokerRole() == BrokerRole.SLAVE) {
                                        DefaultMessageStore.this.storeStatsService
                                            .getSinglePutMessageTopicTimesTotal(dispatchRequest.getTopic()).incrementAndGet();
                                        DefaultMessageStore.this.storeStatsService
                                            .getSinglePutMessageTopicSizeTotal(dispatchRequest.getTopic())
                                            .addAndGet(dispatchRequest.getMsgSize());
                                    }
                                } else if (size == 0) {
                                    this.reputFromOffset = DefaultMessageStore.this.commitLog.rollNextFile(this.reputFromOffset);
                                    readSize = result.getSize();
                                }
                            } else if (!dispatchRequest.isSuccess()) {

                                if (size > 0) {
                                    log.error("[BUG]read total count not equals msg total size. reputFromOffset={}", reputFromOffset);
                                    this.reputFromOffset += size;
                                } else {
                                    doNext = false;
                                    if (DefaultMessageStore.this.brokerConfig.getBrokerId() == MixAll.MASTER_ID) {
                                        log.error("[BUG]the master dispatch message to consume queue error, COMMITLOG OFFSET: {}",
                                            this.reputFromOffset);

                                        this.reputFromOffset += result.getSize() - readSize;
                                    }
                                }
                            }
                        }
                } finally {
                     result.release();
                }
          } else {
                doNext = false;
          }
    }
}
```

代码@1：根据偏移量读取偏移量+到 `commitlog` 文件中有效数据的最大偏移量。如果未找到数据，结束 `doReput` 方法。  
代码@2：循环从 `SelectMappedBufferResult` 中读取消息，每次读取一条。  
代码@3：从 `SelectMappedBufferResult` 中读取一条消息，生成 `DispatchRequest` 对象。  
代码@4：根据 comitlog 文件内容实时构建 `consumequeue`、`index` 文件的关键所在，该部分详情请参考：http://blog.csdn.net/prestigeding/article/details/79156276  
代码@5：如果开启了长轮询并且角色为主节点，则通知有新消息到达，执行一次 pullRequest 验证。  
`NotifyMessageArrivingListener` 代码，最终调用 `pullRequestHoldService` 的 `notifyMessageArriving` 方法，进行一次消息拉取。

只要待拉取偏移量小于消息消费队列的最大偏移量，既可以被唤醒进行消息拉取。

```
public class NotifyMessageArrivingListener implements MessageArrivingListener {
    private final PullRequestHoldService pullRequestHoldService;

    public NotifyMessageArrivingListener(final PullRequestHoldService pullRequestHoldService) {
        this.pullRequestHoldService = pullRequestHoldService;
    }

    @Override
    public void arriving(String topic, int queueId, long logicOffset, long tagsCode,
        long msgStoreTime, byte[] filterBitMap, Map<String, String> properties) {
        this.pullRequestHoldService.notifyMessageArriving(topic, queueId, logicOffset, tagsCode,
            msgStoreTime, filterBitMap, properties);
    }
}
```

`rocketmq` 消息拉取长轮询机制就介绍到这里。

首先，要开启长轮询， 在 `broker` 配置文件中 `longPollingEnable=true`, 默认是开启的。

然后在 broker 端根据偏移量去消息存储文件中查找消息时，如果未找到，会挂起线程，然后轮询查找消息。所谓的轮询是轮询待拉取消息偏移大于消息消费队列的最大偏移量时才挂起，一旦检测发现待拉取消息偏移量小于消费队列最大偏移量时，则尝试拉取消息，结束长轮询过程。

--------------------

备注：本文是《RocketMQ技术内幕》的前期素材，建议关注笔者的书籍：《RocketMQ技术内幕》。

[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/15/img_0914_01_1.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
