前面我们学到了可以使用 `SELECT FROM` 从 MySQL 中查询数据，但是，查出来数据的排序可能不是我们想要的，比如我们想根据 `name` 排序，要怎么做呢？

答案就是使用 MySQL 的 `ORDER BY` 子句

`ORDER BY` 子句可以设定想按哪个字段哪种方式来进行排序，再返回搜索结果

### `ORDER BY` 子句语法 ###

SQL SELECT 中语句使用 ORDER BY 子句对查询数据进行排序的语法格式如下

```
SELECT field1, field2,...fieldN table_name1, table_name2...
ORDER BY field1, [field2...] [ASC [DESC]]
```

 *  可以使用任何字段来作为排序的条件，从而返回排序后的查询结果
 *  可以设定多个字段来排序
 *  可以使用 `ASC` 或 `DESC` 关键字来设置查询结果是按升序或降序排列 默认情况下按升序排列
 *  可以添加 `WHERE` 子句来设置条件

## 范例数据 ##

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

INSERT INTO `tbl_language` VALUES
    (1,'Python','https://www.ycbbs.vip','1991-2-20'),
    (2,'PHP','http://www.php.net','1994-1-1'),
    (3,'Ruby','https://www.ruby-lang.org/','1996-12-25');
```

`tbl_language` 表中的数据如下

```
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
```

## 在命令提示符中使用 ORDER BY 子句 ##

可以在 `mysql>` 命令提示窗口中执行 `SELECT FROM ORDER BY` SQL 语句查询某个表中的数据并对结果进行排序

下面的代码使用 `SELECT FROM ORDER BY` SQL 语句查询表 `tbl_language` 中所有的数据并按照 `name` 升序排序

#### 不使用 `ORDER BY` ####

```
MariaDB [ycbbs]> select * from tbl_language;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
3 rows in set (0.00 sec)
```

### ORDER BY name ###

```
MariaDB [ycbbs]> select * from tbl_language ORDER BY name;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
3 rows in set (0.00 sec)
```

### ORDER BY name DESC ###

```
MariaDB [ycbbs]> select * from tbl_language ORDER BY name DESC;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
|  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
+----+--------+----------------------------+------------+
3 rows in set (0.01 sec)
```

## PHP 脚本中使用 ORDER BY 子句 ##

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

$sql= "SELECT * FROM tbl_language ORDER BY name DESC;";

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
  string(1) "3"
  [0]=>
  string(1) "3"
  ["name"]=>
  string(4) "Ruby"
  [1]=>
  string(4) "Ruby"
  ["url"]=>
  string(26) "https://www.ruby-lang.org/"
  [2]=>
  string(26) "https://www.ruby-lang.org/"
  ["founded_at"]=>
  string(10) "1996-12-25"
  [3]=>
  string(10) "1996-12-25"
}
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