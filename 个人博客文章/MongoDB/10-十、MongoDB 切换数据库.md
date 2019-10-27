`MongoDB` **use** 命令可以用来切换数据库

### 语法 ###

`MongoDB` 切换数据库的语法格式如下：

```
use DATABASE_NAME
```

切换到指定数据库，如果数据库不存在，则创建数据库

### 范例 ###

这个范例我们从 `test` 数据库切换到 `ycbbs.cn` 数据库

```
> db
test
> use ycbbs.cn
switched to db ycbbs.cn
> db
ycbbs.cn
> 
```

MongoDB 中默认的数据库为 `test`，如果没有创建新的数据库，集合将存放在 `test` 数据库中


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")