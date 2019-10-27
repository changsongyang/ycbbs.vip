作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)


1、消息消费需要解决的问题

首先再次重复啰嗦一下RocketMQ消息消费的一些基本元素的关系

主题 ---》 消息队列(`MessageQueue`) 1 对多

主题 ----》 消息生产者，，，一般主题会由多个生产者组成，生产者组

主题 ---- 》 消息消费者，，一般一个主题也会被多个消费者消费


那消息消费至少需要解决如下问题：

1、一个消费组中多个消费者是如何对消息队列（1个主题多个消息队列）进

行负载消费的。

2、一个消费者中多个线程又是如何协作（并发）的消费分配给该消费者的

消息队列中的消息呢？

3、消息消费进度如何保存，包括 `MQ` 是如何知道消息是否正常被消费了。

4、`RocketMQ` 推拉模式实现机制


再提一个业界关于消费者与消息队列的消费规则

1个消费者可以消费多个消息队列，但一个消息队列同一时间只能被一个消费者消费，这又是如何实现的呢？

后续几篇文章都会围绕上述问题进行展开，读者朋友们，带上上述的问题，我们一起遨游在RocketMQ消息消费的世界中吧。

2、消费端拉取消息机制

2、1 消息消费端核心类介绍

![img\_0914\_01\_1.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-01.png) 


`DefaultMQPushConsumerImpl` ：消息消息者默认实现类，应用程序中直接用该类的实例完成消息的消费，并回调业务方法。

`RebalanceImpl` 字面上的意思（重新平衡）也就是消费端消费者与消息队列的重新分布，与消息应该分配给哪个消费者消费息息相关。

`MQClientInstance` 消息客户端实例，负载与MQ服务器（`Broker`,`Nameserver`)交互的网络实现

`PullAPIWrapper` `pull` 与 `Push` 在 `RocketMQ` 中，其实就只有 `Pull` 模式，所以 `Push` 其实就是用 `pull` 封装一下

`MessageListenerInner` 消费消费回调类，当消息分配给消费者消费时，执行的业务代码入口

`OffsetStore` 消息消费进度保存

`ConsumeMessageService` 消息消费逻辑

![img\_0914\_01\_2.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-02.png)


![img\_0914\_01\_3.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-03.png)


消费端使用实例：

![img\_0914\_01\_4.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-04.png) 


2、2 消息消费者启动关键流程

1） 构建 `RebalanceImpl` 

![img\_0914\_01\_5.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-05.png) 


2）`PullAPIWrapper` 对象构建

![img\_0914\_01\_6.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-06.png)


3）消费进度加载

![img\_0914\_01\_7.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-07.png) 


4）消费管理 `ConsumeMessageService`

![img\_0914\_01\_8.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-08.png)


5）`MQClientInstance` 启动，进入消息消费

![img\_0914\_01\_9.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-09.png)


2、2、1 MQClientInstance

![img\_0914\_01\_10.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-10.png) 


2、2、1.1 定时任务一览表

![img\_0914\_01\_11.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-11.png)


每隔2分钟尝试获取一次 `NameServer` 地址


![img\_0914\_01\_12.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-12.png)


每隔30S尝试更新主题路由信息

![img\_0914\_01\_13.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-13.png) 


每隔30S 进行 `Broker` 心跳检测

![img\_0914\_01\_14.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-14.png)


默认每隔5秒持久化ConsumeOffset

![img\_0914\_01\_15.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-15.png)

默认每隔1S检查线程池适配

上述定时任务，下文或后续文章会重点剖析一下【持久化`ConsumeOffset`】

2、2、1.2 `PullMesssageService`

![img\_0914\_01\_16.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-16.png) 

![img\_0914\_01\_17.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-17.png)

![img\_0914\_01\_18.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-18.png)


![img\_0914\_01\_19.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-19.png)


从上面感悟：一个应用程序，一个消费组，只需要一个 `DefaultMQPushConsumerImpl`,，在一个应用中，使用多线程创建多个

消费者，尝试去消费同一个组，没有效果，只会有一个消费者在消费。

`PullMessageService` 的工作职责是 从 `LinkedBlockQueue` 中循环取 `PullRequest` 对象，然后执行 `pullMessage` 方法

看到这，我不禁又冒出2个疑问：

1）`DefaultMQPushConsumerImpl` 与 `PullMessageService` 关系

2）`LinkedBlockQueue` 中的 `PullRequest` 对象在什么时候放入的。

在这里先解决都一个疑问：

