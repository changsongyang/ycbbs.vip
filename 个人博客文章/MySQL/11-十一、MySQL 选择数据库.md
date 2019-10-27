一般 MySQL 数据库服务器上都会有多个可以操作的数据库，我们可能要在数据库之间来回切换

MySQL 允许我们保持连接的时间内切换数据库

## mysql client 命令提示窗口中切换 MySQL 数据库 ##

连接到 MySQL 数据服务器后，可以在 `mysql>` 提示窗口中切换或选择特定的数据库。

使用 `USE` SQL命令来选择指定的数据库

#### `USE` 命令语法格式如下 ####

```
USE **database**;
```

下面的 SQL 语句切换到了 ycbbs 数据库

```
MariaDB [(none)]> select database();
+------------+
| database() |
+------------+
| NULL       |
+------------+
1 row in set (0.00 sec)

MariaDB [(none)]> USE  ycbbs;
Database changed
MariaDB [ycbbs]> select database();
+------------+
| database() |
+------------+
| ycbbs       |
+------------+
1 row in set (0.00 sec)
```

执行 `USE ycbbs;` 语句后命令后，我们就成功切换到了 ycbbs 数据库，在后续的操作中都会在 ycbbs 数据库中执行

`SELECT database();` 语句用来显示当前使用的数据库

### 注意 ###

因为所有的数据库名，表名，表字段都是区分大小写的，所以在使用 SQL 命令时需要输入正确的名称

## 使用 PHP 脚本选择 MySQL 数据库 ##

`PDO_MySQL` 没有直接提供函数来切换数据库，但我们可以用执行 `USE **database**;` 语句的方式来切换当前连接的数据库

我们可以使用 **PDO::query()** 函数来选择或切换 MySQL 数据库

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

下面的 PHP 代码演示了如何用 PDO\_MySQL 切换数据库 `ycbbs`

```
<?php 

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * 
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
 */

try {

    $dbh = new PDO('mysql:host=127.0.0.1;dbname=test', 'root', '');    

    $stmt = $dbh->query('SELECT database()');

    echo '当前数据库',$stmt->fetch(PDO::FETCH_COLUMN),"\n";

    $rs = $dbh->query('USE ycbbs');

    if( !$rs )
    {
        echo '切换到数据库 ycbbs 失败: ' ,$dbh->errorInfo()[2],"\n";
        exit();
    }

    echo "切换到数据库 ycbbs 成功\n"; 

    $stmt = $dbh->query('SELECT database()');
    echo '当前数据库',$stmt->fetch(PDO::FETCH_COLUMN),"\n";
}
catch (PDOException $e) 
{    
    echo "错误!: " , $e->getMessage() , "\n";  
}
```

运行以上 PHP 代码，切换成功后输出如下结果

```
$ php main.php
当前数据库test
切换到数据库 ycbbs 成功
当前数据库ycbbs
```

运行以上 PHP 代码，如果数据库不存在，则输出如下结果

```
$ php main.php
当前数据库test
切换到数据库 ycbbs 失败: Unknown database 'ycbbs'
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")