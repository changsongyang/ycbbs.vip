## 引言
`Redis` 连接命令主要是用于连接到 `Redis` 服务

下面的范例演示客户端如何通过密码验证连接到 `Redis` 服务，并检测服务是否在运行

```
127、0.0.1:6379> AUTH "password"
OK
127、0.0.1:6379> PING
PONG
```

## Redis 连接命令 ##

下表列出了用于 `Redis` 连接相关的命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">AUTH password</a></td> 
   <td align="left">验证密码是否正确</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ECHO message</a></td> 
   <td align="left">打印字符串</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PING</a></td> 
   <td align="left">查看服务是否运行</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">QUIT</a></td> 
   <td align="left">关闭当前连接</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SELECT index</a></td> 
   <td align="left">切换到指定的数据库</td> 
  </tr> 
 </tbody> 
</table>



希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")