我们知道，一个应用程序（消费端），一个消费组 一个 `DefaultMQPushConsumerImpl` ，同一个IP:端口，会有一个 `MQClientInstance` ，而每一个 `MQClientInstance` 中持有一个 `PullMessageServive` 实例，故可以得出如下结论：同一个应用程序中，如果存在多个消费组，那么多个 `DefaultMQPushConsumerImpl` 的消息拉取，都需要依靠一个 `PullMessageServive`。那他们之间又是如何协作的呢？

继续带着疑问，看下文：

`DefaultMQPushConsumerImpl` `pullMessage` 关键代码：

1、首先获取 `PullRequest` 的 处理队列 `ProcessQueue`,然后更新该消息队列最后一次拉取的时间。

![img\_0914\_01\_20.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-20.png) 

![img\_0914\_01\_21.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-21.png)


2、如果消费者 服务状态不为`ServiceState.RUNNING`，或当前处于暂停状态，默认延迟3秒再执行(`PullMessageService.executePullRequestLater`)

![img\_0914\_01\_22.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-22.png) 


3、流量控制，两个维度，消息数量达到阔值（默认 `1000` 个），或者消息体总大小(默认 `100m`)

![img\_0914\_01\_23.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-23.png)


![img\_0914\_01\_24.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-24.png) 


再看一下延迟执行：其实最终就是将 `PullRequest`,在50毫秒后，放入 `LinkedBlockQueue` 中，然后继续尝试拉取。

接下来，先重点分析非顺序消息（顺序消息在后续专题中继续跟进）


4、获取主题订阅信息

![img\_0914\_01\_25.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-25.png)

![img\_0914\_01\_26.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-26.png)


5、如果是集群消费模式，从内存中获取 `MessageQueue` 的 `commitlog` 偏移量。

![img\_0914\_01\_27.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-27.png)


6、构建拉取消息系统 `Flag`: 是否支持`comitOffset,suspend,subExpression,classFilter`

![img\_0914\_01\_28.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-28.png)


接下来，重点关注一下 `PullAPIWrapper` `pullKernelImpl` 的核心逻辑：

![img\_0914\_01\_29.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-29.png)


```java
public PullResult pullKernelImpl(

final MessageQueue mq, // 消息消费队列

final String subExpression, // 消息订阅子模式subscribe( topicName, "模式")

final String expressionType, // 

final long subVersion, // 版本

final long offset, // pullRequest.getNextOffset()

final int maxNums, // defaultMQPushConsumer.getPullBatchSize()

final int sysFlag, // 系统标记，FLAG\_COMMIT\_OFFSET FLAG\_SUSPEND FLAG\_SUBSCRIPTION FLAG\_CLASS\_FILTER

final long commitOffset, // 当前消息队列 commitlog日志中当前的最新偏移量（内存中）

final long brokerSuspendMaxTimeMillis, // 允许的broker 暂停的时间，毫秒为单位，默认为15s

final long timeoutMillis, // 超时时间,默认为30s

final CommunicationMode communicationMode, // SYNC ASYNC ONEWAY

final PullCallback pullCallback // pull 回调

) throws MQClientException, RemotingException, MQBrokerException, InterruptedException 

```
  


### pullKernelImpl

1) 根据 `MQ` 的 `Broker` 信息获取查找 `Broker` 信息，封装成 `FindBrokerResult`。

![img\_0914\_01\_30.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-30.png)


![img\_0914\_01\_31.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-31.png) 


然后通过网络去 拉取具体的消息，也就是消息体 中的数据。具体数据拉取逻辑，在重点分析消息存储时重点去研究。

![img\_0914\_01\_32.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-32.png) 


最终返回一个拉取结果：

![img\_0914\_01\_33.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-33.png)


同时，拉取消息，会根据拉取模式，是同步还是异步模式，调用回调或直接处理：`MQClientAPIImpl`。

![img\_0914\_01\_34.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-34.png)


接下来，已异步调用为例，分析拉取到消息后的回调处理逻辑。

代码入口：`PullCallback pullCallback = new PullCallback()`，见`DefaultMQPushConsumerImpl` 288行

1、首先对PullResult进行处理，主要完成如下3件事：
- 1）对消息体解码成一条条消息 
- 2）执行消息过滤
- 3）执行回调

![img\_0914\_01\_35.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-35.png)


![img\_0914\_01\_36.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-36.png)


2、根据拉取结果分别采取不同的策略

1）拉取到消息，首先放入到处理队列中；然后是消费消息服务提交

![img\_0914\_01\_37.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-37.png) 


