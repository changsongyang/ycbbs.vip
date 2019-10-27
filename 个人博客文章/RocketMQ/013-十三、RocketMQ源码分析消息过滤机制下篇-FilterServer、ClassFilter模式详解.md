作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

继上篇源码分析了 `Tag` 过滤机制实现原理，本文主要阐述 `RocketMQ` `SQL92` 表达式与 `ClassFilte` r过滤机制实现。

# 1、RocketMQ SQL92实现原理分析 #

入口：PullMessageProcessor\#processRequest

```
if (!ExpressionType.isTagType(subscriptionData.getExpressionType())) {
consumerFilterData = ConsumerFilterManager.build(
                        requestHeader.getTopic(), requestHeader.getConsumerGroup(), requestHeader.getSubscription(),
                        requestHeader.getExpressionType(), requestHeader.getSubVersion()
     );
    assert consumerFilterData != null;
```

首先构建ConsumeFilterData数据结构：

![img\_0914\_01\_1.png][img_0914_01_1.png]

```html
 *  consumeGroup: 消费组
 *  topic ： 消息主题
 *  expresstion:消息过滤表达式，例如SQL92表达式，或过滤类全路径名
 *  expresseionType : 表达式类型，可取值TAG、SQL92
 *  compiledExpression:编译后的表达式对象
 *  bornTime: ConsumerFilterData 对象创建创建时间
 *  deadTime: ConsumerFilterData 对象死亡时间，默认0，表示一直有效。
 *  BloomFilterData bloomFilterData
 *  clientVersion:客户端版本
```

接下来我们先重点分析 `ConsumerFilterManager\#build`方法：

```
/**
     * Build consumer filter data.Be care, bloom filter data is not included.
     *
     * @return maybe null
     */
    public static ConsumerFilterData build(final String topic, final String consumerGroup,
        final String expression, final String type,
        final long clientVersion) {   // @1 
        if (ExpressionType.isTagType(type)) {  // @2
            return null;
        }

        ConsumerFilterData consumerFilterData = new ConsumerFilterData();
        consumerFilterData.setTopic(topic);
        consumerFilterData.setConsumerGroup(consumerGroup);
        consumerFilterData.setBornTime(System.currentTimeMillis());
        consumerFilterData.setDeadTime(0);
        consumerFilterData.setExpression(expression);
        consumerFilterData.setExpressionType(type);
        consumerFilterData.setClientVersion(clientVersion);
        try {
            consumerFilterData.setCompiledExpression(
                FilterFactory.INSTANCE.get(type).compile(expression)
            );  // @3
        } catch (Throwable e) {
            log.error("parse error: expr={}, topic={}, group={}, error={}", expression, topic, consumerGroup, e.getMessage());
            return null;
        }

        return consumerFilterData;
```

该方法构建 `ConsumerFilterData` 数据。不包含 `BoomFilter` 的构建。  
代码@1，首先我们来看一下参数：

 ```html
*  topic : 主题。
 *  consumerGroup ：消费组。
 *  expression ：表达式。
 *  type ：消息过滤类型,TAG或SQL92。
 *  clientVersion : 客户端版本号。
```

代码@2：如果是 `TAG` 模式，则返回 `null`,表明 `ConsumerFilterData` 不针对TAG模式。  
代码@3：编译表达式，这里我们就不再深入去看如何解析SQL语句，例如 `a >= 1 and b <=10` 这些语法，如果大家有兴趣，可以重点看一下 `SqlFilter-->SelectorParser`。`这里会返回一个BooleanExpression` 的实现类，用于判断是否匹配。

我们再转入到 MessageFilter\#isMatchedByCommitLog 的实现中来，因为 `SQL92` 是根据消息体中的属性进行过滤，故需要访问 `CommitLog` 文件，也就是消息体。

