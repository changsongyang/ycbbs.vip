作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

## 本节目录 ##

 *  1、什么是ACL?
    
    2、ACL基本流程图
    
    3、如何配置ACL
    
     *  3.1 acl配置文件
     *  3.1.1 globalWhiteRemoteAddresses
        
        3.1.2 accounts
        
         *  3.1.2.1 accessKey
         *  3.1.2.2 secretKey
         *  3.1.2.3 whiteRemoteAddress
         *  3.1.2.4 admin
         *  3.1.2.5 defaultTopicPerm
         *  3.1.2.6 defaultGroupPerm
         *  3.1.2.7 topicPerms
         *  3.1.2.8 groupPerms
     *  3.2 RocketMQ ACL权限可选值
     *  3.3、权限验证流程
    
    4、使用示例
    
     *  4.1 Broker端安装
     *  4.2 消息发送端示例
     *  4.3 消息消费端示例

## 1、什么是ACL? ##

ACL是`access` control list的简称，俗称访问控制列表。访问控制，基本上会涉及到用户、资源、权限、角色等概念，那在RocketMQ中上述会对应哪些对象呢？

 *  用户  
    用户是访问控制的基础要素，也不难理解，`RocketMQ ACL` 必然也会引入用户的概念，即支持用户名、密码。
 *  资源  
    资源，需要保护的对象，在 `RocketMQ` 中，消息发送涉及的 `Topic`、消息消费涉及的消费组，应该进行保护，故可以抽象成资源。
 *  权限  
    针对资源，能进行的操作，
 *  角色  
    `RocketMQ` 中，只定义两种角色：是否是管理员。

另外，`RocketMQ` 还支持按照客户端IP进行白名单设置。

## 2、ACL基本流程图 ##

在讲解如何使用ACL之前，我们先简单看一下`RocketMQ ACL`的请求流程：  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
对于上述具体的实现，将在后续文章中重点讲解，本文的目的只是希望给读者一个大概的了解。

## 3、如何配置ACL ##

### 3.1 acl配置文件 ###

acl默认的配置文件名：`plain\_acl.yml`,需要放在`$\{ROCKETMQ\_HOME\}/store/config`目录下。下面对其配置项一一介绍。

#### 3.1.1 globalWhiteRemoteAddresses ####

全局白名单，其类型为数组，即支持多个配置。其支持的配置格式如下：
```html
 *  空  
    表示不设置白名单，该条规则默认返回false。
 *  “\*”  
    表示全部匹配，该条规则直接返回true，将会阻断其他规则的判断，请慎重使用。
 *  192.168.0.\{100,101\}  
    多地址配置模式，ip地址的最后一组，使用\{\}，大括号中多个ip地址，用英文逗号(,)隔开。
 *  192.168.1.100,192.168.2.100  
    直接使用,分隔，配置多个ip地址。
 *  192.168.\*.*或192.168.100-200.10-20  
    每个IP段使用 "*" 或"-"表示范围。
```

#### 3.1.2 accounts ####

配置用户信息，该类型为数组类型。拥有`accessKey、secretKey、whiteRemoteAddress、admin、defaultTopicPerm、defaultGroupPerm、topicPerms、groupPerms`子元素。

##### 3.1.2.1 accessKey #####

登录用户名，长度必须大于6个字符。

##### 3.1.2.2 secretKey #####

登录密码。长度必须大于6个字符。

##### 3.1.2.3 whiteRemoteAddress #####

用户级别的IP地址白名单。其类型为一个字符串，其配置规则与 `globalWhiteRemoteAddresses`，但只能配置一条规则。

##### 3.1.2.4 admin #####

`boolean` 类型，设置是否是 `admin`。如下权限只有`admin=true`时才有权限执行。

```html
*  UPDATE\_AND\_CREATE\_TOPIC  
    更新或创建主题。
 *  UPDATE\_BROKER\_CONFIG  
    更新Broker配置。
 *  DELETE\_TOPIC\_IN\_BROKER  
    删除主题。
 *  UPDATE\_AND\_CREATE\_SUBSCRIPTIONGROUP  
    更新或创建订阅组信息。
 *  DELETE\_SUBSCRIPTIONGROUP  
    删除订阅组信息。
```

##### 3.1.2.5 defaultTopicPerm #####

默认topic权限。该值默认为DENY(拒绝)。

##### 3.1.2.6 defaultGroupPerm #####

默认消费组权限，该值默认为DENY(拒绝)，建议值为SUB。

##### 3.1.2.7 topicPerms #####

设置topic的权限。其类型为数组，其可选择值在下节介绍。

