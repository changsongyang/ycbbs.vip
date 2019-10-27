`MongoDB` 条件操作符用于比较两个表达式并从 `MongoDB` 集合中获取数据

### MongoDB中条件操作符有 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">操作符</th> 
   <th align="left">描述</th> 
   <th align="left">MongoDB 表示方法</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">></td> 
   <td align="left">大于</td> 
   <td align="left">$gt</td> 
  </tr> 
  <tr> 
   <td align="left"><</td> 
   <td align="left">小于</td> 
   <td align="left">$lt</td> 
  </tr> 
  <tr> 
   <td align="left">>=</td> 
   <td align="left">大于等于</td> 
   <td align="left">$gte</td> 
  </tr> 
  <tr> 
   <td align="left"><=</td> 
   <td align="left">小于等于</td> 
   <td align="left">$lte</td> 
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

使用`find()`命令查看数据

```
> db.lession.find()
{ "_id" : ObjectId("59ede9b2a0f7c7d445f864a6"), "title" : "PHP 基础教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/php/php-basic-index.html", "tags" : [ "php", "php7" ], "favorite" : 2000 }
{ "_id" : ObjectId("59ede9b9a0f7c7d445f864a7"), "title" : "Java 基础教程", "description" : "Java 可以用来开发 JAVA WEB 和 AndRoid APP 运用程序", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/java/java-basic-index.html", "tags" : [ "java", "android" ], "favorite" : 3000 }
{ "_id" : ObjectId("59edea6da0f7c7d445f864a9"), "title" : "MongoDB 基础教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/mongodb/mongodb-basic-index.html", "tags" : [ "mongodb" ], "favorite" : 1000 }
```

### MongoDB 操作符范例 ###

1、   **MongoDB ( > ) 大于操作符 $gt**
    
    获取 "lession" 集合中 "favorite" 大于 1000 的数据
    
```
    db.lession.find({"favorite" : {$gt : 1000}})
```
    
    类似于 SQL 语句
    
```
    SELECT * FROM lession WHERE favorite > 1000;
```
    
    输出结果
    
```
    { "_id" : ObjectId("59ede9b2a0f7c7d445f864a6"), "title" : "PHP 基础教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/php/php-basic-index.html", "tags" : [ "php", "php7" ], "favorite" : 2000 }
    { "_id" : ObjectId("59ede9b9a0f7c7d445f864a7"), "title" : "Java 基础教程", "description" : "Java 可以用来开发 JAVA WEB 和 AndRoid APP 运用程序", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/java/java-basic-index.html", "tags" : [ "java", "android" ], "favorite" : 3000 }
```
2、   **MongoDB（ >= ）大于等于操作符 $gte**
    
    获取 "lession" 集合中 "favorite" 大于等于 1000 的数据
    
```
    db.lession.find({favorite : {$gte : 1000}})
```
    
    类似于 SQL 语句
    
```
    SELECT * FROM lession WHERE favorite >= 1000;
```
    
    输出结果
    
```
    { "_id" : ObjectId("59ede9b2a0f7c7d445f864a6"), "title" : "PHP 基础教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/php/php-basic-index.html", "tags" : [ "php", "php7" ], "favorite" : 2000 }
    { "_id" : ObjectId("59ede9b9a0f7c7d445f864a7"), "title" : "Java 基础教程", "description" : "Java 可以用来开发 JAVA WEB 和 AndRoid APP 运用程序", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/java/java-basic-index.html", "tags" : [ "java", "android" ], "favorite" : 3000 }
    { "_id" : ObjectId("59edea6da0f7c7d445f864a9"), "title" : "MongoDB 基础教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/mongodb/mongodb-basic-index.html", "tags" : [ "mongodb" ], "favorite" : 1000 }
```
3、  **MongoDB ( < ) 小于操作符 - $lt**
    
    获取 "lession" 集合中 "favorite" 小于 2000 的数据
    
```
    db.lession.find({"favorite" : {$lt : 2000}})
```
    
    类似于 SQL 语句
    
```
    SELECT * FROM lession WHERE favorite < 2000;
```
    
    输出结果
    
```
    { "_id" : ObjectId("59edea6da0f7c7d445f864a9"), "title" : "MongoDB 基础教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/mongodb/mongodb-basic-index.html", "tags" : [ "mongodb" ], "favorite" : 1000 }
```
4、  `\*\*MongoDB ( <= )` 小于操作符 `- $lte`
    
    获取 "lession" 集合中 "favorite" 小于等于 2000 的数据
    
```
    db.lession.find({"favorite" : {$lte : 2000}})
```
    
    类似于 SQL 语句
    
```
    SELECT * FROM lession WHERE favorite <= 2000;
```
    
    输出结果
    
```
    { "_id" : ObjectId("59ede9b2a0f7c7d445f864a6"), "title" : "PHP 基础教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/php/php-basic-index.html", "tags" : [ "php", "php7" ], "favorite" : 2000 }
    { "_id" : ObjectId("59edea6da0f7c7d445f864a9"), "title" : "MongoDB 基础教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/mongodb/mongodb-basic-index.html", "tags" : [ "mongodb" ], "favorite" : 1000 }
```
5、  **MongoDB 使用 (<) 和 (>) 查询 - $lt 和 $gt**
    
    获取 "lession" 集合中 "favorite" 小于 3000 且大于 1000 的数据
    
```
    db.lession.find({"favorite" : {$lt : 3000,$gt:1000}})
```
    
    类似于 SQL 语句
    
```
    SELECT * FROM lession WHERE favorite < 3000 and favorite > 1000;
```
    
    输出结果
    
```
    { "_id" : ObjectId("59ede9b2a0f7c7d445f864a6"), "title" : "PHP 基础教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言", "by" : "penglei", "url" : "https://www.ycbbs.vip/l/penglei/php/php-basic-index.html", "tags" : [ "php", "php7" ], "favorite" : 2000 }
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")