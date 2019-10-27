如果要查看当前选择的数据库有哪些表，可以使用 `SHOW TABLES;` 命令

## 在 mysql> 终端中查看数据库列表 ##

打开一个终端(命令行)，输入 `mysql -u root -p` 进入 `mysql>` 命令行

然后输入 `USE ycbbs;` 选择 `ycbbs` 数据库

最后输入 `SHOW TABLES;` 命令就可以查看当前选择的数据库有哪些表

```
$ mysql -uroot -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 117
Server version: 10.2.13-MariaDB Homebrew

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use ycbbs;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [ycbbs]> SHOW TABLES;
+----------------+
| Tables_in_ycbbs |
+----------------+
| tbl_lang       |
| tbl_language   |
+----------------+
2 rows in set (0.00 sec)
```

> 你创建的数据库里可能没有这些表，因为这篇教程是后面添加的

## PHP 列出当前数据库有哪些表 ##

PHP 可以使用 `PDO::query()` 函数可以用来列出当前选择的数据库有哪些表

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

$sql= "SHOW TABLES;";

$dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

$dbh->query("USE ycbbs;");
$stmt = $dbh->query($sql);

foreach($stmt as $row)
{
    echo $row[0],"\n";
}
```

输出结果如下

```
$ php main.php
tbl_lang
tbl_language
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")