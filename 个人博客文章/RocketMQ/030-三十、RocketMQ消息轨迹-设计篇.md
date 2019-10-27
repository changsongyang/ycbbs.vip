作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)


## 本节目录 ##

 *  1、消息轨迹数据格式
    
    2、记录消息轨迹
    
    3、如何存储消息轨迹数据

  
`RocketMQ` 消息轨迹主要包含两篇文章：设计篇与源码分析篇，本节将详细介绍 `RocketMQ` 消息轨迹-设计相关。

`RocketMQ` 消息轨迹，主要跟踪消息发送、消息消费的轨迹，即详细记录消息各个处理环节的日志，从设计上至少需要解决如下三个核心问题：

 *  消费轨迹数据格式
 *  记录消息轨迹(消息日志)
 *  消息轨迹数据存储在哪？

## 1、消息轨迹数据格式 ##

`RocketMQ4.5`版本消息轨迹主要记录如下信息：

```html
 *  traceType  
    跟踪类型，可选值：Pub(消息发送)、SubBefore(消息拉取到客户端，执行业务定义的消费逻辑之前)、SubAfter(消费后)。
 *  timeStamp  
    当前时间戳。
 *  regionId  
    broker所在的区域ID，取自BrokerConfig\#regionId。
 *  groupName  
    组名称，traceType为Pub时为生产者组的名称；如果traceType为subBefore或subAfter时为消费组名称。
 *  requestId  
    traceType为subBefore、subAfter时使用，消费端的请求Id。
 *  topic  
    消息主题。
 *  msgId  
    消息唯一ID。
 *  tags  
    消息tag。
 *  keys  
    消息索引key，根据该key可快速检索消息。
 *  storeHost  
    跟踪类型为PUB时为存储该消息的Broker服务器IP；跟踪类型为subBefore、subAfter时为消费者IP。
 *  bodyLength  
    消息体的长度。
 *  costTime  
    耗时。
 *  msgType  
    消息的类型，可选值：Normal\_Msg(普通消息),Trans\_Msg\_Half(预提交消息),Trans\_msg\_Commit(提交消息),Delay\_Msg(延迟消息)。
 *  offsetMsgId  
    消息偏移量ID,该ID中包含了broker的ip以及偏移量。
 *  success  
    是发送成功。
 *  contextCode  
    消费状态码，可选值：SUCCESS,TIME\_OUT,EXCEPTION,RETURNNULL,FAILED。
```

## 2、记录消息轨迹 ##

消息中间件的两大核心主题：消息发送、消息消费，其核心载体就是消息，消息轨迹（消息的流转）主要是记录消息是何时发送到哪台 `Broker`，发送耗时多少时间，在什么是被哪个消费者消费。记录消息的轨迹主要是集中在消息发送前后、消息消费前后，可以通过 `RokcetMQ` 的 `Hook` 机制。通过如下两个接口来定义钩子函数。  

![img\_0914\_01\_1.png][img_0914_01_1.png]  
通过实行上述两个接口，可以实现在消息发送、消息消费前后记录消息轨迹，为了不明显增加消息发送与消息消费的时延，记录消息轨迹最好使用异步发送模式。

## 3、如何存储消息轨迹数据 ##

消息轨迹需要存储什么消息以及在什么时候记录消息轨迹的问题都以及解决，那接下来就得思考将消息轨迹存储在哪里？

存储在数据库中或其他媒介中，都会加重消息中间件，使其依赖外部组件，最佳的选择还是存储在 `Broker` 服务器中，将消息轨迹数据也当成一条消息存储到Broker服务器。

既然把消息轨迹当成消息存储在 `Broker` 服务器，那存储消息轨迹的 `Topic` 如何确定呢？`RocketMQ` 提供了两种方法来定义消息轨迹的Topic。

 *  系统默认 `Topic`  
    如果 `Broker` 的 `traceTopicEnable` 配置设置为 `true`，表示在该 `Broker` 上创建 `topic` 名为：`RMQ\_SYS\_TRACE\_TOPIC`，队列个数为1，默认该值为 `false`，表示该 `Broker` 不承载系统自定义用于存储消息轨迹的 `topic`。
	
 *  自定义 `Topic`  
    在创建消息生产者或消息消费者时，可以通过参数自定义用于记录消息轨迹的Topic名称，不过要注意的是，rokcetmq控制台(rocketmq-console)中只支持配置一个消息轨迹Topic，故自定义Topic，在目前这个阶段或许还不是一个最佳实践，建议使用系统默认的Topic即可。

通常为了避免消息轨迹的数据与正常的业务数据混合在一起，官方建议，在 `Broker` 集群中，新增加一台机器，只在这台机器上开启消息轨迹跟踪，这样该集群内的消息轨迹数据只会发送到这一台 `Broker` 服务器上，并不会增加集群内原先业务 `Broker` 的负载压力。

`RocketMQ` 消息轨迹的设计细节就介绍到这里了，下一篇将从源码的角度对其实现细节进行详细的剖析；如果觉得本文对您有帮助的话，期待您的点赞，谢谢。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/30/img_0914_01_1.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")




