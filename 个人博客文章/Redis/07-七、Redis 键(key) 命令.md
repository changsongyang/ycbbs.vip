## 引言
`Redis` 键相关的命令用于管理 `redis` 的键

### Redis 键命令语法 ###

`Redis` 键命令的基本语法如下：

```
127、0.0.1:6379> COMMAND KEY_NAME
```

### 范例 ###

```
127、0.0.1:6379> SET site www.ycbbs.vip
OK
127、0.0.1:6379> DEL site  
(integer) 1
```

 **DEL** 是一个命令，用来删除一个键 `site`  
如果键被删除成功，命令执行后输出 **(integer) 1** ，否则将输出 **(integer) 0**

## Redis keys 命令 ##

下表列出了 `Redis` 键相关的命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">DEL</a></td> 
   <td align="left">用于删除 key</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">DUMP</a></td> 
   <td align="left">序列化给定 key ，并返回被序列化的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">EXISTS</a></td> 
   <td align="left">检查给定 key 是否存在</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">EXPIRE</a></td> 
   <td align="left">为给定 key 设置过期时间</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">EXPIREAT</a></td> 
   <td align="left">用于为 key 设置过期时间<br>接受的时间参数是 UNIX 时间戳</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PEXPIRE</a></td> 
   <td align="left">设置 key 的过期时间，以毫秒计</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PEXPIREAT</a></td> 
   <td align="left">设置 key 过期时间的时间戳(unix timestamp)，以毫秒计</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">KEYS</a></td> 
   <td align="left">查找所有符合给定模式的 key</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">MOVE</a></td> 
   <td align="left">将当前数据库的 key 移动到给定的数据库中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PERSIST</a></td> 
   <td align="left">移除 key 的过期时间，key 将持久保持</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PTTL</a></td> 
   <td align="left">以毫秒为单位返回 key 的剩余的过期时间</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">TTL</a></td> 
   <td align="left">以秒为单位，返回给定 key 的剩余生存时间(</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RANDOMKEY</a></td> 
   <td align="left">从当前数据库中随机返回一个 key</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RENAME</a></td> 
   <td align="left">修改 key 的名称</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RENAMENX</a></td> 
   <td align="left">仅当 newkey 不存在时，将 key 改名为 newkey</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">TYPE</a></td> 
   <td align="left">返回 key 所储存的值的类型</td> 
  </tr> 
 </tbody> 
</table>

更多命令请参考：[https://redis.io/commands][https_redis.io_commands]


[https_redis.io_commands]: https://redis.io/commands


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")