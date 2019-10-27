在 MySQSL CREATE TEMPORARY TABLE 创建临时表 中我们学习了 MySQL 临时表的特性和如何创建临时表

临时表只在当前连接可见，当关闭连接时，MySQL 会自动删除表并释放所有空间

但有时候我们可能需要手动删除临时表，比如持久性连接中，我们就要用完即删除

删除临时表和删除普通表的 SQL 语法时一模一样的，都是 `DROP TABLE tablename`

比如要删除临时表 `tbl_language_tmp` 则可以使用下面的语句

```
DROP TABLE tbl_language_tmp;
```

### 演示 ###

我们先使用下面的语句创建一个临时表 `tbl_language_temp`

```
CREATE TEMPORARY TABLE IF NOT EXISTS `tbl_language_tmp`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `name` VARCHAR(64) NOT NULL,
   `url` VARCHAR(128) NOT NULL,
   `founded_at` DATE,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

运行结果如下

```
MariaDB [ycbbs]> CREATE TEMPORARY TABLE IF NOT EXISTS `tbl_language_tmp`(
->            `id` INT UNSIGNED AUTO_INCREMENT,
->            `name` VARCHAR(64) NOT NULL,
->            `url` VARCHAR(128) NOT NULL,
->            `founded_at` DATE,
->            PRIMARY KEY ( `id` )
->         )ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
Query OK, 0 rows affected (0.01 sec)
```

可以使用下面的语句往 `tbl_language_tmp` 中插入数据

```
INSERT INTO `tbl_language_tmp` (`name`,`url`,`founded_at`) VALUES ('Python','https://www.ycbbs.vip','1991-2-20');
```

运行结果如下

```
MariaDB [ycbbs]> INSERT INTO `tbl_language_tmp` (`name`,`url`,`founded_at`) VALUES ('Python','https://www.ycbbs.vip','1991-2-20');
Query OK, 1 row affected (0.00 sec)
```

可以使用 `SELECT * FROM tbl_language_tmp;` 查询表中的数据

```
MariaDB [ycbbs]> SELECT * FROM tbl_language_tmp;
+----+--------+---------------------+------------+
| id | name   | url                 | founded_at |
+----+--------+---------------------+------------+
|  1 | Python | https://www.ycbbs.vip | 1991-02-20 |
+----+--------+---------------------+------------+
1 row in set (0.00 sec)
```

然后使用 `DROP TABLE tbl_language_tmp;`

```
MariaDB [ycbbs]> DROP TABLE tbl_language_tmp;
Query OK, 0 rows affected (0.00 sec)
```

在使用 `SELECT * FROM tbl_language_tmp;` 查询表中的数据

```
MariaDB [ycbbs]> SELECT * FROM tbl_language_tmp;
ERROR 1146 (42S02): Table 'ycbbs.tbl_language_tmp' doesn't exist
```

可以看到临时表已经被删除

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")