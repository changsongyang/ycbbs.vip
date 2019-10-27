作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

主要关注业务方在消息消费失败后，返回 `ConsumeConcurrentlyStatus.RECONSUME\_LATER` ,专业术语：业务方每条消息消费后要告诉 `MQ` 消费者一个结果(`ack`,`message` `back`)，触发 `MQ` 消息消费重试机制，然后 `MQ` 消费者需要反馈给 `MQ(Broker)`。

备注：主要针对的还是非顺序消息，顺序消息在后续专题详细分析。

# 1、消息消费处理 #

代码入口：`ConsumeMessageConcurrentlyService ConsumeRequest run`方法

![img\_0914\_01\_1.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-01.png)

然后进入到结果处理：`ConsumeMessageConcurrentlyService processConsumeResult`

![img\_0914\_01\_2.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-02.png)

![img\_0914\_01\_3.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-03.png)

如果返回结果是 `CONSUME\_SUCCESS`，此时 `ackIndex = msg.size()` `- 1`,再看发送 `sendMessageBack` 循环的条件，`for (int i = ackIndex + 1; i < msg.size() ;;) `从这里可以看出如果消息成功，则无需发送 `sendMsgBack` 给 `broker`。

如果返回结果是 `RECONSUME\_LATER`， 此时 `ackIndex = -1 `，则这批所有的消息都会发送消息给 `Broker`,也就是这一批消息都得重新消费。如果发送 `ack` 失败，则会延迟5s后重新在消费端重新消费。

消费者向 `Broker` 发送 `ACK` 消息，如果发送成功，重试机制由 `broker` 处理，如果发送 `ack` 消息失败，则将该任务直接在消费者这边，再次在本地处理该批消息，默认演出5s后在消费者重新消费,其关键总结如下：

 *  根据消费结果，设置 `ackIndex` 的值
 *  如果是消费失败，根据消费模式（集群消费还是广播消费），广播模式，直接丢弃，集群模式发送 `sendMessageBack`。
 *  更新消息消费进度，不管消费成功与否，上述这些消息消费成功，其实就是修改消费偏移量。（失败的，会进行重试，会创建新的消息)。

然后我们重点跟踪 `sendMessageBack` 方法：

`DefaultMQPushConsumerImpl sendMessageBack`

![img\_0914\_01\_4.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-04.png)

核心实现要点如下：

 *  首先根据 `brokerName` 得到 `broker` 地址信息，然后通过网络发送到指定的 `Broker` 上。
 *  如果上述过程失败，则创建一条新的消息重新发送给 `Broker`,此时新消息的主题为重试主题：`"%RETRY%"` + `ConsumeGroupName`, 注意，这里的主题和原先的消息主题没任何关系而是和消费组相关。

`SendMessageProcessor processRequest`

![img\_0914\_01\_5.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-05.png)

最近在 `Broker` 端会处理 `CONSUMER\_SEND\_MSG\_BACK`命令。

# 2、Broker 端关于CONSUMER\_SEND\_MSG\_BACK 的处理逻辑 #

## 2.1、获取消费组的订阅信息 ##

其代码入口：`SendMessageProcessor\#consumerSendMsgBack`。

![img\_0914\_01\_6.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-06.png)

![img\_0914\_01\_7.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-07.png)

其核心熟悉解释如下：

 *  `groupName`  
    消费组名称，`RocketMQ` 消息消费重试不是以主题，而是以消费组。
 *  `retryQueueNums`  
    重试队列的数量，读队列，写队列个数（主题）。
 *  `retryMaxTimes`  
    允许最大的重复次数。

## 2.2、根据重试主题创建或获取该主题的路由信息 ##

`SendMessageProcessor\#consumerSendMsgBack`

![img\_0914\_01\_8.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-08.png)

`TopicConfigManager \#createTopicInSendMessageBackMethod`

![img\_0914\_01\_9.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-09.png)

如果创建主题配置信息错误，会抛出系统异常，产生的效果是消费端发送ACK消息错误，会创建一条新的消息，消息内部ID为原消息ID,然后重新发送给Broker。

## 2.3、然后根据消息偏移量尝试从commitlog日志文件中获取消息内容 ##

`SendMessageProcessor\#consumerSendMsgBack`

![img\_0914\_01\_10.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-10.png)

2、4、延迟级别、消费次数处理

`SendMessageProcessor\#consumerSendMsgBack`

![img\_0914\_01\_11.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-11.png)

如果消息次数或延迟级别小于0，设置消息的主题为 DLQ+ 消费组名称，如果消息的延迟级别为0,则 3 + 消息重试的次数。

## 2.5、重新发送该消息到commitlog中 ##

![img\_0914\_01\_12.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-12.png)

![img\_0914\_01\_13.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-13.png)

如果消息发送成功，则返回成功，否则返回错误，消费端会将这些消息直接在消费端延迟5S后重新消费。

现在成功将消息发送到 `commitlog` 中，主题为 `RETRY\_TOPIC` + 消费组名称，，也就是消息重试的消息主题是基于消费组。而不是每一个主题都有一个重试主题。而是每一个消费组由一个重试主题。那这些主题的消息，又是如何在被消费者获取并进行消费的。

然后进行消费进度更新：

![img\_0914\_01\_14.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-14.png)

进度更新，本文不深入学习，后续会专门研究消费进度保持机制。

