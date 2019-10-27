`MongoDB` **mongorestore** 脚本命令可以用来恢复备份的数据

### 语法 ###

`MongoDB` **mongorestore** 命令脚本语法如下

```
$ mongorestore -h <hostname><:port> -d dbname <path>
```

### 参数说明 ###

 *  **-h <hostname><:port>, -h<:port>**
    
    MongoDB 所在服务器地址，默认为 localhost:27017
 *  **--db, -d**
    
    需要恢复的数据库实例  
    例如：test，这个名称也可以和备份时候的不一样，比如 test2
 *  \--drop
    
    设置恢复的时候，先删除当前数据，然后恢复备份的数据 就是说，恢复后，备份后添加修改的数据都会被删除
    
    > 慎用
 *  <path>
    
    mongorestore 最后的一个参数，设置备份数据所在位置，例如：/mnt/data/backup/mongodb
    
    不能同时指定 <path> 和 --dir 选项
 *  **--dir**
    
    指定备份的目录
    
    不能同时指定 <path> 和 --dir 选项

### 范例 ###

现在，我们使用刚刚备份的数据来恢复 `MongoDB` 数据库

```
>mongorestore
```

执行以上命令输出结果如下

```
$ mongorestore 
2017-10-24T07:28:58.400+0800    using default 'dump' directory
2017-10-24T07:28:58.401+0800    preparing collections to restore from
2017-10-24T07:28:58.407+0800    reading metadata for gridfs.fs.chunks from dump/gridfs/fs.chunks.metadata.json
2017-10-24T07:28:58.407+0800    reading metadata for rbtj.rb_visit_log from dump/rbtj/rb_visit_log.metadata.json
2017-10-24T07:28:58.408+0800    restoring gridfs.fs.chunks from dump/gridfs/fs.chunks.bson
2017-10-24T07:28:58.408+0800    restoring rbtj.rb_visit_log from dump/rbtj/rb_visit_log.bson
2017-10-24T07:28:58.410+0800    reading metadata for shandai.s_order_log from dump/shandai/s_order_log.metadata.json
2017-10-24T07:28:58.412+0800    restoring shandai.s_order_log from dump/shandai/s_order_log.bson
2017-10-24T07:28:58.414+0800    reading metadata for nodebb.objects from dump/nodebb/objects.metadata.json
2017-10-24T07:28:58.414+0800    restoring nodebb.objects from dump/nodebb/objects.bson

....
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")