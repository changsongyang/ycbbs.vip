作者：唐亚峰 | 出自：[https://blog.battcn.com](https://blog.battcn.com/)

`SpringBoot` 是为了简化 `Spring` 应用的创建、运行、调试、部署等一系列问题而诞生的产物，**`自动装配的特性让我们可以更好的关注业务本身而不是外部的XML配置，我们只需遵循规范，引入相关的依赖就可以轻易的搭建出一个 WEB 工程`**

[上一篇](https://www.ycbbs.vip/?p=1236)介绍了 `SpringBoot` 由来及构建方式，通过第一章的教程我们对 `SpringBoot` 不在感到陌生，可以发现 `SpringBoot` 虽然干掉了 XML 但未做到 **零配置**，它体现出了一种 **约定优于配置，也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。

** 一般情况下默认的配置足够满足日常开发所需，但在特殊的情况下，我们往往需要用到**自定义属性配置、自定义文件配置、多环境配置、外部命令引导**等一系列功能。不用担心，这些 `SpringBoot` 都替我们考虑好了，我们只需要遵循它的规则配置即可

> 准备前提

为了让 `SpringBoot` 更好的生成数据，我们需要添加如下依赖（**该依赖可以不添加，但是在 IDEA 和 STS 中不会有属性提示，没有提示的配置就跟你用记事本写代码一样苦逼，出个问题弄哭你去**），该依赖只会在编译时调用，所以不用担心会对生产造成影响…

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

## 自定义属性配置

在 `application.properties` 写入如下配置内容

```
my1.age=22
my1.name=battcn
```

其次定义 `MyProperties1.java` 文件，用来映射我们在 `application.properties` 中的内容，这样一来我们就可以通过操作对象的方式来获得配置文件的内容了

```
package com.battcn.properties;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * @author Levin
 * @since 2018/4/23 0023
 */
@Component
@ConfigurationProperties(prefix = "my1")
public class MyProperties1 {

    private int age;
    private String name;
    // 省略 get set

    @Override
    public String toString() {
        return "MyProperties1{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}
```

接下来就是定义我们的 `PropertiesController` 用来注入 `MyProperties1` 测试我们编写的代码，**值得注意的是 `Spring4.x` 以后，推荐使用构造函数的形式注入属性…**

```
import com.battcn.properties.MyProperties1;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author Levin
 * @since 2018/4/23 0023
 */
@RequestMapping("/properties")
@RestController
public class PropertiesController {

    private static final Logger log = LoggerFactory.getLogger(PropertiesController.class);

    private final MyProperties1 myProperties1;

    @Autowired
    public PropertiesController(MyProperties1 myProperties1) {
        this.myProperties1 = myProperties1;
    }

    @GetMapping("/1")
    public MyProperties1 myProperties1() {
        log.info("=================================================================================================");
        log.info(myProperties1.toString());
        log.info("=================================================================================================");
        return myProperties1;
    }
}
```

**打开浏览器，输入如下地址：** [http://localhost:8080/properties/1](http://localhost:8080/properties/1)，观察控制台，监听到如下内容则表示程序正确

```
2018-04-23 15:51:43.145  INFO 15352 --- [nio-8080-exec-2] c.b.controller.PropertiesController      : =================================================================================================
2018-04-23 15:51:43.145  INFO 15352 --- [nio-8080-exec-2] c.b.controller.PropertiesController      : MyProperties1{age=22, name='battcn'}
2018-04-23 15:51:43.145  INFO 15352 --- [nio-8080-exec-2] c.b.controller.PropertiesController      : =================================================================================================
```

## 自定义文件配置

定义一个名为 `my2.properties` 的资源文件，**自定义配置文件的命名不强制 `application` 开头**

```
my2.age=22
my2.name=Levin
my2.email=1837307557@qq.com
```

其次定义 `MyProperties2.java` 文件，用来映射我们在 `my2.properties` 中的内容。

```
package com.battcn.properties;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

/**
 * @author Levin
 * @since 2018/4/23 0023
 */
@Component
@PropertySource("classpath:my2.properties")
@ConfigurationProperties(prefix = "my2")
public class MyProperties2 {

    private int age;
    private String name;
    private String email;
    // 省略 get set 

    @Override
    public String toString() {
        return "MyProperties2{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

接下来在 `PropertiesController` 用来注入 `MyProperties2` 测试我们编写的代码

```
@GetMapping("/2")
public MyProperties2 myProperties2() {
    log.info("=================================================================================================");
    log.info(myProperties2.toString());
    log.info("=================================================================================================");
    return myProperties2;
}
```

**打开浏览器，输入如下地址：** [http://localhost:8080/properties/2](http://localhost:8080/properties/2)，观察控制台，监听到如下内容则表示程序正确

```
2018-04-23 15:59:45.395  INFO 6232 --- [nio-8080-exec-4] c.b.controller.PropertiesController      : =================================================================================================
2018-04-23 15:59:45.395  INFO 6232 --- [nio-8080-exec-4] c.b.controller.PropertiesController      : MyProperties2{age=22, name='Levin', email='1837307557@qq.com'}
2018-04-23 15:59:45.395  INFO 6232 --- [nio-8080-exec-4] c.b.controller.PropertiesController      : =================================================================================================
```

## 多环境化配置

在真实的应用中，常常会有多个环境（**如：开发，测试，生产等**），不同的环境数据库连接都不一样，这个时候就需要用到`spring.profile.active` 的强大功能了，它的格式为 `application-{profile}.properties`，这里的 `application` 为前缀不能改，`{profile}` 是我们自己定义的。

创建 `application-dev.properties`、`application-test.properties`、`application-prod.properties`，内容分别如下

> application-dev.properties

```
server.servlet.context-path=/dev
```

> application-test.properties

```
server.servlet.context-path=/test
```

> application-prod.properties

```
server.servlet.context-path=/prod
```

在 `application.properties` 配置文件中写入 `spring.profiles.active=dev`，这个时候我们在次访问 [http://localhost:8080/properties/1](http://localhost:8080/properties/1) 就没用处了，因为我们设置了它的`context-path=/dev`，所以新的路径就是 [http://localhost:8080/dev/properties/1](http://localhost:8080/dev/properties/1) ，**由此可以看出来我们激活不同的配置读取的属性值是不一样的**

## 外部命令引导

前面三种方式都是基于配置文件层面的，那么有没有办法外部引导呢，假设这样的场景，我们对已经开发完成的代码打包发布，期间在测试环境测试通过了，那么即可发布上生产，这个时候是修改`application.properties`的配置方便还是直接在**命令参数配置**方便呢，毫无疑问是后者更有说服力。

**默认情况下，`SpringApplication` 会将命令行选项参数（即：–property，如–server.port=9000）添加到Environment，命令行属性始终优先于其他属性源。**

> 如何测试？

*   进入到项目目录，此处以我本地目录为主：`F:/battcn-workspace/spring-boot2-learning/chapter2`
*   然后打开 `cmd` 程序，不会在当前目录打开 `cmd` 的请自行百度，输入：`mvn package`
*   打包完毕后进入到：`F:/battcn-workspace/spring-boot2-learning/chapter2/target` 目录中去，我们可以发现一个名为**chapter2-0.0.1-SNAPSHOT.jar** 的包
*   接着在打开 `cmd` 程序，输入：`java -jar chapter2-0.0.1-SNAPSHOT.jar --spring.profiles.active=test --my1.age=32`。仔细观察**`spring.profiles.active=test`、`my1.age=32`** 这俩配置的键值是不是似曾相识（不认识的请从开头认真阅读）
*   最后输入测试地址：[http://localhost:8080/test/properties/1](http://localhost:8080/test/properties/1) 我们可以发现返回的JSON变成了 **`{"age":32,"name":"battcn"}`** 表示正确

## 总结

*   掌握`@ConfigurationProperties`、`@PropertySource` 等注解的用法及作用
*   掌握编写自定义配置
*   掌握外部命令引导配置的方式

目前很多大佬都写过关于 **`SpringBoot`** 的教程了，如有雷同，请多多包涵，本教程基于最新的 `spring-boot-starter-parent：2.0.1.RELEASE`编写，包括新版本的特性都会一起介绍…

## 说点什么

全文代码：[https://github.com/battcn/spring-boot2-learning/tree/master/chapter2](https://github.com/battcn/spring-boot2-learning/tree/master/chapter2)


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")