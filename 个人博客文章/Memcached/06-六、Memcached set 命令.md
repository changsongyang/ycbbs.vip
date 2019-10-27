`Memcached` `set` 命令用于将 **value(数据值)** 存储在指定的 **key(键)** 中

### 语法： ###

```
set key flags exptime bytes [noreply] 
value
```

#### 范例 ####

如果我们设置一个 `key` 为 `site` ,值为 www.ycbbs.vip, 过期时间为 `1000` 秒的键值对，那么命令一般如下

```
set site 0 1000 11
www.ycbbs.vip
```

参数说明

 *  **key** ： 键值 `key-value` 结构中的 `key`，用于查找缓存值。
 *  **flags** ：可以包括键值对的整型参数，客户机使用它存储关于键值对的额外信息
 *  **exptime** ：在缓存中保存键值对的时间长度（以秒为单位，`0` 表示永远）
 *  **bytes** ：在缓存中存储的字节数
 *  **noreply** ：可选， 该参数告知服务器不需要返回数据
 *  **value** ：存储的值（始终位于第二行）（可直接理解为`key-value`结构中的`value`）

### 返回值说明 ###

 *  如果数据设置成功，返回 **STORED**
 *  如果 `key` 已经存在，不管有没有过期都会更新数据，返回值为 **STORED**
 *  如果执行错误，返回 **CLIENT\_ERROR**

## 范例 ##

#### 1. 如果数据设置成功，返回 **STORED** ####

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

#### 2. 如果 key 已经存在 ####

如果 `key` 已经存在，不管有没有过期都会更新数据，返回值为 **STORED**

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
set site 0 1000 7
ycbbs.cn
STORED
get site
VALUE site 0 7
ycbbs.cn
END
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")