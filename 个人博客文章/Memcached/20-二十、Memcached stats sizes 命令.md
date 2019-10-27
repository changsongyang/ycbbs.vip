`Memcached` `stats` `sizes` 命令用于显示所有 `item` 的 **大小** 和 **个数**

## 语法 ##

```
stats sizes
```

该命令返回两列，第一列是 `item` 的大小，第二列是 `item` 的个数

> Memcached 1.4.27 及以后的版本自动开启了 `stats sizes` 功能 这之前的版本需要在 Memcached 启动时带上 `-o track_sizes` 则来开启

## 范例 ##

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
set age 0 1000 2
28
STORED
stats sizes
STAT 96 2  # item 大小 96, 总共有2个key
END
```

`1、4.27` 之前的版本，如果启动 `memcached` 时没有设置 `-o track_sizes` 选项会是如下结果

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
set age 0 1000 2
28
STORED
stats sizes
STAT sizes_status disabled  # 提示 sizes_status 不可用
END
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")