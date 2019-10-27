`MongoDB` **limit()** 方法用于读取指定数量的数据记录，

`limit()` 方法接受一个数字参数，该参数指定从 `MongoDB` 中读取的记录条数

### 语法 ###

`limit()` 方法语法如下：

```
> db.COLLECTION_NAME.find().limit(NUMBER)
```

### 范例 ###

使用以下命令添加范例所需要的数据

```
> db.lession.remove({});
WriteResult({ "nRemoved" : 0 })
```

```
> db.lession.insert({
   title: 'MongoDB 基础教程', 
   by_user: 'penglei',
   tags: ['MongoDB', 'database', 'NoSQL'],
   favorite: 100
});
WriteResult({ "nInserted" : 1 })
```

```
> db.lession.insert({
  title: 'NoSQL 基础教程', 
  by_user: 'penglei',
  tags: ['MongoDB', 'database', 'NoSQL'],
  favorite: 10
});
WriteResult({ "nInserted" : 1 })
```

```
> db.lession.insert({
   title: 'Neo4j 基础教程', 
   by_user: 'Neo4j',
   tags: ['Neo4j', 'database', 'NoSQL'],
   favorite: 750
});
WriteResult({ "nInserted" : 1 })
```

#### 现在，我们使用以下命令从文档中读取两条记录 ####

```
> db.lession.find({},{"title":1,_id:0}).limit(2)
{ "title" : "MongoDB 基础教程" }
{ "title" : "NoSQL 基础教程" }
> 
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")