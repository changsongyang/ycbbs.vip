`MongoDB` **sort()** 方法可以用来对集合数据进行排序输出

`MongoDB` `sort()` 方法可以通过参数指定排序的字段，并使用 `1` 和 `-1` 来指定排序的方式，其中 `1` 为升序排列，而`-1`是用于降序排列

### 语法 ###

`sort()` 方法语法如下

```
> db.COLLECTION_NAME.find().sort({KEY:1})
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

现在，我们按字段 `favorite` 的降序排列输出数据

```
> db.lession.find({},{"title":1,_id:0,'favorite':1}).sort({"favorite":-1})
{ "title" : "Neo4j 基础教程", "favorite" : 750 }
{ "title" : "MongoDB 基础教程", "favorite" : 100 }
{ "title" : "NoSQL 基础教程", "favorite" : 10 }
>
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")