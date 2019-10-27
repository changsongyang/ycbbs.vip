通过以下命令来检查 `MySQL` 服务器是否启动

```
ps -ef | grep mysql
```

例如在我的苹果电脑上运行如下

```
[root@localhost ~]# ps -ef | grep mysql
mysql      1520      1  0 18:21 ?        00:00:00 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
mysql      1682   1520  0 18:21 ?        00:00:00 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariadb/mariadb.log --pid-file=/var/run/mariadb/mariadb.pid --socket=/var/lib/mysql/mysql.sock
root       1774   1744  0 18:29 pts/0    00:00:00 grep --color=auto mysql
```

如果 `MySql` 已经启动，上面的命令会输出 `mysql` 进程列表 `mysqld_safe` 和 `mysqld`

## 启动 MySQL 命令 ##

如果 `MySQL` 未启动，使用以下命令来启动 `MySQL` 服务器

```
[root@localhost ~]# mysqld_safe &
```

## 关闭 MySQL 命令 ##

如果想关闭目前运行的 `MySQL` 服务器, 可以执行以下命令

```
[root@localhost ~]# mysqladmin -u root -p shutdown
Enter password: ******
```

## MySQL 添加用户 ##

MySQL 服务器默认已经添加了 **root** 用户

如果需要添加 `MySQL` 用户，我们只需要在数据库 `mysql` 中的表 `user` 中插入新用户即可

下面的 `SQL` 语句可以添加一个新的用户

用户名为 `demo` 密码为 `demo123` 并授权用户可进行 `SELECT,INSERT,UPDATE` 操作权限

```
INSERT INTO mysql.user (host, user, password, select_priv, insert_priv, update_priv) VALUES ('localhost', 'demo', PASSWORD('demo123'), 'Y', 'Y', 'Y');
```

然后刷新权限

```
FLUSH PRIVILEGES;
```

你可以按照下面的步骤尝试一下

```
[root@localhost ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 6
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> USE  mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> INSERT INTO user (host, user, password, select_priv, insert_priv, update_priv) VALUES ('localhost', 'demo', PASSWORD('demo123'), 'Y', 'Y', 'Y');
Query OK, 1 row affected, 4 warnings (0.01 sec)

MariaDB [mysql]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> SELECT host, user, password FROM user WHERE user = 'demo';
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | demo | *3055544BD641D0814B910C4ACA5799F51B80F460 |
+-----------+------+-------------------------------------------+
1 row in set (0.01 sec)
```

在添加用户时，请注意使用 `MySQL` 提供的 `PASSWORD()` 函数来对密码进行加密

我们在上面的范例中看到用户密码加密后为：

```
 3055544BD641D0814B910C4ACA5799F51B80F460
```

### 注意 ###

1、  在 `MySQL 5.7` 中 `user` 表的 `password` 已换成了 **authentication\_string**
2、  添加用户后需要执行 **FLUSH PRIVILEGES** 语句，这个命令执行后会重新载入授权表 如果使用该命令，无法使用新创建的用户来连接 `MySQL` 服务器，除非重启 `MySQL` 服务器

### 用户权限 ###

可以在创建用户时，为用户指定权限，在对应的权限列中，在插入语句中设置为 'Y' 即可

**用户权限说明**