第一步，将消息放入消费队列中：就是将拉取的消息，放入到 `ProcessQueue` 的 `msgTreeMap` 容器中。

![img\_0914\_01\_38.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-38.png)


![img\_0914\_01\_39.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-39.png)


第二步，消费消息服务提交

![img\_0914\_01\_40.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-40.png)


这里十分有必要对顺序消息与非顺序消息的消费方式分别了解一下

1）非顺序消息 消息消费服务 `ConumeMessageService` 的提交消费请求

![img\_0914\_01\_41.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-41.png) 


该方法重点已经标明，如果此次拉取的消息条数大于 `ConsumeMessageBatchMaxSize`,则分批消费。此处更有一个关键点，

`this.consumeExecutor.submit(consumeRequest)`

`consumeExecutor` : 消费端消费线程池

![img\_0914\_01\_42.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-42.png)


线程池的常驻线程数：`consumeThreadMin`

线程池的最大线程数：`consumeThreadMax`

线程池中的线程名：`ConsumeMessageThread`\_

这里就明确了一个点，一个消费者非顺序消费者，内部使用一个线程池来并非消费消息，一个线程一批次最大处理 `consumeMessageBatchMaxSize` 条消息。

再来关注一下，消费任务逻辑类：`ConsumeRequest`

![img\_0914\_01\_43.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-43.png)


### 下面重点分析 run 方法

run part1：执行消息消费前钩子函数

![img\_0914\_01\_44.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-44.png)


首先，获取业务系统定义的消息消费监听器，负责具体消息的消费，例如：

![img\_0914\_01\_45.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-45.png)


如果消费者注册了消息消费者hook(钩子函数，在消息消费之前，消费之后执行相关方法)

![img\_0914\_01\_46.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-46.png) 


run part2：设置消息的重试主题，并开始消费消息，并返回该批次消息消费结果：

![img\_0914\_01\_47.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-47.png)


run part3：根据是否出现异常等，判断处理结果

![img\_0914\_01\_48.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-48.png) 


run part4: 执行消息消费钩子方法，并根据消息消费结果（成功或失败）处理消费进度等。这里目前不关注其细节，如果有兴趣，可以重点看一下`ConsumeMessageConcurrentlyService.processConsumeResult`

![img\_0914\_01\_49.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-49.png)


再总结一下非顺序消费（并非消费）的主要思路：

1、将待消费的消息存入 `ProcessQueue` 中存储，并执行消息消费之前钩子函数

2、修改待消费消息的主题（设置为消费组的重试主题）

3、分页消费（每次传给业务消费监听器的最大数量为配置的 

`sconsumeMessageBatchMaxSize`

4、执行消费后钩子函数，并根据业务方返回的消息消费结果（成功，重试）【`ACK`】确认信息，然后更新消息进度，从 `ProceeQueue` 中删除相应的消息

  


2）顺序消息 消息消费服务 `ConumeMessageService` 的提交消费请求【`ConsumeMessageOrderlyService`】

![img\_0914\_01\_50.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-50.png)


这里与非顺序消息的区别是 `ConsumeRequest` 只针对 `ProcessQueue`,`messageQueue`,接下来，我们重点分析 `ConsumeMessageOrderlyService` 中 `ConsumeRequest`(消息消费任务封装)

![img\_0914\_01\_51.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-51.png)


重点需要关注 `ConsumeRequest` 的 `run` 方法

消息消费的逻辑与非顺序消费差不多，但其关键点，在于消息消费或获取的顺序性，既然要保证顺序性消费，就不可避免的引入锁机制，关键代码剖析如下：

![img\_0914\_01\_52.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/RocketMQ/image/05-52.png)


一个消费者中线程池中线程的锁粒度为，`MessageQueue`,消费队列，也就是说 `RocketMQ` 实现顺序消费是针对 `MessageQueue`,也就是 `RocketMQ` 无法做到多 `MessageQueue` 的全局顺序消费，如果要使用 `RocketMQ` 做的主题的全局顺序消费，那该主题只能允许一个队列。顺序消息消费的更多细节，本文暂不深入分析，在后续专题中会重点分析。

好了，本文到此为止。

  


读者朋友们，您觉得本文重点解答了开篇哪些问题呢？欢迎大家讨论与总结，请继续关注后续文章，继续探讨 `RocketMQ` 消息消费机制。

  


未分析问题：

1、消息消费者负载加载，消息进入 `commitlog` 后，消息分发与消息负载机制【重点待分析】

2、消息消费进度保持机制等

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")