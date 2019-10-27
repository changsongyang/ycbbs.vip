作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)


### 本节目录 ###

 *  1、现象
    
    2、思考
    
    3、原理
    
     *  3.1 `RocketMQ` 基本路由规则
     *  3.2 探究 `autoCreateTopicEnable` 机制
     *  3.2.1 默认 `Topic` 路由创建时机
        
        3.2.2 现象分析

## 1、现象 ##

很多网友会问，为什么明明集群中有多台Broker服务器，`autoCreateTopicEnable` 设置为 `true`，表示开启 `Topic` 自动创建，但新创建的 `Topic` 的路由信息只包含在其中一台 `Broker` 服务器上，这是为什么呢？

期望值：为了消息发送的高可用，希望新创建的 `Topic` 在集群中的每台 `Broker` 上创建对应的队列，避免Broker的单节点故障。

现象截图如下：  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
![img\_0914\_01\_2.png][img_0914_01_2.png]  
正如上图所示，自动创建的topicTest5的路由信息：

 *  `topicTest5` 只在 `broker-a` 服务器上创建了队列，并没有在broker-b服务器创建队列，不符合期望。
 *  默认读写队列的个数为4。

我们再来看一下RocketMQ默认topic的路由信息截图如下：  
![img\_0914\_01\_3.png][img_0914_01_3.png]  
从图中可以默认 `Topic` 的路由信息为`broker-a、broker-b`上各8个队列。

## 2、思考 ##

默认 `Topic` 的路由信息是如何创建的？

1、  `Topic` 的路由信息是存储在哪里？`Nameserver`？`broker`?
2、  `RocketMQ` `Topic` 默认队列个数是多少呢？

## 3、原理 ##

### 3.1 RocketMQ基本路由规则 ###

![img\_0914\_01\_4.png][img_0914_01_4.png]

1、  `Broker` 在启动时向 `Nameserver` 注册存储在该服务器上的路由信息，并每隔30s向 `Nameserver` 发送心跳包，并更新路由信息。
2、  `Nameserver` 每隔10s扫描路由表，如果检测到Broker服务宕机，则移除对应的路由信息。

3、  消息生产者每隔30s会从 `Nameserver` 重新拉取Topic的路由信息并更新本地路由表；在消息发送之前，如果本地路由表中不存在对应主题的路由消息时，会主动向 `Nameserver` 拉取该主题的消息。

回到本文的主题：`autoCreateTopicEnable`，开启自动创建主题，试想一下，如果生产者向一个不存在的主题发送消息时，上面的任何一个步骤都无法获取一个不存在的主题的路由信息，那该如何处理这种情况呢？

在 `RocketMQ` 中，如果 `autoCreateTopicEnable` 设置为 `true`，消息发送者向NameServer查询主题的路由消息返回空时，会尝试用一个系统默认的主题名称(MixAll.AUTO\_CREATE\_TOPIC\_KEY\_TOPIC)，此时消息发送者得到的路由信息为：  
![img\_0914\_01\_5.png][img_0914_01_5.png]  
但问题就来了，默认Topic在集群的每一台 `Broker` 上创建8个队列，那问题来了，为啥新创建的 `Topic` 只在一个 `Broker` 上创建4个队列？

### 3.2 探究autoCreateTopicEnable机制 ###

#### 3.2.1 默认Topic路由创建时机 ####

> 温馨提示：本文不会详细跟踪整个创建过程，只会点出源码的关键入口点，如想详细了解NameServer路由消息、消息发送高可用的实现原理，建议查阅笔者的书籍《RocketMQ技术内幕》第二、三章。

Step1：在 `Broker` 启动流程中，会构建 `TopicConfigManager` 对象，其构造方法中首先会判断是否开启了允许自动创建主题，如果启用了自动创建主题，则向 `topicConfigTable` 中添加默认主题的路由信息。  
`TopicConfigManager` 构造方法  
![img\_0914\_01\_6.png][img_0914_01_6.png]

> 备注：该topicConfigTable中所有的路由信息，会随着Broker向Nameserver发送心跳包中，Nameserver收到这些信息后，更新对应Topic的路由信息表。

BrokerConfig的defaultTopicQueueNum默认为8。两台Broker服务器都会运行上面的过程，故最终Nameserver中关于默认主题的路由信息中，会包含两个Broker分别各8个队列信息。

Step2：生产者寻找路由信息  
生产者首先向 `NameServer` 查询路由信息，由于是一个不存在的主题，故此时返回的路由信息为空，`RocketMQ` 会使用默认的主题再次寻找，由于开启了自动创建路由信息，`NameServer` 会向生产者返回默认主题的路由信息。然后从返回的路由信息中选择一个队列（默认轮询）。消息发送者从 `Nameserver` 获取到默认的 `Topic` 的队列信息后，队列的个数会改变吗？答案是会的，其代码如下：

`MQClientInstance\#updateTopicRouteInfoFromNameServer`  
![img\_0914\_01\_7.png][img_0914_01_7.png]

