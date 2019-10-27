作者：黄小厮 | 出处 [https://blog.csdn.net/dwade_mia/article/category/7527362](https://blog.csdn.net/dwade_mia/article/category/7527362)

# 总体架构 #

tomcat的总体架构如下图所示（摘自[http://blog.csdn.net/jiaomingliang/article/details/47393141][http_blog.csdn.net_jiaomingliang_article_details_47393141]）  
![img\_0907\_01\_\_1.png][img_0907_01_1.png]

如上图所示，`tomcat` 由 `Server`、`Service`、`Engine、Connerctor、Host、Context`组件组成，其中带有s的代表在一个tomcat实例上可以存在多个组件，比如`Context(s)`，`tomcat`允许我们部署多个应用，每个应用对应一个`Context`。这些组件在 `tomcat` 的`conf/server.xml`文件中可以找到，对`tomcat`的调优需要改动该文件

```
server.xml
<Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />

    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />

    <Engine name="Catalina" defaultHost="localhost">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>
      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t "%r" %s %b" />
      </Host>
    </Engine>
```

## Server ##

![img\_0907\_01\_\_2.png][img_0907_01_2.png]

`Server` 组件对应`org.apache.catalina.Server`接口，类图如上所示。  
```html
\- Server继承至LifeCycle，LifeCycle是一个非常重要的接口，各大组件都继承了这个接口，用于管理tomcat的生命周期，比如init、start、stop、destory；

另外，它使用了观察者模式，LifeCycle是一个监听者，它会向注册的LifecycleListener观察者发出各种事件  

\- Server提供了findService、getCatalina、getCatalinaHome、getCatalinaBase等接口，支持查找、遍历Service组件，这里似乎看到了和Serivce组件的些许联系
```

```
public interface Server extends Lifecycle {

    public NamingResourcesImpl getGlobalNamingResources();

    public void setGlobalNamingResources(NamingResourcesImpl globalNamingResources);

    public javax.naming.Context getGlobalNamingContext();

    public int getPort();

    public void setPort(int port);

    public String getAddress();

    public void setAddress(String address);

    public String getShutdown();

    public void setShutdown(String shutdown);

    public ClassLoader getParentClassLoader();

    public void setParentClassLoader(ClassLoader parent);

    public Catalina getCatalina();

    public void setCatalina(Catalina catalina);

    public File getCatalinaBase();

    public void setCatalinaBase(File catalinaBase);

    public File getCatalinaHome();

    public void setCatalinaHome(File catalinaHome);

    public void await();

    public Service findService(String name);

    public Service[] findServices();

    public void removeService(Service service);

    public Object getNamingToken();
```

## Service ##

`Service` 的默认实现类是 `StardardService`，类结构和 `StardardServer` 很相似，也是继承至 `LifecycleMBeanBase`，实现了 `Service` 接口

由 `Service` 接口不难发现 `Service` 组件的内部结构  
```html
\- 持有Engine实例  
\- 持有Server实例  
\- 可以管理多个Connector实例  
\- 持有Executor引用
```

```
public class StandardService extends LifecycleMBeanBase implements Service {
    // 省略若干代码
}

public interface Service extends Lifecycle {

    public Engine getContainer();

    public void setContainer(Engine engine);

    public String getName();

    public void setName(String name);

    public Server getServer();

    public void setServer(Server server);

    public ClassLoader getParentClassLoader();

    public void setParentClassLoader(ClassLoader parent);

    public String getDomain();

    public void addConnector(Connector connector);

    public Connector[] findConnectors();

    public void removeConnector(Connector connector);

    public void addExecutor(Executor ex);

    public Executor[] findExecutors();

    public Executor getExecutor(String name);

    public void removeExecutor(Executor ex);

    Mapper getMapper();
```

## Connector ##

`Connector` 是 `tomcat` 中监听 `TCP` 端口的组件，`server.xml` 默认定义了两个 `Connector`，分别用于监听 `http`、`ajp` 端口。对应的代码是`org.apache.catalina.connector.Connector`，它是一个实现类，并且实现了Lifecycle接口

### http ###

```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
```

`http`对应的`Connector`配置如上所示，其中`protocol`用于指定`http`协议的版本，还可以支持`2.0`；`connectionTimeout`定义了连接超时时间，单位是毫秒；`redirectPort`是`SSL`的重定向端口，它会把请求重定向到`8443`这个端口

### AJP ###

```
```

`Apache` `jserver` 协议(`AJP`)是一种二进制协议，它可以将来自 `web` 服务器的入站请求发送到位于 `web` 服务器后的应用服务器。

如果我们希望把 `Tomcat` 集成到现有的(或新的)`Apache http server`中，并且希望 `Apache` 能够处理web应用程序中包含的静态内容，或者使用 `Apache` 的SSL处理，我们便可以使用该协议。但是，在实际的项目应用中，`AJP` 协议并不常用，大多数应用场景会使用 `nginx+tomcat` 实现负载。

## Container ##

`org.apache.catalina.Container`接口定义了容器的api，它是一个处理用户 `servlet` 请求并返回对象给 `web` 用户的模块，它有四种不同的容器：  
```html
\- Engine，表示整个Catalina的servlet引擎  
\- Host，表示一个拥有若干个Context的虚拟主机  
\- Context，表示一个Web应用，一个context包含一个或多个wrapper  
\- Wrapper，表示一个独立的servlet
```

![img\_0907\_01\_\_3.png][img_0907_01_3.png]

`Engine`、`Host`、`Context`、`Wrapper` 都有一个默认的实现类 `StandardXXX`，均继承至 `ContainerBase`。此外，一个容器还包含一系列的 `Lodder`、`Logger`、`Manager`、`Realm` 和 `Resources` 等

一个容器可以有一个或多个低层次上的子容器，并且一个 `Catalina` 功能部署并不一定需要全部四种容器。一个 `Context` 有一个或多个 `wrapper`，而 `wrapper` 作为容器层次中的最底层，不能包含子容器。从一个容器添加到另一容器中可以使用在 `Container` 接口中定义的 `addChild()` 方法义：


删除一个容器可以使用 `Container` 接口中定义的 `removeChild()` 方法：


另外容器接口支持子接口查找和获得所有子接口集合的方法 `findChild` 和 `findChildren` 方法：

```
public Container findChild(String name); 
```

### Engine ###

![img\_0907\_01\_\_4.png][img_0907_01_4.png]

`Engine` 表示 `Catalina` 的 `Servlet` 引擎，如果使用了 `Engine` 的话，则它是 `Catalina` 的顶层容器，因此在`StardardCataline的setParent()`方法中直接抛出的异常

```
public interface Engine extends Container {

    public String getDefaultHost();

    public void setDefaultHost(String defaultHost);

    public String getJvmRoute();

    public void setJvmRoute(String jvmRouteId);

    public Service getService();

    public void setService(Service service);
}

public class StandardEngine extends ContainerBase implements Engine {

    // other code...

    public void setParent(Container container) {
        throw new IllegalArgumentException(sm.getString("standardEngine.notParent"));
    }
```

server.xml

```
<Engine name="Catalina" defaultHost="localhost">
  <Realm className="org.apache.catalina.realm.LockOutRealm">
    <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
           resourceName="UserDatabase"/>
  </Realm>

  <Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
    <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
           prefix="localhost_access_log" suffix=".txt"
           pattern="%h %l %u %t "%r" %s %b" />
  </Host>
```

### Host ###

`Host` 定义了一个虚拟主机，正所谓虚拟主机，当然是可以用来部署应用程序的，`Tomcat` 的 `Host` 也是如此。它在 `server.xml` 中定义了一个 `localhost` 的 `Host`，应用根目录在 `webapps下面`，默认是支持解压重新部署的。

### Context ###

![img\_0907\_01\_\_5.png][img_0907_01_5.png]

`Context` 代表一个独立的 `web` 应用，针对每个 `Context`，`tomcat` 都是使用不同的 `Classloader` 避免类冲突。如果我们希望使用一个自定义的目录作为部署路径的话，可以在 `server.xml` 中新增Context即可


## 代码模块简介 ##

### catalina包 ###

`Tomcat` 的核心模块，包括了 `HttpServlet`、`HttpSession` 的实现，以及各大组件的实现，这块的代码量是最多的，也是最复杂的一部分

### coyote包 ###

这块主要用于支持各种协议，比如`http1.1、http2.0、ajp`等，代码量较少

### tomcat包 ###

tomcat的基础包，包括了数据库连接池、`websocket` 实现、`tomcate` 的 `jni`、工具类。`org.apache.tomcat.util`包的代码量也不少,其中还包括了对 `jdk` 源码的扩展，比如线程池。

下图罗列各个模块的大致用途以及代码量  
![img\_0907\_01\_\_6.png][img_0907_01_6.png]

# 参考资料 #

[http://blog.csdn.net/jiaomingliang/article/details/47393141][http_blog.csdn.net_jiaomingliang_article_details_47393141]


[http_blog.csdn.net_jiaomingliang_article_details_47393141]: http://blog.csdn.net/jiaomingliang/article/details/47393141
[img_0907_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0907/01/7/img_0907_01__1.png
[img_0907_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0907/01/7/img_0907_01__2.png
[img_0907_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0907/01/7/img_0907_01__3.png
[img_0907_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0907/01/7/img_0907_01__4.png
[img_0907_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0907/01/7/img_0907_01__5.png
[img_0907_01_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0907/01/7/img_0907_01__6.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")