```
public boolean isMatchedByCommitLog(ByteBuffer msgBuffer, Map<String, String> properties) {
        if (subscriptionData == null) {
            return true;
        }

        if (subscriptionData.isClassFilterMode()) {
            return true;
        }

        if (ExpressionType.isTagType(subscriptionData.getExpressionType())) {
            return true;
        }

        ConsumerFilterData realFilterData = this.consumerFilterData;
        Map<String, String> tempProperties = properties;

        // no expression
        if (realFilterData == null || realFilterData.getExpression() == null
            || realFilterData.getCompiledExpression() == null) {
            return true;
        }

        if (tempProperties == null && msgBuffer != null) {  // @1
            tempProperties = MessageDecoder.decodeProperties(msgBuffer);
        }

        Object ret = null;
        try {
            MessageEvaluationContext context = new MessageEvaluationContext(tempProperties);

            ret = realFilterData.getCompiledExpression().evaluate(context);  // @2
        } catch (Throwable e) {
            log.error("Message Filter error, " + realFilterData + ", " + tempProperties, e);
        }

        log.debug("Pull eval result: {}, {}, {}", ret, realFilterData, tempProperties);

        if (ret == null || !(ret instanceof Boolean)) {
            return false;
        }

        return (Boolean) ret;
```

代码@1：从消息体中解码出属性。  
代码@2：然后对表达式进行匹配，上下文环境为消息体中的属性，如果匹配，则返回 `true`,否则返回 `false`。

# 2、ClassFilter 消息过滤机制 FilterServer详解 #

  
![img\_0914\_01\_2.png][img_0914_01_2.png]  
从图中可以看出，如果使用了类模式过滤，`Consumer` 不是直接从Broker拉取，而是从 `FilterServer` 上拉取。那么问题来了，`FilterServer` 是什么、`Consume` 如何与 `FilterServer` 打交道。

我们知道，一个客户端，一个专门的消息拉取线程(`PullMessageService`)专门负责拉取消息，多种过滤模式公用一套消息拉取机制【消息队列负载机制】，那 `ClassFilter` 模式是如何工作呢？首先，`ClassFilter` 模式，顾名思义就是消费端可以上传一个Class类文件到 `FilterServer`, 然后 `FilterServer` 从 `Broker` 拉取消息，执行过滤逻辑然后再返回给 `Consumer`。

`ClassFilter` 模式过滤机制，本文从如下三个方面展开。  
1）`ClassFilter` 注册（消费端如何提交自己的消息过滤实现类、以及消费订阅信息注册）。  
2）消费端如何路由到 `FilterServer上拉取消息`。  
3）`FilterServer` 消息拉取与消息消费。

## 2.1 ClassFilter模式 消息过滤类注册机制 ##

DefaultMQPushConsumerImpl\#subscribe

```
public void subscribe(String topic, String fullClassName, String filterClassSource) throws MQClientException {  // @1
        try {
            SubscriptionData subscriptionData = FilterAPI.buildSubscriptionData(this.defaultMQPushConsumer.getConsumerGroup(),
                topic, "*");
            subscriptionData.setSubString(fullClassName);
            subscriptionData.setClassFilterMode(true);     // @2
            subscriptionData.setFilterClassSource(filterClassSource);
            this.rebalanceImpl.getSubscriptionInner().put(topic, subscriptionData);    // @3
            if (this.mQClientFactory != null) {
                this.mQClientFactory.sendHeartbeatToAllBrokerWithLock();     // @4
            }

        } catch (Exception e) {
            throw new MQClientException("subscription exception", e);
        }
```

代码@1：`topic` : 主题，`fullClassName` ： 过滤类全类路径名，`filterClassSource`： 过滤类内容。  
代码@2：设置 `classFilterMode` 为 `true`,表示类过滤机制。  
代码@3：将该主题的订阅信息放入到 `RebalanceImp` l对象中，一个消费者各自维护一个 `RebalanceImpl` 对象，用于创建消息拉取任务。  
代码@4：`sendHeartbeatToAllBrokerWithLock`，关键，发送心跳到所有Broker。  
`MQClientInstance\#sendHeartbeatToAllBrokerWithLock  `
![img\_0914\_01\_3.png][img_0914_01_3.png]  
重点关注`MQClientInstance\#uploadFilterClassSource`方法：  
![img\_0914\_01\_4.png][img_0914_01_4.png]  
继续进入`MQClientInstance\#uploadFilterClassToAllFilterServer`方法。

