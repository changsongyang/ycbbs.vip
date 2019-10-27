可以使用 SQL 的 `DELETE FROM` 命令来删除 `MySQL` 数据表中的记录

### `DELETE FROM` 语句语法 ###

`DELETE FROM` SQL 语句删除数据的通用语法格式如下

```
DELETE FROM table_name [WHERE Clause]
```

 *  如果没有指定 WHERE 子句，MySQL 表中的所有记录将被删除
 *  可以在 WHERE 子句中指定任何条件
 *  可以在单个表中一次性删除记录

> **注意：** 如果没有 `WHERE` 语句，那会删除所有的数据，而且无法复原，所以请谨慎

当你想删除数据表中指定的记录时 WHERE 子句是非常有用的。

## 通过命令提示符删除数据 ##

可以在 `mysql>` 命令提示窗口中执行 `DELETE FROM` SQL 语句删除表中的数据

下面的代码使用 `DELETE` SQL 语句将 `name='Python'` 的数据行删除

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://www.ycbbs.vip/       | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
3 rows in set (0.00 sec)

MariaDB [ycbbs]> DELETE FROM `tbl_language` WHERE name='Python';
Query OK, 1 row affected (0.01 sec)

MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
+----+------+----------------------------+------------+
| id | name | url                        | founded_at |
+----+------+----------------------------+------------+
|  2 | PHP  | http://www.php.net         | 1994-01-01 |
|  3 | Ruby | https://www.ruby-lang.org/ | 1996-12-25 |
+----+------+----------------------------+------------+
2 rows in set (0.00 sec)
```

如果不加 `WHERE` 字句那么将删除所有的数据

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
+----+------+----------------------------+------------+
| id | name | url                        | founded_at |
+----+------+----------------------------+------------+
|  2 | PHP  | http://www.php.net         | 1994-01-01 |
|  3 | Ruby | https://www.ruby-lang.org/ | 1996-12-25 |
+----+------+----------------------------+------------+
2 rows in set (0.00 sec)

MariaDB [ycbbs]> DELETE FROM `tbl_language`;
Query OK, 2 rows affected (0.00 sec)

MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
Empty set (0.00 sec)
```

## 复原数据 ##

可以在 `mysql>` 命令提示窗口中执行以下语句来复原范例数据

```
truncate tbl_language;
INSERT INTO `tbl_language` VALUES
    (1,'Python','https://www.ycbbs.vip','1991-2-20'),
    (2,'PHP','http://www.php.net','1994-1-1'),
    (3,'Ruby','https://www.ruby-lang.org/','1996-12-25');
```

## 使用 PHP 脚本删除数据 ##

PHP 可以使用 `PDO::exec()` 函数删除表中的数据

**PDO::exec()** 函数只有一个参数，就是想要执行的 SQL 语句

#### PDO::exec 语法格式 ####

```
int PDO::exec ( string $statement )
```

返回成功删除的行数。如果没有数据被删除，则返回 0

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

下面的 PHP 代码演示了如何用 PDO\_MySQL 删除表 `tbl_language` 中 `name='Python'` 的数据

```
<?php 

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * 
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
 */

$sql= "DELETE FROM tbl_language WHERE name='Python';";

try {

    $dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

    $rs = $dbh->exec($sql);

    if ( $rs > 0 )
        echo "成功将 name='Python' 数据的数据删除\n"; 
    else 
        echo "没有数据要删除或者删除失败:",$dbh->errorInfo()[2], "\n"; 
}
catch (PDOException $e) 
{    
    echo "错误!: " , $e->getMessage() , "\n";  
}
```

运行以上 PHP 代码，输出结果如下

```
$ php main.php
成功将 name='Python' 数据的数据删除
```

可以在 `mysql>` 中使用下面的 `SQL` 语句查看 PHP 执行的结果

```
SELECT * FROM `tbl_language`;
```

输出结果

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language`;
+----+------+----------------------------+------------+
| id | name | url                        | founded_at |
+----+------+----------------------------+------------+
|  2 | PHP  | http://www.php.net         | 1994-01-01 |
|  3 | Ruby | https://www.ruby-lang.org/ | 1996-12-25 |
+----+------+----------------------------+------------+
2 rows in set (0.00 sec)
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")