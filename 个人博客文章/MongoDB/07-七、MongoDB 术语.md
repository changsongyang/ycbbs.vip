## 引言
不管我们学习什么知识，首要掌握的就是涉及到的相关术语。

`MongoDB` 涉及到的有关术语是：文档、集合、数据库

下表列出了 `MongoDB` 中的一些术语

<table> 
 <thead> 
  <tr> 
   <th align="left">SQL 术语</th> 
   <th align="left">MongoDB 术语</th> 
   <th align="left">解释</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">database</td> 
   <td align="left">database</td> 
   <td align="left">数据库</td> 
  </tr> 
  <tr> 
   <td align="left">table</td> 
   <td align="left">collection</td> 
   <td align="left">数据库表/集合</td> 
  </tr> 
  <tr> 
   <td align="left">row</td> 
   <td align="left">document</td> 
   <td align="left">数据记录行/文档</td> 
  </tr> 
  <tr> 
   <td align="left">column</td> 
   <td align="left">field</td> 
   <td align="left">数据字段/域</td> 
  </tr> 
  <tr> 
   <td align="left">index</td> 
   <td align="left">index</td> 
   <td align="left">索引</td> 
  </tr> 
  <tr> 
   <td align="left">table joins</td> 
   <td align="left"></td> 
   <td align="left">表连接,MongoDB不支持</td> 
  </tr> 
  <tr> 
   <td align="left">primary key</td> 
   <td align="left">primary key</td> 
   <td align="left">主键,MongoDB自动将_id字段设置为主键</td> 
  </tr> 
 </tbody> 
</table>

### 图示 ###

通过下面的图例，我们能更直观的了解 `MongoDB` 中 术语

![img\_1.png][img_1.png]

## 数据库 ##

一个 `MongoDB` 中可以建立多个数据库

`MongoDB` 默认的数据库是 "`test`"，该数据库存储在 `data` 目录中

`MongoDB` 每个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中

**"show dbs"** 命令可以显示所有数据的列表

```
$ mongo
MongoDB shell version v3.4.9
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.9
> show dbs;
admin       0.078GB
test        0.078GB
```

**db** 命令可以显示当前正在使用的数据库

```
> db
test
> 
```

**use** 命令可以切换数据库

下面我们使用 **use ycbbs** 切换到 ycbbs 数据库

```
> use ycbbs
switched to db ycbbs
> db
ycbbs
```

数据库通过名字来标识

数据库名可以是满足以下条件的任意 `UTF-8` 字符串

1、  不能是空字符串（"")
2、  不得含有 ' '（空格)、`.、$、/、\\和\\0` (空字符)
3、  应全部小写
4、  最多 `64` 字节

有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库

 *  **admin**
    
    从权限的角度来看，这是 "root" 数据库  
    要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限  
    一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器
 *  **local**
    
    这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
 *  **config**
    
    当使用 Mongo 分片技术时，config 数据库在内部使用，用于保存分片的相关信息

## 文档 ##

文档是一组键值(`key-value`)对( 即 `BSON` )

`MongoDB` 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 `MongoDB` 非常突出的特点

下面是一个简单的 `MongoDB` 文档范例

```
{"site":"www.ycbbs.vip", "name":"教程 "}
```

下表列出了 `RDBMS` 与 `MongoDB` 对应的术语

<table> 
 <thead> 
  <tr> 
   <th align="left">RDBMS</th> 
   <th align="left">MongoDB</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">数据库</td> 
   <td align="left">数据库</td> 
  </tr> 
  <tr> 
   <td align="left">表格</td> 
   <td align="left">集合</td> 
  </tr> 
  <tr> 
   <td align="left">行</td> 
   <td align="left">文档</td> 
  </tr> 
  <tr> 
   <td align="left">列</td> 
   <td align="left">字段</td> 
  </tr> 
  <tr> 
   <td align="left">表联合</td> 
   <td align="left">嵌入文档</td> 
  </tr> 
  <tr> 
   <td align="left">主键</td> 
   <td align="left">主键 (MongoDB 提供了 key 为 _id )</td> 
  </tr> 
  <tr> 
   <td align="left">数据库服务和客户端</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">Mysqld/Oracle</td> 
   <td align="left">mongod</td> 
  </tr> 
  <tr> 
   <td align="left">mysql/sqlplus</td> 
   <td align="left">mongo</td> 
  </tr> 
 </tbody> 
</table>

### 注意 ###

 *  文档中的键/值对是有序的
 *  文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)
 *  `MongoDB` 区分类型和大小写
 *  `MongoDB` 的文档不能有重复的键
 *  文档的键是字符串。除了少数例外情况，键可以使用任意 `UTF-8` 字符

### 文档键命名规范 ###

 *  键不能含有\\0 (空字符)。这个字符用来表示键的结尾
 *  .和$有特别的意义，只有在特定环境下才能使用
 *  以下划线"\_"开头的键是保留的(不是严格要求的)

## 集合 ##

集合就是 `MongoDB` 文档组，类似于 `RDBMS` （关系数据库管理系统：`Relational` `Database` `Management` `System`)中的表格。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

比如，我们可以将以下不同数据结构的文档插入到集合中：

