上一章节我们有提到 `Elasticsearch` 的 `RESTful` `API` 使用 `HTTP` 作为传输协议，使用 `JSON` 作为数据交换格式。但是，在 `API` 细节方面，`Elasticsearch` 还有一些简单的约定。

当然了，我们也可以说是 `API` 接口规范

## API ##

`API`，是 `Application` `Programming` `Interface` 的简写，中文译为 **应用程序编程接口**。

`API` 是对一组函数调用或用于访问特定 Web 应用程序中的软件组件的其他编程指令，例如，微博 ( `Weibo` ) `API` 可帮助开发人员通过访问来自 **微博** 的数据或其它功能来创建应用程序，比如实现使用微博账号登录

因为 `Elasticsearch` 的 `RESTful` `API` 使用 `HTTP` 作为传输协议，使用 `JSON` 作为数据交换格式，所以它对如何使用 `HTTP` 发起请求和返回响应，如何使用 `JSON` 制定了一些规范

## 多个索引 ##

有些时候，我们可能需要一次访问 `API` 的过程中在多个位置或所有可用数据中进行搜索，这时候就可能用到一个或多个索引。

`Elasticsearch` `RESTful` `API` 中的大部分操作 ( 主要是搜索 ) ，都适用于一个或多个索引的情况

`Elasticsearch` 提供了有许多不同的符号用于执行多个索引中的操作

### 逗号分隔符 ( , ) ###

逗号分隔符用于分隔不同的索引

#### 范例 ####

下面发起的请求用于在 `index1` ，`index2` ，`index3` 索引中查询所有包含 `any_string` 数据

```
POST http://localhost:9200/index1,index2,index3/_search?pretty
```

请求正文

```
{
"query":{
  "query_string":{
	 "query":"any_string"
  }
}
}
```

### `_all` 关键字用于所有的索引 ###

如果要在所有的索引中查询，则可以使用 `_all` 关键字

#### 范例 ####

下面发起的请求用于查询当前服务器上所有索引中包含 `any_string` 的数据

```
POST http://localhost:9200/_all/_search?pretty
```

请求正文

```
{
"query":{
  "query_string":{
	 "query":"any_string"
  }
}
}
```

### 通配符 `*` , `+` , `–` ###

通配符 `*` , `+` , `–` 可以单独使用，也可以组合使用

1、通配符 `*` 用于匹配任意字符串

例如下面的请求在所有以 `user` 开头的索引中查找包含 `ABCD` 的数据

```
POST http://localhost:9200/user*/_search?pretty
```

请求正文

```
{
   "query":{
	  "query_string":{
		 "query":"ABCD"
	  }
   }
}
```

返回结果如下

```
{
	"took": 29,
	"timed_out": false,
	"_shards": {
		"total": 10,
		"successful": 10,
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": 0,
		"max_score": null,
		"hits": []
	}
}
```
2、通配符 `*` 用于排除某些索引

例如下面的请求在所有以 `user` 开头但不包括 `user_admin` 的索引中查找包含 `ABCD` 的数据

```
POST http://localhost:9200/user*,-user_admin/_search?pretty
```

请求正文

```
{
   "query":{
	  "query_string":{
		 "query":"ABCD"
	  }
   }
}
```

返回结果如下

```
{
	"took": 28,
	"timed_out": false,
	"_shards": {
		"total": 5,
		"successful": 5,
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": 0,
		"max_score": null,
		"hits": []
	}
}
```

除了上面说的这些符号，`Elasticsearch` 还提供了一些 `URI` 查询字符串用于指定某些条件

1、**ignore\_unavailable**

如果 `URL` 中指定的一个或多个索引不存在，则不会抛出错误且不会停止操作，例如存在 `user` 索引但不存在 `php` 的索引的情况

```
POST http://localhost:9200/user*,php/_search
```

请求正文

```
{
   "query":{
	  "query_string":{
		 "query":"CBSE"
	  }
   }
}
```

因为没有添加 `ignore_unavailable` 参数，所以抛出了错误

