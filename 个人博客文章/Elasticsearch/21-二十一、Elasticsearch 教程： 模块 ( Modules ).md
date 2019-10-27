`Elasticsearch` 由许多各自独立的模块组成，这些模块独自对其的功能负责

对这些模块的设置， `Elasticsearch` 提供了两种方法：

1、静态设置
    
    静态设置主要是在启动 Elasticsearch 之前，在配置文件 `elasticsearch.yml` 中配置这些模块
    
    静态配置需要更新群集中的所有关注节点来启用这些设置所做的更改
2、动态配置
    
    可以在 Elasticsearch 运行过程中动态的更改这些配置项

本章节接下来的部分将讨论和介绍各个不同的模块，和允许的配置项

## 集群级别的路由和分片分配 ##

集群级别设置用于控制分配给不同节点的分片和重新平衡时的节点再分配

下面列出的这些选项用于精细的控制分片的分配

### 集群级别的分片分配配置项 ###

1、**cluster.routing.allocation.enable**
    
    是否启用分配器，默认值有四个
    
    <table> 
     <thead> 
      <tr> 
       <th align="left">值</th> 
       <th align="left">说明</th> 
      </tr> 
     </thead> 
     <tbody> 
      <tr> 
       <td align="left">all</td> 
       <td align="left">默认值，允许在任意分片上启用分配器</td> 
      </tr> 
      <tr> 
       <td align="left">primaries</td> 
       <td align="left">只允许在主分片上启用分配器</td> 
      </tr> 
      <tr> 
       <td align="left">new_primaries</td> 
       <td align="left">只允许在新索引的主分片上启用分配器</td> 
      </tr> 
      <tr> 
       <td align="left">none</td> 
       <td align="left">不允许使用任何分片分配器</td> 
      </tr> 
     </tbody> 
    </table>
2、**cluster.routing.allocation.node\_concurrent\_recoveries**
    
    数值型，默认为 2，用于限制分片恢复时的并发数
3、**cluster.routing.allocation.node\_initial\_primaries\_recoveries**
    
    数值型，默认为 4，用于限制主要分片恢复时的开始并发数
4、**cluster.routing.allocation.same\_shard.host**
    
    布尔值，默认为 `false`，用于限制同一物理节点中的同一分片的副本分片是否可以多于 1 个
5、**indices.recovery.concurrent\_streams**
    
    数值型，默认为 2 ，从对等网络恢复分片时，用于控制每个节点的开放网络流的数量
6、**indices.recovery.concurrent\_small\_file\_streams**
    
    数值型，默认为 2，分片恢复时用于控制小于 5MB 的节点的打开流数量
7、**cluster.routing.rebalance.enable**
    
    是否启用重新平衡，可选值有三个
    
    <table> 
     <thead> 
      <tr> 
       <th align="left">值</th> 
       <th align="left">说明</th> 
      </tr> 
     </thead> 
     <tbody> 
      <tr> 
       <td align="left">all</td> 
       <td align="left">默认值，允许在任意类型的分片上执行平衡</td> 
      </tr> 
      <tr> 
       <td align="left">primaries</td> 
       <td align="left">只允许在主分片上执行分片平衡</td> 
      </tr> 
      <tr> 
       <td align="left">replicas</td> 
       <td align="left">只允许在副本分片上执行分片平衡</td> 
      </tr> 
      <tr> 
       <td align="left">none</td> 
       <td align="left">不允许执行任何分片平衡</td> 
      </tr> 
     </tbody> 
    </table>
8、**cluster.routing.allocation.allow\_rebalance**
    
    用于设置是否允许集群重新平衡，可选值有三个
    
    <table> 
     <thead> 
      <tr> 
       <th align="left">值</th> 
       <th align="left">说明</th> 
      </tr> 
     </thead> 
     <tbody> 
      <tr> 
       <td align="left">always</td> 
       <td align="left">默认值，始终允许集群重新平衡</td> 
      </tr> 
      <tr> 
       <td align="left">indices_primaries_active</td> 
       <td align="left">重新分配集群的主分片时允许重新平衡</td> 
      </tr> 
      <tr> 
       <td align="left">indices_all_active</td> 
       <td align="left">重新分配集群中的主分片和副分片时允许重新平衡</td> 
      </tr> 
     </tbody> 
    </table>
9、**cluster.routing.allocation.cluster\_concurrent\_rebalance**
    
    数值，默认为 2， 用于设置集群中并发平衡分片的数量
10. **cluster.routing.allocation.balance.shard**
    
    浮点值，默认为 `0.45f`，用于设置每个节点上分配分片的权重因子
11. **cluster.routing.allocation.balance.index**
    
    浮点值，默认为 `0.55f`，用于设置特定节点上分配每个索引时的分片数量比率
12. **cluster.routing.allocation.balance.threshold**
    
    非负浮点值，默认为 `1.0f`，用于设置执行操作的最小优化值

