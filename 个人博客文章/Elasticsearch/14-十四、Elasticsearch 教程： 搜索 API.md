本章节，我们要学习和介绍的 `API` 用于在 `Elasticsearch` 中搜索内容

我们可以通过以查询字符串作为参数或发送请求消息正文中的查询发送获取请求来进行搜索

## 多索引 ##

`Elasticsearch` 允许我们搜索所有的索引或某些特定索引中存在的文档

例如，如果我们要搜索昵称中包含 `飞` 的所有文档，可以发起下面这种请求

```
GET http://localhost:9200/_search?pretty&q=nickname:飞
```

返回响应

```
{
  "took" : 19,
  "timed_out" : false,
  "_shards" : {
    "total" : 15,
    "successful" : 15,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "user_admin",
        "_type" : "user",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "nickname" : "站长",
          "description" : "搜云库技术团队 ，教程 ",
          "street" : "东四十条",
          "city" : "Beijing",
          "state" : "Beijing",
          "zip" : "100007",
          "location" : [
            116.432727,
            39.937732
          ],
          "money" : 5201814,
          "tags" : [
            "PHP",
            "Python"
          ],
          "vitality" : "9.0"
        }
      }
    ]
  }
}
```

同样，我们可以将搜索范围限制在 `user` 和 `user_admin` 索引中

```
GET http://localhost:9200/user,user_admin/_search?pretty&q=nickname:飞
```

## 多类型 ##

我们还可以搜索所有类型或某种指定类型的索引中的所有文档

例如

```
GET http://localhost:9200/user_admin/_search?pretty&q=tags:Python
```

返回响应

```
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "user_admin",
        "_type" : "user",
        "_id" : "2",
        "_score" : 0.2876821,
        "_source" : {
          "nickname" : "雅少",
          "description" : "虚怀若谷",
          "street" : "四川大学",
          "city" : "Chengdu",
          "state" : "Sichuan",
          "zip" : "610044",
          "location" : [
            104.094537,
            30.640174
          ],
          "money" : 68023,
          "tags" : [
            "Python",
            "HTML"
          ],
          "vitality" : "7.8"
        }
      },
      {
        "_index" : "user_admin",
        "_type" : "user",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "nickname" : "站长",
          "description" : "搜云库技术团队 ，教程 ",
          "street" : "东四十条",
          "city" : "Beijing",
          "state" : "Beijing",
          "zip" : "100007",
          "location" : [
            116.432727,
            39.937732
          ],
          "money" : 5201814,
          "tags" : [
            "PHP",
            "Python"
          ],
          "vitality" : "9.0"
        }
      }
    ]
  }
}
```

## URI 搜索 ##

许多参数都可以放在 URL 中作为查询字符串参数传递

下表就列出了这些参数

<table> 
 <thead> 
  <tr> 
   <th align="left">参数</th> 
   <th align="left">默认值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">q</td> 
   <td align="left">""</td> 
   <td align="left">用于指定要查询的字符串</td> 
  </tr> 
  <tr> 
   <td align="left">lenient</td> 
   <td align="left">false</td> 
   <td align="left">设置为 true 即可以忽略格式化错误</td> 
  </tr> 
  <tr> 
   <td align="left">fields</td> 
   <td align="left"></td> 
   <td align="left">用于筛选返回结果的字段</td> 
  </tr> 
  <tr> 
   <td align="left">sort</td> 
   <td align="left"></td> 
   <td align="left">可以使用这个参数来对结果进行排序<br>可能值有：<code>fieldName</code>，<code>fieldName:asc</code> 或 <code>fieldname:desc</code></td> 
  </tr> 
  <tr> 
   <td align="left">timeout</td> 
   <td align="left"></td> 
   <td align="left">可以使用该参数来限制搜索的时间，如果超过了该时间则只返回该时间内匹配到的结果<br>默认为不限制</td> 
  </tr> 
  <tr> 
   <td align="left">terminate_after</td> 
   <td align="left"></td> 
   <td align="left">可以使用该参数来限制每个分片返回的文档数量，一旦达到则查询终止<br>默认情况下，不存在 <code>terminate_after</code> 参数</td> 
  </tr> 
  <tr> 
   <td align="left">from</td> 
   <td align="left">0</td> 
   <td align="left">返回结果的偏移量</td> 
  </tr> 
  <tr> 
   <td align="left">size</td> 
   <td align="left">10</td> 
   <td align="left">用于限制返回的结果数</td> 
  </tr> 
 </tbody> 
</table>

## 包含请求正文的搜索 ##

我们同样可以在请求正文中使用查询 `DSL` 来指定查询

而且前面我们已经使用了很多次

```
POST http://localhost:9200/user_admin/_search?pretty
```

请求正文

```
{
    "query":{
        "query_string":{
            "query":"语"
        }
    }
}
```

响应内容

```
{
  "took" : 19,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "user_admin",
        "_type" : "user",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "nickname" : "站长",
          "description" : "搜云库技术团队 ，教程 ",
          "street" : "东四十条",
          "city" : "Beijing",
          "state" : "Beijing",
          "zip" : "100007",
          "location" : [
            116.432727,
            39.937732
          ],
          "money" : 5201814,
          "tags" : [
            "PHP",
            "Python"
          ],
          "vitality" : "9.0"
        }
      }
    ]
  }
}
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")