作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

## 本文目录 ##

 *  1、抛出问题
    
     *  1.1 环境准备
     *  1.2 消息发送者代码
     *  1.3 消费端验证代码
    
    2、探究CONSUME\_FROM\_MAX\_OFFSET实现原理
    
     *  2.1 CONSUME\_FROM\_LAST\_OFFSET计算逻辑
     *  2.2 CONSUME\_FROM\_FIRST\_OFFSET
     *  2.4 CONSUME\_FROM\_TIMESTAMP
    
    3、猜想与验证
    
    4、解决方案

## 1、抛出问题 ##

一个新的消费组订阅一个已存在的 `Topic` 主题时，消费组是从该 `Topic` 的哪条消息开始消费呢？

首先翻阅 `DefaultMQPushConsumer` 的 `API` 时，`setConsumeFromWhere(ConsumeFromWhere consumeFromWhere)API`映入眼帘，从字面意思来看是设置消费者从哪里开始消费，正是解开该问题的”钥匙“。`ConsumeFromWhere` 枚举类图如下：  
![img\_0914\_01\_1.png][img_0914_01_1.png]

```html
 *  CONSUME\_FROM\_MAX\_OFFSET  
    从消费队列最大的偏移量开始消费。
 *  CONSUME\_FROM\_FIRST\_OFFSET  
    从消费队列最小偏移量开始消费。
 *  CONSUME\_FROM\_TIMESTAMP  
    从指定的时间戳开始消费，默认为消费者启动之前的30分钟处开始消费。可以通过DefaultMQPushConsumer\#setConsumeTimestamp。
```

是不是点小激动，还不快试试。

需求：新的消费组启动时，从队列最后开始消费，即只消费启动后发送到消息服务器后的最新消息。

### 1.1 环境准备 ###

本示例所用到的Topic路由信息如下：  
![img\_0914\_01\_2.png][img_0914_01_2.png]

**Broker的配置如下(broker.conf)**

```
brokerClusterName = DefaultCluster
brokerName = broker-a
brokerId = 0
deleteWhen = 04
fileReservedTime = 48
brokerRole = ASYNC_MASTER
flushDiskType = ASYNC_FLUSH

storePathRootDir=E:/SH2019/tmp/rocketmq_home/rocketmq4.5_simple/store
storePathCommitLog=E:/SH2019/tmp/rocketmq_home/rocketmq4.5_simple/store/commitlog
namesrvAddr=127.0.0.1:9876
autoCreateTopicEnable=false
mapedFileSizeCommitLog=10240
mapedFileSizeConsumeQueue=2000
```

其中重点修改了如下两个参数：

```html
 *  mapedFileSizeCommitLog  
    单个commitlog文件的大小，这里使用10M，方便测试用。
 *  mapedFileSizeConsumeQueue  
    单个consumequeue队列长度，这里使用1000，表示一个consumequeue文件中包含1000个条目。
```

### 1.2 消息发送者代码 ###

```
public static void main(String[] args) throws MQClientException, InterruptedException {
    DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
    producer.setNamesrvAddr("127.0.0.1:9876");
    producer.start();
    for (int i = 0; i < 300; i++) {
        try {
            Message msg = new Message("TopicTest" ,"TagA" , ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
            SendResult sendResult = producer.send(msg);
            System.out.printf("%s%n", sendResult);
        } catch (Exception e) {
            e.printStackTrace();
            Thread.sleep(1000);
        }
    }
    producer.shutdown();
}
```

通过上述，往 `TopicTest` 发送300条消息，发送完毕后，`RocketMQ` `Broker` 存储结构如下：  
![img\_0914\_01\_3.png][img_0914_01_3.png]

### 1.3 消费端验证代码 ###

```
public static void main(String[] args) throws InterruptedException, MQClientException {
    DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("my_consumer_01");
    consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_LAST_OFFSET);
    consumer.subscribe("TopicTest", "*");
    consumer.setNamesrvAddr("127.0.0.1:9876");
    consumer.registerMessageListener(new MessageListenerConcurrently() {
        @Override
        public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
            ConsumeConcurrentlyContext context) {
            System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), msgs);
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        }
    });
    consumer.start();
    System.out.printf("Consumer Started.%n");
}
```

执行上述代码后，按照期望，应该是不会消费任何消息，只有等生产者再发送消息后，才会对消息进行消费，事实是这样吗？执行效果如图所示：  
![img\_0914\_01\_4.png][img_0914_01_4.png]  
**令人意外的是，竟然从队列的最小偏移量开始消费了**，这就“尴尬”了。难不成是 `RocketMQ` 的 `Bug`。带着这个疑问，从源码的角度尝试来解读该问题，并指导我们实践。

