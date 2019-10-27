## 引言
`Redis` 发布订阅(`pub`/`sub`)是一种消息通信模式

```
发送者(pub)发送消息，订阅者(sub)接收消息
```

`Redis` 允许客户端订阅任意数量的频道

### 发布订阅(pub/sub) 图示 ###

下图展示了频道 `channel1`，以及订阅这个频道的三个客户端 : `client2` 、 `client5` 和 `client1` 之间的关系

![img\_1.png][img_1.png]

当有新消息通过 `PUBLISH` 命令发送给频道 `channel1` 时， 这个消息就会被发送给订阅它的三个客户端

![img\_2.png][img_2.png]

### 范例 ###

下面的范例演示了发布订阅是如何工作的

#### 1. 新建订阅频道 **chart:io** ####

```
127、0.0.1:6379> SUBSCRIBE chart:io
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "chart:io"
3) (integer) 1
```

#### 2. 重新打卡另一个 Redis 客户端 ####

在同一个频道 `chart:io` 发布两次消息，订阅者就能接收到消息

```
127、0.0.1:6379> PUBLISH chart:io "Hello, I am www.ycbbs.vip"
(integer) 1
127、0.0.1:6379> PUBLISH chart:io "Nict to see you, My old friends!"
(integer) 1
127、0.0.1:6379>
```

#### 3.订阅者的客户端会显示如下消息 ####

```
1) "message"
2) "chart:io"
3) "Hello, I am www.ycbbs.vip"
1) "message"
2) "chart:io"
3) "Nict to see you, My old friends!"
```

## Redis 发布订阅命令 ##

下表列出了 `redis` 发布订阅相关的命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">PSUBSCRIBE</a></td> 
   <td align="left">订阅一个或多个符合给定模式的频道</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PUBSUB</a></td> 
   <td align="left">查看订阅与发布系统状态</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PUBLISH</a></td> 
   <td align="left">将信息发送到指定的频道</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">PUNSUBSCRIBE</a></td> 
   <td align="left">退订所有给定模式的频道</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SUBSCRIBE</a></td> 
   <td align="left">订阅给定的一个或多个频道的信息</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">UNSUBSCRIBE</a></td> 
   <td align="left">指退订给定的频道</td> 
  </tr> 
 </tbody> 
</table>


[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/02/15/img_1.png
[img_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/02/15/img_2.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")