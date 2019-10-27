## 引言
`Redis` 字符串数据类型的相关命令用于管理 `Redis` 字符串值

### Redis 字符串命令语法 ###

```
127、0.0.1:6379> COMMAND KEY_NAME
```

### 范例 ###

```
127、0.0.1:6379> SET site www.ycbbs.vip 
OK
127、0.0.1:6379> GET site   
"www.ycbbs.vip"
```

 **SET** 命令用于设置指定 `key` 的值  
 **GET** 命令用于获取指定 `key` 的值

## Redis 字符串命令 ##

下表列出了 `Redis` 字符串命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">SET</a></td> 
   <td align="left">设置指定 key 的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">GET</a></td> 
   <td align="left">获取指定 key 的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">GETRANGE</a></td> 
   <td align="left">返回 key 中字符串值的子字符</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">GETSET</a></td> 
   <td align="left">将给定 key 的值设为 value ，并返回 key 的旧值 ( old value )</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">GETBIT</a></td> 
   <td align="left">对 key 所储存的字符串值，获取指定偏移量上的位 ( bit )</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">MGET</a></td> 
   <td align="left">获取所有(一个或多个)给定 key 的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SETBIT</a></td> 
   <td align="left">对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SETEX</a></td> 
   <td align="left">设置 key 的值为 value 同时将过期时间设为 seconds</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SETNX</a></td> 
   <td align="left">只有在 key 不存在时设置 key 的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SETRANGE</a></td> 
   <td align="left">从偏移量 offset 开始用 value 覆写给定 key 所储存的字符串值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">STRLEN</a></td> 
   <td align="left">返回 key 所储存的字符串值的长度</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">MSET</a></td> 
   <td align="left">同时设置一个或多个 key-value 对</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">MSETNX</a></td> 
   <td align="left">同时设置一个或多个 key-value 对</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PSETEX</a></td> 
   <td align="left">以毫秒为单位设置 key 的生存时间</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">INCR</a></td> 
   <td align="left">将 key 中储存的数字值增一</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">INCRBY</a></td> 
   <td align="left">将 key 所储存的值加上给定的增量值 ( increment )</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">INCRBYFLOAT</a></td> 
   <td align="left">将 key 所储存的值加上给定的浮点增量值 ( increment )</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">DECR</a></td> 
   <td align="left">将 key 中储存的数字值减一</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">DECRBY</a></td> 
   <td align="left">将 key 所储存的值减去给定的减量值 ( decrement )</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">APPEND</a></td> 
   <td align="left">将 value 追加到 key 原来的值的末尾</td> 
  </tr> 
 </tbody> 
</table>

更多命令请参考：[https://redis.io/commands][https_redis.io_commands]


[https_redis.io_commands]: https://redis.io/commands


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
