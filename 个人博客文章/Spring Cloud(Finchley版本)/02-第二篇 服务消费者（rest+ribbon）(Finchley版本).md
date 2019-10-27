> 作者：方志朋
> 出处：[https://blog.csdn.net/forezp/column/info/15197](https://blog.csdn.net/forezp/column/info/15197)

* * *

在上一篇文章，讲了服务的注册和发现。在微服务架构中，业务都会被拆分成一个独立的服务，服务与服务的通讯是基于http restful的。Spring cloud有两种服务调用方式，一种是ribbon+restTemplate，另一种是feign。在这一篇文章首先讲解下基于ribbon+rest。

## 一、ribbon简介

> Ribbon is a client side load balancer which gives you a lot of control over the behaviour of HTTP and TCP clients. Feign already uses Ribbon, so if you are using @FeignClient then this section also applies.
> 
> -----摘自官网

ribbon是一个负载均衡客户端，可以很好的控制htt和tcp的一些行为。Feign默认集成了ribbon。

ribbon 已经默认实现了这些配置bean：

*   IClientConfig ribbonClientConfig: DefaultClientConfigImpl
*   IRule ribbonRule: ZoneAvoidanceRule
*   IPing ribbonPing: NoOpPing
*   ServerList ribbonServerList: ConfigurationBasedServerList
*   ServerListFilter ribbonServerListFilter: ZonePreferenceServerListFilter
*   ILoadBalancer ribbonLoadBalancer: ZoneAwareLoadBalancer

## 二、准备工作

这一篇文章基于上一篇文章的工程，启动eureka-server 工程；启动service-hi工程，它的端口为8762；将service-hi的配置文件的端口改为8763,并启动，这时你会发现：service-hi在eureka-server注册了2个实例，这就相当于一个小的集群。

如何在idea下启动多个实例，请参照这篇文章：
[https://blog.csdn.net/forezp/article/details/76408139](https://blog.csdn.net/forezp/article/details/76408139)

访问localhost:8761如图所示：
如何一个工程启动多个实例，请看这篇文章:[https://blog.csdn.net/forezp/article/details/76408139](https://blog.csdn.net/forezp/article/details/76408139)

![201908021002_1.png][img_1]

## 三、建一个服务消费者

重新新建一个spring-boot工程，取名为：service-ribbon;
在它的pom.xml继承了父pom文件，并引入了以下依赖：

```
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>com.forezp</groupId>
        <artifactId>service-ribbon</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <packaging>jar</packaging>

        <name>service-ribbon</name>
        <description>Demo project for Spring Boot</description>

        <parent>
            <groupId>com.forezp</groupId>
            <artifactId>sc-f-chapter2</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </parent>

        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
            </dependency>
        </dependencies>

    </project>

```

在工程的配置文件指定服务的注册中心地址为http://localhost:8761/eureka/，程序名称为 service-ribbon，程序端口为8764。配置文件application.yml如下：

```
    eureka:
      client:
        serviceUrl:
          defaultZone: http://localhost:8761/eureka/
    server:
      port: 8764
    spring:
      application:
        name: service-ribbon

```

在工程的启动类中,通过@EnableDiscoveryClient向服务中心注册；并且向程序的ioc注入一个bean: restTemplate;并通过@LoadBalanced注解表明这个restRemplate开启负载均衡的功能。

```
    @SpringBootApplication
    @EnableEurekaClient
    @EnableDiscoveryClient
    public class ServiceRibbonApplication {

        public static void main(String[] args) {
            SpringApplication.run( ServiceRibbonApplication.class, args );
        }

        @Bean
        @LoadBalanced
        RestTemplate restTemplate() {
            return new RestTemplate();
        }

    }

```

写一个测试类HelloService，通过之前注入ioc容器的restTemplate来消费service-hi服务的“/hi”接口，在这里我们直接用的程序名替代了具体的url地址，在ribbon中它会根据服务名来选择具体的服务实例，根据服务实例在请求的时候会用具体的url替换掉服务名，代码如下：

```
    @Service
    public class HelloService {

        @Autowired
        RestTemplate restTemplate;

        public String hiService(String name) {
            return restTemplate.getForObject("http://SERVICE-HI/hi?name="+name,String.class);
        }

    }

```

写一个controller，在controller中用调用HelloService 的方法，代码如下：

```
    @RestController
    public class HelloControler {

        @Autowired
        HelloService helloService;

        @GetMapping(value = "/hi")
        public String hi(@RequestParam String name) {
            return helloService.hiService( name );
        }
    }

```

在浏览器上多次访问http://localhost:8764/hi?name=forezp，浏览器交替显示：

> hi forezp,i am from port:8762
> 
> hi forezp,i am from port:8763

这说明当我们通过调用restTemplate.getForObject(“[http://SERVICE-HI/hi?name=](http://service-hi/hi?name=)”+name,String.class)方法时，已经做了负载均衡，访问了不同的端口的服务实例。

## 四、此时的架构

![201908021002_2.png][img_2]

*   一个服务注册中心，eureka server,端口为8761
*   service-hi工程跑了两个实例，端口分别为8762,8763，分别向服务注册中心注册
*   sercvice-ribbon端口为8764,向服务注册中心注册
*   当sercvice-ribbon通过restTemplate调用service-hi的hi接口时，因为用ribbon进行了负载均衡，会轮流的调用service-hi：8762和8763 两个端口的hi接口；

源码下载：[https://github.com/forezp/SpringCloudLearning/tree/master/sc-f-chapter2](https://github.com/forezp/SpringCloudLearning/tree/master/sc-f-chapter2)

## 五、参考资料

本文参考了以下：

[http://blog.csdn.net/forezp/article/details/69788938](http://blog.csdn.net/forezp/article/details/69788938)

[http://cloud.spring.io/spring-cloud-static/Finchley.RELEASE/single/spring-cloud.html](http://cloud.spring.io/spring-cloud-static/Finchley.RELEASE/single/spring-cloud.html)


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")

[img_1]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Spring%20Cloud(Finchley%E7%89%88%E6%9C%AC)/images/02-1.png
[img_2]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Spring%20Cloud(Finchley%E7%89%88%E6%9C%AC)/images/02-2.png