<table> 
 <thead> 
  <tr> 
   <th align="left">命令标识</th> 
   <th align="left">授权表中对应的列</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">CREATE</td> 
   <td align="left">Create_priv</td> 
   <td align="left">创建数据库、表或索引</td> 
  </tr> 
  <tr> 
   <td align="left">CREATE TEMPORARY TABLES</td> 
   <td align="left">Create_tmp_table_priv</td> 
   <td align="left">创建临时数据表</td> 
  </tr> 
  <tr> 
   <td align="left">CREATE ROUTINE</td> 
   <td align="left">Create_routine_priv</td> 
   <td align="left">创建函数或存储</td> 
  </tr> 
  <tr> 
   <td align="left">CREATE VIEW</td> 
   <td align="left">Create_view_priv</td> 
   <td align="left">创建视图</td> 
  </tr> 
  <tr> 
   <td align="left">CREATE USER</td> 
   <td align="left">Create_user_priv</td> 
   <td align="left">创建用户</td> 
  </tr> 
  <tr> 
   <td align="left">EXECUTE</td> 
   <td align="left">Execute_priv</td> 
   <td align="left">执行函数或存储过程</td> 
  </tr> 
  <tr> 
   <td align="left">INDEX</td> 
   <td align="left">Index_priv</td> 
   <td align="left">建立索引</td> 
  </tr> 
  <tr> 
   <td align="left">REFERENCES</td> 
   <td align="left">References_priv</td> 
   <td align="left">建立约束</td> 
  </tr> 
  <tr> 
   <td align="left">DROP</td> 
   <td align="left">Drop_priv</td> 
   <td align="left">删除表</td> 
  </tr> 
  <tr> 
   <td align="left">SELECT</td> 
   <td align="left">Select_priv</td> 
   <td align="left">查询数据</td> 
  </tr> 
  <tr> 
   <td align="left">INSERT</td> 
   <td align="left">Insert_priv</td> 
   <td align="left">插入数据</td> 
  </tr> 
  <tr> 
   <td align="left">UPDATE</td> 
   <td align="left">Update_priv</td> 
   <td align="left">更新数据</td> 
  </tr> 
  <tr> 
   <td align="left">DELETE</td> 
   <td align="left">Delete_priv</td> 
   <td align="left">删除数据</td> 
  </tr> 
  <tr> 
   <td align="left">LOCK TABLES</td> 
   <td align="left">Lock_tables_priv</td> 
   <td align="left">锁定表格</td> 
  </tr> 
  <tr> 
   <td align="left">SHOW DATABASES</td> 
   <td align="left">Show_db_priv</td> 
   <td align="left">列出数据库</td> 
  </tr> 
  <tr> 
   <td align="left">SHOW VIEW</td> 
   <td align="left">Show_view_priv</td> 
   <td align="left">列出视图</td> 
  </tr> 
  <tr> 
   <td align="left">USAGE</td> 
   <td align="left"></td> 
   <td align="left">只有登录权限， 其它权限都没有</td> 
  </tr> 
  <tr> 
   <td align="left">ALL</td> 
   <td align="left"></td> 
   <td align="left">所有权限,除了 WITH GRANT OPTION</td> 
  </tr> 
  <tr> 
   <td align="left">ALTER</td> 
   <td align="left">Alter_priv</td> 
   <td align="left">更改数据表</td> 
  </tr> 
  <tr> 
   <td align="left">ALTER ROUTINE</td> 
   <td align="left">Alter_routine_priv</td> 
   <td align="left">更改函数或存储过程</td> 
  </tr> 
  <tr> 
   <td align="left">PROCESS</td> 
   <td align="left">Process_priv</td> 
   <td align="left">显示连接进程和中断连接进程</td> 
  </tr> 
  <tr> 
   <td align="left">FILE</td> 
   <td align="left">File_priv</td> 
   <td align="left">载入文件</td> 
  </tr> 
  <tr> 
   <td align="left">RELOAD</td> 
   <td align="left">Reload_priv</td> 
   <td align="left">可以用 FLUSH</td> 
  </tr> 
  <tr> 
   <td align="left">REPLICATION CLIENT</td> 
   <td align="left">Repl_client_priv</td> 
   <td align="left">可以检查 Masters 和 Slaves</td> 
  </tr> 
  <tr> 
   <td align="left">REPLICATION SLAVE</td> 
   <td align="left">Repl_slave_priv</td> 
   <td align="left">在 Slave 里的特殊权限</td> 
  </tr> 
  <tr> 
   <td align="left">SHUTDOWN</td> 
   <td align="left">Shutdown_priv</td> 
   <td align="left">关闭 MySQL</td> 
  </tr> 
  <tr> 
   <td align="left">WITH GRANT OPTION</td> 
   <td align="left">Grant_priv</td> 
   <td align="left">可以将自己拥有的权限赋给其它用户</td> 
  </tr> 
  <tr> 
   <td align="left">SUPER</td> 
   <td align="left">Super_priv</td> 
   <td align="left">执行 kill 线程，change master、purge master logs、set global等命令的权限</td> 
  </tr> 
  <tr> 
   <td align="left">create tablespace</td> 
   <td align="left">Create_tablespace_priv</td> 
   <td align="left">创建表空间</td> 
  </tr> 
  <tr> 
   <td align="left">Event</td> 
   <td align="left">Event_priv</td> 
   <td align="left">确定用户能否创建、修改和删除事件</td> 
  </tr> 
  <tr> 
   <td align="left">Trigger</td> 
   <td align="left">Trigger_priv</td> 
   <td align="left">确定用户能否创建和删除触发器</td> 
  </tr> 
 </tbody> 
</table>

### GRANT 命令添加用户 ###

下面的 SQL 语句会给指定数据库 `test` 添加用户 `ycbbs1` ，密码为 `ycbbs123xyz`

```
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON test.* TO 'ycbbs1'@'localhost' IDENTIFIED BY 'ycbbs123xyz';
```

操作演示

```
[root@localhost ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> USE  mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON test.* TO 'ycbbs1'@'localhost' IDENTIFIED BY 'ycbbs123xyz';
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> SELECT host, user, password FROM user;
+-----------------------+-------+-------------------------------------------+
| host                  | user  | password                                  |
+-----------------------+-------+-------------------------------------------+
| localhost             | root  |                                           |
| localhost.localdomain | root  |                                           |
| 127.0.0.1             | root  |                                           |
| ::1                   | root  |                                           |
| localhost             |       |                                           |
| localhost.localdomain |       |                                           |
| localhost             | demo  | *3055544BD641D0814B910C4ACA5799F51B80F460 |
| localhost             | ycbbs1 | *302D386BA9E00875ADDF9ED0AD447B19FAE2FEDB |
+-----------------------+-------+-------------------------------------------+
8 rows in set (0.00 sec)

MariaDB [mysql]>
```

## /etc/my.cnf 配置文件 ##

一般情况下，我们不需要修改该配置文件，该文件默认配置如下：

```
[root@localhost ~]# cat /etc/my.cnf
```

显示内容如下

```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
```

在配置文件中，我们可以指定不同的错误日志文件存放的目录，但一般不需要改动这些配置


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")