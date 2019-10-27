`MongoDB` **skip()** 方法用于跳过指定数量的数据

`skip()` 方法接受一个数字参数作为跳过的记录条数

### 语法 ###

`skip()` 方法语法格式如下

```
> db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```

`skip()` 方法默认参数为 `0` 表示不跳过任何行

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

现在我们使用下面的命令显示第二条文档数据

```
> db.lession.find({},{"title":1,_id:0}).limit(1).skip(1)
{ "title" : "NoSQL 基础教程" }
>
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")