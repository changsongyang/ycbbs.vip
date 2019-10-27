作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

有关RocketMQ ACL的使用请查看上一篇[《RocketMQ ACL使用指南》][RocketMQ ACL]，本文从源码的角度，分析一下RocketMQ ACL的实现原理。

> 备注：RocketMQ在4.4.0时引入了ACL机制，本文代码基于RocketMQ4.5.0版本。

### 本节目录 ###

 *  1、BrokerController\#initialAcl
    
    2、PlainAccessValidator
    
     *  2.1 类图
     *  2.1.2 PlainAccessResource类图
     *  2.2 构造方法
     *  2.3 parse方法
     *  2.4 validate 方法
    
    3、PlainPermissionLoader
    
     *  3.1 类图
     *  3.2 PlainPermissionLoader构造方法
     *  3.3 load
     *  3.4 watch
     *  3.5 validate
     *  3.5.1 checkPerm
    
    4、AclClientRPCHook
    
     *  4.1 doBeforeRequest

  
根据RocketMQ ACL使用手册，我们应该首先看一下Broker服务器在开启ACL机制时如何加载配置文件，并如何工作的。

## 1、BrokerController\#initialAcl ##

Broker端ACL的入口代码为：`BrokerController\#initialAcl`

```
private void initialAcl() {
    if (!this.brokerConfig.isAclEnable()) {                           // @1
        log.info("The broker dose not enable acl");
        return;
    }

    List<AccessValidator> accessValidators = ServiceProvider.load(ServiceProvider.ACL_VALIDATOR_ID, AccessValidator.class);   // @2
    if (accessValidators == null || accessValidators.isEmpty()) {
        log.info("The broker dose not load the AccessValidator");
        return;
    }

    for (AccessValidator accessValidator: accessValidators) {                       // @3
        final AccessValidator validator = accessValidator;
        this.registerServerRPCHook(new RPCHook() {

            @Override
            public void doBeforeRequest(String remoteAddr, RemotingCommand request) {
                //Do not catch the exception
                validator.validate(validator.parse(request, remoteAddr));                         // @4
            }

            @Override
            public void doAfterResponse(String remoteAddr, RemotingCommand request, RemotingCommand response) {
            }
        });
    }
}
```

本方法的实现共4个关键点。  
代码@1：首先判断Broker是否开启了 `acl`，通过配置参数 `aclEnable` 指定，默认为 `false`。

代码@2：使用类似 `SPI` 机制，加载配置的 `AccessValidator`,该方法返回一个列表，其实现逻辑时读取`META-INF/service/org.apache.rocketmq.acl.AccessValidator`文件中配置的访问验证器，默认配置内容如下：  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
代码@3：遍历配置的访问验证器(`AccessValidator`),并向Broker处理服务器注册钩子函数，`RPCHook的doBeforeRequest` 方法会在服务端接收到请求，将其请求解码后，执行处理请求之前被调用;RPCHook的`doAfterResponse`方法会在处理完请求后，将结果返回之前被调用，其调用如图所示：  
![img\_0914\_01\_2.png][img_0914_01_2.png]  
代码@4：在`RPCHook\#doBeforeRequest`方法中调用`AccessValidator\#validate`, 在真实处理命令之前，先执行ACL的验证逻辑，如果拥有该操作的执行权限，则放行，否则抛出AclException。

接下来，我们将重点放到 `Broker` 默认实现的访问验证器：`PlainAccessValidator`。

## 2、PlainAccessValidator ##

### 2.1 类图 ###

![img\_0914\_01\_3.png][img_0914_01_3.png]

```html
 *  AccessValidator  
    访问验证器接口，主要定义两个接口。  
    1）AccessResource parse(RemotingCommand request, String remoteAddr)  
    从请求头中解析本次请求对应的访问资源，即本次请求需要的访问权限。  
    2）void validate(AccessResource accessResource)  
    根据本次需要访问的权限，与请求用户拥有的权限进行对比验证，判断是拥有权限，如果没有访问该操作的权限，则抛出异常，否则放行。
 *  PlainAccessValidator  
    RocketMQ默认提供的基于yml配置格式的访问验证器。
```

