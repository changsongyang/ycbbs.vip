## 引言
`Redis` 事务可以一次执行多个命令， 并且带有以下两个重要的保证

1、  事务是一个单独的隔离操作
    
    事务中的所有命令都会序列化、按顺序地执行 事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
2、  事务是一个原子操作
    
    事务中的命令要么全部被执行，要么全部都不执行

### Redis 事务执行过程 ###

一个事务从开始到执行会经历以下三个阶段

1、  开始事务，使用 `MULTI` 命令
2、  命令入队
3、  执行事务，使用 `EXEC` 命令

### 范例 ###

下面的范例演示了 `Redis` 事务如何工作

它先以 **MULTI** 开始一个事务， 然后将多个命令入队到事务中， 最后由 **EXEC** 命令触发事务， 一并执行事务中的所有命令

```
127、0.0.1:6379> MULTI
OK
127、0.0.1:6379> SET site "www.ycbbs.vip"
QUEUED
127、0.0.1:6379> GET site
QUEUED
127、0.0.1:6379> SADD lession "PHP" "HTML" "Python" "JavaScript"
QUEUED
127、0.0.1:6379> SMEMBERS lession
QUEUED
127、0.0.1:6379> EXEC
1) OK
2) "www.ycbbs.vip"
3) (integer) 4
4) 1) "JavaScript"
   2) "Python"
   3) "HTML"
   4) "PHP"
127、0.0.1:6379> 
```

## Redis 事务命令 ##

下表列出了 Redis 事务的相关命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">DISCARD</a></td> 
   <td align="left">取消事务，放弃执行事务块内的所有命令</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">EXEC</a></td> 
   <td align="left">执行所有事务块内的命令</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">MULTI</a></td> 
   <td align="left">标记一个事务块的开始</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">UNWATCH</a></td> 
   <td align="left">取消 WATCH 命令对所有 key 的监视</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">WATCH</a></td> 
   <td align="left">监视一个(或多个) key</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")