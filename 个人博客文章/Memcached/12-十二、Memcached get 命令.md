`Memcached` `get` 命令获取存储在 **键(key)** 中的 **数据值(value)**

## 语法 ##

```
get key
```

多个 `key` 使用空格隔开

```
get key1 key2 key3
```

 *  `key` ： 键值对 `key-value` 结构中的 `key`，用于查找缓存值

如果 key 不存在，则返回空

### 范例 ###

#### 1. get 单个 key ####

这个范例中，我们设置键 `site` 的值为 www.ycbbs.vip 存活时间设置为 1000 秒

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
```

#### 2. get 多个 key ####

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
set age 0 1000 2
28
STORED
get site age
VALUE site 0 11
www.ycbbs.vip
VALUE age 0 2
28
END
```

#### 3. get 一个不存在的 key 返回空 ####

```
flush_all
OK
get site
END
```

#### 4. get 多个key，有一个key不存在，则那个key 返回空 ####

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
get site age
VALUE site 0 11
www.ycbbs.vip
END
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")