如果想要获取服务器的元数据，可以使用下表的语句

<table> 
 <thead> 
  <tr> 
   <th>命令</th> 
   <th>说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td>SELECT VERSION()</td> 
   <td>返回服务器版本信息</td> 
  </tr> 
  <tr> 
   <td>SELECT DATABASE()</td> 
   <td>返回当前数据库名 (或者返回空)</td> 
  </tr> 
  <tr> 
   <td>SELECT USER()</td> 
   <td>返回当前用户名</td> 
  </tr> 
  <tr> 
   <td>SHOW STATUS</td> 
   <td>返回服务器状态</td> 
  </tr> 
  <tr> 
   <td>SHOW VARIABLES</td> 
   <td>返回服务器配置变量</td> 
  </tr> 
 </tbody> 
</table>

## `SELECT VERSION()` 获取服务器版本信息 ##

```
MariaDB [ycbbs]> SELECT VERSION();
+-----------------+
| VERSION()       |
+-----------------+
| 10.2.13-MariaDB |
+-----------------+
1 row in set (0.01 sec)
```

## `SELECT DATABASE()` 获取当前数据库名 ##

```
MariaDB [ycbbs]> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| ycbbs       |
+------------+
1 row in set (0.00 sec)
```

如果当前没有选择任何数据库，则返回 `NULL`

```
MariaDB [(none)]> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| NULL       |
+------------+
1 row in set (0.00 sec)
```

## `SELECT USER()` 获取当前用户名 ##

```
MariaDB [(none)]> SELECT USER();
+----------------+
| USER()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)
```

## `SHOW STATUS` 获取服务器状态 ##

```
MariaDB [(none)]> SHOW STATUS;
+----------------------+----------------------------------+
| Variable_name        | Value                            |
+----------------------+----------------------------------+
| Aborted_clients      | 14                               |
| Aborted_connects     | 0                                |
| Access_denied_errors | 0                                |
| Acl_column_grants    | 0                                |
| Acl_database_grants  | 2                                |
| Acl_function_grants  | 0                                |
| Acl_procedure_grants | 
| Binlog_commits       | 0                                |
....
....
483 rows in set (0.02 sec)
```

非常多数据，看不完，我们一般会使用 `LIKE` 语句查询某些具体的值，比如使用下面的语句查询缓存命中率相关的信息

```
SHOW STATUS LIKE '%Qcache%';
```

运行结果如下

```
MariaDB [(none)]> SHOW STATUS LIKE '%Qcache%';
+-------------------------+---------+
| Variable_name           | Value   |
+-------------------------+---------+
| Qcache_free_blocks      | 1       |
| Qcache_free_memory      | 1031312 |
| Qcache_hits             | 0       |
| Qcache_inserts          | 0       |
| Qcache_lowmem_prunes    | 0       |
| Qcache_not_cached       | 0       |
| Qcache_queries_in_cache | 0       |
| Qcache_total_blocks     | 1       |
+-------------------------+---------+
8 rows in set (0.00 sec)
```

## `SHOW VARIABLES` 获取服务器配置变量 ##

```
MariaDB [(none)]> SHOW VARIABLES\G;
*********************** 626. row ***********************
Variable_name: wsrep_slave_threads
        Value: 1
*********************** 627. row ***********************
Variable_name: wsrep_sst_auth
        Value: 
*********************** 628. row ***********************
Variable_name: wsrep_sst_donor
        Value: 
*********************** 629. row ***********************
Variable_name: wsrep_sst_donor_rejects_queries
        Value: OFF
*********************** 630. row ***********************
Variable_name: wsrep_sst_method
        Value: rsync
*********************** 631. row ***********************
Variable_name: wsrep_sst_receive_address
        Value: AUTO
*********************** 632. row ***********************
Variable_name: wsrep_start_position
        Value: 00000000-0000-0000-0000-000000000000:-1
*************************** 633. row ********************
Variable_name: wsrep_sync_wait
        Value: 0
633 rows in set (0.01 sec)
...
...
633 rows in set (0.02 sec)
```

非常多数据，看不完，我们一般会使用 `LIKE` 语句查询某些具体的值，比如使用下面的语句查询编码相关信息

```
SHOW VARIABLES LIKE '%character%';
```

运行结果如下

```
MariaDB [(none)]> SHOW VARIABLES LIKE '%character%';
+-----------------------------------------------------------+
| Variable_name            | Value                                                   |
+--------------------------+--------------------------------+
| character_set_client     | utf8                                                    |
| character_set_connection | utf8                                                    |
| character_set_database   | utf8                                                    |
| character_set_filesystem | binary                                                  |
| character_set_results    | utf8                                                    |
| character_set_server     | utf8                                                    |
| character_set_system     | utf8                                                    |
| character_sets_dir       | .../share/mysql/charsets/      |
+--------------------------+--------------------------------+
8 rows in set (0.00 sec)
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")