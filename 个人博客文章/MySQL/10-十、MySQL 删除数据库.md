MySQL 删除数据库的方式有两种

1、  使用 `mysqladmin drop` 命令删除数据库
2、  使用 `DROP DATABASE` 语句删除数据库

在删除数据库过程中，务必要十分谨慎，因为在执行删除命令后，所有数据将会消失，而且不可撤销

如果使用普通用户连接到 MySQL 服务器，可能需要特定的权限来创建或者删除 MySQL 数据库

我们使用 root 用户登录，因为 root 用户拥有最高权限

## 使用 mysqladmin drop 命令删除数据库 ##

可以使用 `mysqladmin drop` 命令来删除数据库

### mysqladmin 命令语法格式如下 ###

```
mysqladmin [OPTIONS] command [command-option] command ...
```

通过执行 `mysqladmin --help`，可以得到 mysqladmin 的版本所支持的一个选项列表

<table> 
 <thead> 
  <tr> 
   <th align="left">命令(command)</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">create databasename</td> 
   <td align="left">创建一个新数据库</td> 
  </tr> 
  <tr> 
   <td align="left">drop databasename</td> 
   <td align="left">删除一个数据库及其所有表</td> 
  </tr> 
  <tr> 
   <td align="left">extended-status</td> 
   <td align="left">给出服务器的一个扩展状态消息</td> 
  </tr> 
  <tr> 
   <td align="left">flush-hosts</td> 
   <td align="left">洗掉所有缓存的主机</td> 
  </tr> 
  <tr> 
   <td align="left">flush-logs</td> 
   <td align="left">洗掉所有日志</td> 
  </tr> 
  <tr> 
   <td align="left">flush-tables</td> 
   <td align="left">洗掉所有表</td> 
  </tr> 
  <tr> 
   <td align="left">flush-privileges</td> 
   <td align="left">再次装载授权表(同 reload )</td> 
  </tr> 
  <tr> 
   <td align="left">kill id,id,...</td> 
   <td align="left">杀死 mysql 线程</td> 
  </tr> 
  <tr> 
   <td align="left">password</td> 
   <td align="left">新口令，将老口令改为新口令</td> 
  </tr> 
  <tr> 
   <td align="left">ping</td> 
   <td align="left">检查 mysqld 是否活着</td> 
  </tr> 
  <tr> 
   <td align="left">processlist</td> 
   <td align="left">显示服务其中活跃线程列表</td> 
  </tr> 
  <tr> 
   <td align="left">reload</td> 
   <td align="left">重载授权表</td> 
  </tr> 
  <tr> 
   <td align="left">refresh</td> 
   <td align="left">洗掉所有表并关闭和打开日志文件</td> 
  </tr> 
  <tr> 
   <td align="left">shutdown</td> 
   <td align="left">关掉服务器</td> 
  </tr> 
  <tr> 
   <td align="left">status</td> 
   <td align="left">给出服务器的简短状态消息</td> 
  </tr> 
  <tr> 
   <td align="left">variables</td> 
   <td align="left">打印出可用变量</td> 
  </tr> 
  <tr> 
   <td align="left">version</td> 
   <td align="left">得到服务器的版本信息</td> 
  </tr> 
 </tbody> 
</table>

下面的 `mysqladmin drop` 可以删除我们前一章节中创建的 ycbbs 数据库

```
mysqladmin -u root -p drop ycbbs
```

完整演示如下

```
[root@localhost ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
| ycbbs               |
+--------------------+
5 rows in set (0.00 sec)

MariaDB [(none)]> exit
Bye
[root@localhost ~]# mysqladmin -u root -p drop ycbbs
Enter password:
Dropping the database is potentially a very bad thing to do.
Any data stored in the database will be destroyed.

Do you really want to drop the 'ycbbs' database [y/N] y
Database "ycbbs" dropped
[root@localhost ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 9
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)
```

在上面的演示过程中，执行删除命令时，会出现一个提示框，来确认是否真的删除数据库

## 使用 DROP DATABASE 语句创建数据库 ##

使用 `DROP DATABASE` 语句删除数据库前先要连接到 MySQL 数据库服务器

#### DROP DATABASE 命令语法格式如下 ####

```
DROP DATABASE **databasename**;
```

下面的 SQL 语句删除了我们上一章节中添加的 ycbbs 数据库

```
DROP DATABASE ycbbs;
```

演示过程如下

```
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
| ycbbs               |
+--------------------+
5 rows in set (0.01 sec)

MariaDB [(none)]> DROP DATABASE ycbbs;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)
```

## 使用 PHP 脚本删除数据库 ##

PHP 使用 `PDO::exec()` 函数来创建或者删除 MySQL 数据库

**PDO::exec()** 函数只有一个参数，就是想要执行的 SQL 语句

#### PDO::exec 语法格式 ####

```
int PDO::exec ( string $statement )
```

返回受修改或删除 SQL 语句影响的行数。如果没有受影响的行，则返回 0

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

下面的 PHP 代码演示了如何用 PDO\_MySQL 删除数据库 ycbbs

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

    $rs = $dbh->exec('DROP DATABASE ycbbs');

    if( !$rs )
    {
        echo '删除数据库 ycbbs 失败:: ' ,$dbh->errorInfo()[2],"\n";
        exit();
    }

    echo "数据库 ycbbs 删除成功\n";  
}
catch (PDOException $e) 
{    
    echo "错误!: " , $e->getMessage() , "\n";  
}
```

运行以上 PHP 代码，删除数据库成功后输出如下结果：

```
$ php main.php
数据库 ycbbs 删除成功
```

### 注意 ###

> 使用 PHP 脚本删除数据库时，不会出现确认是否删除信息，会直接删除指定数据库，所以在删除数据库时要特别小心，谨慎，谨慎再谨慎


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")