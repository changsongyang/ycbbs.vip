## 引言
`Redis` `List`(列表) 是简单的字符串列表，按照插入顺序排序

可以添加一个元素到 `Redis` 列表的头部（左边）或者尾部（右边）

`Redis` 一个列表最多可以包含 `232- 1` 个元素 (`4294967295`)

### 范例 ###

```
127、0.0.1:6379> LPUSH language Python
(integer) 1
127、0.0.1:6379> LPUSH language Perl
(integer) 2
127、0.0.1:6379> LPUSH language Ruby
(integer) 3
127、0.0.1:6379> LRANGE language 0 10

1) "Ruby"
2) "Perl"
3) "Python"
```

上面的范例，我们使用 **LPUSH** 命令将三个值插入了名为 **language** 的列表当中

### Redis 列表命令 ###

下表列出了列表相关命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">BLPOP</a></td> 
   <td align="left">移出并获取列表的第一个元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">BRPOP</a></td> 
   <td align="left">移出并获取列表的最后一个元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">BRPOPLPUSH</a></td> 
   <td align="left">从列表中弹出一个值，并将该值插入到另外一个列表中并返回它</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LINDEX</a></td> 
   <td align="left">通过索引获取列表中的元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LINSERT</a></td> 
   <td align="left">在列表的元素前或者后插入元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LLEN</a></td> 
   <td align="left">获取列表长度</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LPOP</a></td> 
   <td align="left">移出并获取列表的第一个元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LPUSH</a></td> 
   <td align="left">将一个或多个值插入到列表头部</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LPUSHX</a></td> 
   <td align="left">将一个值插入到已存在的列表头部</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LRANGE</a></td> 
   <td align="left">获取列表指定范围内的元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LREM</a></td> 
   <td align="left">移除列表元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LSET</a></td> 
   <td align="left">通过索引设置列表元素的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LTRIM</a></td> 
   <td align="left">对一个列表进行修剪(trim)</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RPOP</a></td> 
   <td align="left">移除并获取列表最后一个元素</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RPOPLPUSH</a></td> 
   <td align="left">移除列表的最后一个元素，并将该元素添加到另一个列表并返回</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RPUSH</a></td> 
   <td align="left">在列表中添加一个或多个值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">RPUSHX</a></td> 
   <td align="left">为已存在的列表添加值</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")