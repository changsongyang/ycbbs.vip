数据库事务 `(Database Transaction)` 是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行

事务处理可以确保除非事务性单元内的所有操作都成功完成，否则不会永久更新面向数据的资源

举个例子，A 向 B 转账 100 元，其实整个转账过程就是一个事务，要么转账成功了，A 的账户扣了 100 元，B 的账户增加了 100 元，要么转账失败，A 还是那么多钱，B 还是没钱，如果出现 A 扣了 100 元，B 的账户却没增加 100 元，那是要出问题的，是不？

事务，就是用来做这件事的，用来保证要么转账成功，要么转账失败

## MySQL 事务 ##

MySQL 支持事务，但是，但是只有使用 `Innodb` 数据库引擎的数据库或表才支持事务

事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行

一般来说，事务是必须满足4个条件 ( ACID )

Atomicity(原子性)、Consistency(稳定性)、Isolation(隔离性)、Durability(可靠性)

1、  **原子性**
    
    一组事务，要么成功；要么失败回滚当作什么事都没发生
2、  **稳定性**
    
    有非法数据 (外键约束之类)，事务撤回
3、  **隔离性**
    
    事务独立运行。一个事务处理后的结果，影响了其它事务，那么其它事务会撤回
    
    事务的100%隔离，需要牺牲速度
4、  **可靠性**
    
    软、硬件崩溃后，InnoDB 数据表驱动会利用日志文件重构修改
    
    可靠性和高速度不可兼得 `innodb_flush_log_at_trx_commit` 选项决定什么时候吧事务保存到日志里

MySQL 命令行的默认设置下，事务都是自动提交的，即执行 SQL 语句后就会马上执行 COMMIT 操作

因此要显式地开启一个事务务须使用命令 `BEGIN` 或 `START TRANSACTION`，或者执行命令 `SET AUTOCOMMIT=0`，用来禁止使用当前会话的自动提交

### MySQL 事物控制语句 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">语句</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">BEGIN<br>START TRANSACTION</td> 
   <td align="left">显式地开启一个事务</td> 
  </tr> 
  <tr> 
   <td align="left">COMMIT</td> 
   <td align="left">提交事务<br>并使已对数据库进行的所有修改成为永久性的</td> 
  </tr> 
  <tr> 
   <td align="left">ROLLBACK</td> 
   <td align="left">回滚会结束用户的事务<br>并撤销正在进行的所有未提交的修改</td> 
  </tr> 
  <tr> 
   <td align="left">SAVEPOINT identifier</td> 
   <td align="left">SAVEPOINT 允许在事务中创建一个保存点<br>一个事务中可以有多个 SAVEPOINT</td> 
  </tr> 
  <tr> 
   <td align="left">RELEASE SAVEPOINT identifier</td> 
   <td align="left">删除一个事务的保存点<br>当没有指定的保存点时，执行该语句会抛出一个异常</td> 
  </tr> 
  <tr> 
   <td align="left">ROLLBACK TO identifier</td> 
   <td align="left">把事务回滚到标记点</td> 
  </tr> 
  <tr> 
   <td align="left">SET TRANSACTION</td> 
   <td align="left">用来设置事务的隔离级别<br>InnoDB 存储引擎提供事务的隔离级别有 READ UNCOMMITTED<br>READ COMMITTED<br>REPEATABLE READ<br>SERIALIZABLE</td> 
  </tr> 
 </tbody> 
</table>

### MYSQL 事务处理主要有两种方法 ###

1、  用 `BEGIN`, `ROLLBACK`, `COMMIT` 来实现
    
    <table> 
     <thead> 
      <tr> 
       <th align="left">命令</th> 
       <th align="left">说明</th> 
      </tr> 
     </thead> 
     <tbody> 
      <tr> 
       <td align="left">BEGIN</td> 
       <td align="left">开始一个事务</td> 
      </tr> 
      <tr> 
       <td align="left">ROLLBACK</td> 
       <td align="left">事务回滚</td> 
      </tr> 
      <tr> 
       <td align="left">COMMIT</td> 
       <td align="left">事务确认</td> 
      </tr> 
     </tbody> 
    </table>