### 磁盘级别的分片的分配配置项 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">配置项</th> 
   <th align="left">数据类型</th> 
   <th align="left">默认值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">cluster.routing.allocation.disk.threshold_enabled</td> 
   <td align="left">boolean</td> 
   <td align="left">true</td> 
   <td align="left">禁用或启用磁盘分配决定器</td> 
  </tr> 
  <tr> 
   <td align="left">cluster.routing.allocation.disk.watermark.low</td> 
   <td align="left">string</td> 
   <td align="left">85%</td> 
   <td align="left">磁盘的最大使用量<br>超过此值，该磁盘上将不再分配任何分片</td> 
  </tr> 
  <tr> 
   <td align="left">cluster.routing.allocation.disk.watermark.high</td> 
   <td align="left">string</td> 
   <td align="left">90%</td> 
   <td align="left">分配值的最大使用量<br>如果分配时超过此值，那么 Elasticsearch 会将新分片分配到其它磁盘上</td> 
  </tr> 
  <tr> 
   <td align="left">cluster.info.update.interval</td> 
   <td align="left">string</td> 
   <td align="left">30s</td> 
   <td align="left">磁盘使用量检查时间间隔</td> 
  </tr> 
  <tr> 
   <td align="left">cluster.routing.allocation.disk.include_relocations</td> 
   <td align="left">boolean</td> 
   <td align="left">true</td> 
   <td align="left">设置计算磁盘使用情况时是否考虑当前正在分配的分片</td> 
  </tr> 
 </tbody> 
</table>

## 发现模块 ( Discovery ) ##

此模块可以帮助集群发现和维护其中所有节点的状态

从集群添加或删除节点时，群集状态会变更

集群名称用于设置用于创建不同集群之间的逻辑差异

下面这些模块可以帮助我们使用云供应商提供的 API

1、Azure 发现服务
2、EC2 发现服务
3、Google compute engine 发现服务
4、Zen 发现服务

## 网关模块 ( Gateway ) ##

此模块用于整个集群重新启动时维护集群状态和分片数据

提供了以下选项用于定制集群网关服务

<table> 
 <thead> 
  <tr> 
   <th align="left">配置项</th> 
   <th align="left">数据类型</th> 
   <th align="left">默认值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">gateway.expected_ nodes</td> 
   <td align="left">numeric</td> 
   <td align="left">0</td> 
   <td align="left">恢复本地分片时的预计的集群中的节点数量</td> 
  </tr> 
  <tr> 
   <td align="left">gateway.expected_ master_nodes</td> 
   <td align="left">numeric</td> 
   <td align="left">0</td> 
   <td align="left">开始恢复前预计的集群中主节点的数量</td> 
  </tr> 
  <tr> 
   <td align="left">gateway.expected_ data_nodes</td> 
   <td align="left">numeric</td> 
   <td align="left">0</td> 
   <td align="left">开始恢复前预计的数据节点的数量</td> 
  </tr> 
  <tr> 
   <td align="left">gateway.recover_ after_time</td> 
   <td align="left">String</td> 
   <td align="left">5m</td> 
   <td align="left">用于指定恢复进程启动前的等待时间<br>如果设置了此参数，那么就会忽略加入集群的节点数量</td> 
  </tr> 
  <tr> 
   <td align="left">gateway.recover_ after_nodes</td> 
   <td align="left">numeric</td> 
   <td align="left"></td> 
   <td align="left">用于指定恢复进程启动前的加入节点的数量</td> 
  </tr> 
  <tr> 
   <td align="left">gateway.recover_after_ master_nodes</td> 
   <td align="left">numeric</td> 
   <td align="left"></td> 
   <td align="left">用于指定恢复进程启动前的加入的主节点的数量</td> 
  </tr> 
  <tr> 
   <td align="left">gateway.recover_after_ data_nodes</td> 
   <td align="left">numeric</td> 
   <td align="left"></td> 
   <td align="left">用于指定恢复进程启动前的加入的数据节点的数量</td> 
  </tr> 
 </tbody> 
</table>

## HTTP 模块 ##

该模块主要用于管理 HTTP 客户端和 Elasticsearch API 之间的通信

可以将配置 `http.enabled` 的值更改为 `false` 来禁用此模块

下表列出的选项用于定制 HTTP 模块 ( 主要在 `config/elasticsearch.yml` 中配置 )

