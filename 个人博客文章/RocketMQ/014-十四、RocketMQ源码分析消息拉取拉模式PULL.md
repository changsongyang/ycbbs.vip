作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

### 本节目录 ###

 *  1、`RocketMQ` 推拉模式简介
    
    2、`DefaultMQPullConsumer` 核心属性
    
    3、消息消费者启动流程分析

## 1、RocketMQ 推拉模式简介 ##

消费者与消息存储方 Broker一般有两种通信机制：推（`PUSH`）、拉(`PULL`)。

 *  推模式：消息发送者将消息发送到 `Broker`，然后 `Broker` 主动推送给订阅了该消息的消费者。
 *  拉模式：消息发送者将消息发送到 `Broker` 上，然后由消息消费者自发的向 `Broker` 拉取消息。

`RocketMQ` 推拉机制实现：严格意义上来讲，`RocketMQ` 并没有实现 `PUSH` 模式，而是对拉模式进行一层包装，在消费端开启一个线程 `PullMessageService` 循环向 `Broke` r拉取消息，一次拉取任务结束后马上又发起另一次拉取操作，实现准实时自动拉取，`PUSH` 模式的实现请参考如下博文：

1、  [推模式消息拉取机制][Link 1]
2、  [推模式消息队列负载机制][Link 2]

本文重点在讨论 `RocketMQ` 拉模式 `DefaultMQPullConsumer` 实现。

`RocketMQ` 拉模式，`RocketMQ` 消费者不自动向消息服务器拉取消息，而是将控制权移交给应用程序，`RocketMQ` 消费者只是提供拉取消息API。

为了对 `RocketMQ` 拉模式有一个直观的了解，我们先大概浏览一下 `MQPullConsumer` 接口。  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
从上面我们可以看到除了启动、关闭，注册消息监听器，其他的就是针对 `MessageQueue` 拉取消息，特别值得留意的是每一个拉取 `pull` 方法，都是直接针对消息消费队列。`PUSH` 模式可以说基于订阅与发布模式，而PULL模式可以说是基于消息队列模式。

> 特别说明：PULL模式根据主题注册消息监听器，这里的消息监听器，不是用来消息消费的，而是在该主题的队列负载发生变化时，做一下通知。

我们应该带着我们对 PUSH 模式的相关知识来认识一下 PULL 模式，对比学习.  
PUSH模式主要知识点：

 *  消息拉取机制：PullMessageServer线程 根据PullRequest拉取任务循环拉取。
 *  消息队列负载机制，按照消费组，对主题下的消息队列，结合当前消费组内消费者数量动态负载。

按照上面API的描述，PULL模式应该无需考虑上面两个情形，我们带着上述疑问，开始我们今天的学习。

## 2、DefaultMQPullConsumer 核心属性 ##

```
/**
     * Do the same thing for the same Group, the application must be set,and
     * guarantee Globally unique
     */
    private String consumerGroup;
    /**
     * Long polling mode, the Consumer connection max suspend time, it is not
     * recommended to modify
     */
    private long brokerSuspendMaxTimeMillis = 1000 * 20;
    /**
     * Long polling mode, the Consumer connection timeout(must greater than
     * brokerSuspendMaxTimeMillis), it is not recommended to modify
     */
    private long consumerTimeoutMillisWhenSuspend = 1000 * 30;
    /**
     * The socket timeout in milliseconds
     */
    private long consumerPullTimeoutMillis = 1000 * 10;
    /**
     * Consumption pattern,default is clustering
     */
    private MessageModel messageModel = MessageModel.CLUSTERING;
    /**
     * Message queue listener
     */
    private MessageQueueListener messageQueueListener;
    /**
     * Offset Storage
     */
    private OffsetStore offsetStore;
    /**
     * Topic set you want to register
     */
    private Set<String> registerTopics = new HashSet<String>();
    /**
     * Queue allocation algorithm
     */
    private AllocateMessageQueueStrategy allocateMessageQueueStrategy = new AllocateMessageQueueAveragely();
    /**
     * Whether the unit of subscription group
     */
    private boolean unitMode = false;

    private int maxReconsumeTimes = 16;
```

