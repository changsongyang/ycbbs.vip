## 引言
`Redis` 服务通过同时执行多个命令来测试性能表现

### Redis 性能测试语法 ###

`Redis` 性能测试的基本命令如下

```
redis-benchmark [option] [option value]
```

### 范例 ###

以下范例同时执行 `10000` 个请求来检测性能

```
$ redis-benchmark -n 10000 -q                                  
PING_INLINE: 40000.00 requests per second
PING_BULK: 54347.82 requests per second
SET: 50251.26 requests per second
GET: 51813.47 requests per second
INCR: 52631.58 requests per second
LPUSH: 48309.18 requests per second
RPUSH: 47846.89 requests per second
LPOP: 49261.09 requests per second
RPOP: 44247.79 requests per second
SADD: 51020.41 requests per second
HSET: 42372.88 requests per second
SPOP: 50505.05 requests per second
LPUSH (needed to benchmark LRANGE): 52083.33 requests per second
LRANGE_100 (first 100 elements): 13927.58 requests per second
LRANGE_300 (first 300 elements): 6443.30 requests per second
LRANGE_500 (first 450 elements): 4587.16 requests per second
LRANGE_600 (first 600 elements): 3507.54 requests per second
MSET (10 keys): 25906.73 requests per second
```

你可以去掉 `-q` 查看详细测试

### 可选参数如下所示 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">选项</th> 
   <th align="left">描述</th> 
   <th align="left">默认值</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">-h</td> 
   <td align="left">指定服务器主机名</td> 
   <td align="left">127.0.0.1</td> 
  </tr> 
  <tr> 
   <td align="left">-p</td> 
   <td align="left">指定服务器端口</td> 
   <td align="left">6379</td> 
  </tr> 
  <tr> 
   <td align="left">-s</td> 
   <td align="left">指定服务器 socket</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">-c</td> 
   <td align="left">指定并发连接数</td> 
   <td align="left">50</td> 
  </tr> 
  <tr> 
   <td align="left">-n</td> 
   <td align="left">指定请求数</td> 
   <td align="left">10000</td> 
  </tr> 
  <tr> 
   <td align="left">-d</td> 
   <td align="left">以字节的形式指定 SET/GET 值的数据大小</td> 
   <td align="left">2</td> 
  </tr> 
  <tr> 
   <td align="left">-k</td> 
   <td align="left">1=keep alive 0=reconnect</td> 
   <td align="left">1</td> 
  </tr> 
  <tr> 
   <td align="left">-r</td> 
   <td align="left">SET/GET/INCR 使用随机 key, SADD 使用随机值</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">-P</td> 
   <td align="left">通过管道传输 <numreq> 请求</td> 
   <td align="left">1</td> 
  </tr> 
  <tr> 
   <td align="left">-q</td> 
   <td align="left">强制退出 redis。仅显示 query/sec 值</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">--csv</td> 
   <td align="left">以 CSV 格式输出</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">-l</td> 
   <td align="left">生成循环，永久执行测试</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">-t</td> 
   <td align="left">仅运行以逗号分隔的测试命令列表</td> 
   <td align="left"></td> 
  </tr> 
  <tr> 
   <td align="left">-I</td> 
   <td align="left">Idle 模式。仅打开 N 个 idle 连接并等待</td> 
   <td align="left"></td> 
  </tr> 
 </tbody> 
</table>

### 范例 ###

下面的 `Shell` 命令使用了多个参数来测试 `redis` 性能

```
$ redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 10000 -q
SET: 49751.24 requests per second
LPUSH: 49261.09 requests per second
```

这个测试命令设置 主机为 `127.0.0.1` 端口号为 `6379` 测试的 `Redis` 命令为 `set`,`lpush` 请求数为 `10000`， 通过 `-q` 参数让结果只显示每秒执行的请求数


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")