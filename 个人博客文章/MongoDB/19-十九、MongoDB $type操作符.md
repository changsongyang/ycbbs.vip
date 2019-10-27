`MongoDB` **$type** 操作符是基于 `BSON` 类型来检索集合中匹配的数据类型，并返回结果

### 下表是 MongoDB 中的数据类型 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">类型</th> 
   <th align="left">数字</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">Double</td> 
   <td align="left">1</td> 
  </tr> 
  <tr> 
   <td align="left">String</td> 
   <td align="left">2</td> 
  </tr> 
  <tr> 
   <td align="left">Object</td> 
   <td align="left">3</td> 
  </tr> 
  <tr> 
   <td align="left">Array</td> 
   <td align="left">4</td> 
  </tr> 
  <tr> 
   <td align="left">Binary data</td> 
   <td align="left">5</td> 
  </tr> 
  <tr> 
   <td align="left">Undefined<sup>已废弃</sup></td> 
   <td align="left">6</td> 
  </tr> 
  <tr> 
   <td align="left">Object id</td> 
   <td align="left">7</td> 
  </tr> 
  <tr> 
   <td align="left">Boolean</td> 
   <td align="left">8</td> 
  </tr> 
  <tr> 
   <td align="left">Date</td> 
   <td align="left">9</td> 
  </tr> 
  <tr> 
   <td align="left">Null</td> 
   <td align="left">10</td> 
  </tr> 
  <tr> 
   <td align="left">Regular Expression</td> 
   <td align="left">11</td> 
  </tr> 
  <tr> 
   <td align="left">JavaScript</td> 
   <td align="left">13</td> 
  </tr> 
  <tr> 
   <td align="left">Symbol</td> 
   <td align="left">14</td> 
  </tr> 
  <tr> 
   <td align="left">JavaScript (with scope)</td> 
   <td align="left">15</td> 
  </tr> 
  <tr> 
   <td align="left">32-bit integer</td> 
   <td align="left">16</td> 
  </tr> 
  <tr> 
   <td align="left">Timestamp</td> 
   <td align="left">17</td> 
  </tr> 
  <tr> 
   <td align="left">64-bit integer</td> 
   <td align="left">18</td> 
  </tr> 
  <tr> 
   <td align="left">Min key</td> 
   <td align="left">255</td> 
  </tr> 
  <tr> 
   <td align="left">Max key</td> 
   <td align="left">127</td> 
  </tr> 
 </tbody> 
</table>

### 范例数据 ###

使用以下命令向 数据库 `ycbbs` 中的 **lession** 集合中插入数据

```
> db.lession.remove({});
```

```
> db.lession.insert({
    title: 'PHP 基础教程', 
    description: 'PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言',
    by: 'penglei',
    url: 'https://www.ycbbs.vip/l/penglei/php/php-basic-index.html',
    tags: ['php','php7'],
    favorite: 2000
})
```

```
> db.lession.insert({title: 'Java 基础教程', 
    description: 'Java 可以用来开发 JAVA WEB 和 AndRoid APP 运用程序',
    by: 'penglei',
    url: 'https://www.ycbbs.vip/l/penglei/java/java-basic-index.html',
    tags: ['java','android'],
    favorite: 3000
})
```

```
> db.lession.insert({title: 'MongoDB 基础教程', 
    description: 'MongoDB 是一个 Nosql 数据库',
    by: 'penglei',
    url: 'https://www.ycbbs.vip/l/penglei/mongodb/mongodb-basic-index.html',
    tags: ['mongodb'],
    favorite: 1000
})
```

可以使用 **find()** 方法查看数据

```
> db.lession.find()
{ "_id" : ObjectId("59ede9b2a0f7c7d445f864a6"), "title" : "PHP 基础教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/php/php-basic-index.html", "tags" : [ "php", "php7" ], "favorite" : 2000 }
{ "_id" : ObjectId("59ede9b9a0f7c7d445f864a7"), "title" : "Java 基础教程", "description" : "Java 可以用来开发 JAVA WEB 和 AndRoid APP 运用程序", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/java/java-basic-index.html", "tags" : [ "java", "android" ], "favorite" : 3000 }
{ "_id" : ObjectId("59edea6da0f7c7d445f864a9"), "title" : "MongoDB 基础教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/mongodb/mongodb-basic-index.html", "tags" : [ "mongodb" ], "favorite" : 1000 }
```

### MongoDB $type 操作符范例 ###

下面的命令可以获取 "`lession`" 集合中 `title` 为 `String` 的数据

```
> db.lession.find({"title" : {$type : 2}},{"title":1,"favorite":1,_id:0})
```

输出结果为：

```
{ "title" : "PHP 基础教程", "favorite" : 2000 }
{ "title" : "Java 基础教程", "favorite" : 3000 }
{ "title" : "MongoDB 基础教程", "favorite" : 1000 }
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