2、  直接用 `SET` 来改变 MySQL 的自动提交模式
    
     *  **SET AUTOCOMMIT=0** 禁止自动提交
     *  **SET AUTOCOMMIT=1** 开启自动提交

## MySQL 事务测试 ##

1、  测试数据准备
    
    运行下面的 SQL 语句在 `ycbbs` 数据库中准备测试数据
    
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
        (3,'Ruby','https://www.ruby-lang.org/','1996-12-25'),
        (4,'Kotlin','http://kotlinlang.org/','2016-02-17');
```
2、  打开两个终端(命令行提示符)，我们称之为 `a` 和 `b` ，然后分别运行下面的语句切换到 `ycbbs` 数据库
    
```
    mysql -u root -p
    use ycbbs;
```
3、  分别在 `a` 和 `b` 终端中运行下列语句查看表中数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    a 终端
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    +----+--------+----------------------------+------------+
    4 rows in set (0.01 sec)
```
    
    b 终端
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    +----+--------+----------------------------+------------+
    4 rows in set (0.01 sec)
```
    
    两个终端中的数据一模一样
4、  在 a 终端中执行以下 SQL 语句开启事务
    
```
    begin;
```
    
    运行结果如下
    
```
    MariaDB [ycbbs]> begin;
    Query OK, 0 rows affected (0.00 sec)
```
5、  在 a 终端中执行以下语句插入两条数据
    
```
    INSERT INTO `tbl_language` (name,url) VALUES
        ('Perl','http://www.perl.org/'),
        ('Scala','http://www.scala-lang.org/');
```
    
    执行结果如下
    
```
    MariaDB [ycbbs]> INSERT INTO `tbl_language` (name,url) VALUES
        ->  ('Perl','http://www.perl.org/'),
        ->  ('Scala','http://www.scala-lang.org/');
    Query OK, 2 rows affected (0.00 sec)
    Records: 2  Duplicates: 0  Warnings: 0
```
    
    提示成功插入 2 两条数据，然后继续在 a 终端中使用下面的语句查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    运行结果如下
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
    6 rows in set (0.00 sec)
```
    
    说明刚刚真的是插入成功了
6、  在 b 终端中输入以下 SQL 命令查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    运行结果如下
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
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
    
    只有 4 条，a 终端中的插入操作没有影响到 b 终端，这就是事务的原理和独特性
7、  在 a 终端中执行以下语句提交事务
    
```
    commit;
```
    
    执行结果如下
    
```
    MariaDB [ycbbs]> commit;
    Query OK, 0 rows affected (0.00 sec)
```
    
    然后继续在 a 终端中使用下面的语句查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    运行结果如下
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
    6 rows in set (0.00 sec)
```
    
    有 6 条记录，说明插入和提交事务时成功的
8、  在 b 终端中输入以下 SQL 命令查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    运行结果如下
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
    6 rows in set (0.00 sec)
```
    
    记录变成 6 条了，说明事务执行成功了，

### 事务回滚演示 ###

1、  继续上面的操作，我们先在 `a` 和 `b` 终端中运行以下 SQL 语句查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    a 终端执行结果
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
    6 rows in set (0.00 sec)
```
    
    b 终端执行结果
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
    6 rows in set (0.00 sec)
```
    
    两个终端数据相同
2、  在 a 终端中执行以下语句开启事务
    
```
    begin;
```
    
    执行结果如下
    
```
    MariaDB [ycbbs]> begin;
    Query OK, 0 rows affected (0.00 sec)
```
    
    继续在 a 终端中执行以下一句删除 id 大于等于 5 的记录
    
```
    DELETE FROM tbl_language where id >= 5;
```
    
    执行结果如下
    
```
    MariaDB [ycbbs]> DELETE FROM tbl_language where id >= 5;
    Query OK, 2 rows affected (0.02 sec)
```
    
    然后执行以下语句查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    执行结果
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
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
    
    哇，只剩下 4 条数据了
