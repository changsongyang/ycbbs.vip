上一章节中我们学习了使用 `MongoDB` 的引用来规范数据结构文档

### MongoDB 引用有两种 ###

1、  手动引用 ( `Manual` `References` )
2、  `DBRefs`

## DBRefs vs 手动引用 ##

假如有这样一个场景，在不同的集合中 ( `address\_home`, `address\_office`, `address\_mailing,` 等) 存储不同的地址 ( 住址，办公室地址，邮件地址等 )

这样，我们在调用不同地址时，也需要指定集合，一个文档从多个集合引用文档，我们应该使用 DBRefs

## DBRefs ##

`MongoDB` `DBRef` 格式

```
{ $ref : , $id : , $db :  }
```

三个字段表示的意义为：

 *  **$ref ：** 集合名称
 *  **$id ：** 引用的id
 *  **$db :**  数据库名称，可选参数

下面的范例中用户数据文档使用了 `DBRef`, 字段 `address`

```
{
   "_id" : ObjectId("59ee8457a0f7c7d445f864aa"),
   "name" : "penglei",
   "tel" : "13866668888",
   "birthday" : "11-11"
   "address": {
      "$ref": "address_home",
      "$id": ObjectId("59ee861ba0f7c7d445f864ac"),
      "$db": "ycbbs"
   }
}
```

**address** `DBRef` 字段指定了引用的地址文档是在 `address`\`_home` 集合下的 ycbbs 数据库，id 为 59ee861ba0f7c7d445f864ac

下面的代码通过指定 $ref 参数（`address`\`_home` 集合）来查找集合中指定 id 的用户地址信息

```
> var user = db.users.findOne({"name":"penglei"})
> var dbRef = user.address
> db[dbRef.$ref].findOne({"_id":(dbRef.$id)})
```

运行以上命令，返回了 `address`\`_home` 集合中的地址数据

```
{
   "_id" : ObjectId("59ee861ba0f7c7d445f864ac"),
   "pincode" : 100007,
   "user" : "penglei",
   "city" : "Pek",
   "state" : "China",
   "building" : "东城区东四君临天下大酒店 220220"
}
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")