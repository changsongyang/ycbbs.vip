Memcached `stats` 命令返回 `Memcached` 的统计信息，比如 `PID`(进程号)、版本号、连接数等。

## 语法 ##

```
stats
```

### stats 返回的每一项解释如下 ###

```html
*  **pid** ： memcache服务器进程ID
 *  **uptime** ：服务器已运行秒数
 *  **time** ：服务器当前Unix时间戳
 *  **version**：memcache版本
 *  **pointer\_size** ：操作系统指针大小
 *  **rusage\_user** ：进程累计用户时间
 *  **rusage\_system** ：进程累计系统时间
 *  **curr\_connections** ：当前连接数量
 *  **total\_connections** ：Memcached运行以来连接总数
 *  **connection\_structures** ：Memcached分配的连接结构数量
 *  **cmd\_get** ：get命令请求次数
 *  **cmd\_set** ：set命令请求次数
 *  **cmd\_flush** ：flush命令请求次数
 *  **get\_hits** ：get命令命中次数
 *  **get\_misses** ：get命令未命中次数
 *  **delete\_misses** ：delete命令未命中次数
 *  **delete\_hits** ：delete命令命中次数
 *  **incr\_misses** ：incr命令未命中次数
 *  **incr\_hits** ：incr命令命中次数
 *  **decr\_misses** ：decr命令未命中次数
 *  **decr\_hits** ：decr命令命中次数
 *  **cas\_misses** ：cas命令未命中次数
 *  **cas\_hits** ：cas命令命中次数
 *  **cas\_badval** ：使用擦拭次数
 *  **auth\_cmds** ：认证命令处理的次数
 *  **auth\_errors** ：认证失败数目
 *  **bytes\_read** ：读取总字节数
 *  **bytes\_written** ：发送总字节数
 *  **limit\_maxbytes** ：分配的内存总大小（字节）
 *  **accepting\_conns** ：服务器是否达到过最大连接（0/1）
 *  **listen\_disabled\_num** ：失效的监听数
 *  **threads** ：当前线程数
 *  **conn\_yields** ：连接操作主动放弃数目
 *  **bytes** ：当前存储占用的字节数
 *  **curr\_items** ：当前存储的数据总数
 *  **total\_items** ：启动以来存储的数据总数
 *  **evictions** ：LRU释放的对象数目
 *  **reclaimed** ：已过期的数据条目来存储新数据的数目
```

## 范例 ##

```
flush_all
OK
set site 0 1000 11
www.ycbbs.vip
STORED
set age 0 1000 2
28
STORED
stats  # 统计信息开始
STAT pid 32207
STAT uptime 918
STAT time 1504619428
STAT version 1.4.36
STAT libevent 2.1.8-stable
STAT pointer_size 64
STAT rusage_user 0.155947
STAT rusage_system 0.269330
STAT curr_connections 5
STAT total_connections 6
STAT connection_structures 6
STAT reserved_fds 20
STAT cmd_get 0
STAT cmd_set 2
STAT cmd_flush 1
STAT cmd_touch 0
STAT get_hits 0
STAT get_misses 0
STAT get_expired 0
STAT get_flushed 0
STAT delete_misses 0
STAT delete_hits 0
STAT incr_misses 0
STAT incr_hits 0
STAT decr_misses 0
STAT decr_hits 0
STAT cas_misses 0
STAT cas_hits 0
STAT cas_badval 0
STAT touch_hits 0
STAT touch_misses 0
STAT auth_cmds 0
STAT auth_errors 0
STAT bytes_read 152
STAT bytes_written 1246
STAT limit_maxbytes 268435456
STAT accepting_conns 1
STAT listen_disabled_num 0
STAT time_in_listen_disabled_us 0
STAT threads 4
STAT conn_yields 0
STAT hash_power_level 16
STAT hash_bytes 524288
STAT hash_is_expanding 0
STAT malloc_fails 0
STAT log_worker_dropped 0
STAT log_worker_written 0
STAT log_watcher_skipped 0
STAT log_watcher_sent 0
STAT bytes 151
STAT curr_items 2
STAT total_items 2
STAT expired_unfetched 0
STAT evicted_unfetched 0
STAT evictions 0
STAT reclaimed 0
STAT crawler_reclaimed 0
STAT crawler_items_checked 0
STAT lrutail_reflocked 0
END
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")