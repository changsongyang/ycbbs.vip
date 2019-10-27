## 引言
`Redis` 在 `2.8.9` 版本添加了 `HyperLogLog` 结构

`Redis` `HyperLogLog` 是用来做基数统计的算法

`HyperLogLog` 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的

每个 `HyperLogLog` 键只需要花费 `12 KB` 内存，就可以计算接近` 2^64` 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 `HyperLogLog` 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 `HyperLogLog` 不能像集合那样，返回输入的各个元素

### 什么是基数? ###

假如我们有一个数据集 `\{1, 3, 5, 7, 5, 7, 8\} ` 
那么这个数据集的基数集为` \{1, 3, 5 ,7, 8\}`, 基数 (不重复元素个数) 为`5`

**基数估计** 就是在误差可接受的范围内，快速计算基数

### 范例 ###

下面的范例演示了 `HyperLogLog` 的工作过程

```
127、0.0.1:6379> PFADD language "PHP"
1) (integer) 1
127、0.0.1:6379> PFADD language "Python"
1) (integer) 1
127、0.0.1:6379> PFADD language "Perl"
1) (integer) 1
127、0.0.1:6379> PFCOUNT language
(integer) 3
```

## Redis HyperLogLog 命令 ##

下表列出了 Redis HyperLogLog 命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">PFADD</a></td> 
   <td align="left">添加指定元素到 HyperLogLog 中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PFCOUNT</a></td> 
   <td align="left">返回给定 HyperLogLog 的基数估算值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PFMERGE</a></td> 
   <td align="left">将多个 HyperLogLog 合并为一个 HyperLogLog</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")