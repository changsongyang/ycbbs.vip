# Redis 有序集合(sorted set) #

`Redis` `sorted` `set` 和 `set` 一样也是 `string` 类型元素的集合，且不允许重复的成员

`Redis` `sorted` `set` 的每个元素都会关联一个 `double` 类型的分数(`score`)

`Redis` `sorted` `set` 通过分数(`score`) 来为集合中的成员进行从小到大的排序

`Redis` `sorted` `set`(有序集合) 的成员是唯一的,但分数 (`score`) 却可以重复

`Redis` `sorted` `set` 是通过哈希表实现的，所以添加，删除，查找的复杂度都是`O(1)`

`Redis` `sorted` `set` 中最大的成员数为 `232 - 1`

### 范例 ###

```
127、0.0.1:6379> ZADD language 1 PHP
(integer) 1
127、0.0.1:6379> ZADD language 2 Python
(integer) 1
127、0.0.1:6379> ZADD language 3 Ruby
(integer) 1
127、0.0.1:6379> ZADD language 3 Perl
(integer) 0
127、0.0.1:6379> ZADD language 4 Perl
(integer) 0
127、0.0.1:6379> ZRANGE language 0 10 WITHSCORES

1) "PHP"
2) "1"
3) "Python"
4) "2"
5) "Ruby"
6) "3"
7) "Perl"
8) "4"
```

上面的范例，我们通过 **ZADD** 命令向 `Redis` 的有序集合中添加了三个值并关联上分数

## Redis 有序集合命令 ##

下表列出了 `Redis` 有序集合的基本命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">ZADD</a></td> 
   <td align="left">向有序集合添加一个或多个成员，或者更新已存在成员的分数</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZCARD</a></td> 
   <td align="left">获取有序集合的成员数</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZCOUNT</a></td> 
   <td align="left">计算在有序集合中指定区间分数的成员数</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZINCRBY</a></td> 
   <td align="left">有序集合中对指定成员的分数加上增量 increment</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZINTERSTORE</a></td> 
   <td align="left">计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZLEXCOUNT</a></td> 
   <td align="left">在有序集合中计算指定字典区间内成员数量</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZRANGE</a></td> 
   <td align="left">通过索引区间返回有序集合成指定区间内的成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZRANGEBYLEX</a></td> 
   <td align="left">通过字典区间返回有序集合的成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZRANGEBYSCORE</a></td> 
   <td align="left">通过分数返回有序集合指定区间内的成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZRANK</a></td> 
   <td align="left">返回有序集合中指定成员的索引</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREM</a></td> 
   <td align="left">移除有序集合中的一个或多个成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREMRANGEBYLEX</a></td> 
   <td align="left">移除有序集合中给定的字典区间的所有成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREMRANGEBYRANK</a></td> 
   <td align="left">移除有序集合中给定的排名区间的所有成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREMRANGEBYSCORE</a></td> 
   <td align="left">移除有序集合中给定的分数区间的所有成员</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREVRANGE</a></td> 
   <td align="left">返回有序集中指定区间内的成员，通过索引，分数从高到底</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREVRANGEBYSCORE</a></td> 
   <td align="left">返回有序集中指定分数区间内的成员，分数从高到低排序</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZREVRANK</a></td> 
   <td align="left">返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZSCORE</a></td> 
   <td align="left">返回有序集中，成员的分数值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZUNIONSTORE</a></td> 
   <td align="left">计算一个或多个有序集的并集，并存储在新的 key 中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ZSCAN</a></td> 
   <td align="left">迭代有序集合中的元素（包括元素成员和元素分值）</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")