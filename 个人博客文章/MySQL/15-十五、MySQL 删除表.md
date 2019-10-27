MySQL 使用 **DROP TABLE** SQL 语句删除数据库中的一个表

> 要小心，因为执行删除命令后表中所有的数据都会消失，而且无法撤销

### `DROP TABLE` SQL 语句语法 ###

使用 `DROP TABLE` SQL 语句删除数据表的通用语法如下

```
DROP TABLE table_name ;
```

## 在命令提示窗口中删除数据表 ##

可以在 `mysql>` 命令提示窗口中执行 `DROP TABLE` SQL 语句删除数据表

下面的代码演示了如何删除 `ycbbs` 中的数据表 `tbl_language`

```
MariaDB [ycbbs]> use ycbbs;
Database changed
MariaDB [ycbbs]> SHOW TABLES;
+----------------+
| Tables_in_ycbbs |
+----------------+
| customer       |
| sites          |
| tbl_language   |
+----------------+
3 rows in set (0.00 sec)

MariaDB [ycbbs]> DROP TABLE tbl_language;
Query OK, 0 rows affected (0.01 sec)   # 即使删除成功，返回受影响的行数也为 0

MariaDB [ycbbs]> SHOW TABLES;
+----------------+
| Tables_in_ycbbs |
+----------------+
| customer       |
| sites          |   # 没看到 tbl_language 表说明删除成功
+----------------+
2 rows in set (0.00 sec)

MariaDB [ycbbs]> 
```

## PHP 删除数据表 ##

PHP 可以使用 `PDO::exec()` 函数来创建或者删除 MySQL 表

**PDO::exec()** 函数只有一个参数，就是想要执行的 SQL 语句

#### PDO::exec 语法格式 ####

```
int PDO::exec ( string $statement )
```

返回受修改或删除 SQL 表影响的行数。如果没有受影响的行，则返回 0

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

### 注意 ###

我们从前面的命令行运行中知道

```
MariaDB [ycbbs]> DROP TABLE tbl_language;
Query OK, 0 rows affected (0.01 sec)
```

即使删除成功，返回受影响的行数也为 0 ，所以无法根据 `PDO::exec()` 的返回值来判断是否删除表

### 范例 ###

下面的 PHP 代码演示了如何用 PDO\_MySQL 在数据库 ycbbs 中删除 `tbl_language` 表

```
<?php 

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * 
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
 */

$sql= "DROP TABLE `tbl_language`";

try {

    $dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

    $rs = $dbh->exec($sql);

    echo "成功删除 ycbbs 数据库中的表 tbl_language \n";  
}
catch (PDOException $e) 
{    
    echo "错误!: " , $e->getMessage() , "\n";  
}
```

运行以上 PHP 代码，输出结果如下

```
$ php main.php
成功删除 ycbbs 数据库中的表 tbl_language 
```

我们可以使用 `mysql` 客户端命令查看 `ycbbs` 表 `tbl_language` 还在不在

```
MariaDB [ycbbs]> USE ycbbs;
Database changed
MariaDB [ycbbs]> SHOW TABLES;
+----------------+
| Tables_in_ycbbs |
+----------------+
| customer       |
| sites          |
+----------------+
2 rows in set (0.00 sec)

MariaDB [ycbbs]>
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")