##### 3.1.2.8 groupPerms #####

设置消费组的权限。其类型为数组，其可选择值在下节介绍。可以为每一消费组配置不一样的权限。

### 3.2 RocketMQ ACL权限可选值 ###

 *  DENY  
    拒绝。
 *  PUB  
    拥有发送权限。
 *  SUB  
    拥有订阅权限。

### 3.3、权限验证流程 ###

上面定义了全局白名单、用户级别的白名单，用户级别的权限，为了更好的配置ACL权限规则，下面给出权限匹配逻辑。  
![img\_0914\_01\_2.png][img_0914_01_2.png]

## 4、使用示例 ##

### 4.1 Broker端安装 ###

首先，需要在`broker.conf`文件中，增加参数aclEnable=true。并拷贝distribution/conf/plain\_acl.yml文件到$\{ROCKETMQ\_HOME\}/conf目录。

`broker.conf`的配置文件如下：

```
brokerClusterName = DefaultCluster
brokerName = broker-b
brokerId = 0
deleteWhen = 04
fileReservedTime = 48
brokerRole = ASYNC_MASTER
flushDiskType = ASYNC_FLUSH
listenPort=10915
storePathRootDir=E:/SH2019/tmp/rocketmq_home/rocketmq4.5MB/store
storePathCommitLog=E:/SH2019/tmp/rocketmq_home/rocketmq4.5MB/store/commitlog
namesrvAddr=127.0.0.1:9876
autoCreateTopicEnable=false
aclEnable=true
```

plain\_acl.yml文件内容如下：

```
globalWhiteRemoteAddresses:

accounts:
- accessKey: RocketMQ
  secretKey: 12345678
  whiteRemoteAddress:
  admin: false
  defaultTopicPerm: DENY
  defaultGroupPerm: SUB
  topicPerms:
  - TopicTest=PUB
  groupPerms:
  # the group should convert to retry topic
  - oms_consumer_group=DENY

- accessKey: admin
  secretKey: 12345678
  whiteRemoteAddress:
  # if it is admin, it could access all resources
  admin: true
```

从上面的配置可知，用户 `RocketMQ` 只能发送 To`p`icTest 的消息，其他 `topic` 无权限发送；拒绝`oms\_consumer\_group`消费组的消息消费，其他消费组默认可消费。

### 4.2 消息发送端示例 ###

```
public class AclProducer {
    public static void main(String[] args) throws MQClientException, InterruptedException {
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name", getAclRPCHook());
        producer.setNamesrvAddr("127.0.0.1:9876");
        producer.start();
        for (int i = 0; i < 1; i++) {
            try {
                Message msg = new Message("TopicTest3" ,"TagA" , ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
                SendResult sendResult = producer.send(msg);
                System.out.printf("%s%n", sendResult);
            } catch (Exception e) {
                e.printStackTrace();
                Thread.sleep(1000);
            }
        }
        producer.shutdown();
    }

    static RPCHook getAclRPCHook() {
        return new AclClientRPCHook(new SessionCredentials("rocketmq","12345678"));
    }
}
```

运行效果如图所示：  
![img\_0914\_01\_3.png][img_0914_01_3.png]

### 4.3 消息消费端示例 ###

```
public class AclConsumer {

    public static void main(String[] args) throws InterruptedException, MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("please_rename_unique_group_name_4", getAclRPCHook(),new AllocateMessageQueueAveragely());
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
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

    static RPCHook getAclRPCHook() {
        return new AclClientRPCHook(new SessionCredentials("rocketmq","12345678"));
    }
}
```

发现并不没有消费消息，符合预期。

关于`RocketMQ ACL`的使用就介绍到这里了，下一篇将介绍`RocketMQ ACL`实现原理。

--------------------

推荐阅读：  
1、[RocketMQ实战：生产环境中，autoCreateTopicEnable为什么不能设置为true][RocketMQ_autoCreateTopicEnable_true]

2、[RocketMQ 消息发送system busy、broker busy原因分析与解决方案][RocketMQ _system busy_broker busy]

3、[RocketMQ HA机制(主从同步)][RocketMQ HA]

4、[RocketMQ事务消息实战][RocketMQ]


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/28/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/28/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/28/img_0914_01_3.png
[RocketMQ_autoCreateTopicEnable_true]: https://www.ycbbs.vip/?p=1058
[RocketMQ _system busy_broker busy]: https://www.ycbbs.vip/?p=1063
[RocketMQ HA]: https://www.ycbbs.vip/?p=1069
[RocketMQ]: https://www.ycbbs.vip/?p=1056

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")