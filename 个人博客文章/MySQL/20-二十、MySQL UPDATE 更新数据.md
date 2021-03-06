如果需要修改或更新 MySQL 中某个表中的数据，可以使用 `UPDATE` SQL 语句

### `UPDATE` SQL 语句语法 ###

`UPDATE` SQL 语句修改数据的通用语法格式如下

```
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause]
```

 *  可以同时更新一个或多个字段
 *  可以在 WHERE 子句中指定任何条件
 *  可以在一个单独表中同时更新数据
 *  如果没有 `WHERE` 语句，那么会更新表中的全部数据

> **注意：** 不使用 WHERE 子句将数据表的全部数据进行更新，所以要慎重

## 通过命令提示符更新数据 ##

可以在 `mysql>` 命令提示窗口中执行 `UPDATE` SQL 语句更新表中的数据

下面的代码使用 `UPDATE` SQL 语句将 `name='Python'` 的数据行中的 `url` 改成 `https://www.ycbbs.vip/'`

```
MariaDB [ycbbs]> SELECT * FROM tbl_language WHERE BINARY  name='Python';
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)

MariaDB [ycbbs]> SELECT * FROM tbl_language WHERE  name='Python';
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)

MariaDB [ycbbs]> UPDATE tbl_language SET url='https://www.ycbbs.vip/' WHERE name='Python';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [ycbbs]> SELECT * FROM tbl_language WHERE  name='Python';
+----+--------+----------------------+------------+
| id | name   | url                  | founded_at |
+----+--------+----------------------+------------+
|  1 | Python | https://www.ycbbs.vip/ | 1991-02-20 |
+----+--------+----------------------+------------+
1 row in set (0.00 sec)
```

如果省略 `WHERE` 语句，那么将更新全部的数据，下面的 `SQL` 语句将所有的 `name` 字段改成 `Java`

```
MariaDB [ycbbs]> SELECT * FROM tbl_language;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://www.ycbbs.vip/       | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
3 rows in set (0.00 sec)

MariaDB [ycbbs]> UPDATE tbl_language SET name='Java';
Query OK, 3 rows affected (0.01 sec)
Rows matched: 3  Changed: 3  Warnings: 0

MariaDB [ycbbs]> SELECT * FROM tbl_language;
+----+------+----------------------------+------------+
| id | name | url                        | founded_at |
+----+------+----------------------------+------------+
|  1 | Java | https://www.ycbbs.vip/       | 1991-02-20 |
|  2 | Java | http://www.php.net         | 1994-01-01 |
|  3 | Java | https://www.ruby-lang.org/ | 1996-12-25 |
+----+------+----------------------------+------------+
3 rows in set (0.00 sec)
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

## 使用 PHP 脚本更新数据 ##

PHP 可以使用 `PDO::exec()` 函数更新表中的数据

**PDO::exec()** 函数只有一个参数，就是想要执行的 SQL 语句

#### PDO::exec 语法格式 ####

```
int PDO::exec ( string $statement )
```

返回成功修改数据的行数。如果没有数据要更新，则返回 0

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

下面的 PHP 代码演示了如何用 PDO\_MySQL 更新表 `tbl_language` 中 `name='Python'` 的数据

```
<?php 

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * 
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
 */

$sql= "UPDATE tbl_language SET url='https://www.ycbbs.vip/' WHERE name='Python';";

try {

    $dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

    $rs = $dbh->exec($sql);

    if ( $rs > 0 )
        echo "成功将 name='Python' 数据中的 url 改为 'https://www.ycbbs.vip/'\n"; 
    else 
        echo "没有数据要更新或者更新失败:",$dbh->errorInfo()[2], "\n"; 
}
catch (PDOException $e) 
{    
    echo "错误!: " , $e->getMessage() , "\n";  
}
```

运行以上 PHP 代码，输出结果如下

```
$ php main.php
成功将 name='Python' 数据中的 url 改为 'https://www.ycbbs.vip/'
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
|  1 | Python | https://www.ycbbs.vip/       | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")