```
private void uploadFilterClassToAllFilterServer(final String consumerGroup, final String fullClassName,
        final String topic,
        final String filterClassSource) throws UnsupportedEncodingException {
        byte[] classBody = null;
        int classCRC = 0;
        try {
            classBody = filterClassSource.getBytes(MixAll.DEFAULT_CHARSET);
            classCRC = UtilAll.crc32(classBody);
        } catch (Exception e1) {
            log.warn("uploadFilterClassToAllFilterServer Exception, ClassName: {} {}",
                fullClassName,
                RemotingHelper.exceptionSimpleDesc(e1));
        }  // @1

        TopicRouteData topicRouteData = this.topicRouteTable.get(topic);    
        if (topicRouteData != null
            && topicRouteData.getFilterServerTable() != null && !topicRouteData.getFilterServerTable().isEmpty()) {    // @2
            Iterator<Entry<String, List<String>>> it = topicRouteData.getFilterServerTable().entrySet().iterator();     
            while (it.hasNext()) {
                Entry<String, List<String>> next = it.next();
                List<String> value = next.getValue();
                for (final String fsAddr : value) {
                    try {
                        this.mQClientAPIImpl.registerMessageFilterClass(fsAddr, consumerGroup, topic, fullClassName, classCRC, classBody,
                            5000);    // @3

                        log.info("register message class filter to {} OK, ConsumerGroup: {} Topic: {} ClassName: {}", fsAddr, consumerGroup,
                            topic, fullClassName);

                    } catch (Exception e) {
                        log.error("uploadFilterClassToAllFilterServer Exception", e);
                    }
                }
            }
        } else {
            log.warn("register message class filter failed, because no filter server, ConsumerGroup: {} Topic: {} ClassName: {}",
                consumerGroup, topic, fullClassName);
        }
```

代码@1：将代码转换成字节数值。  
代码@2：根据主题找到路由信息，如果路由信息中的 `filterServerTable` 不为空，则通过网络将 `classname`,`class` 内容注册到 `FilterServer` 中。这里不免有一 个疑问：`TopicRouteInfo` 中的 filterserver 地址从何而来？我们先简单了解一下代码@3，再来分析 这个问题，也就是 `FilterServer` 注册机制。  
代码@3：`registerMessageFilterClass`，向路由信息中包含的 `FilterServer` 服务器注册过滤类，该方法主要是构建`RequestCode.REGISTER\_MESSAGE\_FILTER\_CLASS` 消息，发往 `FilterServer`。具体处理逻辑，在 `FilterServer` 端。

### 2.1.1 FilterClassManager 源码分析 ###

FilterServer收到REGISTER\_MESSAGE\_FILTER\_CLASS，完成类的注册与类加载。

**2、1.1.1 FilterClassManager\#registerFilterClass**

```
public boolean registerFilterClass(final String consumerGroup, final String topic,
        final String className, final int classCRC, final byte[] filterSourceBinary) {   // @1
        final String key = buildKey(consumerGroup, topic);                                        // @2
 
        boolean registerNew = false;
        FilterClassInfo filterClassInfoPrev = this.filterClassTable.get(key);
        if (null == filterClassInfoPrev) {                                                                       // @3
            registerNew = true;
        } else {
            if (this.filtersrvController.getFiltersrvConfig().isClientUploadFilterClassEnable()) {    // @4
                if (filterClassInfoPrev.getClassCRC() != classCRC && classCRC != 0) {
                    registerNew = true;
                }
            }
        }
 
        if (registerNew) {  // @5
            synchronized (this.compileLock) {
                filterClassInfoPrev = this.filterClassTable.get(key);
                if (null != filterClassInfoPrev && filterClassInfoPrev.getClassCRC() == classCRC) {  //@6
                    return true;
                }
 
                try {
 
                    FilterClassInfo filterClassInfoNew = new FilterClassInfo();
                    filterClassInfoNew.setClassName(className);
                    filterClassInfoNew.setClassCRC(0);
                    filterClassInfoNew.setMessageFilter(null);
 
                    if (this.filtersrvController.getFiltersrvConfig().isClientUploadFilterClassEnable()) {   // @7
                        String javaSource = new String(filterSourceBinary, MixAll.DEFAULT_CHARSET);
                        Class<?> newClass = DynaCode.compileAndLoadClass(className, javaSource);
                        Object newInstance = newClass.newInstance();
                        filterClassInfoNew.setMessageFilter((MessageFilter) newInstance);
                        filterClassInfoNew.setClassCRC(classCRC);
                    }
 
                    this.filterClassTable.put(key, filterClassInfoNew);
                } catch (Throwable e) {
                    String info =
                        String
                            .format(
                                "FilterServer, registerFilterClass Exception, consumerGroup: %s topic: %s className: %s",
                                consumerGroup, topic, className);
                    log.error(info, e);
                    return false;
                }
            }
        }
 
        return true;
}
```

