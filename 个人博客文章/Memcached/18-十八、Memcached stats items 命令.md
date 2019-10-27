`Memcached` `stats items` 命令用于显示各个 **slab** 中 **item** 的数目和存储时长(最后一次访问距离现在的秒数)

### 语法 ###

```
stats items
```

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
stats items
STAT items:1:number 2
STAT items:1:age 477
STAT items:1:evicted 0
STAT items:1:evicted_nonzero 0
STAT items:1:evicted_time 0
STAT items:1:outofmemory 0
STAT items:1:tailrepairs 0
STAT items:1:reclaimed 0
STAT items:1:expired_unfetched 0
STAT items:1:evicted_unfetched 0
STAT items:1:crawler_reclaimed 0
STAT items:1:crawler_items_checked 0
STAT items:1:lrutail_reflocked 0
END
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