接下来我们重点看一下 `PlainAccessValidator` 的 `parse` 方法与 `validate` 方法的实现细节。在讲解该方法之前，我们首先认识一下 `RocketMQ` 封装访问资源的 `PlainAccessResource`。

#### 2.1.2 PlainAccessResource类图 ####

![img\_0914\_01\_4.png][img_0914_01_4.png]  
我们对其属性一一做个介绍：

```html
*  private String accessKey  
    访问Key，用户名。
 *  private String secretKey  
    用户密码。
 *  private String whiteRemoteAddress  
    远程IP地址白名单。
 *  private boolean admin  
    是否是管理员角色。
 *  private byte defaultTopicPerm = 1  
    默认topic访问权限，即如果没有配置topic的权限，则Topic默认的访问权限为1，表示为DENY。
 *  private byte defaultGroupPerm = 1  
    默认的消费组访问权限，默认为DENY。
 *  private Map<String, Byte> resourcePermMap  
    资源需要的访问权限映射表。
 *  private RemoteAddressStrategy remoteAddressStrategy  
    远程IP地址验证策略。
 *  private int requestCode  
    当前请求的requestCode。
 *  private byte\[\] content  
    请求头与请求体的内容。
 *  private String signature  
    签名字符串，这是通常的套路，在客户端时，首先将请求参数排序，然后使用secretKey生成签名字符串，服务端重复这个步骤，然后对比签名字符串，如果相同，则认为登录成功，否则失败。
 *  private String secretToken  
    密钥token。
 *  private String recognition  
    目前作用未知，代码中目前未被使用。
```

### 2.2 构造方法 ###

```
public PlainAccessValidator() {
    aclPlugEngine = new PlainPermissionLoader();
}
```

构造函数，直接创建 `PlainPermissionLoader` 对象，从命名上来看，应该是触发acl规则的加载，即解析`plain\_acl.yml`，接下来会重点探讨，即acl启动流程之配置文件的解析。

### 2.3 parse方法 ###

该方法的作用就是从请求命令中解析出本次访问所需要的访问权限，最终构建 `AccessResource` 对象，为后续的校验权限做准备。

```
PlainAccessResource accessResource = new PlainAccessResource();
if (remoteAddr != null && remoteAddr.contains(":")) {
    accessResource.setWhiteRemoteAddress(remoteAddr.split(":")[0]);
} else {
    accessResource.setWhiteRemoteAddress(remoteAddr);
}
```

Step1：首先创建 `PlainAccessResource`，从远程地址中提取出远程访问IP地址。

```
if (request.getExtFields() == null) {
    throw new AclException("request's extFields value is null");
}
accessResource.setRequestCode(request.getCode());
accessResource.setAccessKey(request.getExtFields().get(SessionCredentials.ACCESS_KEY));
accessResource.setSignature(request.getExtFields().get(SessionCredentials.SIGNATURE));
accessResource.setSecretToken(request.getExtFields().get(SessionCredentials.SECURITY_TOKEN));
```

Step2：如果请求头中的扩展字段为空，则抛出异常，如果不为空，则从请求头中读取 `requestCode`、`accessKey`(请求用户名)、签名字符串(`signature`)、`secretToken`。

