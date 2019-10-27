`Memcached add` 命令用于将 **value(数据值)** 存储在指定的 **key(键)** 中

## 语法 ##

```
add key flags exptime bytes [noreply]
value
```

### 参数说明 ###

 *  **key** ： 键值 `key-value` 结构中的 `key`
 *  **flags** ：可以包括键值对的整型参数，客户机使用它存储关于键值对的额外信息
 *  **exptime** ：在缓存中保存键值对的时间长度（以秒为单位，0 表示永远）
 *  **bytes** ：在缓存中存储的字节数
 *  **noreply** ：可选，该参数告知服务器不需要返回数据
 *  **value** ：存储的值（始终位于第二行）（可直接理解为`key-value`结构中的`value`）

### 返回值说明 ###

 *  如果数据添加成功，返回 **STORED**
 *  如果 `key` 已经存在，且没过期，则不会更新数据，返回值为 **NOT\_STORED**
 *  如果 `key` 已经存在，但已经过期，那么替换成功，返回值为 **STORED**
 *  如果执行错误，返回 **CLIENT\_ERROR**

## 范例 ##

#### 1. 如果数据添加成功，返回 **STORED** ####

```
add site 0 1000 11
www.ycbbs.vip
STORED
```

#### 2. 如果 key 已经存在，且没过期 ####

如果 key 已经存在，且没过期，则不会更新数据，返回值为 **NOT\_STORED**

```
flush_all
OK
add site 0 1000 11
www.ycbbs.vip
STORED
get site  # key 已经存在
VALUE site 0 11
www.ycbbs.vip
END
add site 0 1000 11
csd.ycbbs.vcn
NOT_STORED  #  返回 NOT_STORED
get site
VALUE site 0 11
www.ycbbs.vip  # 数据没更新
END
```

#### 3. 如果 key 已经存在，但已经过期 ####

如果 `key` 已经存在，但已经过期，那么替换成功，返回值为 `STORED`

```
flush_all
OK
add site 0 10 11
www.ycbbs.vip
STORED
get site
VALUE site 0 11
www.ycbbs.vip
END
add site 0 11 7  # 等待10秒后再添加
ycbbs.vcn
STORED   # 返回成功
get site
VALUE site 0 7
ycbbs.vcn  # 数据已经更新
END
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")