> 温馨提示：消息发送者在到默认路由信息时，其队列数量，会选择DefaultMQProducer\#defaultTopicQueueNums与Nameserver返回的的队列数取最小值，DefaultMQProducer\#defaultTopicQueueNums默认值为4，故自动创建的主题，其队列数量默认为4。

Step3：发送消息

`DefaultMQProducerImpl\#sendKernelImpl  `
![img\_0914\_01\_8.png][img_0914_01_8.png]  
在消息发送时的请求报文中，设置默认 `topic` 名称，消息发送 `topic` 名称，使用的队列数量为`DefaultMQProducer\#defaultTopicQueueNums`，即默认为4。

Step4：Broker端收到消息后的处理流程  
服务端收到消息发送的处理器为：`SendMessageProcessor`，在处理消息发送时，会调用super.msgCheck方法：

`AbstractSendMessageProcessor\#msgCheck  `
![img\_0914\_01\_9.png][img_0914_01_9.png]  
在Broker端，首先会使用 `TopicConfigManager` 根据 `topic` 查询路由信息，如果 `Broker` 端不存在该主题的路由配置(路由信息),此时如果 `Broker` 中存在默认主题的路由配置信息，则根据消息发送请求中的队列数量，在 `Broker` 创建新 `Topic` 的路由信息。这样 `Broker` 服务端就会存在主题的路由信息。

在Broker端的topic配置管理器中存在的路由信息，一会向 `Nameserver` 发送心跳包，汇报到 `Nameserver`，另一方面会有一个定时任务，定时存储在 `broker` 端，具体路径为$\{ROCKET\_HOME\}/store/config/topics.json中，这样在 `Broker` 关闭后再重启，并不会丢失路由信息。

广大读者朋友，跟踪到这一步的时候，大家应该对启用自动创建主题机制时，新主题是的路由信息是如何创建的，为了方便理解，给出创建主题序列图：  
![img\_0914\_01\_10.png][img_0914_01_10.png]

#### 3.2.2 现象分析 ####

经过上面自动创建路由机制的创建流程，我们可以比较容易的分析得出如下结论：  
因为开启了自动创建路由信息，消息发送者根据 `Topic` 去 `NameServer` 无法得到路由信息，但接下来根据默认 `Topic` 从 `NameServer` 是能拿到路由信息(在每个Broker中，存在8个队列)，因为两个 `Broker` 在启动时都会向 `NameServer` 汇报路由信息。此时消息发送者缓存的路由信息是2个Broker，每个Broker默认4个队列（原因见3.2.1:Step2的分析）。

消息发送者然后按照轮询机制，发送第一条消息选择(`broker-a`的`messageQueue:0`)，向Broker发送消息，Broker服务器在处理消息时，首先会查看自己的路由配置管理器(`TopicConfigManager`)中的路由信息，此时不存在对应的路由信息，然后尝试查询是否存在默认 `Topic` 的路由信息，如果存在，说明启用了 `autoCreateTopicEnable`，则在 `TopicConfigManager` 中创建新Topic的路由信息，此时存在与 `Broker` 服务端的内存中，然后本次消息发送结束。此时，在 `NameServer` 中还不存在新创建的Topic的路由信息。

这里有三个关键点：

1、  启用autoCreateTopicEnable创建主题时，在Broker端创建主题的时机为，消息生产者往Broker端发送消息时才会创建。

2、  然后Broker端会在一个心跳包周期内，将新创建的路由信息发送到NameServer，于此同时，Broker端还会有一个定时任务，定时将内存中的路由信息，持久化到Broker端的磁盘上。

3、  消息发送者会每隔30s向NameServer更新路由信息，如果消息发送端一段时间内未发送消息，就不会有消息发送集群内的第二台Broker，那么NameServer中新创建的Topic的路由信息只会包含Broker-a。

然后消息发送者会向NameServer拉取最新的路由信息，此时就会消息发送者原本缓存了2个broker的路由信息，将会变为一个Broker的路由信息，则该Topic的消息永远不会发送到另外一个Broker，就出现了上述现象。

原因就分析到这里了，现在我们还可以的大胆假设，开启autoCreateTopicEnable机制，什么情况会在两个Broker上都创建队列，其实，我们只需要连续快速的发送9条消息，就有可能在2个Broker上都创建队列，验证代码如下：

```
public static void main(String[] args) throws MQClientException, InterruptedException {
    DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
    producer.setNamesrvAddr("127.0.0.1:9876");
    producer.start();
    for (int i = 0; i < 9; i++) {
        try {
            Message msg = new Message("TopicTest10" ,"TagA" , ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
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

验证结果如图所示：  
![img\_0914\_01\_11.png][img_0914_01_11.png]

本文就分析到这里了，大家如果喜欢这篇文章，麻烦大家帮忙点点赞，同时大家也可以给作者留言，告知在使用 `RocketMQ` 的过程中遇到的疑难杂症，与作者互动。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_5.png
[img_0914_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_6.png
[img_0914_01_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_7.png
[img_0914_01_8.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_8.png
[img_0914_01_9.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_9.png
[img_0914_01_10.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_10.png
[img_0914_01_11.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/25/img_0914_01_11.png

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")