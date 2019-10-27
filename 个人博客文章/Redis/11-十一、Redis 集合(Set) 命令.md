## 引言
`Redis` `Set` 是 `string` 类型的无序集合

`Redis` `set` 集合成员是唯一的，这就意味着集合中不能出现重复的数据

`Redis` `set` 是通过哈希表实现的，所以添加，删除，查找的复杂度都是 `O(1)`

`Redis` `set` 最大的成员数量为 `232 - 1(4294967295)`

### 范例 ###

```
127、0.0.1:6379> SADD language PHP
(integer) 1
127、0.0.1:6379> SADD language Python
(integer) 1
127、0.0.1:6379> SADD language Perl
(integer) 1
127、0.0.1:6379> SADD language Python
(integer) 0
127、0.0.1:6379> SMEMBERS language

1) "Perl"
2) "Python"
3) "PHP"
```

上面的范例，我们通过 **SADD** 命令向名为 **language** 的集合插入的三个元素

## Redis 集合命令 ##

下表列出了 `Redis` 集合相关命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">SADD</a></td> 
   <td align="left">向集合添加一个或多个成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SCARD</a></td> 
   <td align="left">获取集合的成员数</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SDIFF</a></td> 
   <td align="left">返回给定所有集合的差集</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SDIFFSTORE</a></td> 
   <td align="left">返回给定所有集合的差集并存储在 destination 中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SINTER</a></td> 
   <td align="left">返回给定所有集合的交集</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SINTERSTORE</a></td> 
   <td align="left">返回给定所有集合的交集并存储在 destination 中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SISMEMBER</a></td> 
   <td align="left">判断 member 元素是否是集合 key 的成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SMEMBERS</a></td> 
   <td align="left">返回集合中的所有成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SMOVE</a></td> 
   <td align="left">将 member 元素从 source 集合移动到 destination 集合</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SPOP</a></td> 
   <td align="left">移除并返回集合中的一个随机元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SRANDMEMBER</a></td> 
   <td align="left">返回集合中一个或多个随机数</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SREM</a></td> 
   <td align="left">移除集合中一个或多个成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SUNION</a></td> 
   <td align="left">返回所有给定集合的并集</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SUNIONSTORE</a></td> 
   <td align="left">所有给定集合的并集存储在 destination 集合中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SSCAN</a></td> 
   <td align="left">迭代集合中的元素</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")