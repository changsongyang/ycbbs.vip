`Memcached` `append` 命令用于向已存在 **key(键)** 的 **value(数据值)** 后面追加数据

## 语法 ##

```
append key flags exptime bytes [noreply]
value
```

### 参数说明 ###

 *  **key：** 键值 `key-value` 结构中的 `key`
 *  **flags** ：可以包括键值对的整型参数，客户机使用它存储关于键值对的额外信息
 *  **exptime** ：在缓存中保存键值对的时间长度（以秒为单位，`0` 表示永远）
 *  **bytes** ：在缓存中存储的字节数
 *  **noreply** ：可选， 该参数告知服务器不需要返回数据
 *  **value** ：存储的值（始终位于第二行）（可直接理解为`key-value`结构中的`value`）

### 返回值说明 ###

 *  如果数据添加成功，返回 **STORED**
 *  如果键不存在，返回 **NOT\_STORED**
 *  如果执行错误，返回 **CLIENT\_ERROR**

### 范例 ###

#### 1. 如果数据添加成功，返回 **STORED** ####

```
flush_all
OK
set greeting 0 1000 6
hello,
STORED
append greeting 0 1000 11
www.ycbbs.vip
STORED
get greeting
VALUE greeting 0 17
hello,www.ycbbs.vip
END
```

#### 2. 如果键不存在，返回 **NOT\_STORED** ####

```
flush_all
OK
append greeting 0 1000 11
www.ycbbs.vip
NOT_STORED
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")