```
try {
            switch (request.getCode()) {
                case RequestCode.SEND_MESSAGE:
                    accessResource.addResourceAndPerm(request.getExtFields().get("topic"), Permission.PUB);
                    break;
                case RequestCode.SEND_MESSAGE_V2:
                    accessResource.addResourceAndPerm(request.getExtFields().get("b"), Permission.PUB);
                    break;
                case RequestCode.CONSUMER_SEND_MSG_BACK:
                    accessResource.addResourceAndPerm(request.getExtFields().get("originTopic"), Permission.PUB);
                    accessResource.addResourceAndPerm(getRetryTopic(request.getExtFields().get("group")), Permission.SUB);
                    break;
                case RequestCode.PULL_MESSAGE:
                    accessResource.addResourceAndPerm(request.getExtFields().get("topic"), Permission.SUB);
                    accessResource.addResourceAndPerm(getRetryTopic(request.getExtFields().get("consumerGroup")), Permission.SUB);
                    break;
                case RequestCode.QUERY_MESSAGE:
                    accessResource.addResourceAndPerm(request.getExtFields().get("topic"), Permission.SUB);
                    break;
                case RequestCode.HEART_BEAT:
                    HeartbeatData heartbeatData = HeartbeatData.decode(request.getBody(), HeartbeatData.class);
                    for (ConsumerData data : heartbeatData.getConsumerDataSet()) {
                        accessResource.addResourceAndPerm(getRetryTopic(data.getGroupName()), Permission.SUB);
                        for (SubscriptionData subscriptionData : data.getSubscriptionDataSet()) {
                            accessResource.addResourceAndPerm(subscriptionData.getTopic(), Permission.SUB);
                        }
                    }
                    break;
                case RequestCode.UNREGISTER_CLIENT:
                    final UnregisterClientRequestHeader unregisterClientRequestHeader =
                        (UnregisterClientRequestHeader) request
                            .decodeCommandCustomHeader(UnregisterClientRequestHeader.class);
                    accessResource.addResourceAndPerm(getRetryTopic(unregisterClientRequestHeader.getConsumerGroup()), Permission.SUB);
                    break;
                case RequestCode.GET_CONSUMER_LIST_BY_GROUP:
                    final GetConsumerListByGroupRequestHeader getConsumerListByGroupRequestHeader =
                        (GetConsumerListByGroupRequestHeader) request
                            .decodeCommandCustomHeader(GetConsumerListByGroupRequestHeader.class);
                    accessResource.addResourceAndPerm(getRetryTopic(getConsumerListByGroupRequestHeader.getConsumerGroup()), Permission.SUB);
                    break;
                case RequestCode.UPDATE_CONSUMER_OFFSET:
                    final UpdateConsumerOffsetRequestHeader updateConsumerOffsetRequestHeader =
                        (UpdateConsumerOffsetRequestHeader) request
                            .decodeCommandCustomHeader(UpdateConsumerOffsetRequestHeader.class);
                    accessResource.addResourceAndPerm(getRetryTopic(updateConsumerOffsetRequestHeader.getConsumerGroup()), Permission.SUB);
                    accessResource.addResourceAndPerm(updateConsumerOffsetRequestHeader.getTopic(), Permission.SUB);
                    break;
                default:
                    break;

            }
        } catch (Throwable t) {
            throw new AclException(t.getMessage(), t);
        }
```

Step3：根据请求命令，设置本次请求需要拥有的权限，上述代码比较简单，就是从请求中得出本次操作的 `Topic`、消息组名称，为了方便区分 `topic` 与消费组，消费组使用消费者对应的重试主题，当成资源的Key，从这里也可以看出，当前版本需要进行ACL权限验证的请求命令如下：

 *  SEND\_MESSAGE
 *  SEND\_MESSAGE\_V2
 *  CONSUMER\_SEND\_MSG\_BACK
 *  PULL\_MESSAGE
 *  QUERY\_MESSAGE
 *  HEART\_BEAT
 *  UNREGISTER\_CLIENT
 *  GET\_CONSUMER\_LIST\_BY\_GROUP
 *  UPDATE\_CONSUMER\_OFFSET

```
// Content
SortedMap<String, String> map = new TreeMap<String, String>();
for (Map.Entry<String, String> entry : request.getExtFields().entrySet()) {
    if (!SessionCredentials.SIGNATURE.equals(entry.getKey())) {
        map.put(entry.getKey(), entry.getValue());
    }
}
accessResource.setContent(AclUtils.combineRequestContent(request, map));
return accessResource;
```

Step4：对扩展字段进行排序，便于生成签名字符串，然后将扩展字段与请求体(`body`)写入 `content` 字段。完成从请求头中解析出本次请求需要验证的权限。

### 2.4 validate 方法 ###

```
public void validate(AccessResource accessResource) {
    aclPlugEngine.validate((PlainAccessResource) accessResource);
}
```

