`Elasticsearch` 使用 `Java` 语言开发，使用 `Elastic` 开源协议，实时分布式开源的全文搜索和分析引擎。

`Elasticsearch` 通过 `RESTful` `Web` 服务接口访问，并使用 `JSON` 文档来存储数据。

`Elasticsearch` 使用 Java 作为开发语言，但却能够跨平台运行，可以使用者非常快速的探索大量的数据

`Elasticsearch` 由 **Shay Banon** 于 `2010` 年创建，当前的最新版本是 `2018` 年 `6` 月 13 日发布的 `6.3.0`

![img\_1.png][img_1.png]

### Elasticsearch 官网 ###

Elasticsearch 的官方网址是： [https://www.elastic.co/products/elasticsearch][https_www.elastic.co_products_elasticsearch]

Elasticsearch 的下载地址是： [https://www.elastic.co/downloads/elasticsearch][https_www.elastic.co_downloads_elasticsearch]

### Elasticsearch 背后的公司 ###

`Elasticsearch` 发布不久后就受到开发的持续关注，成为 `Github` 上最受欢迎的项目之一。

之后，一家主营 `Elasticsearch` 的公司成立了，它们一边提供商业支持一边开发新功能，但它们保证， `Elasticsearch` 将永远开源且对所有人可用

## Elasticsearch 的特性 ##

1、安装方便：没有其他依赖，下载后安装非常方便；只用修改几个参数就可以搭建起来一个集群
2、`JSON`：输入/输出格式为 `JSON`，意味着不需要定义 `Schema`，快捷方便
3、`RESTful`：基本所有操作 ( 索引、查询、甚至是配置 ) 都可以通过 `HTTP` 接口进行
4、分布式：节点对外表现对等（每个节点都可以用来做入口）；加入节点自动均衡
5、多租户：可根据不同的用途分索引；可以同时操作多个索引
6、支持超大数据： 可以扩展到 PB 级的结构化和非结构化数据
7、`Elasticsearch` 可以用来替代 `MongoDB` 和 `RavenDB` 等文档数据库

说了这么多，其实只有一个优点： 如果想要创建自己的搜索引擎，那么 `Elasticsearch` 是唯一的不可替代的方案，没有之一

## Elasticsearch 优点 ##

```html
1、Elasticsearch 使用 Java 作为开发语言，所以可以运行在任何平台上
2、Elasticsearch 是实时的，换句话说，就是在添加了文档后就可以立刻搜索到刚刚添加的文档
3、Elasticsearch 是分布式的，可以轻松扩展并集成到任何大型组织中
4、通过使用 Elasticsearch 中的网关概念，轻松创建完整备份
5、与 Apache Solr 相比，Elasticsearch 中处理多租户非常容易
6、Elasticsearch 使用 JSON 对象作为响应，这是当下最流行的数据交换格式
7、Elasticsearch 几乎支持所有文档类型，但不能渲染的文本除外，例如二进制数据
```

## Elasticsearch 缺点 ##

1、与 `Apache` `Solr` 不同，`Elasticsearch` 在处理请求和响应数据方面不支持多种数据格式，也就是说只支持 JSON
    
    Apache Solr 支持多种数据传输格式，比如 CSV，XML 和 JSON 格式
2、`Elasticsearch` 也存在脑裂 ( `Split-Brain` ) 的情况，但发生的可能性很少
    
    Split-Brain，中文一般翻译为 **脑裂**， 脑裂问题就是产生了两个 leader,导致集群行为不一致


[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/2/img_1.png
[https_www.elastic.co_products_elasticsearch]: https://www.elastic.co/products/elasticsearch
[https_www.elastic.co_downloads_elasticsearch]: https://www.elastic.co/downloads/elasticsearch


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")