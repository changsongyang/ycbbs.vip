emcached quit 命令用户关闭一个客户端连接

## 语法 ##

```
quit
```

### 范例 ###

连接到 127.0.0.1 上 11211 的 Memcached 服务, 然后退出

```
$ telnet 127.0.0.1 11211
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
quit                                # 退出
Connection closed by foreign host.  # 从返回信息看是 Memcached 主动关闭了连接
```




希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")