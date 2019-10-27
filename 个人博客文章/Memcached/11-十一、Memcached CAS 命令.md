`CAS` （`Check-And-Set` 或 `Compare-And-Swap`） 又称原子指令或者原子操作

`Memcached CAS `命令用于执行一个 **检查并设置** 的操作

它仅在当前客户端最后一次取值后，该 `key` 对应的值没有被其他客户端修改的情况下， 才能够将值写入。

## 语法 ##

```
cas key flags exptime bytes unique_cas_token [noreply]
value
```

通过 `cas_token` 参数进行检查操作， 这个参数是`Memcach`指定给已经存在的元素的一个唯一的64位值

### 参数说明 ###

 *  **key：** 键值 key-value 结构中的 key，用于查找缓存值。
 *  **flags** ：可以包括键值对的整型参数，Memcached 使用它存储键值对的额外信息
 *  **exptime** ：缓存中键值对存活的时间长度（以秒为单位，0 表示永远）
 *  **bytes** ：缓存中存储的字节数
 *  **unique\_cas\_token** 通过 gets 命令获取的一个唯一的64位值。
 *  **noreply（可选）** ： 该参数告知服务器不需要返回数据
 *  **value** ：存储的值（始终位于第二行）（可直接理解为`key-value`结构中的`value`）

### 返回 ###

 *  如果数据添加成功，则返回 **STORED**
 *  如果出现语法错误，则返回 **ERROR**
 *  最后一次取值后另外一个用户也在更新该数据，返回 **EXISTS**
 *  Memcached 上不存在该键，则返回 **NOT\_FOUND**

## 范例 ##

使用 `CAS` 命令之前，要先用 gets 命令获取令牌（`token`）

使用 `CAS` 操作的步骤一般如下：

1、  如果没有设置唯一令牌，则 `CAS` 命令执行错误
2、  如果键 `key` 不存在，执行失败
3、  添加键值对
4、  通过 `gets` 命令获取唯一令牌
5、  使用 `cas` 命令更新数据
6、  使用 `get` 命令查看数据是否更新

### 一般情况下 ###

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
gets site
VALUE site 0 11 37
www.ycbbs.vip
END
cas site 0 1000 7 37
ycbbs.vcn
STORED
```

### 缺少 token 返回 ERROR ###

```
flush_all
OK
cas site 0 1000 11
ERROR
```

### 键(key) 不存在返回 **NOT\_FOUND** ###

```
flush_all
OK
cas site 0 1000 11 2
www.ycbbs.vip
NOT_FOUND
```

### CAS 时另一个客户端已经修改 返回 **EXISTS** ###

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
gets site
VALUE site 0 11 34
www.ycbbs.vip
END
```

此时的 token 为 `34`

打开一个新的 memcached 客户端输入

```
gets site
VALUE site 0 11 34
www.ycbbs.vip
END
cas site 0 1000  7 34
ycbbs.vcn
STORED
```

在新的客户端中已经使用 `token=34` 修改了值 那么原来的客户端中再使用 `token=34` 则返回 **EXISTS**

```
cas site 0 1000 7 34
ycbbs.vcn
EXISTS
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")