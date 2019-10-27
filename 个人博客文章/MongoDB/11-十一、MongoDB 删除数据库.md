MongoDB 中可以使用 `db.dropDatabase()` 命令来删除当前数据库

```
db.dropDatabase()
```

默认数据库为 `test`，可以使用 `db` 命令查看当前数据库名

## 范例 ##

接下来我们将演示如何删除 `ycbbs.cn` 数据库

1、  首先查看所有数据库
    
```
    > show dbs
    local   0.000GB
    ycbbs.cn    0.000GB
    test    0.000GB
```
2、  接下来切换到数据库 ycbbs.cn
    
```
    > use ycbbs.cn
    switched to db ycbbs.cn
    >
```
3、  执行删除命令
    
```
    > db.dropDatabase()
    { "dropped" : "ycbbs.cn", "ok" : 1 }
```
4、  最后通过 `show dbs` 命令数据库是否删除成功
    
```
    > show dbs
    local  0.000GB
    test   0.000GB
    >
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")