```html
 *  consumerGroup ： 消费组名称.
 *  brokerSuspendMaxTimeMillis ：长轮询模式下挂起的最大超时时间，在Broker端根据偏移量从存储文件中查找消息时如果返回 PULL\_NOT\_FOUND时，不理解返回给拉取客户端，而是交给PullRequestHoldService线程，每隔5秒再去拉取一次消息，如果找到则返回给消息拉取客户端，否则超时。
 *  consumerTimeoutMillisWhenSuspend : 整个消息拉取过程中，拉取客户端等待服务器响应结果的超时时间，默认30s
 *  consumerPullTimeoutMillis ：默认10s,拉消息时建立网络连接的超时时间
 *  messageModel ：消费模式，广播、集群
 *  messageQueueListener ： 业务消息监听器
 *  OffsetStore ：消息消费进度管理器
 *  registerTopics ：注册主题数
 *  allocateMessageQueueStrategy ：队列分配器
 *  maxReconsumeTimes ：最大消息重试次数，默认16次
```

## 3、消息消费者启动流程分析 ##

DefaultMQPullConsumerImpl\#start

```
 public synchronized void start() throws MQClientException {
        switch (this.serviceState) {
            case CREATE_JUST:
                this.serviceState = ServiceState.START_FAILED;

                this.checkConfig();  

                this.copySubscription(); // @1

                if (this.defaultMQPullConsumer.getMessageModel() == MessageModel.CLUSTERING) { 
                    this.defaultMQPullConsumer.changeInstanceNameToPID();
                }

                this.mQClientFactory = MQClientManager.getInstance().getAndCreateMQClientInstance(this.defaultMQPullConsumer, this.rpcHook);   // @2

                this.rebalanceImpl.setConsumerGroup(this.defaultMQPullConsumer.getConsumerGroup());
                this.rebalanceImpl.setMessageModel(this.defaultMQPullConsumer.getMessageModel());
                this.rebalanceImpl.setAllocateMessageQueueStrategy(this.defaultMQPullConsumer.getAllocateMessageQueueStrategy());
                this.rebalanceImpl.setmQClientFactory(this.mQClientFactory);   // @3

                this.pullAPIWrapper = new PullAPIWrapper(
                    mQClientFactory,
                    this.defaultMQPullConsumer.getConsumerGroup(), isUnitMode());
                this.pullAPIWrapper.registerFilterMessageHook(filterMessageHookList);    // @:4

                if (this.defaultMQPullConsumer.getOffsetStore() != null) {
                    this.offsetStore = this.defaultMQPullConsumer.getOffsetStore();
                } else {
                    switch (this.defaultMQPullConsumer.getMessageModel()) {
                        case BROADCASTING:
                            this.offsetStore = new LocalFileOffsetStore(this.mQClientFactory, this.defaultMQPullConsumer.getConsumerGroup());
                            break;
                        case CLUSTERING:
                            this.offsetStore = new RemoteBrokerOffsetStore(this.mQClientFactory, this.defaultMQPullConsumer.getConsumerGroup());
                            break;
                        default:
                            break;
                    }
                    this.defaultMQPullConsumer.setOffsetStore(this.offsetStore);   // @5
                }

                this.offsetStore.load();  

                boolean registerOK = mQClientFactory.registerConsumer(this.defaultMQPullConsumer.getConsumerGroup(), this);   // @6
                if (!registerOK) {
                    this.serviceState = ServiceState.CREATE_JUST;

                    throw new MQClientException("The consumer group[" + this.defaultMQPullConsumer.getConsumerGroup()
                        + "] has been created before, specify another name please." + FAQUrl.suggestTodo(FAQUrl.GROUP_NAME_DUPLICATE_URL),
                        null);
                }

                mQClientFactory.start();  // @7
                log.info("the consumer [{}] start OK", this.defaultMQPullConsumer.getConsumerGroup());
                this.serviceState = ServiceState.RUNNING;
                break;
            case RUNNING:
            case START_FAILED:
            case SHUTDOWN_ALREADY:
                throw new MQClientException("The PullConsumer service state not OK, maybe started once, "
                    + this.serviceState
                    + FAQUrl.suggestTodo(FAQUrl.CLIENT_SERVICE_NOT_OK),
                    null);
            default:
                break;
        }

    }
```

