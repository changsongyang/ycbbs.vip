`Memcached` `incr` 命令用于对已存在的 `key`(键) 的数字值进行自增操作

## 语法 ##

```
incr key increment_value
```

 *  `key` ： 键值 `key-value` 结构中的 `key`，用于查找缓存值
 *  `increment_value` ： 需要增加的数值

`incr` 命令操作的数据必须是十进制的`32`位无符号整数

### 返回值 ###

 *  如果 `key` 不存在返回 `NOT_FOUND`
 *  如果 `key` 的值不为数字，则返回 `CLIENT_ERROR`
 *  其他错误返回 `ERROR`

### 范例 ###

下面的范例，我们使用 `countdown` 作为 `key`，初始值为 `5`，之后进行加 `5` 操作

```
set countdown 0 1000 1
5
STORED
incr countdown 5
10
get countdown
VALUE countdown 0 2
10
END
```

### 如果 key 不存在 ###

如果 `key` 不存在，那么返回 **NOT\_FOUND** `key` 不存在错误

```
flush_all
OK
incr age 5
NOT_FOUND
```

### 如果 key 的值不为数字 ###

如果 `key` 的值不为数字,那么返回 **CLIENT\_ERROR** 自增值不是数字错误

```
flush_all
OK
incr age 5
NOT_FOUND
set site 0 1000 11
www.ycbbs.vip
STORED
incr site 5
CLIENT_ERROR cannot increment or decrement non-numeric value
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")