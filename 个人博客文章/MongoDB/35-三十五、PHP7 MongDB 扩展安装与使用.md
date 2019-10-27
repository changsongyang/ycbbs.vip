在前面的章节中我们学习了 `MongoDB` `PHP` 扩展使用范例，不过那篇文章只能针对 `PHP5` 使用，`PHP7` 以上版本则需要使用其它的 `PHP` `MongoDB` 扩展

## PHP7 Mongdb 扩展安装 ##

假设我们的 `PHP7` 安装在 `/usr/local/php7` 目录

我们可以使用 `pecl` 命令来安装 `PHP` `MongoDB` 扩展

```
$ /usr/local/php7/bin/pecl install mongodb
```

执行成功后，会输出以下信息

```
...
Build process completed successfully
Installing '/usr/local/php7/lib/php/extensions/no-debug-non-zts-20151012/mongodb.so'
install ok: channel://pecl.php.net/mongodb-1.1.7
configuration option "php_ini" is not set to php.ini location
You should add "extension=mongodb.so" to php.ini
```

然后打开 `php.ini` 文件添加 `extension=mongodb.so` 配置

或者可以直接执行以下命令来添加

```
$ echo "extension=mongodb.so" >> `/usr/local/php7/bin/php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

> **注意：** 以上执行的命令中 php7 的安装目录为 /usr/local/php7/，如果你安装在其他目录，需要相应修改 pecl 与 php 命令的路径

## PHP7 MongoDB 使用范例 ##

PHP7 连接 MongoDB 数据库

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$driver = new MongoDB\Driver\Manager("mongodb://localhost:27017");
```

### PHP7 MongoDB 插入数据 ###

我们可以使用以下代码将 `Python` 插入到 `ycbbs` 数据库的 `language` 集合中

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$driver = new MongoDB\Driver\Manager("mongodb://localhost:27017");

$bulk = new MongoDB\Driver\BulkWrite;
$document = ['_id' => new MongoDB\BSON\ObjectID, 'name' => 'Python'];

$_id= $bulk->insert($document);

var_dump($_id);

$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $driver->executeBulkWrite('test.language', $bulk, $writeConcern);
```

运行以上 PHP7 脚本，输出结果如下

```
$ php main.php                           
object(MongoDB\BSON\ObjectId)#4 (1) {
  ["oid"]=>
  string(24) "59eef1902a2f950ad3720422"
}
```

### 读取数据 ###

我们先将 `Perl` 、`PHP`、`Ruby` 插入到 `language` 集合中，然后读取迭代出来

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$driver = new MongoDB\Driver\Manager("mongodb://localhost:27017");

$bulk = new MongoDB\Driver\BulkWrite;

$bulk->insert(["name"=> "Perl"]);
$bulk->insert(["name"=> "PHP"]);
$bulk->insert(["name"=> "Ruby"]);

$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $driver->executeBulkWrite('test.language', $bulk, $writeConcern);


$filter = [];
$options = [
    'projection' => ['_id' => 0],
    'sort' => ['name' => -1],
];

// 查询数据
$query = new MongoDB\Driver\Query($filter, $options);
$cursor = $driver->executeQuery('test.language', $query);

foreach ($cursor as $document) {
    print_r($document);
}
```

运行以上 `PHP7` 脚本，输出结果如下

```
$ php main.php
stdClass Object
(
    [name] => Ruby
)
stdClass Object
(
    [name] => Python
)
stdClass Object
(
    [name] => Perl
)
stdClass Object
(
    [name] => PHP
)
```

### 更新数据 ###

我们将 **PHP** 语言改成 **PHP7**

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$driver = new MongoDB\Driver\Manager("mongodb://localhost:27017");

$bulk = new MongoDB\Driver\BulkWrite;

$bulk->update(
    ['name' => 'PHP'],
    ['$set' => ['name' => 'PHP7']],
    ['multi' => false, 'upsert' => false]
);

$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $driver->executeBulkWrite('test.language', $bulk, $writeConcern);


$filter = [];
$options = [
    'projection' => ['_id' => 0],
    'sort' => ['name' => -1],
];

// 查询数据
$query = new MongoDB\Driver\Query($filter, $options);
$cursor = $driver->executeQuery('test.language', $query);

foreach ($cursor as $document) {
    print_r($document);
}
```

运行以上 `PHP7` 脚本，输出结果如下

```
$ php main.php
stdClass Object
(
    [name] => Ruby
)
stdClass Object
(
    [name] => Python
)
stdClass Object
(
    [name] => Perl
)
stdClass Object
(
    [name] => PHP7
)
```

### 删除数据 ###

现在我们删除 **PHP7** 这条数据

```
<?php

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/


$driver = new MongoDB\Driver\Manager("mongodb://localhost:27017");

$bulk = new MongoDB\Driver\BulkWrite;

$bulk->delete(['name' => 'PHP7'], ['limit' => 0]);  // limit 为 0 时，删除所有匹配数据

$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $driver->executeBulkWrite('test.language', $bulk, $writeConcern);


$filter = [];
$options = [
    'projection' => ['_id' => 0],
    'sort' => ['name' => -1],
];

// 查询数据
$query = new MongoDB\Driver\Query($filter, $options);
$cursor = $driver->executeQuery('test.language', $query);

foreach ($cursor as $document) {
    print_r($document);
}
```

运行以上 `PHP7` 脚本，输出结果如下

```
$ php main.php
stdClass Object
(
    [name] => Ruby
)
stdClass Object
(
    [name] => Python
)
stdClass Object
(
    [name] => Perl
)
```

## 延伸阅读 ##

更多使用方法请参考：[http://php.net/manual/en/book.mongodb.php][http_php.net_manual_en_book.mongodb.php] 。


[http_php.net_manual_en_book.mongodb.php]: http://php.net/manual/en/book.mongodb.php


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")