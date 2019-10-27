作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)


消息组接到某项目组反馈，`topic` 在扩容后出现部分队列无法被消费者，导致消息积压，影响线上业务？

考虑到该问题是发送在真实的线上环境，为了避免泄密，本文先在笔者的虚拟机中来重现问题。  


## 本节目录 ##

 *  1、案情回顾
    
     *  1.1 集群现状
     *  1.2、RocketMQ 在线扩容队列
     *  1.3 消息发送
    
    2、问题暴露
    
    3、问题分析
    
    4、问题复盘

## 1、案情回顾 ##

### 1.1 集群现状 ###

集群信息如下：  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
例如业务主体名 topic\_dw\_test\_by\_order\_01 的路由信息如图所示：  
![img\_0914\_01\_2.png][img_0914_01_2.png]  
当前的消费者信息：  
![img\_0914\_01\_3.png][img_0914_01_3.png]  
broker 的配置信息如下：

```
brokerClusterName = DefaultCluster
brokerName = broker-a
brokerId = 0
deleteWhen = 04
fileReservedTime = 48
brokerRole = ASYNC_MASTER
flushDiskType = ASYNC_FLUSH
brokerIP1=192.168.0.220
brokerIP2-192.168.0.220
namesrvAddr=192.168.0.221:9876;192.168.0.220:9876
storePathRootDir=/opt/application/rocketmq-all-4.5.2-bin-release/store
storePathCommitLog=/opt/application/rocketmq-all-4.5.2-bin-release/store/commitlog
autoCreateTopicEnable=false
autoCreateSubscriptionGroup=false
```

> 备注：公司对 topic、消费组进行了严格的管控，项目组需要使用时需要向运维人员申请，故 broker 集群不允许自动创建主题与自动创建消费组。

由于该业务量稳步提升，项目组觉得该主题的队列数太少，不利于增加消费者来提高其消费能力，故向运维人员提出增加队列的需求。

### 1.2、RocketMQ 在线扩容队列 ###

运维通过公司自研的消息运维平台，直接以指定集群的方式为 `topic` 扩容，该运维平台底层其实使用了 `RocketMQ` 提供的 `updateTopic` 命令，其命令说明如下：  
![img\_0914\_01\_4.png][img_0914_01_4.png]  
从上图可以得知可以通过 `-c` 命令来指定在集群中所有的 `broker` 上创建队列，在本例中，将队列数从 4 设置为 8，具体命令如下：

```
sh ./mqadmin upateTopic -n 192.168.0.220:9876 -c DefaultCluster -t topic_dw_test_by_order_01 -r 8 -w 8
```

执行效果如图所示，表示更新成功。  
![img\_0914\_01\_5.png][img_0914_01_5.png]  
我们再来从 rocketmq-console 中来看命令执行后的效果：  
![img\_0914\_01\_6.png][img_0914_01_6.png]  
从上图可以得知，主题的队列数已经扩容到了8个，并且在集群的两台 `broker` 上都创建了队列。

### 1.3 消息发送 ###

从 `RocketMQ` 系列可知，`RocketMQ` 是支持在线 `topic` 在线扩容机制的，故无需重启 消息发送者、消息消费者，随着时间的推移，我们可以查看 `topic` 的所有队列都参与到了消息的负载中，如图所示：  
![img\_0914\_01\_7.png][img_0914_01_7.png]  
我们可以清晰的看到，所有的16个队列(每个 `broker` 8个队列)都参与到了消息发送的，运维小哥愉快的完成了 `topic` 的扩容。

## 2、问题暴露 ##

该 `topic` 被 5个消费组所订阅，突然接到通知，其中有两个消费组反馈，部分队列的消息没有被消费，导致下游系统并没有及时处理。

## 3、问题分析 ##

当时到项目组提交到消息组时，我第一反应是先看消费者的队列，打开该主题的消费情况，如图所示：  
![img\_0914\_01\_8.png][img_0914_01_8.png]  
发现队列数并没有积压，备注（由于生产是4主4从，每一个 `broker`上8个队列，故总共32个队列），当时由于比较急，并没有第一时间发现这个界面，竟然只包含一个消费者，觉得并没有消息积压，又由于同一个集群，其他消费组没有问题，只有两个消费组有问题，怀疑是应用的问题，就采取了重启，打印线程栈等方法。

