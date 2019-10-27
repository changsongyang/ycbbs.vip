## 引言
`PHP` 语言可是使用 `mongo.s`o ( `Windows` 下是 `mongo.dll` ) 扩展访问 `MongoDB` 数据库

`MongoDB` `PHP` 在各平台上的安装及驱动包下载请查看: `PHP` 安装 `MongoDB` 扩展驱动

如果你使用的是 `PHP7`，请移步： `PHP7` `MongoDB` 安装与使用

## PHP 连接 MongoDB 和 选择一个数据库 ##

为了确保正确连接，我们需要指定数据库名，如果数据库在 `mongoDB` 中不存在， `mongoDB` 会自动创建

```
<?php


/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$m = new MongoClient(); // 连接默认主机和端口为：mongodb://localhost:27017
$db = $m->ycbbs;         // 切换到 "ycbbs" 数据库
```

## PHP MongoDB 创建集合 ##

`PHP` `MongoDB` 创建创建集合可以使用下面的代码

```
<?php


/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$m          = new MongoClient();                // 连接
$db         = $m->ycbbs;                         // 切换到 "ycbbs" 数据库
$c_lession  = $db->createCollection("lession"); // 创建 lession 集合

echo "集合创建成功\n";
```

运行以上 `PHP` 脚本，输出结果如下：

```
$ php main.php
集合创建成功
```

## PHP MongoDB 插入文档 ##

可以使用 **insert()** 方法向 `lession` 集合中插入文档

```
<?php


/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$m          = new MongoClient();                // 连接
$db         = $m->ycbbs;                         // 切换到 "ycbbs" 数据库
$collection = $db->lession;                     // 选择集合

$document = array
( 
    "title"     => "MongoDB 基础教程", 
    "favorite"  => 1580000,
    "url"       => "https://www.ycbbs.vip/l/penglei/mongodb/",
    "by"        => "penglei"
);

$collection->insert($document);

echo "数据插入成功\n";
```

运行以上 `PHP` 脚本，输出结果如下：

```
$ php main.php 
数据插入成功
```

然后我们在 `mongo` 客户端使用 `db.lession.find().pretty();` 命令查看数据

```
> db.lession.find().pretty()
{
    "_id" : ObjectId("59eee84c3f8c88a4f10041a7"),
    "title" : "MongoDB 基础教程",
    "favorite" : NumberLong(1580000),
    "url" : "https://www.ycbbs.vip/l/penglei/mongodb/",
    "by" : "penglei"
}
```

## PHP MongoDB 查找文档 ##

可以使用 **find()** 方法查询 `lession` 集合中的文档

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

$m          = new MongoClient();                // 连接
$db         = $m->ycbbs;                         // 切换到 "ycbbs" 数据库
$collection = $db->lession;                     // 选择集合
$cursor     = $collection->find();

// 迭代显示文档标题
foreach( $cursor as $document )
{
    echo $document["title"],"\n";
}

echo "\n";
```

运行以上 `PHP` 脚本，输出结果如下：

```
$ php main.php
MongoDB 基础教程
```

## PHP MongoDB 更新文档 ##

可以使用 **update()** 方法来更新 `lession` 集合中的文档

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

$m          = new MongoClient();                // 连接
$db         = $m->ycbbs;                         // 切换到 "ycbbs" 数据库
$collection = $db->lession;                     // 选择集合
$cursor     = $collection->find();

$collection->update(array("title"=>"MongoDB 基础教程"), array('$set'=>array("title"=>"MongoDB 教程")));
// 显示更新后的文档
$cursor = $collection->find();
// 循环显示文档标题
foreach( $cursor as $document)
{
    echo $document["title"],"\n";
}

echo "\n";
```

运行以上 `PHP` 脚本，输出结果如下：

```
$ php main.php
MongoDB 教程
```

然后我们在 `mongo` 客户端使用 **db.ycbbs.find().pretty();** 命令查看数据：

![Image 1][]

## PHP MongoDB 删除文档 ##

可以使用 **remove()** 方法来删除 `lession` 集合中的文档

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

$m          = new MongoClient();                // 连接
$db         = $m->ycbbs;                         // 切换到 "ycbbs" 数据库
$collection = $db->lession;                     // 选择集合
$cursor     = $collection->find();

// 移除文档
$collection->remove(array("title"=>"MongoDB 教程"), array("justOne" => true));

// 显示可用文档数据
$cursor = $collection->find();
foreach ( $cursor as $document)
{
    echo $document["title"],"\n";
}
```

运行以上 `PHP` 脚本，输出结果如下：

```
$ php main.php
```

除了以上方法外，`PHP` `MongoDB` 扩展还有以下方法:

```html
1、  findOne()
2、  save()
3、  limit()
4、  skip()
5、  sort()
```

## 延伸阅读 ##

更多的操作方法可以参考 `PHP` `MongoDB` 扩展 [http://docs.php.net/set.mongodb][http_docs.php.net_set.mongodb]


[Image 1]: 
[http_docs.php.net_set.mongodb]: http://docs.php.net/set.mongodb


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")