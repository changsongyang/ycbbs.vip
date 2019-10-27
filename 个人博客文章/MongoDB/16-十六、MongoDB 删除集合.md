文章永久连接：[https://www.ycbbs.vip/?p=3663](https://www.ycbbs.vip/?p=3663)
`MongoDB` 中可以使用 `db.collection_name.drop()` 命令来删除当前数据库中的某个集合

```
db.collection_name.drop()
```

## 范例 ##

下面的命令演示了如何删除 `ycbbs` 数据库中的集合 `site`

```
> use ycbbs
switched to db ycbbs
> show tables
site
> db.site.drop()
true
> show tables
>
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")