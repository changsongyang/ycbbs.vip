## 引言
`Redis` `hash` 是一个 `string` 类型的 `field` 和 `value` 的映射表

`Redis` `hash` 特别适合用于存储对象

`Redis` 中每个 `hash` 可以存储 `232- 1` 键值对（`40`多亿)

### 范例 ###

```
127、0.0.1:6379> HMSET company:qq name "腾讯" url "http://www.qq.com" rank 1 visitors 230000000
OK
127、0.0.1:6379> HGETALL company:qq
1) "name"
2) "\xe8\x85\xbe\xe8\xae\xaf"
3) "url"
4) "http://www.qq.com"
5) "rank"
6) "1"
7) "visitors"
8) "230000000"
```

在上面的实例中，我们设置了 `qq` 的一些描述信息(`name`, `url`, `rank`, `visitors`) 到哈希表的 **company:qq** 中

## Redis hash 命令 ##

下表列出了 `redis` `hash` 命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">HDEL</a></td> 
   <td align="left">删除一个或多个哈希表字段</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HEXISTS</a></td> 
   <td align="left">查看哈希表 key 中，指定的字段是否存在</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HGET</a></td> 
   <td align="left">获取存储在哈希表中指定字段的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HGETALL</a></td> 
   <td align="left">获取在哈希表中指定 key 的所有字段和值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HINCRBY</a></td> 
   <td align="left">为哈希表 key 中的指定字段的整数值加上增量 increment</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HINCRBYFLOAT</a></td> 
   <td align="left">为哈希表 key 中的指定字段的浮点数值加上增量 increment</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HKEYS</a></td> 
   <td align="left">获取所有哈希表中的字段</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HLEN</a></td> 
   <td align="left">获取哈希表中字段的数量</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HMGET</a></td> 
   <td align="left">获取所有给定字段的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HMSET</a></td> 
   <td align="left">同时将多个 field-value (域-值)对设置到哈希表 key 中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HSET</a></td> 
   <td align="left">将哈希表 key 中的字段 field 的值设为 value</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HSETNX</a></td> 
   <td align="left">只有在字段 field 不存在时，设置哈希表字段的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HVALS</a></td> 
   <td align="left">获取哈希表中所有值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HSCAN</a></td> 
   <td align="left">迭代哈希表中的键值对</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">HSTRLEN</a></td> 
   <td align="left">返回哈希表 key 中， 与给定域 field 相关联的值的字符串长度</td> 
  </tr> 
 </tbody> 
</table>

更多命令请参考：[https://redis.io/commands][https_redis.io_commands]


[https_redis.io_commands]: https://redis.io/commands


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")