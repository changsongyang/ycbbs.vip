## 引言
`Redis` 服务器命令主要是用于管理 `redis` 服务

### 范例 ###

以下范例演示了如何获取 `redis` 服务器的统计信息

```
$ redis-cli
127、0.0.1:6379> info
# Server
redis_version:3.0.7
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:aa27a151289c9b98
redis_mode:standalone
os:Darwin 17.2.0 x86_64
arch_bits:64
multiplexing_api:kqueue
gcc_version:4.2.1
process_id:702
run_id:63a232c39b249561664fb3a427a95f1bfe33e33e
tcp_port:6379
uptime_in_seconds:611995
uptime_in_days:7
hz:10
lru_clock:15307220
config_file:/usr/local/etc/redis.conf

# Clients
connected_clients:1
client_longest_output_list:0
client_biggest_input_buf:0
blocked_clients:0

# Memory
used_memory:1009664
used_memory_human:986.00K
used_memory_rss:602112
used_memory_peak:1009664
used_memory_peak_human:986.00K
used_memory_lua:36864
mem_fragmentation_ratio:0.60
mem_allocator:libc

# Persistence
loading:0
rdb_changes_since_last_save:0
rdb_bgsave_in_progress:0
rdb_last_save_time:1507867449
rdb_last_bgsave_status:ok
rdb_last_bgsave_time_sec:-1
rdb_current_bgsave_time_sec:-1
aof_enabled:0
aof_rewrite_in_progress:0
aof_rewrite_scheduled:0
aof_last_rewrite_time_sec:-1
aof_current_rewrite_time_sec:-1
aof_last_bgrewrite_status:ok
aof_last_write_status:ok

# Stats
total_connections_received:1
total_commands_processed:1
instantaneous_ops_per_sec:0
total_net_input_bytes:31
total_net_output_bytes:6049617
instantaneous_input_kbps:0.00
instantaneous_output_kbps:0.00
rejected_connections:0
sync_full:0
sync_partial_ok:0
sync_partial_err:0
expired_keys:0
evicted_keys:0
keyspace_hits:0
keyspace_misses:0
pubsub_channels:0
pubsub_patterns:0
latest_fork_usec:0
migrate_cached_sockets:0

# Replication
role:master
connected_slaves:0
master_repl_offset:0
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

# CPU
used_cpu_sys:174.97
used_cpu_user:51.83
used_cpu_sys_children:0.00
used_cpu_user_children:0.00

# Cluster
cluster_enabled:0

# Keyspace
db0:keys=2,expires=0,avg_ttl=0
127、0.0.1:6379> 
```

### Redis 管理 redis 服务相关命令 ###

下表列出了管理 `redis` 服务相关的命令

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left"><a rel="nofollow">BGREWRITEAOF</a></td> 
   <td align="left">异步执行一个 AOF（AppendOnly File） 文件重写操作</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">BGSAVE</a></td> 
   <td align="left">在后台异步保存当前数据库的数据到磁盘</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT</a></td> 
   <td align="left">关闭客户端连接</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT LIST</a></td> 
   <td align="left">获取连接到服务器的客户端连接列表</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT GETNAME</a></td> 
   <td align="left">获取连接的名称</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT PAUSE</a></td> 
   <td align="left">在指定时间内终止运行来自客户端的命令</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLIENT SETNAME</a></td> 
   <td align="left">设置当前连接的名称</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CLUSTER SLOTS</a></td> 
   <td align="left">获取集群节点的映射数组</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">COMMAND</a></td> 
   <td align="left">获取 Redis 命令详情数组</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">COMMAND COUNT</a></td> 
   <td align="left">获取 Redis 命令总数</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">COMMAND GETKEYS</a></td> 
   <td align="left">获取给定命令的所有键</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">TIME</a></td> 
   <td align="left">返回当前服务器时间</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">COMMAND INFO</a></td> 
   <td align="left">获取指定 Redis 命令描述的数组</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CONFIG GET</a></td> 
   <td align="left">获取指定配置参数的值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CONFIG REWRITE</a></td> 
   <td align="left">修改 redis.conf 配置文件</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CONFIG SET</a></td> 
   <td align="left">修改 redis 配置参数，无需重启</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">CONFIG RESETSTAT</a></td> 
   <td align="left">重置 INFO 命令中的某些统计数据</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">DBSIZE</a></td> 
   <td align="left">返回当前数据库的 key 的数量</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">DEBUG OBJECT</a></td> 
   <td align="left">获取 key 的调试信息</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">DEBUG SEGFAULT</a></td> 
   <td align="left">让 Redis 服务崩溃</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">FLUSHALL</a></td> 
   <td align="left">删除所有数据库的所有key</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">FLUSHDB</a></td> 
   <td align="left">删除当前数据库的所有key</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">INFO</a></td> 
   <td align="left">获取 Redis 服务器的各种信息和统计数值</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">LASTSAVE</a></td> 
   <td align="left">返回最近一次 Redis 成功将数据保存到磁盘上的时间</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">MONITOR</a></td> 
   <td align="left">实时打印出 Redis 服务器接收到的命令，调试用</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">ROLE</a></td> 
   <td align="left">返回主从实例所属的角色</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SAVE</a></td> 
   <td align="left">异步保存数据到硬盘</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SHUTDOWN</a></td> 
   <td align="left">异步保存数据到硬盘，并关闭服务器</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SLAVEOF</a></td> 
   <td align="left">将当前服务器转变从属服务器(slave server)</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SLOWLOG</a></td> 
   <td align="left">管理 redis 的慢日志</td> 
  </tr> 
  <tr> 
   <td align="left"><a rel="nofollow">SYNC</a></td> 
   <td align="left">用于复制功能 ( replication ) 的内部命令</td> 
  </tr> 
 </tbody> 
</table>


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")