验证权限，即根据本次请求需要的权限与当前用户所拥有的权限进行对比，如果符合，则正常执行；否则抛出AclException。

为了揭开配置文件的解析与验证，我们将目光投入到 `PlainPermissionLoader`。

## 3、PlainPermissionLoader ##

该类的主要职责：加载权限，即解析acl主要配置文件`plain\_acl.yml`。

### 3.1 类图 ###

![img\_0914\_01\_5.png][img_0914_01_5.png]  
下面对其核心属性与核心方法一一介绍：

```html
 *  DEFAULT\_PLAIN\_ACL\_FILE  
    默认acl配置文件名称，默认值为conf/plain\_acl.yml。
 *  String fileName  
    acl配置文件名称，默认为DEFAULT\_PLAIN\_ACL\_FILE ,可以通过系统参数-Drocketmq.acl.plain.file=fileName指定。
 *  Map<String, PlainAccessResource> plainAccessResourceMap  
    解析出来的权限配置映射表，以用户名为键。
 *  RemoteAddressStrategyFactory remoteAddressStrategyFactory  
    远程IP解析策略工厂，用于解析白名单IP地址。
 *  boolean isWatchStart  
    是否开启了文件监听，即自动监听plain\_acl.yml文件，一旦该文件改变，可在不重启服务器的情况下自动生效。
 *  public PlainPermissionLoader()  
    构造方法。
 *  public void load()  
    加载配置文件。
 *  public void validate(PlainAccessResource plainAccessResource)  
    验证是否有权限访问待访问资源。
```

### 3.2 PlainPermissionLoader构造方法 ###

```
public PlainPermissionLoader() {
    load();
    watch();
}
```

在构造方法中调用load与watch方法。

### 3.3 load ###

```
Map<String, PlainAccessResource> plainAccessResourceMap = new HashMap<>();
List<RemoteAddressStrategy> globalWhiteRemoteAddressStrategy = new ArrayList<>();
String path = fileHome + File.separator + fileName;
JSONObject plainAclConfData = AclUtils.getYamlDataObject(path,JSONObject.class);
```

Step1：初始化 `plainAccessResourceMap`(用户配置的访问资源，即权限容器)、`globalWhiteRemoteAddressStrategy`：全局IP白名单访问策略。配置文件，默认为$\{ROCKETMQ\_HOME\}/conf/plain\_acl.yml。

```
JSONArray globalWhiteRemoteAddressesList = plainAclConfData.getJSONArray("globalWhiteRemoteAddresses");
if (globalWhiteRemoteAddressesList != null && !globalWhiteRemoteAddressesList.isEmpty()) {
    for (int i = 0; i < globalWhiteRemoteAddressesList.size(); i++) {
        globalWhiteRemoteAddressStrategy.add(remoteAddressStrategyFactory.
        getRemoteAddressStrategy(globalWhiteRemoteAddressesList.getString(i)));
    }
}
```

Step2：globalWhiteRemoteAddresses：全局白名单，类型为数组。根据配置的规则，使用 `remoteAddressStrategyFactory` 获取一个访问策略，下文会重点介绍其配置规则。

```
JSONArray accounts = plainAclConfData.getJSONArray("accounts");
if (accounts != null && !accounts.isEmpty()) {
    List<PlainAccessConfig> plainAccessConfigList = accounts.toJavaList(PlainAccessConfig.class);
    for (PlainAccessConfig plainAccessConfig : plainAccessConfigList) {
        PlainAccessResource plainAccessResource = buildPlainAccessResource(plainAccessConfig);
        plainAccessResourceMap.put(plainAccessResource.getAccessKey(),plainAccessResource);
    }
}
this.globalWhiteRemoteAddressStrategy = globalWhiteRemoteAddressStrategy;
this.plainAccessResourceMap = plainAccessResourceMap;
```

Step3：解析`plain\_acl.yml`文件中的另外一个根元素 `accounts`，用户定义的权限信息。从 `PlainAccessConfig` 的定义来看，`accounts` 标签下支持如下标签：

