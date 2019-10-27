有多种方式可以和 `Elasticsearch` 进行交互，它们的唯一区别就是我们是否使用了 `Java`

## Java API ##

`Elasticsearch` 为 `Java` 用户提供了两种内置客户端

1、节点客户端 ( `node` `client` )
    
    节点客户端以无数据节点 (none data node) 身份加入集群，换言之，它自己不存储任何数据，但是它知道数据在集群中的具体位置，并且能够直接转发请求到对应的节点上
2、传输客户端 ( Transport client )
    
    传输客户端更轻量，且能够发送请求到远程集群
    
    它自己不加入集群，只是简单转发请求给集群中的节点

两个 `Java` 客户端都通过 `9300` 端口和 `Elasticsearch` 传输协议 ( `Elasticsearch` `Transport` `Protocol` ) 与 `Elasticsearch` 进行交互

其实，集群中的节点之间也通过 `9300` 端口进行通信，如果此端口未开放，我们的节点将不能组成集群

需要注意的是，`Java` 客户端所在的 `Elasticsearch` 版本必须与集群中其他节点一致，否则，它们可能互相无法识别

> ### 注意 ###
> 
> 即使你用的是 Java 语言，我们也不推荐你使用 Java API。而是使用下面要介绍的 RESTFul API

## RESTful API ##

`Elasticsearch` 的 `RESTful` `API` 使用 `HTTP` 作为传输协议，使用 `JSON` 作为数据交换格式

所有的语言都可以使用 `RESTful` `API`，通过 `9200` 端口的与 `Elasticsearch` 进行通信

我们可以使用自己最喜欢的 `WEB` 客户端，事实上，`Unix` 安装课程中所见，我们甚至可以使用 `curl` 命令与 `Elasticsearch` 通信

> #### 提示 ####
> 
> Elasticsearch 官方提供了多种程序语言的客户端: Groovy，Javascript， .NET，PHP，Perl，Python，以及 Ruby，还有很多由社区提供的客户端和插件，你可以访问 [文档][Link 1] 查看详情

因为是基于 `HTTP` 的 `RESTFul` `API`，所以向 `Elasticsearch` 发出的请求的组成部分与其它普通的 `HTTP` 请求是一样的

```
curl -X<VERB> -HContent-Type:application/json '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

<table> 
 <thead> 
  <tr> 
   <th align="left">参数</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">VERB</td> 
   <td align="left">HTTP 请求方法：GET, POST, PUT, HEAD, DELETE</td> 
  </tr> 
  <tr> 
   <td align="left">Content-Type:application/json</td> 
   <td align="left">可选，用于指定请求正文的数据格式为 JSON</td> 
  </tr> 
  <tr> 
   <td align="left">PROTOCOL</td> 
   <td align="left">http 或者 https 协议，只有在 Elasticsearch 前有 https 代理的时候可用</td> 
  </tr> 
  <tr> 
   <td align="left">HOST</td> 
   <td align="left">Elasticsearch 集群中的任何一个节点的主机名，如果是在本地的节点，则是 <code>localhost</code></td> 
  </tr> 
  <tr> 
   <td align="left">PORT</td> 
   <td align="left">Elasticsearch HTTP 服务所在的端口，默认为9200</td> 
  </tr> 
  <tr> 
   <td align="left">PATH</td> 
   <td align="left">API 路径 ( 例如 <code>_count</code> 将返回集群中文档的数量<br>PATH 可以包含多个组件，例如 <code>_cluster/stats</code> 或者 <code>_nodes/stats/jvm</code></td> 
  </tr> 
  <tr> 
   <td align="left">QUERY_STRING</td> 
   <td align="left">一些可选的查询请求参数，例如 <code>?pretty</code>参数将使请求返回更加美观易读的 <code>JSON</code> 数据</td> 
  </tr> 
  <tr> 
   <td align="left">BODY</td> 
   <td align="left">一个 <code>JSON</code> 格式的请求主体，如果请求需要的话</td> 
  </tr> 
 </tbody> 
</table>

我们举几个例子来演示下这些参数的使用

例如，为了计算集群中的文档数量，我们可以这样做

```
curl -XGET -HContent-Type:application/json 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}
'
```

`Elasticsearch` 返回一个类似 `200 OK` 的 HTTP 状态码和 JSON 格式的响应主体 ( 除了 `HEAD` 请求 )

所以上面的请求会得到如下的 `JSON` 格式的响应主体

```
{
  "count" : 0,
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "skipped" : 0,
    "failed" : 0
  }
}
```

我们看不到 `HTTP` 头是因为我们没有让 `curl` 显示它们，如果要显示，可以在 `curl` 命令后跟 `-i` 参数

```
curl -i -XGET 'http://localhost:9200/'
```

## 约定 ##

本教程的剩余部分，我们简写 `curl` 请求中重复的部分，例如主机名和端口，还有 `curl` 命令本身

例如一个完整的请求

```
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}'
```

我们将简写为以下格式

```
GET /_count
{
    "query": {
        "match_all": {}
    }
}
```

[Link 1]: http://www.elasticsearch.org/guide/

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")