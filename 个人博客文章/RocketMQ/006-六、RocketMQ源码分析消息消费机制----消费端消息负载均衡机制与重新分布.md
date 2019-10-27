作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

# 1、消息消费需要解决的问题 #

首先再次重复啰嗦一下 `RocketMQ` 消息消费的一些基本元素的关系

主题 ---》 消息队列(`MessageQueue`) 1 对多。

主题 ---》 消息生产者，一般主题会由多个生产者组成，生产者组。

主题 ---》 消息消费者，一般一个主题也会被多个消费者消费。

那消息消费至少需要解决如下问题：

1、一个消费组中多个消费者是如何对消息队列（1个主题多个消息队列）进行负载消费的。

2、一个消费者中多个线程又是如何协作（并发）的消费分配给该消费者的消息队列中的消息呢？

3、消息消费进度如何保存，包括MQ是如何知道消息是否正常被消费了。

4、`RocketMQ` 推拉模式实现机制。

再提一个业界关于消费者与消息队列的消费规则。

1个消费者可以消费多个消息队列，但一个消息队列同一时间只能被一个消费者消费，这又是如何实现的呢？

本文紧接着上文：[消息消费概述](https://www.ycbbs.vip/?p=996)。

继续探讨消息分发与消费端负载均衡。

我们从上文知道，`PullMessageService` 线程主要是负责 `pullRequestQueue` 中的 `PullResult`，那问题来了，`pullRequestQueue` 中的数据从哪来，在什么时候由谁来填充呢。

![img\_0914\_01\_1.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-01.png)

那我们就先沿着这条线索分析下去，看一下 `PullMessageService` 的 `pullReqestQueue` 添加元素的方法的调用链条如下：

![img\_0914\_01\_2.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-02.png)

也就是调用链：

```
RebalanceService. run()

MQClientInstance.doRebalance()

DefaultMQPulConsumerImpl.doRebalance()

RebalanceImpl.doRebalance()

RebalanceImpl.rebalanceByTopic

RebalanceImpl.updateProcessQueueTableInRebalance

RebalanceImpl.dispatchPullRequest

DefaultMQPushConsumerImpl.executePullRequestImmediately

```

从上面可以直观的看出，向 `PullMesssageService` 的 `LinkedBlockingQueue<PullRequest> pullRequestQueue` 添加 `PullRequest` 的是 `RebalanceService.run` 方法，就是向 `PullMessageService` 中放入 `PullRequest`,才会驱动 `PullMessageSerivce` `run` 方法的运行，如果 `pullRequestQueue` 中没有元素，`PullMessageService` 线程将被阻塞。

那么 `RebalanceService` 是何许人也，让我们一起来揭开其神秘面纱。

# 2、消息消费负载机制分析 #

## 2.1 RebalanceService 线程 ##

![img\_0914\_01\_3.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-03.png)

![img\_0914\_01\_4.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-04.png)

从上面可以看出，`MQClientInstance` 持有一个 `RebalanceService` 线程并启动它。`RebalanceService` 线程的 `run` 方法比较简单，就是直接调用 `mqClientFactory.doRebalance`。

下面重点分步骤来详细探究 `MQClientInstance`.doRebalance 方法的执行流程。

### 2.1.1 MQClientInstance.doRebalance ###

循环遍历每个消费组获取 `MQConsumeInner` 对象（其实就是 `DefaultMQPushConsumerImpl` 或 `DefaultMQPullConsumerImpl` 对象），并执行其 `doRebalance` 方法。

![img\_0914\_01\_5.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-05.png)

### 2.1.2 DefaultMQPushConsumerImpl.doRebalance ###

![img\_0914\_01\_6.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-06.png)

RebalanceImpl doRebalance

![img\_0914\_01\_7.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-07.png)

到这里，经过层层对象委托，终于进入到实现消息负载分发的核心地带了，RebalanceImpl 类，我们应该停下脚步，先重点认识一下RebalanceImpl类。

# 3、RebalanceImpl 类初探 #

我们先来看看其核心属性：

![img\_0914\_01\_8.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-08.png)
```html
 *  ConcurrentMap<MessageQueue, ProcessQueue> processQueueTable  
    消息处理队列。
 *  ConcurrentMap<String, Set<MessageQueue> topicSubscribeInfoTable  
    topic 的队列信息。
 *  ConcurrentMap<String, SubscriptionData> subscriptionInner  
    订阅信息。
 *  String consumerGroup  
    消费组名称。
 *  MessageModel messageModel  
    消费模式。
 *  AllocateMessageQueueStrategy allocateMessageQueueStrategy  
    队列分配算法。
 *  MQClientInstance mqClientFactory  
    MQ 客户端实例。
```

下面还是从 `doRebalance` 方法入手：

![img\_0914\_01\_9.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-09.png)

1、根据 `topic` 来进行负载。

2、移除 `MessageQueue`，如果 `MesageQueue` 的 `topic` 不在订阅的主题中，接下来重点关注 `rebalanceByTopic` 方法。

`RebalanceImpl` `rebalanceByTopic` 详解:

part1:根据消息消费模式（集群还是广播）我们先重点看集群模式。

part2: 获取主题的消息消费队列、主题与该消费组的消费者id列表,任意一个为空，则退出方法的执行。

![img\_0914\_01\_10.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-10.png)

part3: 主要是对主题的消息队列排序、消费者 `ID` 进行排序，然后利用分配算法，计算当前消费者`ID(mqClient.clientId)` 分配出需要拉取的消息队列。

具体的消息消费队列分配算法参考：`AllocateMessageQueueStrategy` 的实现类，具体算法实现就不细化研究了。

在这里举一个最简单的队列分配机制，，比如一个 `topic` 有8个消息队列(`q1,q2,q3,q4,q5,q6,q7,q8`) ，比如有三个消费者 `c1,c2,c3`

一种队列负载算法： `q1,q4,q7` 分给`c1,,q2,q5,q8 c2,,q3,q5 `给 `c3`。下文会专题研究一下负载算法。

![img\_0914\_01\_11.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-11.png)

part4: 更新主题的消息消费处理队列，并返回消息队列负载是否改变。

![img\_0914\_01\_12.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-12.png)

![img\_0914\_01\_13.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-13.png)

![img\_0914\_01\_14.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-14.png)

遍历消息队列-处理队列缓存，只处理 `mq` 的主题与该主题相关的 `ProcessQueue`, 如果 `mq` 不在当期主题的处理范围内（由于消息队列数量变化等原因，消费者的消费队列发生了变化，该消息队列已经分配给别的消费者去消费了），首先设置该消息队列为丢弃 (`dropped` 为 `voliate` 修饰)，可以及时的阻止继续向 `ProceeQueue` 中拉取数据，然后执行`removeUnecessaryMessageQueue(mq,pq) `来判断是否需要移除。

![img\_0914\_01\_15.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-15.png)

既然我们都是从 `Push` 进入的，本文以 `Push` 模式展开(同时我们也可以先思考思考 `push`,`pull` 差别)，移步到 `RebalancePushImpl`。

![img\_0914\_01\_16.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-16.png)

目前只看非顺序消息，逻辑就比较简单了，丢弃之前，先将 `MessageQueue` 消息消费进度 持久化，然后丢弃，重新被其他消费者加载。顺序消息将会本系列的后续文章中详细介绍。

接下来处理 `MessageQueue` 的 `ProcessQueue`,也就是在 `ProcessQueueTable` 中没有 `mq` 的处理队列（因为重新负载后，可能会分配一些新的队列）。

![img\_0914\_01\_17.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-17.png)

主要就是在内存中移除 `MessageQueue` 的 `offerset`, 然后计算下一个拉取偏移量，然后每一个 `MessageQueue` 创建一个拉取任务(`PullRequest`)。

![img\_0914\_01\_18.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-18.png)

RebalancePushImpl

![img\_0914\_01\_19.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-19.png)

PullMessageService

![img\_0914\_01\_20.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-20.png)

往 `PullServiceMessage` 中的 `pullRequestQueue` 中放入 `PullRequest`,则 `PullMessageService` 线程 的 `run` 方法就不会阻塞

![img\_0914\_01\_21.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-21.png)

part5:如果消息负载发生变化，需处理

![img\_0914\_01\_22.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-22.png)

主要是调整主题小各个队列的拉取阔值。

![img\_0914\_01\_23.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/06-23.png)

这里，主要看出来当消费者挂断后，或主题消息队列动态变化后，消息负载会发生变化的重新分布情况。

**总结：**

--------------------

本文主要阐述了消息消费端负载机制，这里消息非顺序消息机制就梳理到这里了，大概再总结一下：

1、首先 `RebalanceService` 线程启动，为消费者分配消息队列，其实每一个 `MessageQueue` 会构建一个 `PullRequest` 对象，然后通过 `RebalanceImpl` 将 `PullRequest` 放入到 `PullMessageService` 线程的 `LinkedBlockingQueue`, 进而唤醒 `queue.take()`方法，然后执行 `DefaultMQPushConsumerImpl` 的 `pullMessage`,通过网络从 `broker` 端拉取消息，一次最多拉取的消息条数可配置，默认为32条，然后然后将拉取的消息，执行过滤等，然后封装成任务（`ConsumeRequest`）,提交到消费者的线程池去执行，每次消费消息后，又将该 `PullRequest` 放入到 `PullMessageService` 中（`DefaultMQPushConsumerImpl` 的机制就是 `pullInterval` 为 `0`；

下文预告：

`CommitLog` 写入与 `ConsumeQueue` 队列的持久化机制

消息消费进度存储机制，再谈 `RocketMQ` 消息存储

`RocketMQ` 顺序消息

`RocketMQ` 主从机制

备注：本文是《 `RocketMQ` 技术内幕》的原始素材，建议关注笔者的书籍：《 `RocketMQ` 技术内幕》。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")