覆盖查询是：所有的查询字段是索引的一部分、所有的查询返回字段在同一个索引中

因为所有出现在查询中的字段是索引的一部分， `MongoDB` 无需在整个数据文档中检索匹配查询条件和返回使用相同索引的查询结果

因为索引存在于 `RAM` 中，从索引中获取数据比通过扫描文档读取数据要快得多

## 使用覆盖索引查询 ##

为了测试盖索引查询，假设我们有以下 `users` 集合

```
{
    "_id" : ObjectId("59ee8a8ba0f7c7d445f864af"),
    "tel" : "13888886666",
    "birthday" : "11-11",
    "sex" : "M",
    "name" : "Ro penglei",
    "user_name" : "penglei"
}
```

我们在 `users` 集合中创建联合索引，字段为 `sex` 和 `user\_name`

```
> db.users.ensureIndex({sex:1,user_name:1})
{
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
}
```

现在，该索引会覆盖以下查询

```
> db.users.find({sex:"M"},{user_name:1,_id:0})
{ "user_name" : "penglei" }
```

也就是说，对于上述查询，`MongoDB` 不会去数据库文件中查找  
相反，它会从索引中提取数据，这是非常快速的数据查询

由于我们的索引中不包括 `\_id` 字段，`\_id`在查询中会默认返回，我们可以在 `MongoDB` 的查询结果集中排除它

下面的范例没有排除`\_id`，查询就不会被覆盖：

```
>db.users.find({sex:"M"},{user_name:1})
```

最后，如果是以下的查询，不能使用覆盖索引查询：

1、  所有索引字段是一个数组
2、  所有索引字段是一个子文档

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")