## 2、探究CONSUME\_FROM\_MAX\_OFFSET实现原理 ##

对于一个新的消费组，无论是集群模式还是广播模式都不会存储该消费组的消费进度，可以理解为-1,此时就需要根据`DefaultMQPushConsumer\#consumeFromWhere`属性来决定其从何处开始消费，首先我们需要找到其对应的处理入口。我们知道，消息消费者从 `Broker` 服务器拉取消息时，需要进行消费队列的负载，即 `RebalanceImpl`。

> 温馨提示：本文不会详细介绍RocketMQ消息队列负载、消息拉取、消息消费逻辑，只会展示出通往该问题的简短流程，如想详细了解消息消费具体细节，建议购买笔者出版的《RocketMQ技术内幕》书籍。

RebalancePushImpl\#computePullFromWhere

```
public long computePullFromWhere(MessageQueue mq) {
        long result = -1;                                                                                                                                                                                                                  // @1
        final ConsumeFromWhere consumeFromWhere = this.defaultMQPushConsumerImpl.getDefaultMQPushConsumer().getConsumeFromWhere();    
        final OffsetStore offsetStore = this.defaultMQPushConsumerImpl.getOffsetStore();
        switch (consumeFromWhere) {
            case CONSUME_FROM_LAST_OFFSET_AND_FROM_MIN_WHEN_BOOT_FIRST:
            case CONSUME_FROM_MIN_OFFSET:
            case CONSUME_FROM_MAX_OFFSET:
            case CONSUME_FROM_LAST_OFFSET: {                                                                                                                                                                // @2
               // 省略部分代码
                break;
            }
            case CONSUME_FROM_FIRST_OFFSET: {                                                                                                                                                              // @3
                // 省略部分代码
                break;
            }
            case CONSUME_FROM_TIMESTAMP: {                                                                                                                                                                  //@4
                // 省略部分代码
                break;
            }
            default:
                break;
        }
        return result;                                                                                                                                                                                                                  // @5
    }
```

代码@1：先解释几个局部变量。

 *  result  
    最终的返回结果，默认为-1。
 *  consumeFromWhere  
    消息消费者开始消费的策略，即CONSUME\_FROM\_LAST\_OFFSET等。
 *  offsetStore  
    offset存储器，消费组消息偏移量存储实现器。

代码@2：CONSUME\_FROM\_LAST\_OFFSET(从队列的最大偏移量开始消费)的处理逻辑，下文会详细介绍。

代码@3：CONSUME\_FROM\_FIRST\_OFFSET(从队列最小偏移量开始消费)的处理逻辑，下文会详细介绍。

代码@4：CONSUME\_FROM\_TIMESTAMP(从指定时间戳开始消费)的处理逻辑，下文会详细介绍。

代码@5：返回最后计算的偏移量，从该偏移量出开始消费。

### 2.1 CONSUME\_FROM\_LAST\_OFFSET计算逻辑 ###

```
case CONSUME_FROM_LAST_OFFSET: {
    long lastOffset = offsetStore.readOffset(mq, ReadOffsetType.READ_FROM_STORE);   // @1
    if (lastOffset >= 0) {                                                                                                             // @2
        result = lastOffset;
    }
    // First start,no offset
    else if (-1 == lastOffset) {                                                                                                  // @3
        if (mq.getTopic().startsWith(MixAll.RETRY_GROUP_TOPIC_PREFIX)) {               
            result = 0L;
        } else {
            try {
                result = this.mQClientFactory.getMQAdminImpl().maxOffset(mq);                     
            } catch (MQClientException e) {                                                                              // @4
                result = -1;
            }
        }
    } else {
        result = -1;    
    }
	break;
}
```

代码@1：使用 `offsetStore` 从消息消费进度文件中读取消费消费进度，本文将以集群模式为例展开。稍后详细分析。

代码@2：如果返回的偏移量大于等于0，则直接使用该 `offset`，这个也能理解，大于等于0，表示查询到有效的消息消费进度，从该有效进度开始消费，但我们要特别留意 `lastOffset` 为0是什么场景，因为返回0，并不会执行CONSUME\_FROM\_LAST\_OFFSET(语义)。

代码@3：如果 `lastOffset` 为-1,表示当前并未存储其有效偏移量，可以理解为第一次消费，如果是消费组重试主题，从重试队列偏移量为0开始消费；如果是普通主题，则从队列当前的最大的有效偏移量开始消费，即CONSUME\_FROM\_LAST\_OFFSET语义的实现。