代码@1：根据注册的主题，构建订阅信息，放入到 `RebalanceImpl` 的订阅表中。

> DefaultMQPullConsumerImpl 可以注册多个主题，但多个主题使用同一个消息处理监听器。

![img\_0914\_01\_2.png][img_0914_01_2.png]  
代码@2：`创建MQClientInstance`,每一个 `clientConfig` 一个 `MqClientInstance` 对象。  
代码@3：填充 `rebalanceImpl` 对象的消费组、消息队列分配器、消费模式。  
代码@4：构建 `PullAPIWrapper` 对象，该对象封装了具体拉取消息的逻辑，`PULL`,`PUSH` 模式最终都会调用 `PullAPIWrapper` 类的方法从 `Broker` 拉取消息。  
代码@5：根据集群消费模式（广播、集群）初始化消息进度管理器 `offsetStore`。  
代码@6：将该消费者加入到 `MQClientInstance` 消费者列表中。  
代码@7：启动 `MQClientInstance`。该方法我们在讲解 `DefaultMQPushConsumer` 时详细讲解过，我们再简单浏览一下该方法：  
![img\_0914\_01\_3.png][img_0914_01_3.png]  
既然 Pull 模式无需自动拉取消息，但 `PullMessageService` 线程（消息拉取）+ `RebalanceService` 线程（消息队列负载）这个两个线程就没必要启动，这里启动了，会不会带来问题？

答案是不会，因为虽然 `PullMessageService` 线程启动，但是一开始会在获取拉取任务(`PullRequest`)  
![img\_0914\_01\_4.png][img_0914_01_4.png]  
`PullRequest` 是由 `RebalanceService` 产生，它根据主题消息队列个数和当前消费组内消费者个数进行负载，然后产生对应的 `PullRequest` 对象，再将这些对象放入到 `PullMessageService` 的 `pullRequestQueue` 队列。具体放入逻辑调用：`RebalanceImpl\#dispatchPullRequest(final List < PullRequest>pullRequestList);`

我们来看一下 RebalanceImpl 的子类 `RebalancePullImpl` 的 `dispatchPullRequest` 方法。  
![img\_0914\_01\_5.png][img_0914_01_5.png]  
再对比一下 `RebalancePushImpl` 的 `dispatchPullRequest`。  
![img\_0914\_01\_6.png][img_0914_01_6.png]  
再结合 `PullMessageService` 被唤醒后，执行的 `pullMessage方法`。  
![img\_0914\_01\_7.png][img_0914_01_7.png]  
我们可以得出结论，`PullMessageService` 只为 PUSH 模式服务，`ReblanceService进行路由重新分布时`，如果是 `RebalancePullImpl`, 并不会产 `PullRequest`，从而唤醒 `PullMessageService`、`PullMessageService` 被唤醒后，也是执行 `DefaultMQPushConsumerImpl` 的 `pullMessage` 方法。

`ReblanceService` 线程默认每 20s 进行一次消息队列重新负载，判断消息队列是否需要进行重新分布（如果消费者个数和主题的队列数没有发生改变），则继续保持原样。对于 `PULL` 模型，如果消费者需要监听某些主题队列发生事件，注册消息队列变更事件方法，则 `RebalanceService` 会将消息队列负载变化事件通知消费者。

至于 `PULL` 模式那些根据消息队列拉取消息的方法，与 `PUSH` 模式走的逻辑是一样的，唯一的区别是 `PULL` 模式是需要应用程序收到触发消息拉取动作。

通过上述分析，我们总结一下`RocketMQ,PUSH,PULL`模式区别：

 *  `PUSH`: 消费者订阅主题，然后自动进行集群内消息队列的动态负载，自动拉取消息。准实时。
 *  `PULL`:消费者无需订阅主题，由业务方（应用程序）直接根据MessageQueue拉取消息。

项目中一般采用PUSH模式。

--------------------

备注：本文是《RocketMQ技术内幕》的前期素材，建议关注笔者的书籍：《RocketMQ技术内幕》。


[Link 1]: http://blog.csdn.net/prestigeding/article/details/78885420
[Link 2]: http://blog.csdn.net/prestigeding/article/details/78927447
[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_5.png
[img_0914_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_6.png
[img_0914_01_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/14/img_0914_01_7.png



写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")