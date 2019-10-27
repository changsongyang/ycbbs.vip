作者：唐亚峰 | 出自：[https://blog.battcn.com](https://blog.battcn.com/)

`SpringBoot` 是为了简化 `Spring` 应用的创建、运行、调试、部署等一系列问题而诞生的产物，**`自动装配的特性让我们可以更好的关注业务本身而不是外部的XML配置，我们只需遵循规范，引入相关的依赖就可以轻易的搭建出一个 WEB 工程`**

目前 `Spring Boot` 支持较好的两款工具分别是 `flyway`、`liquibase`，支持 `sql script`，在初始化数据源之后执行指定的`脚本代码`或者`脚本文件`，本章基于 `Liquibase`…

## Liquibase

`LiquiBase` 是一个用于数据库重构和迁移的开源工具，通过 `changelog文件` 的形式记录数据库的变更，然后执行 `changelog文件` 中的修改，将数据库更新或回滚到一致的状态。

> 主要特点

*   支持几乎所有主流的数据库，**如MySQL、PostgreSQL、Oracle、Sql Server、DB2等**
*   支持多开发者的协作维护；
*   日志文件支持多种格式；**如XML、YAML、SON、SQL等**
*   支持多种运行方式；**如命令行、Spring 集成、Maven 插件、Gradle 插件等**

在平时开发中，无可避免测试库增加字段或者修改字段以及创建表之类的，环境切换的时候如果忘记修改数据库那么肯定会出现 `不可描述的事情` ，这个时候不妨考虑考虑`Liquibase`。

官方文档：[http://www.liquibase.org/documentation/index.html](http://www.liquibase.org/documentation/index.html)

## 本章目标

利用 `Spring Boot` 集成 `Liquibase`，避免因粗心大意导致环境迁移时缺少字段….

### 导入依赖

依赖 `spring-boot-starter-jdbc` 目的是为了让 `liquibase` 能够获得 `datasource` ，这里换成 `mybatis`、`hibernate` 等也是一样，主要偷懒不想写配置….

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.liquibase</groupId>
        <artifactId>liquibase-core</artifactId>
    </dependency>
</dependencies>
```

### 属性配置

只要依赖了 `liquibase-core` 默认可以不用做任何配置，但还是需要知道默认配置值是什么，这样方便定位和解决问题

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/chapter23?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&useSSL=false
spring.datasource.username=root
spring.datasource.password=root
# 只要依赖了 liquibase-core 默认可以不用做任何配置,但还是需要知道默认配置值是什么
# spring.liquibase.enabled=true
# spring.liquibase.change-log=classpath:/db/changelog/db.changelog-master.yaml
```

> 更多配置

*   **`spring.liquibase.change-log`** 配置文件的路径，默认值为 `classpath:/db/changelog/db.changelog-master.yaml`
*   **`spring.liquibase.check-change-log-location`** 检查 `change log`的位置是否存在，默认为true.
*   **`spring.liquibase.contexts`** 用逗号分隔的运行环境列表。
*   **`spring.liquibase.default-schema`** 默认数据库 `schema`
*   **`spring.liquibase.drop-first`** 是否先 `drop schema`（默认 `false`）
*   **`spring.liquibase.enabled`** 是否开启 `liquibase`（默认为 `true`）
*   **`spring.liquibase.password`** 数据库密码
*   **`spring.liquibase.url`** 要迁移的JDBC URL，如果没有指定的话，将使用配置的主数据源.
*   **`spring.liquibase.user`** 数据用户名
*   **`spring.liquibase.rollback-file`** 执行更新时写入回滚的 SQL文件

### db.changelog-master.yaml

```
databaseChangeLog:
  # 支持 yaml 格式的 SQL 语法
  - changeSet:
      id: 1
      author: Levin
      changes:
        - createTable:
            tableName: person
            columns:
              - column:
                  name: id
                  type: int
                  autoIncrement: true
                  constraints:
                    primaryKey: true
                    nullable: false
              - column:
                  name: first_name
                  type: varchar(255)
                  constraints:
                    nullable: false
              - column:
                  name: last_name
                  type: varchar(255)
                  constraints:
                    nullable: false

  - changeSet:
      id: 2
      author: Levin
      changes:
        - insert:
            tableName: person
            columns:
              - column:
                  name: first_name
                  value: Marcel
              - column:
                  name: last_name
                  value: Overdijk
  # 同时也支持依赖外部SQL文件（TODO 个人比较喜欢这种）
  - changeSet:
      id: 3
      author: Levin
      changes:
        - sqlFile:
            encoding: utf8
            path: classpath:db/changelog/sqlfile/test1.sql
```

### test1.sql

```
INSERT INTO `person` (`id`, `first_name`, `last_name`) VALUES ('2', '哈哈', '呵呵');
```

> 上面的`yaml`文件其实就是从下面的 `XML` 演变而来的，官方是支持 `xml`，`yaml`，`json` 三种格式，写法也比较简单

传送门（官方给出了三种写法格式，依样画葫芦就可以了）：[http://www.liquibase.org/documentation/changes/sql_file.html](http://www.liquibase.org/documentation/changes/sql_file.html)

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>  
<databaseChangeLog xmlns="http://www.liquibase.org/xml/ns/dbchangelog"   
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-2.0.xsd">  
    <changeSet id="1" author="Levin">
        <sqlFile path="classpath:db/changelog/changelog/test1.sql"/>
    </changeSet>
</databaseChangeLog>  
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
public class Chapter23Application {

    public static void main(String[] args) {


        SpringApplication.run(Chapter23Application.class, args);

    }
}
```

### 测试

1.启动`Chapter23Application.java`中的`main`方法

![img_1.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/yz/0709/24/img_1.png)

从日志中可以看到`Liquibase` 在帮我们执行定义好的SQL，如果是第一次启动，那么数据库会存在`databasechangelog` 和 `databasechangeloglock`两种表，从名字就可以看出，故而不作过多解释

![img_2.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/yz/0709/24/img_2.png)

2.SQL中的语法是创建一张`person`表和 两次 `INSERT` 操作

![img_3.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/yz/0709/24/img_3.png)

## 总结

目前很多大佬都写过关于 **`SpringBoot`** 的教程了，如有雷同，请多多包涵，本教程基于最新的 `spring-boot-starter-parent：2.0.3.RELEASE`编写，包括新版本的特性都会一起介绍…

## 说点什么

全文代码：[https://github.com/battcn/spring-boot2-learning/tree/master/chapter23](https://github.com/battcn/spring-boot2-learning/tree/master/chapter23 "https://github.com/battcn/spring-boot2-learning/tree/master/chapter23")


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")