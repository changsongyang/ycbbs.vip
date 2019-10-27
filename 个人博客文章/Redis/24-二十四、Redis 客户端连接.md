## 引言
Redis 通过监听一个 `TCP` 端口或者 `Unix` `socket` 的方式来接收来自客户端的连接

当一个连接建立后，Redis 内部会进行以下一些操作：

1、  客户端 `socket` 会被设置为非阻塞模式，因为 Redis 在网络事件处理上采用的是非阻塞多路复用模型
2、  为这个 `socket` 设置 `TCP\_NODELAY` 属性，禁用 `Nagle` 算法
3、  创建一个可读的文件事件用于监听这个客户端 `socket` 的数据发送

## 最大连接数 ##

在 `Redis 2.6` 以前的版本中 **最大连接数 ( maxclients )** 被直接编码在 `Redis` 源代码中

但那之后的版本，这个值是可配置的

`maxclients` 的默认值是 **10000**

#### 查看当前连接的 Redis 最大连接数 ####

使用以下命令可以查看当前连接的 `Redis` 服务允许的最大连接数

```
config get maxclients

1) "maxclients"
2) "10000"
```

#### 在 redis.conf 文件中配置 ####

我们可以在 `redis.conf` 中对这个值进行修改

```
# maxclients 10000  
```

只要去掉 `\#` 号，并把 `10000` 改成你想要的数字

#### Redis 启动时配置 ####

下面的 `Shell` 命令用来启动 `Redis` 服务并设置设置最大连接数为 `100000`

```
redis-server --maxclients 100000
```

## Redis 客户端命令 ##

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT LIST</a></td> 
   <td align="left">返回连接到 redis 服务的客户端列表</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT SETNAME</a></td> 
   <td align="left">设置当前连接的名称</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT GETNAME</a></td> 
   <td align="left">获取通过 CLIENT SETNAME 命令设置的服务名称</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT PAUSE</a></td> 
   <td align="left">挂起客户端连接，指定挂起的时间以毫秒计</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT KILL</a></td> 
   <td align="left">关闭客户端连接</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")