代码@4：如果从远程服务拉取最大偏移量拉取异常或其他情况，则使用-1作为第一次拉取偏移量。

**分析，上述执行的现象，虽然设置的是CONSUME\_FROM\_LAST\_OFFSET，但现象是从队列的第一条消息开始消费，根据上述源码的分析，只有从消费组消费进度存储文件中取到的消息偏移量为0时，才会从第一条消息开始消费，故接下来重点分析消息消费进度存储器(`OffsetStore`)在什么情况下会返回0。**

接下来我们将以集群模式来查看一下消息消费进度的查询逻辑，集群模式的消息进度存储管理器实现为：  `RemoteBrokerOffsetStore`,最终 `Broker` 端的命令处理类为：`ConsumerManageProcessor`。

```
ConsumerManageProcessor#queryConsumerOffset
private RemotingCommand queryConsumerOffset(ChannelHandlerContext ctx, RemotingCommand request) throws RemotingCommandException {
    final RemotingCommand response =
        RemotingCommand.createResponseCommand(QueryConsumerOffsetResponseHeader.class);
    final QueryConsumerOffsetResponseHeader responseHeader =
        (QueryConsumerOffsetResponseHeader) response.readCustomHeader();
    final QueryConsumerOffsetRequestHeader requestHeader =
        (QueryConsumerOffsetRequestHeader) request
            .decodeCommandCustomHeader(QueryConsumerOffsetRequestHeader.class);

    long offset =
        this.brokerController.getConsumerOffsetManager().queryOffset(
            requestHeader.getConsumerGroup(), requestHeader.getTopic(), requestHeader.getQueueId());    // @1

    if (offset >= 0) {                                                                                                                                          // @2
        responseHeader.setOffset(offset);
        response.setCode(ResponseCode.SUCCESS);
        response.setRemark(null);
    } else {                                                                                                                                                       // @3
        long minOffset =
            this.brokerController.getMessageStore().getMinOffsetInQueue(requestHeader.getTopic(),
                requestHeader.getQueueId());                                                                                                     // @4
        if (minOffset <= 0
            && !this.brokerController.getMessageStore().checkInDiskByConsumeOffset(                                // @5
            requestHeader.getTopic(), requestHeader.getQueueId(), 0)) {
            responseHeader.setOffset(0L);
            response.setCode(ResponseCode.SUCCESS);
            response.setRemark(null);
        } else {                                                                                                                                                 // @6
            response.setCode(ResponseCode.QUERY_NOT_FOUND);
            response.setRemark("Not found, V3_0_6_SNAPSHOT maybe this group consumer boot first");
        }
    }
    return response;
}
```

代码@1：从消费消息进度文件中查询消息消费进度。

代码@2：如果消息消费进度文件中存储该队列的消息进度，其返回的`offset`必然会大于等于0，则直接返回该偏移量该客户端，客户端从该偏移量开始消费。

代码@3：如果未从消息消费进度文件中查询到其进度，`offset`为-1。则首先获取该主题、消息队列当前在Broker服务器中的最小偏移量(@4)。如果小于等于0(返回0则表示该队列的文件还未曾删除过)并且其最小偏移量对应的消息存储在内存中而不是存在磁盘中，则返回偏移量0，这就意味着 `ConsumeFromWhere` 中定义的三种枚举类型都不会生效，直接从0开始消费，到这里就能解开其谜团了(@5)。

代码@6：如果偏移量小于等于0，但其消息已经存储在磁盘中，此时返回未找到，最终`RebalancePushImpl\#computePullFromWhere`中得到的偏移量为-1。

看到这里，大家应该能回答文章开头处提到的问题了吧？

**看到这里，大家应该明白了，为什么设置的CONSUME\_FROM\_LAST\_OFFSET，但消费组是从消息队列的开始处消费了吧，原因就是消息消费进度文件中并没有找到其消息消费进度，并且该队列在Broker端的最小偏移量为0，说的更直白点，`consumequeue/topicName/queueNum`的第一个消息消费队列文件为00000000000000000000,并且消息其对应的消息缓存在`Broke`r端的内存中(`pageCache`)，其返回给消费端的偏移量为0，故会从0开始消费，而不是从队列的最大偏移量处开始消费。**

为了知识体系的完备性，我们顺便来看一下其他两种策略的计算逻辑。

### 2.2 CONSUME\_FROM\_FIRST\_OFFSET ###

```
case CONSUME_FROM_FIRST_OFFSET: {
    long lastOffset = offsetStore.readOffset(mq, ReadOffsetType.READ_FROM_STORE);   // @1
    if (lastOffset >= 0) {    // @2
        result = lastOffset;
    } else if (-1 == lastOffset) {  // @3
        result = 0L;
    } else {                                  
        result = -1;                    // @4
    }
    break;
}
```

