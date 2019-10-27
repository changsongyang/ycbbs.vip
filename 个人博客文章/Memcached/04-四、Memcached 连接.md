可以通过 telnet 命令并指定 **主机IP** 和 **端口(port)**来连接 Memcached

## 语法 ##

```
telnet HOST PORT
```

命令中的 **HOST** 和 **PORT** 为运行 `Memcached` 服务的 `IP` 和 端口。

> **11211** 为所有 Memcached 服务默认的端口号

### 范例 ###

#### 1. 连接到 127.0.0.1 上 11211 的 Memcached 服务 ####

假设我们的 `Memcached` 服务运行在本机， `IP` 为 `127.0.0.1` ,端口为 `11211`

那么连接到 `Memcached` 的命令为

```
telnet 127.0.0.1 11211
```

输出如下

```
$ telnet 127.0.0.1 11211
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
```

#### 2. 进行简单的 set 和 get 操作 ####

```
$ telnet 127.0.0.1 11211    # 连接服务器
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
set site 0 1000 11          # set 命令
tech.souyunku.com                 # 要设置的值(value)
STORED                      # 命令 执行结果
get site                    # get 命令
VALUE site 0 11             # key 的元数据
tech.souyunku.com                 #值(value) 数据
END                         # 命令执行结束标志
quit                        # 断开与 Memcached 的连接
Connection closed by foreign host.
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")