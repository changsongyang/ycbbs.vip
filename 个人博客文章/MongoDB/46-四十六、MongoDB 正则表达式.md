`MongoDB` 使用 **$regex** 操作符来设置匹配字符串的正则表达式

正则表达式是使用单个字符串来描述、匹配一系列符合某个句法规则的字符串。

许多程序设计语言都支持利用正则表达式进行字符串操作。

`MongoDB` 使用 `PCRE (Perl Compatible Regular Expression)` 作为正则表达式语言

不同于全文检索，我们使用正则表达式不需要做任何配置。

假设有以下 **posts** 集合的文档结构，该文档包含了文章内容和标签：

```
{
   "post_text": "enjoy the mongodb articles on ycbbs",
   "tags": [
      "mongodb",
      "ycbbs"
   ]
}
```

## 使用正则表达式 ##

以下命令使用正则表达式查找包含 `ycbbs` 字符串的文章：

```
>db.posts.find({post_text:{$regex:"ycbbs"}})
```

以上查询也可以写为：

```
>db.posts.find({post_text:/ycbbs/})
```

## 不区分大小写的正则表达式 ##

如果检索需要不区分大小写，我们可以设置` $options` 为 `$i`。

以下命令将查找不区分大小写的字符串 `ycbbs`：

```
>db.posts.find({post_text:{$regex:"ycbbs",$options:"$i"}})
```

集合中会返回所有包含字符串 `ycbbs` 的数据，且不区分大小写：

```
{
   "_id" : ObjectId("53493d37d852429c10000004"),
   "post_text" : "hey! this is my post on  ycbbs", 
   "tags" : [ "ycbbs" ]
}
```

## 数组元素使用正则表达式 ##

我们还可以在数组字段中使用正则表达式来查找内容。 这在标签的实现上非常有用，如果你需要查找包含以 `run` 开头的标签数据(`ru` 或 `run` 或 `ycbbs`)， 你可以使用以下代码：

```
>db.posts.find({tags:{$regex:"run"}})
```

## 优化正则表达式查询 ##

 *  如果你的文档中字段设置了索引，那么使用索引相比于正则表达式匹配查找所有的数据查询速度更快。
 *  如果正则表达式是前缀表达式，所有匹配的数据将以指定的前缀字符串为开始。例如： 如果正则表达式为 **^tut** ，查询语句将查找以 `tut` 为开头的字符串。

**这里面使用正则表达式有两点需要注意：**

正则表达式中使用变量。一定要使用 `eval` 将组合的字符串进行转换，不能直接将字符串拼接后传入给表达式。否则没有报错信息，只是结果为空！实例如下：

```
var name=eval("/" + 变量值key +"/i");
```

以下是模糊查询包含 `title` 关键词, 且不区分大小写:

```
title:eval("/"+title+"/i")    // 等同于 title:{$regex:title,$Option:"$i"}
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")