<table> 
 <thead> 
  <tr> 
   <th align="left">配置项</th> 
   <th align="left">默认值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">http.port</td> 
   <td align="left">9200</td> 
   <td align="left">Elasticsearch RESTFul API 的端口号，默认范围为：9200-9300</td> 
  </tr> 
  <tr> 
   <td align="left">http.publish_port</td> 
   <td align="left"></td> 
   <td align="left">HTTP 客户端的端口号，对于防火墙设置很有用</td> 
  </tr> 
  <tr> 
   <td align="left">http.bind_host</td> 
   <td align="left"></td> 
   <td align="left">HTTP 服务器端的地址</td> 
  </tr> 
  <tr> 
   <td align="left">http.publish_host</td> 
   <td align="left"></td> 
   <td align="left">HTTP 客户端的地址</td> 
  </tr> 
  <tr> 
   <td align="left">http.max_content_length</td> 
   <td align="left">100mb</td> 
   <td align="left">设置请求消息体内容长度的最大值</td> 
  </tr> 
  <tr> 
   <td align="left">http.max_initial_line_length</td> 
   <td align="left">4kb</td> 
   <td align="left">URL 的最大长度</td> 
  </tr> 
  <tr> 
   <td align="left">http.max_header_size</td> 
   <td align="left">8kb</td> 
   <td align="left">设置 HTTP 头部数据允许的最大值</td> 
  </tr> 
  <tr> 
   <td align="left">http.compression</td> 
   <td align="left">false</td> 
   <td align="left">是否启用压缩功能</td> 
  </tr> 
  <tr> 
   <td align="left">http.pipelinig</td> 
   <td align="left"></td> 
   <td align="left">是否启用 HTTP 多路复用功能</td> 
  </tr> 
  <tr> 
   <td align="left">http.pipelining.max_events</td> 
   <td align="left"></td> 
   <td align="left">用于限制关闭 HTTP 请求之前的请求处理数量</td> 
  </tr> 
 </tbody> 
</table>

## 索引模块 ( Indices ) ##

该模块用于维护每个索引的全局配置

下面罗列的配置主要都是和内存使用相关的

### 断路器 ( Circuit Breaker ) ###

该配置主要用于限制 `JVM` 堆的大小，以防止 `OutOfMemroyError`

可以配置的选项有

```
indices.breaker.total.limit
```

默认为 JVM 堆的 70％

### 字段数据缓存 ###

该缓存主要用于存储字段上的统计信息，建议给它分配足够的内存

可以使用下面的选项来定制字段数据缓存的大小

```
indices.fielddata.cache.size
```

### 节点查询缓存 ###

该缓存用于缓存查询结果，采用最近最少使用策略 ( `LRU` )

可以使用以下配置来定制节点查询缓存的大小

```
indices.queries.cahce.size
```

### 索引缓冲区 ( Indexing Buffer ) ###

索引缓冲区用于存储索引中新创建的文档，而且会在缓冲区满时删除就的文档

可以使用以下配置来定制索引缓冲区的大小

```
indices.memory.index_buffer_size
```

### 分片请求缓存 ( Shard Request Cache ) ###

该缓存用于存储每个分片的本地搜索数据

可以在创建索引期间启用缓存，也可以在任意请求过程中通过 URL 参数禁用缓存

1、禁用缓存参数
    
```
    ?request_cache = true
```
2、启用缓存配置
    
```
    index.requests.cache.enable": true
```

### 索引恢复 ( Indices Recovery ) ###

在索引恢复的是，可以使用下表列出的选项类定制恢复的行为

<table> 
 <thead> 
  <tr> 
   <th align="left">选项</th> 
   <th align="left">默认值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">indices.recovery.concurrent_streams</td> 
   <td align="left">3</td> 
   <td align="left">最大进程数量</td> 
  </tr> 
  <tr> 
   <td align="left">indices.recovery.concurrent_small_file_streams</td> 
   <td align="left">2</td> 
   <td align="left">最小进程数量</td> 
  </tr> 
  <tr> 
   <td align="left">indices.recovery.file_chunk_size</td> 
   <td align="left">512kb</td> 
   <td align="left">文件分割大小，当文件达到此值将会新建一个文件</td> 
  </tr> 
  <tr> 
   <td align="left">indices.recovery.translog_ops</td> 
   <td align="left">1000</td> 
   <td align="left">转换条数最大值</td> 
  </tr> 
  <tr> 
   <td align="left">indices.recovery.translog_size</td> 
   <td align="left">512kb</td> 
   <td align="left">转换日志文件最大值</td> 
  </tr> 
  <tr> 
   <td align="left">indices.recovery.compress</td> 
   <td align="left">true</td> 
   <td align="left">是否启用压缩</td> 
  </tr> 
  <tr> 
   <td align="left">indices.recovery.max_bytes_per_sec</td> 
   <td align="left">40mb</td> 
   <td align="left">没秒最大恢复数据</td> 
  </tr> 
 </tbody> 
</table>

### TTL 时间间隔 ( TTL Interval ) ###

TTL 是 `Time To Live` 的缩写，中文译为生存时间或过期时间，用于定义文档的过期时间，过期之后文档将被删除

> 其实还是推荐译为 "生存时间" 的，虽然主要目的是标记什么时候无效，但对于缓存系统来说，标记什么时候有效才是最重要的

可以使用下表列出的选项来控制生存时间

<table> 
 <thead> 
  <tr> 
   <th align="left">选项</th> 
   <th align="left">默认值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">indices.ttl.interval</td> 
   <td align="left">60s</td> 
   <td align="left">索引生存时间</td> 
  </tr> 
  <tr> 
   <td align="left">indices.ttl.bulk_size</td> 
   <td align="left">1000</td> 
   <td align="left">存放索引生存时间桶的大小</td> 
  </tr> 
 </tbody> 
</table>

## 节点 ( Node ) ##

每个节点都有一个选项 `data` 来设置该节点是否为数据节点

我们可以修改 `node.data` 属性，比如设置为 `false` 来表示该节点并非数据节点

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
