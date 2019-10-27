`MongoDB` **mongodump** 脚本命令可以用来备份 **MongoDB** 数据

`MongoDB` `mongodump` 脚本命令可以导出所有数据到指定目录中

### 语法 ###

`MongoDB` `mongodump` 脚本命令语法如下：

```
mongodump -h dbhost -d dbname -o dbdirectory
```

### 参数说明 ###

```html
*  **-h：** 需要导出 MongDB 数据所在的服务器地址  
    例如 127.0.0.1 ，当然也可以同时指定端口号：127.0.0.1:27017
 *  **-d：** 需要备份的数据库，例 test
 *  **-o：** 备份的数据存放位置，例如：/mnt/data/backup/mongodb/  
    该目录需要提前建立，在备份完成后，系统自动在 mongodb 目录下建立一个 test 目录，这个目录里面存放该数据库实例的备份数据
```

### mongodump 命令可选参数 ###

1、  **mongodump --host HOST\_NAME --port PORT\_NUMBER**
    
    该命令将备份所有 MongoDB 数据
    
```
    mongodump --host db1.ycbbs.cn --port 27017
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

1、  首先使用` --port 27017` 启动 `MongoDB` 服务
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
    2017-10-24T07:11:25.252+0800    writing ycbbs.language to 
    2017-10-24T07:11:25.253+0800    done dumping log.pushlog (8 documents)
    2017-10-24T07:11:25.253+0800    writing ycbbs.col to 
    2017-10-24T07:11:25.255+0800    done dumping nodebb.objects (327 documents)
    2017-10-24T07:11:25.255+0800    writing ycbbs.products to 
    2017-10-24T07:11:25.255+0800    done dumping ycbbs.language (2 documents)
    2017-10-24T07:11:25.255+0800    writing gridfs.fs.files to 
    2017-10-24T07:11:25.257+0800    done dumping ycbbs.col (1 document)
    2017-10-24T07:11:25.257+0800    writing test.col to 
    2017-10-24T07:11:25.257+0800    done dumping ycbbs.products (1 document)
    2017-10-24T07:11:25.257+0800    writing ycbbs.counters to 
    2017-10-24T07:11:25.258+0800    done dumping gridfs.fs.files (1 document)
    2017-10-24T07:11:25.258+0800    writing ycbbs.mycapped_log to 
    2017-10-24T07:11:25.259+0800    done dumping ycbbs.counters (1 document)
    2017-10-24T07:11:25.259+0800    writing rrs_result.content to 
    2017-10-24T07:11:25.259+0800    done dumping test.col (1 document)
    2017-10-24T07:11:25.259+0800    writing nodebb.sessions to 
    2017-10-24T07:11:25.260+0800    done dumping ycbbs.mycapped_log (0 documents)
    2017-10-24T07:11:25.260+0800    writing ycbbs.lession to 
    2017-10-24T07:11:25.260+0800    done dumping rrs_result.content (0 documents)
    2017-10-24T07:11:25.261+0800    done dumping nodebb.sessions (0 documents)
    2017-10-24T07:11:25.261+0800    done dumping ycbbs.lession (0 documents)
    2017-10-24T07:11:25.312+0800    done dumping gridfs.fs.chunks (19 documents)
```

--------------------

## MongoDB数据恢复 ##

`mongodb` 使用 `mongorestore` 命令来恢复备份的数据。

### 语法 ###

`mongorestore`命令脚本语法如下：

```
>mongorestore -h <hostname><:port> -d dbname <path>
```

 *  **--host<:port>, -h<:port>：** MongoDB所在服务器地址，默认为： localhost:27017
 *  **--db, -d：** 需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2
 *  **--drop：** 恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！
 *  **：**  mongorestore 最后的一个参数，设置备份数据所在位置，例如：c:\\data\\dump\\test。

你不能同时指定  和 --dir 选项，--dir也可以设置备份目录。

 *  **--dir：** 指定备份的目录

你不能同时指定  和 `--dir` 选项。

接下来我们执行以下命令:

```
>mongorestore
```

执行以上命令输出结果如下：

![MongoDB数据恢复][MongoDB]

[MongoDB]: http://tech.souyunku.com/wp-content/uploads/2013/12/mongorestore.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")