3、  然后在 b 终端中输入以下命令查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    执行结果
    
```
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
```
    
    还好还好，还剩下 6 条
4、  赶紧在 a 终端中执行以下命令回滚操作
    
```
    rollback;
```
    
    执行结果如下
    
```
    MariaDB [ycbbs]> rollback;
    Query OK, 0 rows affected (0.01 sec)
```
    
    然后执行以下语句查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    执行结果
    
```
    MariaDB [ycbbs]> SELECT * FROM tbl_language;
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
    6 rows in set (0.00 sec)
```
    
    哇，数据有回来了
5、  然后在 b 终端中输入以下命令查看数据情况
    
```
    SELECT * FROM tbl_language;
```
    
    执行结果
    
```
    +----+--------+----------------------------+------------+
    | id | name   | url                        | founded_at |
    +----+--------+----------------------------+------------+
    |  1 | Python | https://www.ycbbs.vip        | 1991-02-20 |
    |  2 | PHP    | http://www.php.net         | 1994-01-01 |
    |  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
    |  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
    |  5 | Perl   | http://www.perl.org/       | NULL       |
    |  6 | Scala  | http://www.scala-lang.org/ | NULL       |
    +----+--------+----------------------------+------------+
```
    
    原来数据真的没删除

## PHP 中使用事务实例 ##

PHP 中的 `PHP_PDO` 提供了以下三个方法用户进行事务操作

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">PDO::beginTransaction()</td> 
   <td align="left">启动一个事务</td> 
  </tr> 
  <tr> 
   <td align="left">PDO::commit()</td> 
   <td align="left">提交一个事务</td> 
  </tr> 
  <tr> 
   <td align="left">bool PDO::rollBack ()</td> 
   <td align="left">回滚事务</td> 
  </tr> 
 </tbody> 
</table>

我们这里只做简单的演示

### 测试数据准备 ###

运行下面的 SQL 语句在 `ycbbs` 数据库中准备测试数据

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
    (3,'Ruby','https://www.ruby-lang.org/','1996-12-25'),
    (4,'Kotlin','http://kotlinlang.org/','2016-02-17');
```

运行下面的 SQL 语句查看数据情况

```
SELECT * FROM tbl_language;
```

执行结果

```
MariaDB [ycbbs]> SELECT * FROM tbl_language;
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

然后创建一个文件 `main.php` 复制以下代码

```
<?php 

$dbh = new PDO('mysql:host=127.0.0.1;dbname=ycbbs', 'root', '');    

try {
    // 开启事务
    $dbh->beginTransaction();

    $sql=<<<END
INSERT INTO `tbl_language` (name,url) VALUES
    ('Perl','http://www.perl.org/'),
    ('Scala','http://www.scala-lang.org/');
END;


    // 添加两条记录
    $dbh->exec($sql);


    // 删除 id = 1 的记录
    $dbh->exec("DELETE FROM tbl_language WHERE id = 1");

    // 将 id= 3 记录的 name 改成 `php7.2`
    $dbh->exec("UPDATE tbl_language SET name = 'php7.2' WHERE id = 2");

    // 提交事务
    $dbh->commit();

    echo "执行事务成功\n";

} catch (Exception $e) {

  // 如果发生错误，就回滚
  $dbh->rollBack();
  echo "执行事务失败：" , $e->getMessage(),"\n";
}
```

运行 PHP 脚本输出结果如下

```
$ php main.php
执行事务成功
```

在 `mysql>` 终端中输入以下命令查看情况

```
SELECT * FROM tbl_language;
```

执行结果如下

```
MariaDB [ycbbs]> SELECT * FROM tbl_language;
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  2 | php7.2 | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
|  4 | Kotlin | http://kotlinlang.org/     | 2016-02-17 |
|  5 | Perl   | http://www.perl.org/       | NULL       |
|  6 | Scala  | http://www.scala-lang.org/ | NULL       |
+----+--------+----------------------------+------------+
5 rows in set (0.01 sec)
```

可以看到，所有我们想做的修改，都成功了

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")