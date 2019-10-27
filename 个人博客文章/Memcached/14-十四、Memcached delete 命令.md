`Memcached` `delete` 命令用于删除已存在的 `key`(键)

### 语法： ###

```
delete key [noreply]
```

 *  `key` ： 键值对 `key-value` 结构中的 `key`，用于查找缓存值
 *  `noreply`: 可选, 该参数告知服务器不需要返回数据

删除成功时返回 `DELETED`

### 范例 ###

我们先设置 site 的值为 `www.ycbbs.vip` ,存活时间 `1000` 秒，然后使用 `delete` 命令删除

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
get site
VALUE site 0 11
www.ycbbs.vip
END
delete site
DELETED
get site
END
```

删除一个不存在的键(`key`) 会返回 `NOT_FOUND` key不存在信息

```
flush_all
OK
delete site
NOT_FOUND
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")