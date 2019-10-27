文章永久连接：[https://tech.souyunku.com/?p=4937](https://tech.souyunku.com/?p=4937)

MySQL 导出数据有两种方法

1、  使用 `SELECT...INTO OUTFILE` 语句来简单的导出数据到某个文件中
2、  使用 `mysqldump`

### 准备测试数据 ###

可以在 `mysql>` 命令行中运行以下语句填充范例数据

```
DROP TABLE IF EXISTS `tbl_language`;
DROP TABLE IF EXISTS `tbl_rank`;

CREATE TABLE IF NOT EXISTS `tbl_language`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `name` VARCHAR(64) NOT NULL,
   `url` VARCHAR(128) NOT NULL,
   `founded_at` DATE,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE IF NOT EXISTS `tbl_rank`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `name` VARCHAR(64) NOT NULL,
   `month` VARCHAR(7) NOT NULL,
   `rank` TINYINT NOT NULL,
   `rate` VARCHAR(32) NOT NULL,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO `tbl_language` VALUES
    (1,'Python','https://tech.souyunku.com','1991-2-20'),
    (2,'PHP','http://www.php.net','1994-1-1'),
    (3,'Ruby','https://www.ruby-lang.org/','1996-12-25');

INSERT INTO `tbl_rank` VALUES
    (1, 'Python','2018-04',4,'5.083%'),
    (2, 'PHP','2018-04',6,'4.218%'),
    (3, 'Ruby','2018-04',11,'2.018%'),
    (4, 'Java','2018-04',1,'15.777%'),
    (5, 'Python','2018-03',4,'5.869%'),
    (6, 'PHP','2018-03',7,'4.010%'),
    (7, 'Ruby','2018-03',12,'2.744%'),
    (8, 'Java','2018-03',1,'14.941'),
    (9, 'Python','2018-02',4,'5.168%'),
    (10, 'PHP','2018-02',7,'3.420%'),
    (11, 'Ruby','2018-02',10,'2.534%'),
    (12, 'Java','2018-02',1,'14.988%');
```

`tbl_language` 表中的数据如下

```
+----+--------+----------------------------+------------+
| id | name   | url                        | founded_at |
+----+--------+----------------------------+------------+
|  1 | Python | https://tech.souyunku.com        | 1991-02-20 |
|  2 | PHP    | http://www.php.net         | 1994-01-01 |
|  3 | Ruby   | https://www.ruby-lang.org/ | 1996-12-25 |
+----+--------+----------------------------+------------+
```

`tbl_rank` 表中的数据如下

```
+----+--------+---------+------+---------+
| id | name   | month   | rank | rate    |
+----+--------+---------+------+---------+
|  1 | Python | 2018-04 |    4 | 5.083%  |
|  2 | PHP    | 2018-04 |    6 | 4.218%  |
|  3 | Ruby   | 2018-04 |   11 | 2.018%  |
|  4 | Java   | 2018-04 |    1 | 15.777% |
|  5 | Python | 2018-03 |    4 | 5.869%  |
|  6 | PHP    | 2018-03 |    7 | 4.010%  |
|  7 | Ruby   | 2018-03 |   12 | 2.744%  |
|  8 | Java   | 2018-03 |    1 | 14.941  |
|  9 | Python | 2018-02 |    4 | 5.168%  |
| 10 | PHP    | 2018-02 |    7 | 3.420%  |
| 11 | Ruby   | 2018-02 |   10 | 2.534%  |
| 12 | Java   | 2018-02 |    1 | 14.988% |
+----+--------+---------+------+---------+
```

## `SELECT ... INTO OUTFILE` 语句导出数据 ##

我们可以使用下面的语句将 `tbl_language` 表导出到 `d:/tbl_language.sql` 文件中

```
SELECT * FROM tbl_language INTO OUTFILE 'd:/tbl_language.sql';
```

如果想到处其它格式，比如 `.csv` 文件，那么可以使用下面的关键字设定格式

```
FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\r\n';
```

<table> 
 <thead> 
  <tr> 
   <th align="left">关键字</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">FIELDS TERMINATED BY</td> 
   <td align="left">设置字段数据间的分隔符</td> 
  </tr> 
  <tr> 
   <td align="left">ENCLOSED BY</td> 
   <td align="left">设置每个字段数据使用 <code>"</code> 引起来</td> 
  </tr> 
  <tr> 
   <td align="left">LINES TERMINATED BY</td> 
   <td align="left">设定每行数据间的分隔符</td> 
  </tr> 
 </tbody> 
</table>

例如下面的语句将 `tbl_language` 导出到 `d:/tbl_language.csv`

```
SELECT * FROM tbl_language INTO OUTFILE 'd:/tbl_language.csv' 
  FIELDS TERMINATED BY ',' 
  ENCLOSED BY '"'
  LINES TERMINATED BY '\r\n';
```

### SELECT ... INTO OUTFILE 语句属性 ###

1、  `LOAD DATA INFILE` 是 `SELECT ... INTO OUTFILE` 的逆操作
    
    为了将一个数据库的数据写入一个文件，使用 `SELECT ... INTO OUTFILE`
    
    为了将文件读回数据库，使用 `LOAD DATA INFILE`
2、  `SELECT...INTO OUTFILE 'file_name'` 可以把被选择的行写入一个文件中
    
    该文件被创建到服务器主机上，因此您必须拥有FILE权限，才能使用此语法
3、  输出不能是一个已存在的文件，防止文件数据被篡改

## mysqldump 导出表作为原始数据 ##

`mysqldump` 是 `MySQL` 用于转存储数据库的实用程序

它主要产生一个 SQL 脚本，其中包含从头重新创建数据库所必需的命令 `CREATE TABLE INSERT` 等

`mysqldump` 导出数据需要使用 `--tab` 选项来指定导出文件指定的目录，该目标必须是可写的

下面的命令用于将 `souyunku` 数据库中的 `tbl_language` 导出到文件 `d:/tbl_language2.sql`

```
$ mysqldump -u root -p --no-create-info  --tab=d:/tbl_language2.sql souyunku tbl_language
```

## 导出 SQL 格式的数据 ##

`mysqldump` 导出 SQL 格式的数据到当前文件夹下的 `tbl_language.sql`

```
mysqldump -u root -p souyunku tbl_language > tbl_language.sql
```

打开文件 `tbl_language.sql` 可以看到下面的内容

```
-- MySQL dump 10.16  Distrib 10.2.13-MariaDB, for osx10.13 (x86_64)
--
-- Host: localhost    Database: souyunku
-- ------------------------------------------------------
-- Server version   10.2.13-MariaDB

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `tbl_language`
--

DROP TABLE IF EXISTS `tbl_language`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `tbl_language` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(64) NOT NULL,
  `url` varchar(128) NOT NULL,
  `founded_at` date DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `tbl_language`
--

LOCK TABLES `tbl_language` WRITE;
/*!40000 ALTER TABLE `tbl_language` DISABLE KEYS */;
INSERT INTO `tbl_language` VALUES (1,'Python','https://tech.souyunku.com','1991-02-20'),(2,'PHP','http://www.php.net','1994-01-01'),(3,'Ruby','https://www.ruby-lang.org/','1996-12-25');
/*!40000 ALTER TABLE `tbl_language` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-04-09 11:00:50
```

如果需要导出整个数据库的数据，可以使用以下命令

```
mysqldump -u root -p souyunku > souyunku.sql
```

如果需要备份所有数据库，可以使用以下命令：

```
mysqldump -u root -p --all-databases > all_database.sql
```

如果只想导出单张表的结构，可以加上 `-d databasename tablename` 参数

```
mysqldump -u root -p  -d souyunku tbl_language > tbl_language.sql
```

如果只想导出某个数据库的所有表结构，可以加上 `-d databasename` 参数

```
mysqldump -u root -p  -d souyunku > souyunku.sql
```

如果只想导出所有数据库的所有表结构，可以加上 `-d` 参数

```
mysqldump -u root -p --all-databases -d > all_database_c.sql
```

## 将数据表及数据库拷贝至其它主机 ##

在 `Linux` 系统或者 `Mac OS` 系统上，如果需要将将数据表及数据库拷贝至其它主机，可以使用管道操作符 `|`

```
mysqldump -u root -p souyunku | mysql -h other-host.com database_name
```


## 干货推荐

[本站推荐：精选优质专栏](https://tech.souyunku.com/?page_id=2)

## 附录：MySQL 教程：系列文章


- [一、MySQLMariaDB 基础教程](https://tech.souyunku.com/?p=4859)
- [二、MySQL 简介](https://tech.souyunku.com/?p=4861)
- [三、MySQL  MariaDB 安装](https://tech.souyunku.com/?p=4863)
- [四、MySQL 管理](https://tech.souyunku.com/?p=4865)
- [五、MySQL 日常管理](https://tech.souyunku.com/?p=4867)
- [六、MySQL PHP 语法](https://tech.souyunku.com/?p=4869)
- [七、MySQL 创建连接](https://tech.souyunku.com/?p=4871)
- [八、MySQL 获取数据库列表](https://tech.souyunku.com/?p=4873)
- [九、MySQL 创建数据库](https://tech.souyunku.com/?p=4875)
- [十、MySQL 删除数据库](https://tech.souyunku.com/?p=4877)
- [十一、MySQL 选择数据库](https://tech.souyunku.com/?p=4879)
- [十二、MySQL 数据类型](https://tech.souyunku.com/?p=4881)
- [十三、MySQL 列出数据表](https://tech.souyunku.com/?p=4883)
- [十四、MySQL 创建数据表](https://tech.souyunku.com/?p=4885)
- [十五、MySQL 删除表](https://tech.souyunku.com/?p=4887)
- [十六、MySQL 插入数据](https://tech.souyunku.com/?p=4889)
- [十七、MySQL 获取插入数据的 ID](https://tech.souyunku.com/?p=4891)
- [十八、MySQL SELECT FROM 查询数据](https://tech.souyunku.com/?p=4893)
- [十九、MySQL WHERE 子句有条件的查询数据](https://tech.souyunku.com/?p=4895)
- [二十、MySQL UPDATE 更新数据](https://tech.souyunku.com/?p=4897)
- [二十一、MySQL DELETE FROM 语句删除数据](https://tech.souyunku.com/?p=4899)
- [二十二、MySQL 返回删改查受影响的行数](https://tech.souyunku.com/?p=4901)
- [二十三、MySQL LIKE 子句模糊查询数据](https://tech.souyunku.com/?p=4903)
- [二十四、MySQL UNION 操作符查询多张表](https://tech.souyunku.com/?p=4905)
- [二十五、MySQL ORDER BY 排序](https://tech.souyunku.com/?p=4907)
- [二十六、MySQL GROUP BY 分组查询数据](https://tech.souyunku.com/?p=4909)
- [二十七、MySQL JOIN 进行多表查询](https://tech.souyunku.com/?p=4911)
- [二十八、MySQL NULL 值处理](https://tech.souyunku.com/?p=4913)
- [二十九、MySQL REGEXP 子句正则表达式查询](https://tech.souyunku.com/?p=4915)
- [三十、MySQL 数据库事务](https://tech.souyunku.com/?p=4917)
- [三十一、MySQL ALTER 命令](https://tech.souyunku.com/?p=4919)
- [三十二、MySQL 索引](https://tech.souyunku.com/?p=4921)
- [三十三、CREATE TEMPORARY TABLE 创建临时表](https://tech.souyunku.com/?p=4923)
- [三十四、MySQL DROP TABLE 删除临时表](https://tech.souyunku.com/?p=4925)
- [三十五、MySQL INSERT INTO SELECT 复制表](https://tech.souyunku.com/?p=4927)
- [三十六、MySQL 获取服务器元数据](https://tech.souyunku.com/?p=4929)
- [三十七、MySQL 自增序列 AUTO_INCREMENT](https://tech.souyunku.com/?p=4931)
- [三十八、MySQL 处理重复数据](https://tech.souyunku.com/?p=4933)
- [三十九、MySQL 安全及防止 SQL 注入攻击](https://tech.souyunku.com/?p=4935)
- <a style="font-size: 18px;color: #FF0000; font-weight: 600;" href="https://tech.souyunku.com/?p=4937">【当前读到】四十、MySQL 导出数据</a>
- [四十一、MySQL 导入数据](https://tech.souyunku.com/?p=4939)