```
{
	"error": {
		"root_cause": [
			{
				"type": "index_not_found_exception",
				"reason": "no such index",
				"resource.type": "index_or_alias",
				"resource.id": "php",
				"index_uuid": "_na_",
				"index": "php"
			}
		],
		"type": "index_not_found_exception",
		"reason": "no such index",
		"resource.type": "index_or_alias",
		"resource.id": "php",
		"index_uuid": "_na_",
		"index": "php"
	},
	"status": 404
}
```

但如果使用下面的请求

```
POST http://localhost:9200/user*,php/_search?ignore_unavailable=true&pretty
```

请求正文

```
{
   "query":{
	  "query_string":{
		 "query":"CBSE"
	  }
   }
}
```

则不会抛出任何错误

```
{
	"took": 164,
	"timed_out": false,
	"_shards": {
		"total": 10,
		"successful": 10,
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": 0,
		"max_score": null,
		"hits": []
	}
}
```
2、**allow\_no\_indices**

如果一个通配符没有任何匹配的索引存在，如果设置 `allow_no_indices` 为 `true` ，则可以阻止抛出错误

例如，假如 `Elasticsearch` 集群中不存在 `PHP*` 的索引，那么下面的查询请求将不会报错

```
POST http://localhost:9200/PHP*/_search?allow_no_indices=true&pretty
```

请求正文

```
{
   "query":{
	  "match_all":{}
   }
}
```

返回响应 ( 没有报错 )

```
{
	"took": 0,
	"timed_out": false,
	"_shards": {
		"total": 0,
		"successful": 0,
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": 0,
		"max_score": 0,
		"hits": []
	}
}
```
3、**expand\_wildcards**

`expand_wildcards` 用于设置是否需要扩展通配符以打开索引或封闭索引或两者

该参数的指可以是

<table> 
 <thead> 
  <tr> 
   <th align="left">值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">open</td> 
   <td align="left">打开索引</td> 
  </tr> 
  <tr> 
   <td align="left">close</td> 
   <td align="left">关闭索引</td> 
  </tr> 
  <tr> 
   <td align="left">none</td> 
   <td align="left">未定义，使用系统默认</td> 
  </tr> 
  <tr> 
   <td align="left">all</td> 
   <td align="left">全部打开</td> 
  </tr> 
 </tbody> 
</table>

例如我们先使用下面的请求关闭索引 **user**

```
POST http://localhost:9200/user/_close?pretty
```

返回响应

```
{
	"acknowledged": true
}
```

然后使用下面的请求来查询

```
POST http://localhost:9200/user*/_search?expand_wildcards=closed
```

请求正文

```
{
   "query":{
	  "match_all":{}
   }
}
```

返回响应如下

```
{
	"error": {
		"root_cause": [
			{
				"type": "index_closed_exception",
				"reason": "closed",
				"index_uuid": "VYLD0ybxRLeVB_KsJ8ZjDw",
				"index": "user"
			}
		],
		"type": "index_closed_exception",
		"reason": "closed",
		"index_uuid": "VYLD0ybxRLeVB_KsJ8ZjDw",
		"index": "user"
	},
	"status": 400
}
```

## 索引名称中的日期数学运算支持 ##

Elasticsearch 提供了根据日期和时间搜索索引的功能

当然，我们需要使用特定的日期和时间格式，例如，`accountdetail-2015.12.30` 索引用于存储 `2015` 年 `12` 月 `30` 日的银行账户详细信息

Elasticsearch 还允许执行数学运算以获取特定日期或日期和时间范围的详细信息

例如下面的语句用于格式化索引中的日期