目前，重试机制的前半部分已经讲解完成，再次复习一下：

 *  根据消费结果，设置 `ackIndex` 的值。
 *  如果是消费失败，根据消费模式（集群消费还是广播消费），广播模式，直接丢弃，集群模式发送 `sendMessageBack`，这里会创建新的消息（重试次数，延迟执行）。
 *  更新消息消费进度，不管消费成功与否，上述这些消息消费成功，其实就是修改消费偏移量。（失败的，会进行重试，会创建新的消息)。

消息现在是存储到 `commitlog` 文件里了，那怎么消费呢。

# 3、延迟消息机制 #

通篇搜索 `DelayLevel`,一个比较关键的类 `org.apache.rocketmq.store.schedule.ScheduleMessageService` 映入眼帘，稍微浏览一下，就知道该类与延迟类消息息息相关，但是处理的主题却是 `SCHEDULE\_TOPIC = "SCHEDULE\_TOPIC\_XXXX"`，我们延迟消息的主题却是 `RETRY` + 消费组名称，主题不一样呀，得继续找，继续全文搜索 `delayLevel`,发现 `CommitLog` 类的 `putMessage` 中竟然也出现了 `delayLevel` 相关的处理，我们重点观察一下该代码：`org.apache.rocketmq.store.CommitLog\#putMessage`

![img\_0914\_01\_15.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-15.png)

注意，在消息存入 `commitlog` 之前，如果发现延迟 `level` 大于0，会将消息的主题设置为`SCHEDULE\_TOPIC = "SCHEDULE\_TOPIC\_XXXX"`，然后备份原主题名称。那就清晰明了，延迟消息统一由 `ScheduleMessageService` 来处理。

`ScheduleMessageService` 的源码我就不一一分析了，从此类可以得出如下结论：关于 `RocketMQ` 延迟消息机制：

 *  `RocketMQ` 不支持任意频率的延时调用，而是设定不同的延时级别delayLevel,默认的延时级别对应的时间如下：`1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h`，通过`MessageStoreConfig的messageDelayLevel`属性配置，具体解析代码参考：ScheduleMessageService\#parseDelayLevel方法。
 *  延时调用通过 `java.util.timer`,该线程的名称：ScheduleMessageTimerThread
 *  延时消息主题的队列与延迟等级的关系如下：

![img\_0914\_01\_16.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-16.png)

 *  根据主题`（SCHEDULE\_TOPIC\_XXXX）`与消息队列ID，先找到 `ConsumeQueue`,然后根据偏移量，找到 `ConsumeQueue` 中的结构，，然后从 commitlog 日志中找到消息，然后将消息的延迟消息清空，然后将主题又设置为原先的主题（如果是消费失败，需要重新消费的话，主题为 `retry` + 消费组名称，然后再次进入到重试主题的队列中。

到这里，我们弄清楚了消息重试，消息的流转，但还是没有找到 `RETRY`+消费组（队列的订阅信息）。

那消费者是如何订阅 `RETRY`+消费组名称 的消费队列的呢？

原来在消费者启动时，就默认会订阅该消费组的重试主题的队列。

`org.apache.rocketmq.client.impl.consumer.DefaultMQPushConsumerImpl`

![img\_0914\_01\_17.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-17.png)

![img\_0914\_01\_18.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/07-18.png)

那一切关于RocketMQ消息重试机制的谜底就一一揭晓了。

# 4、总结 #

下面对消息消费重试做一个简单的总结：

1、如果返回结果是 `CONSUME\_SUCCESS`，此时 `ackIndex = msg.size() - 1`, 再看发送 `sendMessageBack` 循环的条件，`for (int i = ackIndex + 1; i < msg.size() ;;)` 从这里可以看出如果消息成功，则无需发送 `sendMsgBack` 给 `broker`；如果返回结果是` RECONSUME\_LATER`， 此时 `ackIndex = -1 `，则这批所有的消息都会发送消息给 `Broker`,也就是这一批消息都得重新消费。

如果发送ack消息失败，则会延迟5s后重新在消费端重新消费。

首先消费者向 `Broker` 发送 `ACK` 消息，如果发生成功，重试机制由 `broker` 处理，如果发送 `ack` 消息失败，则将该任务直接在消费者这边，再次将本次消费任务，默认演出5S后在消费者重新消费。

 *  根据消费结果，设置 `ackIndex` 的值。
 *  如果是消费失败，根据消费模式（集群消费还是广播消费），广播模式，直接丢弃，集群模式发送 `sendMessageBack`。
 *  更新消息消费进度，不管消费成功与否，上述这些消息消费成功，其实就是修改消费偏移量。（失败的，会进行重试，会创建新的消息)。

2、需要延迟执行的消息，在存入 `commitlog` 之前，会备份原先的主题(`retry`+消费组名称)、与消费队列ID，然后将主题修改为`SCHEDULE\_TOPIC\_XXXX`，会被延迟任务 `ScheduleMessageService` 延迟拉取。

3、`ScheduleMessageService` 在执行过程中，会再次存入 `commitlog` 文件中放入之前，会清空延迟等级，并恢复主题与队列，这样，就能被消费者所消费，因为消费者在启动时就订阅了该消费组的重试主题。

--------------------

备注：本文是《`RocketMQ` 技术内幕》的前期素材，建议关注笔者的书籍：《`RocketMQ` 技术内幕》。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")