```
{"site":"www.baidu.com"}
{"site":"www.tencent.com","name":"腾讯"}
{"site":"www.ycbbs.vip","name":"教程 ","num":5}
```

当第一个文档插入时，集合就会被创建。

### 合法的集合名 ###

 *  集合名不能是空字符串""。
 *  集合名不能含有\\0字符（空字符)，这个字符表示集合名的结尾。
 *  集合名不能以"`system`."开头，这是为系统集合保留的前缀。
 *  用户创建的集合名字不能含有保留字符 有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符 除非你要访问这种系统创建的集合，否则千万不要在名字里出现 `$`

```
db.col.findOne()
```

## capped collections ##

`Capped` `collections` 是固定大小的 `collection`

`Capped` `collections` 有很高的性能以及队列过期的特性(过期按照插入的顺序)

`Capped` `collections` 是高性能自动的维护对象的插入顺序

`Capped` `collections` 非常适合类似记录日志的功能

和标准的 `collection` 不同，`capped` `collection` 必须显示的创建，而且需要 指定一个 `collection` 的大小，单位是字节

`collection`的数据存储空间值提前分配的，要注意的是指定的存储大小包含了数据库的头信息

```
db.createCollection("mycoll", {capped:true, size:100000})
```

 *  在 `capped` `collection` 中，可以添加新的对象。
 *  能进行更新，然而，对象不会增加存储空间。如果增加，更新就会失败
 *  数据库不允许进行删除。使用`drop()`方法删除`collection`所有的行
 *  注意: 删除之后，你必须显式的重新创建这个`collection`
 *  在32bit机器中，`capped` `collection`最大存储为`1e9( 1X109)`个字节

## 元数据 ##

数据库的信息是存储在集合中

它们使用了系统的命名空间：

```
dbname.system.*
```

MongoDB 数据库中名字空间 <dbname>.system.\* 是包含多种系统信息的特殊集合

这些集合分别是

<table> 
 <thead> 
  <tr> 
   <th align="left">集合命名空间</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">dbname.system.namespaces</td> 
   <td align="left">列出所有名字空间</td> 
  </tr> 
  <tr> 
   <td align="left">dbname.system.indexes</td> 
   <td align="left">列出所有索引</td> 
  </tr> 
  <tr> 
   <td align="left">dbname.system.profile</td> 
   <td align="left">包含数据库概要(profile)信息</td> 
  </tr> 
  <tr> 
   <td align="left">dbname.system.users</td> 
   <td align="left">列出所有可访问数据库的用户</td> 
  </tr> 
  <tr> 
   <td align="left">dbname.local.sources</td> 
   <td align="left">包含复制对端（slave）的服务器信息和状态</td> 
  </tr> 
 </tbody> 
</table>

修改系统集合中的对象有如下限制:

1、  在 **system.indexes** 插入数据，可以创建索引 但除此之外该表信息是不可变的 (特殊的 dropIndex 命令将自动更新相关信息)
2、  **system.users** 是可修改的
3、  **system.profile** 是可删除的

## MongoDB 数据类型 ##

下表列出了 MongoDB 支持的数据类型

<table> 
 <thead> 
  <tr> 
   <th align="left">数据类型</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">String</td> 
   <td align="left">字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的</td> 
  </tr> 
  <tr> 
   <td align="left">Integer</td> 
   <td align="left">整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位</td> 
  </tr> 
  <tr> 
   <td align="left">Boolean</td> 
   <td align="left">布尔值。用于存储布尔值（真/假）</td> 
  </tr> 
  <tr> 
   <td align="left">Double</td> 
   <td align="left">双精度浮点值。用于存储浮点值</td> 
  </tr> 
  <tr> 
   <td align="left">Min/Max keys</td> 
   <td align="left">将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比</td> 
  </tr> 
  <tr> 
   <td align="left">Array</td> 
   <td align="left">用于将数组或列表或多个值存储为一个键</td> 
  </tr> 
  <tr> 
   <td align="left">Timestamp</td> 
   <td align="left">时间戳。记录文档修改或添加的具体时间</td> 
  </tr> 
  <tr> 
   <td align="left">Object</td> 
   <td align="left">用于内嵌文档</td> 
  </tr> 
  <tr> 
   <td align="left">Null</td> 
   <td align="left">用于创建空值</td> 
  </tr> 
  <tr> 
   <td align="left">Symbol</td> 
   <td align="left">符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言</td> 
  </tr> 
  <tr> 
   <td align="left">Date</td> 
   <td align="left">日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息</td> 
  </tr> 
  <tr> 
   <td align="left">Object ID</td> 
   <td align="left">对象 ID。用于创建文档的 ID</td> 
  </tr> 
  <tr> 
   <td align="left">Binary Data</td> 
   <td align="left">二进制数据。用于存储二进制数据</td> 
  </tr> 
  <tr> 
   <td align="left">Code</td> 
   <td align="left">代码类型。用于在文档中存储 JavaScript 代码</td> 
  </tr> 
  <tr> 
   <td align="left">Regular expression</td> 
   <td align="left">正则表达式类型。用于存储正则表达式</td> 
  </tr> 
 </tbody> 
</table>


[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/ycbbs-web/2019/08/0803/01/7/img_1.png



希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")