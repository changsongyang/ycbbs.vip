## 引言
`Redis` 脚本使用 `Lua` 解释器来执行脚本

`Reids 2.6` 版本通过内嵌支持 `Lua` 环境

`Redis` 执行脚本的常用命令为 **EVAL**

### EVAL 命令语法格式 ###

`Redis` `Eval` 命令的基本语法如下：

```
127、0.0.1:6379> EVAL script numkeys key [key ...] arg [arg ...]
```

### 范例 ###

下面的范例演示了 `Redis` 脚本工作过程

```
127、0.0.1:6379> EVAL "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second

1) "key1"
2) "key2"
3) "first"
4) "second"
```

## Redis 脚本命令 ##

下表列出了 `Redis` 脚本相关的命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">EVAL</a></td> 
   <td align="left">执行 Lua 脚本</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">EVALSHA</a></td> 
   <td align="left">执行 Lua 脚本</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SCRIPT EXISTS</a></td> 
   <td align="left">查看指定的脚本是否已经被保存在缓存当中</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SCRIPT FLUSH</a></td> 
   <td align="left">从脚本缓存中移除所有脚本</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SCRIPT KILL</a></td> 
   <td align="left">杀死当前正在运行的 Lua 脚本</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SCRIPT LOAD</a></td> 
   <td align="left">将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")