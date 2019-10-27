`MongoDB` **use** 命令可以用来创建数据库

### 语法 ###

`MongoDB` 创建数据库的语法格式如下：

```
use DATABASE_NAME
```

如果数据库不存在，则创建数据库，否则切换到指定数据库

### 范例 ###

以下实例我们创建了数据库 `ycbbs`:

```
> use ycbbs
switched to db ycbbs
> db
ycbbs
>
```

如果想查看所有数据库，可以使用 **show dbs** 命令

```
> show dbs
local   0.078GB
test  0.078GB
> 
```

咦，没创建成功 ？刚创建的数据库 *ycbbs* 并不在数据库的列表中啊

`MongoDB` 默认不会显示没有数据的数据库，要显示 `ycbbs` 数据库

需要向 `ycbbs` 数据库插入一些数据

```
> db.ycbbs.insert({"name":"教程 ","site":"https://www.ycbbs.vip/"})
WriteResult({ "nInserted" : 1 })
> show dbs
local   0.078GB
ycbbs  0.078GB
test    0.078GB
>
```

`MongoDB` 中默认的数据库为 `test`，如果你没有创建新的数据库，集合将存放在 `test` 数据库中

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")