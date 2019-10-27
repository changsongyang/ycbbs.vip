MySQL 中使用 **INSERT INTO** SQL 语句来往某个表中插入数据

### `INSERT INTO` SQL 语句语法 ###

使用 `INSERT INTO` SQL 语句往表中插入数据的语法格式如下

```
INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN );
```

如果数据是字符型，必须使用单引号或者双引号，如 `"value"`

例如

```
INSERT INTO `tbl_language` (`name`,`url`,`founded_at`) VALUES ('Python','https://www.ycbbs.vip','1991-2-20');
```

如果数据包含了全部字段，且按照字段的顺序，那么可以用下面的简写语法

```
INSERT INTO table_name VALUES ( value1, value2,...valueN );
```

例如

```
INSERT INTO `tbl_language` VALUES (1,'Python','https://www.ycbbs.vip','1991-2-20');
```

如果是多条语句，可以用 **逗号(,)** 分隔每条数据

```
INSERT INTO `tbl_language` VALUES
    (1,'Python','https://www.ycbbs.vip','1991-2-20'),
    (2,'PHP','http://www.php.net','1994-1-1'),
    (3,'Ruby','https://www.ruby-lang.org/','1996-12-25')
```

## 通过命令提示窗口插入数据 ##

可以在 `mysql>` 命令提示窗口中执行 `INSERT INTO` SQL 语句向某个表中插入数据

比如下面的代码使用 `INSERT INTO` 向表 `tbl_language` 中插入一条数据

```
MariaDB [ycbbs]> use ycbbs;
Database changed
MariaDB [ycbbs]> show tables;
+----------------+
| Tables_in_ycbbs |
+----------------+
| customer       |
| sites          |
| tbl_language   |
+----------------+
3 rows in set (0.01 sec)

MariaDB [ycbbs]> INSERT INTO `tbl_language` (`name`,`url`,`founded_at`) VALUES ('Python','https://www.ycbbs.vip','1991-2-20');
Query OK, 1 row affected (0.01 sec)

MariaDB [ycbbs]> INSERT INTO tbl_language VALUES (2,'PHP','http://www.php.net','1994-1-1');
Query OK, 1 row affected (0.01 sec)

MariaDB [ycbbs]> INSERT INTO `tbl_language` (`name`,`url`,`founded_at`) VALUES ('Ruby','https://www.ruby-lang.org/','1996-12-25');
Query OK, 1 row affected (0.01 sec)
```

第一条和最后一条插入语句，我们并没有提供 `id` 字段的数据，因为该字段我们在创建表的时候已经设置它为 `AUTO_INCREMENT`(自动增加) 属性

所以，该字段会自动递增而不需要我们去设置

我们可以通过以下语句查看刚刚插入的数据

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
+----+--------+----------------------------+------------+
3 rows in set (0.00 sec)
```

## PHP 脚本插入数据 ##

PHP 可以使用 `PDO::exec()` 函数向某个表中插入数据

**PDO::exec()** 函数只有一个参数，就是想要执行的 SQL 语句

#### PDO::exec 语法格式 ####

```
int PDO::exec ( string $statement )
```

返回成功插入数据的的行数。如果插入失败，则返回 0

#### 参数 ####

<table> 
 <thead> 
  <tr> 
   <th align="left">参数</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">statement</td> 
   <td align="left">要被预处理和执行的 SQL 语句，查询中的数据应该被妥善地转义</td> 
  </tr> 
 </tbody> 
</table>

### 范例 ###

下面的 PHP 代码演示了如何用 PDO\_MySQL 往表 `tbl_language` 中插入数据

```
<?php 

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * 
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
 */

$sql= "INSERT INTO `tbl_language` (`name`,`url`,`founded_at`) VALUES ('Python','https://www.ycbbs.vip','1991-2-20');";

try {

    $dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

    $rs = $dbh->exec($sql);

    if ( $rs > 0 )
        echo "成功往 ycbbs 数据库中的表 tbl_language 中插入数据\n"; 
    else 
        echo "插入数据失败:",$dbh->errorInfo()[2], "\n"; 
}
catch (PDOException $e) 
{    
    echo "错误!: " , $e->getMessage() , "\n";  
}
```

运行以上 PHP 代码，输出结果如下

```
$ php main.php 
成功往 ycbbs 数据库中的表 tbl_language 中插入数据
```

可以在 `mysql>` 中使用下面的 `SQL` 语句查看 PHP 执行的结果

```
SELECT * FROM `tbl_language`;
```

输出结果

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")