从队列的开始偏移量开始消费，其计算逻辑如下：  
代码@1：首先通过偏移量存储器查询消费队列的消费进度。

代码@2：如果大于等于0，则从当前该偏移量开始消费。

代码@3：如果远程返回-1，表示并没有存储该队列的消息消费进度，从0开始。

代码@4：否则从-1开始消费。

### 2.4 CONSUME\_FROM\_TIMESTAMP ###

从指定时戳后的消息开始消费。

```
case CONSUME_FROM_TIMESTAMP: {
    ong lastOffset = offsetStore.readOffset(mq, ReadOffsetType.READ_FROM_STORE);   // @1
    if (lastOffset >= 0) {                                                                                                            // @2
        result = lastOffset;
    } else if (-1 == lastOffset) {                                                                                                 // @3
        if (mq.getTopic().startsWith(MixAll.RETRY_GROUP_TOPIC_PREFIX)) {
            try {
                result = this.mQClientFactory.getMQAdminImpl().maxOffset(mq);
            } catch (MQClientException e) {
                result = -1;
            }
        } else {
            try {
                long timestamp = UtilAll.parseDate(this.defaultMQPushConsumerImpl.getDefaultMQPushConsumer().getConsumeTimestamp(),
                    UtilAll.YYYYMMDDHHMMSS).getTime();
                result = this.mQClientFactory.getMQAdminImpl().searchOffset(mq, timestamp);
            } catch (MQClientException e) {
                result = -1;
            }
        }
    } else {
        result = -1;
    }
    break;
}
```

其基本套路与CONSUME\_FROM\_LAST\_OFFSET一样：  
代码@1：首先通过偏移量存储器查询消费队列的消费进度。

代码@2：如果大于等于0，则从当前该偏移量开始消费。

代码@3：如果远程返回-1，表示并没有存储该队列的消息消费进度，如果是重试主题，则从当前队列的最大偏移量开始消费，如果是普通主题，则根据时间戳去 `Broker` 端查询，根据查询到的偏移量开始消费。

原理就介绍到这里，下面根据上述理论对其进行验证。

## 3、猜想与验证 ##

根据上述理论分析我们得知设置CONSUME\_FROM\_LAST\_OFFSET但并不是从消息队列的最大偏移量开始消费的“罪魁祸首”是因为消息消费队列的最小偏移量为0，如果不为0，则就会符合预期，我们来验证一下这个猜想。  
首先我们删除commitlog目录下的文件，如图所示：  
![img\_0914\_01\_5.png][img_0914_01_5.png]  
其消费队列截图如下：  
![img\_0914\_01\_6.png][img_0914_01_6.png]  
消费端的验证代码如下：

```
public static void main(String[] args) throws InterruptedException, MQClientException {
    DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("my_consumer_02");
    consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_LAST_OFFSET);
    consumer.subscribe("TopicTest", "*");
    consumer.setNamesrvAddr("127.0.0.1:9876");
    consumer.registerMessageListener(new MessageListenerConcurrently() {
        @Override
        public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
            ConsumeConcurrentlyContext context) {
            System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), msgs);
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        }
    });
    consumer.start();
    System.out.printf("Consumer Started.%n");
}
```

运行结果如下：  
![img\_0914\_01\_7.png][img_0914_01_7.png]  
并没有消息存在的消息，符合预期。

## 4、解决方案 ##

如果在生产环境下，一个新的消费组订阅一个已经存在比较久的 `topic`，设置CONSUME\_FROM\_MAX\_OFFSET是符合预期的，即该主题的`consumequeue/\{queueNum\}/fileName，fileName`通常不会是`00000000000000000000`，如是是上面文件名，想要实现从队列的最后开始消费，该如何做呢？那就走自动创建消费组的路子，执行如下命令：

```
./mqadmin updateSubGroup -n 127.0.0.1:9876 -c DefaultCluster -g my_consumer_05

//克隆一个订阅了该topic的消费组消费进度
./mqadmin cloneGroupOffset -n 127.0.0.1:9876 -s my_consumer_01 -d my_consumer_05 -t TopicTest

//重置消费进度到当前队列的最大值
./mqadmin resetOffsetByTime -n 127.0.0.1:9876 -g my_consumer_05 -t TopicTest -s -1
```

按照上上述命令后，即可实现其目的。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_5.png
[img_0914_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_6.png
[img_0914_01_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/32/img_0914_01_7.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