事后诸葛亮：其实这完成是错误的，为什么这样说呢？因为项目组（业务方）已经告知一部分业务未处理，说明肯定有队列的消息积压，当根据自己的知识，结合看到的监控页面做出的判断与业务方反馈的出现冲突时，一定是自己的判断出了问题。

正在我们“如火如荼”的认定是项目有问题时，团队的另一成员提出了自己的观点，原来在得到业务方反馈时，他得知同一个主题，被5个消费组订阅，只有其中两个有问题，那他通过`rocketmq-console`来找两者的区别，找到区别，找到规律，就离解决问题的路近了。

他通过对比发现，出问题的消费组只有两个客户端在消费（通常生产环境是4节点消费），而没有出现问题的发现有4个进程都在处理，即发现现象：出错的消费组，并没有全员参与到消费。正如上面的图所示：只有其中一个进程在处理8个队列，另外8个队列并没有在消费。

那现在就是要分析为啥 `topic` 共有16个队列，但这里只有1个消费者队列在消费，另外一个消费者不作为？

首先根据 `RocketMQ` 消息队列负载机制，2个消费者，只有1个消费者在消费，并且一个有一个明显的特点是，只有broker-a上的队列在消费，`broker-b`上的队列一个也没消费。

正在思考为啥会出现这种现象时，他又在思考是不是集群是不是`broker-b`(对应我们生产环境是broker-c、broker-d上的队列都未消费)是新扩容的机器？

扩容的时候是不是没有把订阅关系在新的集群上创建？提出了疑问，接下来肖工就开始验证猜想，通过查阅`broker-c`、`broker-d`在我们系统中创建的时间是2018-4月的时候，就基本得出结论，扩容时并没有在新集群上创建订阅消息，故无法消费消息。

于是运维小哥使用运维工具创建订阅组，创建方法如图所示：  
![img\_0914\_01\_9.png][img_0914_01_9.png]  
创建好消费组后，再去查看topic的消费情况时，另外一个消费组也开始处理消息了，如下图所示：  
![img\_0914\_01\_10.png][img_0914_01_10.png]

## 4、问题复盘 ##

潜在原因：`DefaultCluster` 集群进行过一次集群扩容，从原来的一台消息服务器( `broker-a` )额外增加一台broker服务器( `broker-b` )，但扩容的时候并没有把原先的存在于 `broker-a` 上的主题、消费组扩容到` broker-b` 服务器。

触发原因：接到项目组的扩容需求，将集群队列数从4个扩容到8个，这样该 `topic` 就在集群的a、b都会存在8个队列，但 `Broker` 不允许自动创建消费组（订阅关系），消费者无法从`broker-b`上队列上拉取消息，导致在 `broker-b` 队列上的消息堆积，无法被消费。

解决办法：运维通过命令，在 `broker-b` 上创建对应的订阅消息，问题解决。

经验教训：集群扩容时，需要同步在集群上的 `topic.json、subscriptionGroup.json`文件。

`RocketMQ` 理论基础，消费者向 `Broker` 发起消息拉取请求时，如果broker上并没有存在该消费组的订阅消息时，如果不允许自动创建(`autoCreateSubscriptionGroup` 设置为 `false`)，默认为 `true`，则不会返回消息给客户端，其代码如下：  
![img\_0914\_01\_11.png][img_0914_01_11.png]  
问题解决后，我们团队的成员也分享了一下他在本次排查问题的处理方法：**寻找出现问题的规律、推断问题、 然后验证问题。规律可以是问题本身的规律 也可以是和正常对比的差。**

如果觉得文章写的还不错的话，麻烦帮忙点个赞，谢谢。

> 作者简介：  
> 丁威，《RocketMQ技术内幕》作者，维护微信公众号：中间件兴趣圈。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_5.png
[img_0914_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_6.png
[img_0914_01_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_7.png
[img_0914_01_8.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_8.png
[img_0914_01_9.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_9.png
[img_0914_01_10.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_10.png
[img_0914_01_11.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/36/img_0914_01_11.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")