代码@1：`consumerGroup` 消费组名称；`topic`：消费主题；`className`：过滤类; `classCRC`：过滤类 `crc`,`filterSourceBinary` 过滤类内容字节数组。

代码@2：构建 `FilterClass` 信息的缓存key,主题名 + `“@”` + 消费组名。

代码@3：如果当前不存在该key的过滤器信息，则认为是第一次注册。

代码@4：如果允许客户端编译上传的类，并且原先的过滤信息的crc与新的额crc不一样，也认为是第一次注册，将覆盖原先的注册信息。

代码@5：加锁，防止并发修改注册信息 `Map`。

代码@6：这里是双重检查（并发编程通用的手段），例如，同一个消费组多个消费者同时注册，进行排队，一个处理好了之后，其他的获取锁，再检查一次，避免重复操作。

代码@7： 如果允许客户端编译上传的类(`clientUploadFilterClassEnable=true`)，则根据过滤类名，过滤类源代码，利用jdk提供的编译API（`JavaCompiler`），具体封装类(`DynaCode`),将类编译好，如果不允许编译的话（`clientUploadFilterClassEnable=false`），就只是 收集这些信息，真正的类加载需要去服务器去下载，然后再编译。这里主要是基于安全考虑，因为允许消费者（应用程序）直接上传`JAVA`类，本身就是一件危险的事情。那如果`clientUploadFilterClassEnable=false`,那如何编译呢？

**2、1.1.2 FilterClassManager\#fetchClassFromRemoteHost**

```
public void start() {
        if (!this.filtersrvController.getFiltersrvConfig().isClientUploadFilterClassEnable()) {
            this.scheduledExecutorService.scheduleAtFixedRate(new Runnable() {

                @Override
                public void run() {
                    fetchClassFromRemoteHost();
                }
            }, 1, 1, TimeUnit.MINUTES);         // @1
        }
    }

    private void fetchClassFromRemoteHost() {
        Iterator<Entry<String, FilterClassInfo>> it = this.filterClassTable.entrySet().iterator();
        while (it.hasNext()) {
            try {
                Entry<String, FilterClassInfo> next = it.next();
                FilterClassInfo filterClassInfo = next.getValue();
                String[] topicAndGroup = next.getKey().split("@");
                String responseStr =
                    this.filterClassFetchMethod.fetch(topicAndGroup[0], topicAndGroup[1],
                        filterClassInfo.getClassName());    // @2
                byte[] filterSourceBinary = responseStr.getBytes("UTF-8");
                int classCRC = UtilAll.crc32(responseStr.getBytes("UTF-8"));
                if (classCRC != filterClassInfo.getClassCRC()) {
                    String javaSource = new String(filterSourceBinary, MixAll.DEFAULT_CHARSET);
                    Class<?> newClass =
                        DynaCode.compileAndLoadClass(filterClassInfo.getClassName(), javaSource);
                    Object newInstance = newClass.newInstance();
                    filterClassInfo.setMessageFilter((MessageFilter) newInstance);
                    filterClassInfo.setClassCRC(classCRC);

                    log.info("fetch Remote class File OK, {} {}", next.getKey(),
                        filterClassInfo.getClassName());
                }
            } catch (Exception e) {
                log.error("fetchClassFromRemoteHost Exception", e);
            }
        }
```

代码@1：首先，如果 `isClientUploadFilterClassEnable` 设置为 `false`, 则开启一个定时任务，每一分钟尝试从远程服务器，i`sClientUploadFilterClassEnable=false` 时，需要配置 `filterClassRepertoryUr` l属性，根据类名拉取类的源码。

代码@2：通过HTTP协议从远程服务器拉取代码，远程服务器URL: `filterClassRepertoryUrl/classname.java`。

## 2.2 ClassFilter模式 FilterServer注册机制 ##

上文提到，消费者采用类过滤机制时，需要向 `FilterServer` 服务器列表注册过滤器类，`FilterServer` 的列表是从 `topic` 的路由信息 `TopicInfo` 中获取, `topicInfo` 中的 `FilterServer` 从何而来呢？本节将揭开谜底.