```html
 *  accessKey
 *  secretKey
 *  whiteRemoteAddress
 *  admin
 *  defaultTopicPerm
 *  defaultGroupPerm
 *  topicPerms
 *  groupPerms  
```
    
上述标签的说明，请参考：:[《RocketMQ ACL使用指南》][RocketMQ ACL] 。具体的解析过程比较容易，就不再细说。

load方法主要完成acl配置文件的解析，将用户定义的权限加载到内存中。

### 3.4 watch ###

```
private void watch() {
    try {
        String watchFilePath = fileHome + fileName;
        FileWatchService fileWatchService = new FileWatchService(new String[] {watchFilePath}, new FileWatchService.Listener() {
                @Override
                public void onChanged(String path) {   
                    log.info("The plain acl yml changed, reload the context");
                    load();
                }
        });
        fileWatchService.start();
        log.info("Succeed to start AclWatcherService");
        this.isWatchStart = true;
    } catch (Exception e) {
        log.error("Failed to start AclWatcherService", e);
    }
}
```

监听器，默认以500ms的频率判断文件的内容是否变化。在文件内容发生变化后调用`load()`方法，重新加载配置文件。那 `FileWatchService` 是如何判断两个文件的内容发生了变化呢？

```
FileWatchService#hash
private String hash(String filePath) throws IOException, NoSuchAlgorithmException {
    Path path = Paths.get(filePath);
    md.update(Files.readAllBytes(path));
    byte[] hash = md.digest();
    return UtilAll.bytes2string(hash);
}
```

获取文件md5签名来做对比，这里为什么不在启动时先记录上一次文件的修改时间，然后先判断其修改时间是否变化，再判断其内容是否真正发生变化。

### 3.5 validate ###

```
// Check the global white remote addr
for (RemoteAddressStrategy remoteAddressStrategy : globalWhiteRemoteAddressStrategy) {
    if (remoteAddressStrategy.match(plainAccessResource)) {
        return;
    }
}
```

Step1：首先使用全局白名单对资源进行验证，只要一个规则匹配，则返回，表示认证成功。

```
if (plainAccessResource.getAccessKey() == null) {
    throw new AclException(String.format("No accessKey is configured"));
}
if (!plainAccessResourceMap.containsKey(plainAccessResource.getAccessKey())) {
    throw new AclException(String.format("No acl config for %s", plainAccessResource.getAccessKey()));
}
Step2：如果请求信息中，没有设置用户名，则抛出未配置AccessKey异常；如果Broker中并为配置该用户的配置信息，则抛出AclException。

// Check the white addr for accesskey
PlainAccessResource ownedAccess = plainAccessResourceMap.get(plainAccessResource.getAccessKey());
if (ownedAccess.getRemoteAddressStrategy().match(plainAccessResource)) {
    return;
}
```

Step3：如果用户配置的白名单与待访问资源规则匹配的话，则直接发认证通过。

```
// Check the signature
String signature = AclUtils.calSignature(plainAccessResource.getContent(), ownedAccess.getSecretKey());
if (!signature.equals(plainAccessResource.getSignature())) {
    throw new AclException(String.format("Check signature failed for accessKey=%s", plainAccessResource.getAccessKey()));
}
```

Step4：验证签名。

```
checkPerm(plainAccessResource, ownedAccess);
```

Step5：调用 `checkPerm` 方法，验证需要的权限与拥有的权限是否匹配。

#### 3.5.1 checkPerm ####

```
if (Permission.needAdminPerm(needCheckedAccess.getRequestCode()) && !ownedAccess.isAdmin()) {
    throw new AclException(String.format("Need admin permission for request code=%d, but accessKey=%s is not", needCheckedAccess.getRequestCode(), ownedAccess.getAccessKey()));
}
```

Step6：如果当前的请求命令属于必须是Admin用户才能访问的权限，并且当前用户并不是管理员角色，则抛出异常，如下命令需要admin角色才能进行的操作：

