作者：唐亚峰 | 出自：[https://blog.battcn.com](https://blog.battcn.com/)

`SpringBoot` 是为了简化 `Spring` 应用的创建、运行、调试、部署等一系列问题而诞生的产物，**`自动装配的特性让我们可以更好的关注业务本身而不是外部的XML配置，我们只需遵循规范，引入相关的依赖就可以轻易的搭建出一个 WEB 工程`**

前面两章中详细介绍了[数据有效性校验的重要性](http://blog.battcn.com/2018/06/05/springboot/v2-other-validate1/)、[自定有数据有效性校验注解](http://blog.battcn.com/2018/06/06/springboot/v2-other-validate2/) 本章也是`轻松搞定数据验证`的最后一篇， **`一起来揭开神秘的分组验证`**

## 分组验证

有的时候，我们对一个实体类需要有多中验证方式，在不同的情况下使用不同验证方式，比如说对于一个实体类来的 id 来说，新增的时候是不需要的，对于更新时是必须的，这个时候你是选择写一个实体类呢还是写两个呢？

在[自定有数据有效性校验注解](http://blog.battcn.com/2018/06/06/springboot/v2-other-validate2/)中介绍到注解需要有一个 `groups` 属性，这个属性的作用又是什么呢？

接下来就让我们看看如何用一个验证类实现多个接口之间不同规则的验证…

## 本章目标

利用一个验证类实现多个接口之间不同规则的验证…

## 具体代码

非常简单…

### 导入依赖

在 `pom.xml` 中添加上 `spring-boot-starter-web` 的依赖即可

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 分组验证器

定义一个验证组，里面写上不同的空接口类即可

```
package com.battcn.groups;

/**
 * 验证组
 *
 * @author Levin
 * @since 2018/6/7 0007
 */
public class Groups {

    public interface Update {

    }

    public interface Default {

    }
}
```

### 实体类

**`groups` 属性的作用就让 `@Validated` 注解只验证与自身 value 属性相匹配的字段，可多个，只要满足就会去纳入验证范围；**我们都知道针对新增的数据我们并不需要验证 ID 是否存在，我们只在做修改操作的时候需要用到，因此这里将 ID 字段归纳到 `Groups.Update.class` 中去，而其它字段是不论新增还是修改都需要用到所以归纳到 `Groups.Default.class` 中…

```
package com.battcn.pojo;

import com.battcn.groups.Groups;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import java.math.BigDecimal;

/**
 * @author Levin
 * @since 2018/6/7 0005
 */
public class Book {

    @NotNull(message = "id 不能为空", groups = Groups.Update.class)
    private Integer id;
    @NotBlank(message = "name 不允许为空", groups = Groups.Default.class)
    private String name;
    @NotNull(message = "price 不允许为空", groups = Groups.Default.class)
    private BigDecimal price;

    // 省略 GET SET ...
}
```

### 控制层

创建一个 `ValidateController` 类，然后定义好 `insert`、`update` 俩个方法，比由于 `insert` 方法并不关心 ID 字段，所以这里 `@Validated` 的 value 属性写成 `Groups.Default.class` 就可以了；而 `update` 方法需要去验证 ID 是否为空，所以此处 `@Validated` 注解的 value 属性值就要写成 `Groups.Default.class, Groups.Update.class`；代表只要是这分组下的都需要进行数据有效性校验操作…

```
package com.battcn.controller;

import com.battcn.groups.Groups;
import com.battcn.pojo.Book;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * 参数校验
 *
 * @author Levin
 * @since 2018/6/06 0031
 */

@RestController
public class ValidateController {

    @GetMapping("/insert")
    public String insert(@Validated(value = Groups.Default.class) Book book) {
        return "insert";
    }

    @GetMapping("/update")
    public String update(@Validated(value = {Groups.Default.class, Groups.Update.class}) Book book) {
        return "update";
    }
}
```

### 主函数

```
package com.battcn;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author Levin
 */
@SpringBootApplication
public class Chapter20Application {

    public static void main(String[] args) {

        SpringApplication.run(Chapter20Application.class, args);

    }
}
```

### 测试

完成准备事项后，启动 `Chapter20Application` 自行测试即可，测试手段相信大伙都不陌生了，如 `浏览器`、`postman`、`junit`、`swagger`，此处基于 `postman`，如果你觉得自带的异常信息不够友好，那么配上[一起来学SpringBoot | 第十八篇：轻松搞定全局异常](https://www.ycbbs.vip/?p=1281) 可以轻松搞定…

> insert 接口

![img_1.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/yz/0709/21/img_1.png)

> update 接口

![img_2.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/yz/0709/21/img_2.png)

**两个接口参数内容一致，都缺少 id 字段 ，但 `insert` 是成功的，而 `update` 接口中提示了 id 不能为空；
测试结果表明，符合我们的预期要求。**

## 总结

目前很多大佬都写过关于 **`SpringBoot`** 的教程了，如有雷同，请多多包涵，本教程基于最新的 `spring-boot-starter-parent：2.0.2.RELEASE`编写，包括新版本的特性都会一起介绍…

## 说点什么

全文代码：[https://github.com/battcn/spring-boot2-learning/tree/master/chapter20](https://github.com/battcn/spring-boot2-learning/tree/master/chapter20 "https://github.com/battcn/spring-boot2-learning/tree/master/chapter20")


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")