`RocketMQ` 路由信息维护，`Broker` 通过心跳向 `NameServer` 注册。再结合此图，应该能想到 `FilterServer` 向 `Broker` 注册，然后 `Broker` 再发送给 `NameServer`。  
![img\_0914\_01\_5.png][img_0914_01_5.png]

### 2.2.1 FilterServer注册 ###

`FiltersrvController\#initialize  `
![img\_0914\_01\_6.png][img_0914_01_6.png]

`FilterServerOuterAPI\#registerFilterServerToBroker`，该方法就是向 `Broker` 发送 `RequestCode.REGISTER\_FILTER\_SERVER` 请求。  
`Broker` 端：`AdminBrokerProcessor\#registerFilterServer`。  
![img\_0914\_01\_7.png][img_0914_01_7.png]  
这里的关键点就是调用 `brokerControl.getFilterServerManager().registerFillterServer` 方法，将该注册信息存入到 `brokerControl` 的 `FilerServerManager` 路由表中`（this.filterServerTable.put(channel, filterServerInfo)）`，完成 `FilterServer` 到 `Broker` 的注册过程。

我们知道，消费者调用 `subscribe` 方法，会首先将订阅信息存储在消费者中，其实存储在消费端 `RebalanceImpl` 的订阅消息中。然后通过心跳来注册消息到 `Broker`, 然后 `Broker` 通过心跳发送到 `NameServer`, 将 `FilterServer` 服务器列表加入到路由信息中。

由于该部分源码简单，就不一一介绍了，`NameServer` 接收 `Broker` 心跳，并注册 `FilterServer` 服务器的代码入口：  
`DefaultRequestProcessor\#registerBrokerWithFilterServer`，接下来进入到消息拉取环节。

## 2.3 消息拉取 ##

进入本篇最后一个议题，消息拉取。

消息拉取大家应该不陌生了吧，一个 `MQClientInstance` 一个拉取消息线程。

消息拉去以推模式来讲解，拉模式是应用程序手动去拉。并不是通过 `PullMessageServer` 线程。

`PullMessageServer` 线程在执行消息拉取时，最终将进入到：`PullAPIWrapper\#pullKernelImpl`。  
![img\_0914\_01\_8.png][img_0914_01_8.png]  
核心代码：如果使用了类过滤消息模式，将改变拉取地址为 `FilterServer` 地址：  
![img\_0914\_01\_9.png][img_0914_01_9.png]

从该 `Broker` 中的 `FilterServer` 地址列表中，随机选择一个，进行拉取。

在 `FilterServer` 端，就是通过拉模式根据拉取需求从 `Broker` 上拉取消息，然后执行消息过滤逻辑。

# 4、总结 #

本文重点分析了 `FilterServer` 类消息过滤的实现原理，总结如下：  
1、订阅消息与 `FilterServer` 注册流程

 *  消息消费者 先用类过滤模式将过滤器类，过滤器类内容 添加到订阅消息中（`RebalanceImpl`)。
 *  消费者拉取消息时，向 `FilterServer` 发送请求，那 `FilterServer` 怎么得到 `FilterServer` 服务器地址呢？通过主题路由信息，向 `NameServer获` 取。消费者在得到 `FilterServervf` 服务地址的时候，会将自己过滤器类代码发送到 `FilterServer`,方便 `FilterServer` 在拉取消息时执行过滤逻辑。
 *  `FilterServer` 启动时向 `Broker` 注册 `Filter` 服务地址信息。（服务列表），然后 `Broker` 将该信息通过心跳向 `NameServer` 注册，将 `FilterServer` 服务列表保存在主题的路由信息中。

2、`clientUploadFilterClassEnable` 参数，如果设置为 `false`, 则需要一个 `url`，去下载过滤类：`url/classname.java` 这样的请求，`FilterServer` 要能拿到过滤类代码。

3、类过滤模式，上传的类必须实现 `MessageFilter` 接口。

--------------------

备注：本文是《RocketMQ技术内幕》的前期素材，建议关注笔者的书籍：《RocketMQ技术内幕》。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_5.png
[img_0914_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_6.png
[img_0914_01_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_7.png
[img_0914_01_8.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_8.png
[img_0914_01_9.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/13/img_0914_01_9.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")