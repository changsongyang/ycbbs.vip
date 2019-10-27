任何系统或软件，当需要升级到更新版本时，都会需要按照几个步骤来维护应用程序设置，配置，数据和其他内容

这些步骤是使应用程序在新系统中稳定或维护数据完整性 ( 防止数据损坏 ) 所必需的

以下是升级 `Elasticsearch` 的几个步骤

1、阅读 [https://www.elastic.co/][https_www.elastic.co] 发布的重大更改文档
2、在非生产环境中测试升级版本，如 `UAT`，`E2E`，`SIT` 或 `DEV` 环境
3、建议在升级到更高版本之前进行数据备份，因为如果没有数据备份，则无法回滚到之前的 `Elasticsearch` 版本
4、可以使用 **完整集群重新启动** 或 **滚动升级** 进行升级操作。 滚动升级适用于新版本 ( 适用于 `2.x` 和更新版本 )。 当使用滚动升级方法迁移时，服务不会中断
5、在迁移之前进行数据备份，并按照说明执行备份过程。 快照和还原模块可用于进行备份。 此模块用于拍摄索引或完整集群的快照，并可存储在远程存储库中

## 快照和还原模块 ##

开始着手备份之前，先需要在 `Elasticsearch` 中注册快照服务

```
PUT /_snapshot/backup1
{
   "type": "fs", "settings": {
      ... repository settings ...
   }
}
```

以上文本是对 `http://localhost:9200/_snapshot/backup1` 的 `HTTP` `PUT` 请求

你可以使用自己的远程服务器的 IP 地址而不是 `localhost`

而其余的文本是请求正文

我们使用共享文件系统 ( 类型:`fs` ) 进行备份时需要在每个主节点和数据节点中注册

我们只需要添加具有备份存储库路径的 `path.repo` 变量作为值

添加存储库路径后，需要重新启动节点，然后通过执行以下命令执行注册

```
PUT http://localhost:9200/_snapshot/backup1
{
   "type": "fs", "settings": {
      "location": "/mount/backups/backup1", "compress": true
   }
}
```

## 完整集群重启 ##

完整集群重启升级包含以下步骤

1、禁用分片分配并关闭节点
    
```
    PUT http://localhost:9200/_cluster/settings
    {
        "persistent": {
            "cluster.routing.allocation.enable": "none"
        }
    }
```
    
    比如将 `0.90.x` 升级到 `1.x` ，可以使用以下请求
    
```
    PUT http://localhost:9200/_cluster/settings
    {
       "persistent": {
            "cluster.routing.allocation.disable_allocation": false,
            "cluster.routing.allocation.enable": "none"
       }
    }
```
2、对 `Elasticsearch` 进行同步刷新
    
```
    POST http://localhost:9200/_flush/synced
```
3、在所有节点上，杀掉所有 `Elasticsearch` 弹性服务
4、在所有节点上执行以下操作
    
    1.  如果节点部署在 `Debian` 或 `RedHat` 系统上
        
        使用 `rmp` 或 `dpkg` 通过安装新软件包来升级节点
        
        注意不要覆盖配置文件
    2.  在 Windows ( zip ) 或 UNIX ( tar ) 系统上
        
        提取新版本而不覆盖 config 目录
        
        我们可以从旧的安装中复制 `config` 目录，或更改新的安装的 `path.conf` 或 `path.data` 配置项
5、从集群中的主节点 ( `node.master` 设置为 `true` 且 `node.data` 设置为 `false` 的节点 ) 开始重新启动节点
    
    等待一段时间来建立一个集群
    
    这段时间内可以通过监视日志或使用以下请求进行检查
    
```
    GET http://localhost:9200/_cat/health
    GET http://localhost:9200/_cat/health
```
6、发起 `GET _cat/health` 请求监视群集的创建进程，并等待响应中出现 `yellow` 关键字
    
    当出现 `yellow` 关键字时响应内容如下
    
```
    1451295971 17:46:11 elasticsearch yellow 1 1 5 5 0 0 5 0 - 50.0%
```
7、通过发起以下请求启用在步骤 1 中禁用的分片分配进程
    
```
    PUT http://localhost:9200/_cluster/settings
    {
       "persistent": {
          "cluster.routing.allocation.enable": "all"
       }
    }
```
8、如果将版本从 `0.90.x` 升级到 `1.x`，请使用下面的请求
    
```
    PUT http://localhost:9200/_cluster/settings
    {
       "persistent": {
          "cluster.routing.allocation.disable_allocation": true,
          "cluster.routing.allocation.enable": "all"
       }
    }
```

## 滚动升级 ##

滚动升级的步骤与完整集群重启一样，除了步骤 `3` 以外

此处，我们需要停止一个节点并进行升级

升级后，重新启动节点并为所有节点重复这些节点

启用分片分配过程后，可以通过以下请求监视它

```
GET http://localhost:9200/_cat/recovery
```


[https_www.elastic.co]: https://www.elastic.co/

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")