## 引言
默认的 `Redis` 服务是不需要用户名和密码就能访问的，如果把 `Redis` 服务开放给公网访问是非常危险的。

`Redis` 服务安全设置包括以下几个方面

1、  设置 `iptables` 过滤 `IP` 地址
2、  更改 `Redis` 监听的 `IP` 地址和端口
3、  设置 `Redis` 密码

`1` 和 `2` 我们不做过多讨论，本章节主要学习如何设置 `Redis` 密码

## 设置 Redis 密码 ##

可以通过 `Redis` 的配置文件设置密码参数，这样客户端连接到 `redis` 服务就需要密码验证

设置 `Redis` 密码后让我们的 `Redis` 服务更安全

### 1. 通过命令查看是否设置了密码验证 ###

```
127、0.0.1:6379> CONFIG get requirepass
1) "requirepass"
2) ""
```

默认情况下 `requirepass` 参数是空的，无需通过密码验证就可以连接到 `Redis` 服务

### 2. 通过命令来设置密码 ###

```
127、0.0.1:6379> CONFIG set requirepass "1F26e@dF1b"
OK
127、0.0.1:6379> CONFIG get requirepass
1) "requirepass"
2) "1F26e@dF1b"
```

设置密码后，客户端连接 `Redis` 服务就需要密码验证，否则无法执行命令

### 3. 通过 AUTH 命名进行密码验证 ###

`Redis` **AUTH** 命令语法格式如下

```
127、0.0.1:6379> AUTH password
```

#### 范例 ####

如果不验证密码

```
127、0.0.1:6379> get site
(error) NOAUTH Authentication required.
```

设置密码之后

```
127、0.0.1:6379> AUTH "1F26e@dF1b"
OK
127、0.0.1:6379> SET site "www.ycbbs.vip"
OK
127、0.0.1:6379> GET site
"www.ycbbs.vip"
```

有关密码设置和 `AUTH` 更详细的资料，请移步 `Redis` `AUTH` 命令

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")