```
Map<String, Byte> needCheckedPermMap = needCheckedAccess.getResourcePermMap();
Map<String, Byte> ownedPermMap = ownedAccess.getResourcePermMap();
if (needCheckedPermMap == null) {
    // If the needCheckedPermMap is null,then return
    return;
}
if (ownedPermMap == null && ownedAccess.isAdmin()) {
    // If the ownedPermMap is null and it is an admin user, then return
    return;
}
```

Step7：如果该请求不需要进行权限验证，则通过认证，如果当前用户的角色是管理员，并且没有配置用户权限，则认证通过，返回。

```
for (Map.Entry<String, Byte> needCheckedEntry : needCheckedPermMap.entrySet()) {
    String resource = needCheckedEntry.getKey();
    Byte neededPerm = needCheckedEntry.getValue();
    boolean isGroup = PlainAccessResource.isRetryTopic(resource);

    if (ownedPermMap == null || !ownedPermMap.containsKey(resource)) {
        // Check the default perm
        byte ownedPerm = isGroup ? ownedAccess.getDefaultGroupPerm() : ownedAccess.getDefaultTopicPerm();
        if (!Permission.checkPermission(neededPerm, ownedPerm)) {
            throw new AclException(String.format("No default permission for %s", PlainAccessResource.printStr(resource, isGroup)));
        }
        continue;
    }
    if (!Permission.checkPermission(neededPerm, ownedPermMap.get(resource))) {
        throw new AclException(String.format("No default permission for %s", PlainAccessResource.printStr(resource, isGroup)));
    }
}
```

Step8：遍历需要权限与拥有的权限进行对比，如果配置对应的权限，则判断是否匹配；如果未配置权限，则判断默认权限时是否允许，不允许，则抛出 `AclException`。

验证逻辑就介绍到这里了，下面给出其匹配流程图：  
![img\_0914\_01\_6.png][img_0914_01_6.png]  
上述阐述了从Broker服务器启动、加载acl配置文件流程、动态监听配置文件、服务端权限验证流程，接下来我们看一下客户端关于ACL需要处理的事情。

## 4、AclClientRPCHook ##

回顾一下，我们引入ACL机制后，客户端的代码示例如下：  
![img\_0914\_01\_7.png][img_0914_01_7.png]  
其在创建DefaultMQProducer时，注册AclClientRPCHook钩子，会在向服务端发送远程命令前后执行其钩子函数，接下来我们重点分析一下AclClientRPCHook。

### 4.1 doBeforeRequest ###

```
public void doBeforeRequest(String remoteAddr, RemotingCommand request) {
    byte[] total = AclUtils.combineRequestContent(request,
           parseRequestContent(request, sessionCredentials.getAccessKey(), sessionCredentials.getSecurityToken()));   // @1
    String signature = AclUtils.calSignature(total, sessionCredentials.getSecretKey());                                                      // @2
    request.addExtField(SIGNATURE, signature);                                                                                                               // @3
    request.addExtField(ACCESS_KEY, sessionCredentials.getAccessKey());         
    // The SecurityToken value is unneccessary,user can choose this one.
    if (sessionCredentials.getSecurityToken() != null) {
        request.addExtField(SECURITY_TOKEN, sessionCredentials.getSecurityToken());
    }
}
```

代码@1：将 `Request` 请求参数进行排序，并加入 `accessKey`。

代码@2：对排好序的请参数，使用用户配置的密码生成签名，并最近到扩展字段 `Signature`，然后服务端也会按照相同的算法生成`Signature`，如果相同，则表示签名验证成功(类似于实现登录的效果)。

代码@3：将`Signature、AccessKey`等加入到请求头的扩展字段中，服务端拿到这些元数据，结合请求头中的信息，根据配置的权限，进行权限校验。

关于ACL客户端生成签名是一种通用套路，就不在细讲了。

源码分析ACL的实现就介绍到这里了，下文将介绍RocketMQ 消息轨迹的使用与实现原理分析。如果大家觉得文章写的还不错的话，期待帮忙点赞，谢谢。


[RocketMQ ACL]: https://blog.csdn.net/prestigeding/article/details/94317946
[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_5.png
[img_0914_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_6.png
[img_0914_01_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/29/img_0914_01_7.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")