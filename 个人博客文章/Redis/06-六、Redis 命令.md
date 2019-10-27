## 引言
Redis 命令用于在 Redis 服务上执行操作

我们使用 Redis 服务自带的 `redis-cli` 客户端来发送命令  
最新版的 `redis-cli` 会有命令提示功能，方便学些

### 启动 redis-cli 客户端 ###

```
$ redis-cli
```

### 范例 ###

下面的范例演示了如何启动 redis 客户端，并发送 `ping` 命令

```
$ redis-cli
127、0.0.1:6379>
127、0.0.1:6379> PING
PONG
```

**PING** 命令用于检测 Redis 服务是否启动

## 使用 redis-cli 在远程 Redis 服务上执行命令 ##

使用 `redis-cli` 也可以在远程 Redis 服务上执行命令

### 启动远程 redis-cli 语法 ###

```
$ redis-cli -h host -p port -a password
```

下面的范例演示了如何连接到主机为 192.168.1.100，端口为 6379 ，密码为 123456 的 Redis 服务上

```
$ redis-cli -h 192.168.1.100 -p 6379 -a "123456"
192、168.1.100>
192、168.1.100> PING
PONG
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")