```
<static_name{date_math_expr{date_format|time_zone}}>
http://localhost:9200/<accountdetail-{now-2d{YYYY.MM.dd|utc}}>/_search
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
<td align="left">static_name</td> 
<td align="left">是表达式的一部分，它在每个包含了日期的索引 ( 如帐户详细信息 ) 中保持不变</td> 
</tr> 
<tr> 
<td align="left">date_math_expr</td> 
<td align="left">动态地确定日期和时间的数学表达式，如 <code>now-2d</code></td> 
</tr> 
<tr> 
<td align="left">date_format</td> 
<td align="left">生成的日期的格式，如 <code>YYYY.MM.dd</code><br>例如今天的日期是 2018 年 6 月 27 日，那么 <code><accountdetail-{now-2d{YYYY.MM.dd}}></code> 将返回 <code>accountdetail-2018.06.25</code></td> 
</tr> 
</tbody> 
</table>

相关的范例如下

<table> 
<thead> 
<tr> 
<th align="left">表达式</th> 
<th align="left">结果</th> 
</tr> 
</thead> 
<tbody> 
<tr> 
<td align="left"><accountdetail-{now-d}></td> 
<td align="left">accountdetail-2018.06.26</td> 
</tr> 
<tr> 
<td align="left"><accountdetail-{now-M}></td> 
<td align="left">accountdetail-2018.05.27</td> 
</tr> 
<tr> 
<td align="left"><accountdetail-{now{YYYY.MM}}></td> 
<td align="left">accountdetail-2018.06</td> 
</tr> 
</tbody> 
</table>

## 响应参数 ##

接下来我们将介绍一些 `Elasticsearch` 中常用的用于定制响应的参数

1、**pretty=true**

通过添加 `pretty=true` 参数，返回的 `JSON` 数据将具有良好的格式以方便阅读

例如下面的请求

```
POST http://localhost:9200/user_admin/_search?pretty=true
```

请求正文

```
{
   "query":{
	  "match_all":{}
   }
}
```

返回的响应为

```
{
	"took": 13,
	"timed_out": false,
	"_shards": {
		"total": 5,
		"successful": 5,
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": 3,
		"max_score": 1,
		"hits": [
			{
				"_index": "user_admin",
				"_type": "user",
				"_id": "2",
				"_score": 1,
				"_source": {
					"nickname": "雅少",
					"description": "虚怀若谷",
					"street": "四川大学",
					"city": "Chengdu",
					"state": "Sichuan",
					"zip": "610044",
					"location": [
						104.094537,
						30.640174
					],
					"money": 68023,
					"tags": [
						"Python",
						"HTML"
					],
					"vitality": "7.8"
				}
			},
			{
				"_index": "user_admin",
				"_type": "user",
				"_id": "1",
				"_score": 1,
				"_source": {
					"nickname": "站长",
					"description": "创业是的天赋是天生的，而我偏偏是后生的",
					"street": "东四十条",
					"city": "Beijing",
					"state": "Beijing",
					"zip": "100007",
					"location": [
						116.432727,
						39.937732
					],
					"money": 5201314,
					"tags": [
						"PHP",
						"Python"
					],
					"vitality": "9.0"
				}
			},
			{
				"_index": "user_admin",
				"_type": "user",
				"_id": "3",
				"_score": 1,
				"_source": {
					"nickname": "歌者",
					"description": "程序设计也是设计，研发新菜也是研发",
					"street": "五道口",
					"city": "Beijing",
					"state": "Beijing",
					"zip": "100083",
					"location": [
						116.346346,
						39.999333
					],
					"money": 71128,
					"tags": [
						"Java",
						"Scala"
					],
					"vitality": "6.9"
				}
			}
		]
	}
}
```
2、**human=true**

通过添加 **human=true** 参数，可以格式化返回结果中的数字，默认值为 `false`

假如返回的响应存在某个字段 `distance_kilometer`，保存的值为 `20000`

1.  如果 **human=true**，那么将返回 `distance_kilometer=20KM`
2.  如果 **human=false**，那么将返回 `distance_meter=20000`
3、**filter\_path**

该参数用于筛选响应中的字段

例如下面的请求

```
POST http://localhost:9200/user/_search?filter_path=hits.total
```

请求正文

```
{
   "query":{
	  "match_all":{}
   }
}
```

那么返回结果就只会包含下面的内容

```
{
	"hits": {
		"total": 3
	}
}
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")