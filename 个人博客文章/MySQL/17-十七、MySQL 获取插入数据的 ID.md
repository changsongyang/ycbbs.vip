在 MySQL 插入数据 我们已经学会了如何往一张表中插入数据，但要如何获取插入数据的 id 值呢 ?

不要疑惑，不然会长皱纹，MySQL 提供了 `LAST_INSERT_ID()` 函数用于获取上一次插入数据的 ID

我们可以使用下面的 SQL 命令获取上一次插入数据的 ID;

```
SELECT LAST_INSERT_ID();
```

## 准备范例数据 ##

可以在 `mysql>` 命令行中运行以下语句填充范例数据

```
DROP TABLE IF EXISTS `tbl_language`;

CREATE TABLE IF NOT EXISTS `tbl_language`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `name` VARCHAR(64) NOT NULL,
   `url` VARCHAR(128) NOT NULL,
   `founded_at` DATE,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

INSERT INTO `tbl_language` (name,url,founded\_at) VALUES ('Ruby','https://www.ruby-lang.org/','1996-12-25');

## 通过命令提示窗口获取插入数据的 ID ##

可以在 `mysql>` 命令提示窗口中执行 `INSERT INTO` SQL 语句向某个表中插入数据

然后使用 `SELECT LAST_INSERT_ID();` 命令获取上一次插入数据的 ID

比如下面的代码使用 `INSERT INTO` 向表 `tbl_language` 中插入一条数据

```
MariaDB [ycbbs]> use ycbbs;
Database changed
MariaDB [ycbbs]> INSERT INTO `tbl_language` (name,url,founded_at) VALUES ('Python','https://www.ycbbs.vip','1991-2-20');
Query OK, 1 row affected (0.01 sec)

MariaDB [ycbbs]> SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                1 |
+------------------+
1 row in set (0.00 sec)

MariaDB [ycbbs]> SELECT * FROM tbl_language;
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)

MariaDB [ycbbs]> SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                1 |
+------------------+
1 row in set (0.00 sec)

MariaDB [ycbbs]> 
```

可以看到 `SELECT LAST_INSERT_ID();` 可以调用多次，返回的都是上一次的 ID

如果我们先插入两条数据，然后在运行 `SELECT LAST_INSERT_ID();` 呢

```
MariaDB [ycbbs]> INSERT INTO `tbl_language` (name,url,founded_at) VALUES ('PHP','http://www.php.net','1994-1-1');
Query OK, 1 row affected (0.01 sec)

MariaDB [ycbbs]> INSERT INTO `tbl_language` (name,url,founded_at) VALUES ('Ruby','https://www.ruby-lang.org/','1996-12-25');
Query OK, 1 row affected (0.01 sec)

MariaDB [ycbbs]> SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                3 |
+------------------+
1 row in set (0.00 sec)
MariaDB [ycbbs]> SELECT * FROM tbl_language;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
3 rows in set (0.00 sec)
```

所以，`LAST_INSERT_ID()` 真的是获取上一条插入数据的 ID，如果连续插入两条数据，再获取，那么第一条插入的 ID 就获取不到了

## PHP 脚本获取插入数据的 ID ##

PHP 中可以使用 `PDO::lastInsertId()` 函数获取插入数据的 ID，这个要在 `PDO::exec()` 运行插入数据后立刻调用

`PDO::lastInsertId()` 函数原型

```
string PDO::lastInsertId ([ string $name = NULL ] )
```

返回最后插入行的 id，或者是一个序列对象最后的值，取决于底层的驱动

### 范例 ###

下面的 PHP 代码演示了如何用 PDO\_MySQL 往表 `tbl_language` 中插入数据

```
<?php 

$sql= "INSERT INTO `tbl_language` (`name`,`url`,`founded_at`) VALUES ('Kotlin','http://kotlinlang.org/','2016-02-17');";

$dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

$rs = $dbh->exec($sql);

if ( $rs > 0 ) {
    echo "成功往 ycbbs 数据库中的表 tbl_language 中插入数据\n";
    $id = $dbh->lastInsertId();
    echo "插入数据的ID值为:",$id,"\n"; 
} else {
    echo "插入数据失败:",$dbh->errorInfo()[2], "\n"; 
}
```

运行以上 PHP 代码，输出结果如下

```
$ php main.php
成功往 ycbbs 数据库中的表 tbl_language 中插入数据
插入数据的ID值为:4
```

可以在 `mysql>` 中使用下面的 `SQL` 语句查看 PHP 执行的结果

```
SELECT * FROM `tbl_language`;
```

输出结果

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
|  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
+----+--------+----------------------------+------------+
4 rows in set (0.00 sec)
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")