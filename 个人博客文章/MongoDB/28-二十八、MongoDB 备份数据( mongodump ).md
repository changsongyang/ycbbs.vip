文章永久连接：[https://tech.souyunku.com/?p=3687](https://tech.souyunku.com/?p=3687)
MongoDB **mongodump** 脚本命令可以用来备份 **MongoDB** 数据

MongoDB mongodump 脚本命令可以导出所有数据到指定目录中

### 语法 ###

MongoDB mongodump 脚本命令语法如下：

```
mongodump -h dbhost -d dbname -o dbdirectory
```

### 参数说明 ###

 *  **-h：** 需要导出 MongDB 数据所在的服务器地址  
    例如 127.0.0.1 ，当然也可以同时指定端口号：127.0.0.1:27017
 *  **-d：** 需要备份的数据库，例 test
 *  **-o：** 备份的数据存放位置，例如：/mnt/data/backup/mongodb/  
    该目录需要提前建立，在备份完成后，系统自动在 mongodb 目录下建立一个 test 目录，这个目录里面存放该数据库实例的备份数据

### mongodump 命令可选参数 ###

1、  **mongodump --host HOST\_NAME --port PORT\_NUMBER**
    
    该命令将备份所有 MongoDB 数据
    
```
    mongodump --host db1.souyunku.cn --port 27017
```
2、  **mongodump --dbpath DB\_PATH --out BACKUP\_DIRECTORY**
    
    该命令备份指定的 DB\_PATH 数据库到 BACKUP\_DIRECTORY 目录
    
```
    mongodump --dbpath /data/db/ --out /data/backup/
```
3、  **mongodump --collection COLLECTION --db DB\_NAME**
    
    该命令将备份指定数据库 DB\_NAME 的 COLLECTION 集合
    
```
    mongodump --collection lession --db test
```

### 范例 ###

1、  首先使用 --port 27017 启动 MongoDB 服务
2、  打开命令提示符窗口，输入命令 **mongodump**  
      
    
    
```
    $ mongodump
```
    
    执行以上命令后，客户端会连接到 ip 为 127.0.0.1 端口号为 27017 的 MongoDB 服务上，然后备份所有数据到 bin/dump/ 目录中
    
    命令输出结果如下
    
```
    $ mongodump
    2017-10-24T07:11:25.229+0800    writing admin.system.indexes to 
    2017-10-24T07:11:25.236+0800    done dumping admin.system.indexes (3 documents)
    2017-10-24T07:11:25.236+0800    writing admin.system.users to 
    2017-10-24T07:11:25.242+0800    done dumping admin.system.users (1 document)
    2017-10-24T07:11:25.242+0800    writing admin.system.version to 
    2017-10-24T07:11:25.244+0800    done dumping admin.system.version (1 document)
    2017-10-24T07:11:25.244+0800    writing nodebb.objects to 
    2017-10-24T07:11:25.244+0800    writing shandai.s_order_log to 
    2017-10-24T07:11:25.244+0800    writing rbtj.rb_visit_log to 
    2017-10-24T07:11:25.244+0800    writing gridfs.fs.chunks to 
    2017-10-24T07:11:25.251+0800    done dumping rbtj.rb_visit_log (92 documents)
    2017-10-24T07:11:25.251+0800    writing log.pushlog to 
    2017-10-24T07:11:25.251+0800    done dumping shandai.s_order_log (105 documents)
    2017-10-24T07:11:25.251+0800    writing test.lession to 
    2017-10-24T07:11:25.252+0800    done dumping test.lession (3 documents)
    2017-10-24T07:11:25.252+0800    writing souyunku.language to 
    2017-10-24T07:11:25.253+0800    done dumping log.pushlog (8 documents)
    2017-10-24T07:11:25.253+0800    writing souyunku.col to 
    2017-10-24T07:11:25.255+0800    done dumping nodebb.objects (327 documents)
    2017-10-24T07:11:25.255+0800    writing souyunku.products to 
    2017-10-24T07:11:25.255+0800    done dumping souyunku.language (2 documents)
    2017-10-24T07:11:25.255+0800    writing gridfs.fs.files to 
    2017-10-24T07:11:25.257+0800    done dumping souyunku.col (1 document)
    2017-10-24T07:11:25.257+0800    writing test.col to 
    2017-10-24T07:11:25.257+0800    done dumping souyunku.products (1 document)
    2017-10-24T07:11:25.257+0800    writing souyunku.counters to 
    2017-10-24T07:11:25.258+0800    done dumping gridfs.fs.files (1 document)
    2017-10-24T07:11:25.258+0800    writing souyunku.mycapped_log to 
    2017-10-24T07:11:25.259+0800    done dumping souyunku.counters (1 document)
    2017-10-24T07:11:25.259+0800    writing rrs_result.content to 
    2017-10-24T07:11:25.259+0800    done dumping test.col (1 document)
    2017-10-24T07:11:25.259+0800    writing nodebb.sessions to 
    2017-10-24T07:11:25.260+0800    done dumping souyunku.mycapped_log (0 documents)
    2017-10-24T07:11:25.260+0800    writing souyunku.lession to 
    2017-10-24T07:11:25.260+0800    done dumping rrs_result.content (0 documents)
    2017-10-24T07:11:25.261+0800    done dumping nodebb.sessions (0 documents)
    2017-10-24T07:11:25.261+0800    done dumping souyunku.lession (0 documents)
    2017-10-24T07:11:25.312+0800    done dumping gridfs.fs.chunks (19 documents)
```


## 干货推荐

[本站推荐：精选优质专栏](https://tech.souyunku.com/?page_id=2)

## 附录：MongoDB 教程：系列文章

- [一、MongoDB 基础教程](https://tech.souyunku.com/?p=3633)
- [二、NoSQL 简介](https://tech.souyunku.com/?p=3635)
- [三、什么是 MongoDB ?](https://tech.souyunku.com/?p=3637)
- [四、Windows 平台安装 MongoDB](https://tech.souyunku.com/?p=3639)
- [五、Linux 平台安装 MongoDB](https://tech.souyunku.com/?p=3641)
- [六、Mac OSX 平台安装 MongoDB](https://tech.souyunku.com/?p=3643)
- [七、MongoDB 术语](https://tech.souyunku.com/?p=3645)
- [八、MongoDB -连接](https://tech.souyunku.com/?p=3647)
- [九、MongoDB 创建数据库](https://tech.souyunku.com/?p=3649)
- [十、MongoDB 切换数据库](https://tech.souyunku.com/?p=3651)
- [十一、MongoDB 删除数据库](https://tech.souyunku.com/?p=3653)
- [十二、MongoDB 备份数据( mongodump )](https://tech.souyunku.com/?p=3655)
- [十三、MongoDB 插入文档](https://tech.souyunku.com/?p=3657)
- [十四、MongoDB 更新文档](https://tech.souyunku.com/?p=3659)
- [十五、MongoDB 删除文档](https://tech.souyunku.com/?p=3661)
- [十六、MongoDB 删除集合](https://tech.souyunku.com/?p=3663)
- [十七、MongoDB 查询文档](https://tech.souyunku.com/?p=3665)
- [十八、MongoDB 条件操作符](https://tech.souyunku.com/?p=3667)
- [十九、MongoDB $type操作符](https://tech.souyunku.com/?p=3669)
- [二十、MongoDB 限制条数 (limit 方法)](https://tech.souyunku.com/?p=3671)
- [二十一、MongoDB 跳过 (skip 方法)](https://tech.souyunku.com/?p=3673)
- [二十二、MongoDB 排序](https://tech.souyunku.com/?p=3675)
- [二十三、MongoDB 索引](https://tech.souyunku.com/?p=3677)
- [二十四、MongoDB 聚合运算( aggregate )](https://tech.souyunku.com/?p=3679)
- [二十五、MongoDB 聚合运算 - 管道](https://tech.souyunku.com/?p=3681)
- [二十六、MongoDB 副本集群复制](https://tech.souyunku.com/?p=3683)
- [二十七、MongoDB 分片集群技术](https://tech.souyunku.com/?p=3685)
- <a style="font-size: 18px;color: #FF0000; font-weight: 600;" href="https://tech.souyunku.com/?p=3687">【当前读到】二十八、MongoDB 备份数据( mongodump )</a>
- [二十九、MongoDB 恢复数据( mongorestore )](https://tech.souyunku.com/?p=3689)
- [三十、MongoDB 性能跟踪 ( mongotop )](https://tech.souyunku.com/?p=3691)
- [三十一、MongoDB 状态检测 ( mongostat )](https://tech.souyunku.com/?p=3693)
- [三十二、MongoDB Java](https://tech.souyunku.com/?p=3695)
- [三十三、MongoDB PHP 扩展](https://tech.souyunku.com/?p=3697)
- [三十四、MongoDB PHP](https://tech.souyunku.com/?p=3699)
- [三十五、PHP7 MongDB 扩展安装与使用](https://tech.souyunku.com/?p=3701)
- [三十六、MongoDB 关系](https://tech.souyunku.com/?p=3703)
- [三十七、MongoDB 数据库引用](https://tech.souyunku.com/?p=3705)
- [三十八、MongoDB 覆盖索引查询](https://tech.souyunku.com/?p=3707)
- [三十九、MongoDB 查询分析](https://tech.souyunku.com/?p=3709)
- [四十、MongoDB 原子操作](https://tech.souyunku.com/?p=3711)
- [四十一、MongoDB 高级索引](https://tech.souyunku.com/?p=3713)
- [四十二、MongoDB 索引限制](https://tech.souyunku.com/?p=3715)
- [四十三、MongoDB ObjectId](https://tech.souyunku.com/?p=3717)
- [四十四、MongoDB Map Reduce](https://tech.souyunku.com/?p=3719)
- [四十五、MongoDB 全文检索](https://tech.souyunku.com/?p=3721)
- [四十六、MongoDB 正则表达式](https://tech.souyunku.com/?p=3723)
- [四十七、MongoDB 管理工具: Rockmongo](https://tech.souyunku.com/?p=3725)
- [四十八、MongoDB GridFS](https://tech.souyunku.com/?p=3727)
- [四十九、MongoDB 固定集合（Capped Collections）](https://tech.souyunku.com/?p=3729)
- [五十、MongoDB 自增 ID](https://tech.souyunku.com/?p=3731)
