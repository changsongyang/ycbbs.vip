我们知道在 MySQL 中可以使用 SQL `SELECT FROM` 命令查询数据，也可以在 `SELECT FROM` 语句中使用 WHERE 子句有条件的获取指定的记录

我们知道 `=` 操作符用于完全匹配某个条件，比如 `name='Python` 只能查询 `name` 为 `Python` 的记录，却不能查询以 `P` 开头的记录

但如果我们想要查询 `name` 以 `P` 开头的记录要怎么做呢？

答案就是在 `WHERE` 字句中使用 `LIKE` 子句

## LIKE 子句 ##

**LIKE** 子句用于设定模糊查询条件

`LIKE` 子句中可以使用 **百分号%** 字符来表示任意字符，比如 `P%` 可以匹配以 `P` 开头的所有字符串，`%on` 可以匹配所有以 `on` 结尾的字符串

### `LIKE` 子句语法 ###

在 `SELECT FROM` 语句使用 `LIKE` 子句从数据表中读取数据的语法格式如下

```
SELECT field1, field2,...fieldN 
FROM table_name
WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'
```

 *  可以在 WHERE 子句中使用 `LIKE` 子句
 *  LIKE 通常与 `%` 一同使用，类似于一个元字符的搜索

可以在 `DELETE` 或 `UPDATE` 命令中使用 `WHERE...LIKE` 子句来指定条件

## 复原数据 ##

可以在 `mysql>` 命令提示窗口中执行以下语句复原范例数据

```
truncate tbl_language;
INSERT INTO `tbl_language` VALUES
    (1,'Python','https://www.ycbbs.vip','1991-2-20'),
    (2,'PHP','http://www.php.net','1994-1-1'),
    (3,'Ruby','https://www.ruby-lang.org/','1996-12-25');
```

## 通过命令提示符获取数据 ##

可以在 `mysql>` 命令提示窗口中执行 `SELECT FROM LIKE` SQL 语句查询某个表中的数据

下面的代码使用 `SELECT FROM LIKE` SQL 语句查询表 `tbl_language` 中所有以 `P` 开头的数据

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

MariaDB [ycbbs]> SELECT * FROM `tbl_language` WHERE `name` LIKE 'P%';
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
|  2 | PHP    | http://www.php.net  | 1994-01-01 |
+----+--------+---------------------+------------+
2 rows in set (0.01 sec)
```

使用 `WHERE name LIKE '%on'` 查询所有 `name` 以 `on` 结尾的记录

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language` WHERE `name` LIKE '%on';
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)
```

使用 `WHERE name LIKE '%th%'` 查询所有 `name` 包含 `th` 字符串的记录

```
MariaDB [ycbbs]> SELECT * FROM `tbl_language` WHERE `name` LIKE '%th%';
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)
```

## PHP 脚本中使用 LIKE 子句 ##

PHP 可以使用 `PDO::query()` 函数来查询某个表中的数据

#### PDO::query() 函数原型 ####

`PDO::query()` 有四个函数重载

```
PDOStatement PDO::query ( string $statement )

PDOStatement PDO::query ( string $statement , int $PDO::FETCH_COLUMN , int $colno )

PDOStatement PDO::query ( string $statement , int $PDO::FETCH_CLASS , string $classname , array $ctorargs )

PDOStatement PDO::query ( string $statement , int $PDO::FETCH_INTO , object $object )
```

如果成功，`PDO::query()` 返回 `PDOStatement` 对象，如果失败返回 FALSE

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

第二个参数有以下几个可选值，默认为 `PDO::FETCH_BOTH`

<table> 
 <thead> 
  <tr> 
   <th align="left">值</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">PDO::FETCH_ASSOC</td> 
   <td align="left">返回一个索引为结果集列名的数组</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_BOTH</td> 
   <td align="left">默认，返回一个索引为结果集列名和以0开始的列号的数组</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_BOUND</td> 
   <td align="left">返回 TRUE ，并分配结果集中的列值给 PDOStatement::bindColumn() 方法绑定的 PHP 变量</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_CLASS</td> 
   <td align="left">返回一个请求类的新实例，映射结果集中的列名到类中对应的属性名。如果 fetch_style 包含 PDO::FETCH_CLASSTYPE（例如：PDO::FETCH_CLASS |PDO::FETCH_CLASSTYPE），则类名由第一列的值决定</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_INTO</td> 
   <td align="left">更新一个被请求类已存在的实例，映射结果集中的列到类中命名的属性</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_LAZY</td> 
   <td align="left">结合使用 PDO::FETCH_BOTH 和 PDO::FETCH_OBJ，创建供用来访问的对象变量名</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_NUM</td> 
   <td align="left">返回一个索引为以0开始的结果集列号的数组</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::FETCH_OBJ</td> 
   <td align="left">返回一个属性名对应结果集列名的匿名对象</td> 
  </tr> 
 </tbody> 
</table>

我们使用默认的 `PDO::FETCH_BOTH` 获取所有数据，其它方式请移步我们的 PHP 基础教程

```
<?php 

$sql= "SELECT * FROM tbl_language WHERE name LIKE 'P%';";

$dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

$stmt = $dbh->query($sql);

foreach($stmt as $row)
{
    var_dump($row);
}
```

输出结果如下

```
$ php main.php
array(8) {
  ["id"]=>
  string(1) "1"
  [0]=>
  string(1) "1"
  ["name"]=>
  string(6) "Python"
  [1]=>
  string(6) "Python"
  ["url"]=>
  string(19) "https://www.ycbbs.vip"
  [2]=>
  string(19) "https://www.ycbbs.vip"
  ["founded_at"]=>
  string(10) "1991-02-20"
  [3]=>
  string(10) "1991-02-20"
}
array(8) {
  ["id"]=>
  string(1) "2"
  [0]=>
  string(1) "2"
  ["name"]=>
  string(3) "PHP"
  [1]=>
  string(3) "PHP"
  ["url"]=>
  string(18) "http://www.php.net"
  [2]=>
  string(18) "http://www.php.net"
  ["founded_at"]=>
  string(10) "1994-01-01"
  [3]=>
  string(10) "1994-01-01"
}
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")