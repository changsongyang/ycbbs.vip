当我们要重新设置 `MySQL sql\_mode` 的时候，可能看到那一串长长的列表就会患头痛病，这哪个是哪个啊，哪个是重要的啊？哪个是可以缺少的啊？

我们就会想要呼唤一个简单的版本，要是有三个特殊的 `sql\_mode` 分别表示宽松、标准、严格那该多好，出现 `sql\_mode` 问题的时候就先设置宽松版本

`MySQL` 也考虑到了这个问题，所以在 `5.0` 以后的版本中新增加了三个特殊的 `sql\_mode`

<table> 
<thead> 
<tr> 
<th align="left">sql_mode</th> 
<th align="left">说明</th> 
</tr> 
</thead> 
<tbody> 
<tr> 
<td align="left">ANSI</td> 
<td align="left">宽松模式，对于大部分的 sql 语句按照古老的模式执行成功，但会抛出一个警告</td> 
</tr> 
<tr> 
<td align="left">TRADITIONAL</td> 
<td align="left">算是标准模式吧，这种模式下有些 <a rel="nofollow">sql</a> 语句就会直接出错</td> 
</tr> 
<tr> 
<td align="left">STRICT_TRANS_TABLES</td> 
<td align="left">严格模式，这种模式下只要一条 SQL 语句出错就会直接中断事务的执行</td> 
</tr> 
</tbody> 
</table>

而设置这三种模式的语法也很简单

1、设置当前连接的 sql\_mode

```
set @@sql_mode=TRADITIONAL; 
set @@sql_mode=STRICT_TRANS_TABLES;
set @@sql_mode=ANSI;
```
2、设置全局的 sql\_mode

```
set global @@sql_mode=TRADITIONAL; 
set global @@sql_mode=STRICT_TRANS_TABLES;
set global @@sql_mode=ANSI;
```
3、持久化设置，也就是更改 my.cnf 文件

```
[mysqlnd]
sql_mode=TRADITIONAL
sql_mode=STRICT_TRANS_TABLES
sql_mode=ANSI
```

对了，三者你只能选择一个

对于我们前面提到的 `timestamp` 默认值 `0` 错误等，可以直接使用 `ANSI` 模式，这样便可以插入数据，而对于除数为0的结果的字段值，数据库将会用 `NULL` 值代替

刚刚我们不是说了这三种特殊的模式是一些其它模式的结合体吗？我们下面就来看看它们包括哪些具体的 sql\_mode

1、ANSI

相当于

```
REAL_AS_FLOAT
PIPES_AS_CONCAT
ANSI_QUOTES
IGNORE_SPACE
ONLY_FULL_GROUP_BY
```

注意，还是包含了 `ONLY_FULL_GROUP_BY`
2、STRICT\_TRANS\_TABLES

它不是一个结合体，而是一个单独体，在这个模式下，会进行数据的严格校验，错误数据不能插入，报 error 错误

如果不能将给定的值插入到事务表中，则放弃该语句

对于非事务表，如果值出现在单行语句或多行语句的第1行，则放弃该语句
3、`TRADITIONAL`

相当于

```
STRICT_TRANS_TABLES
STRICT_ALL_TABLES
NO_ZERO_IN_DATE
NO_ZERO_DATE
ERROR_FOR_DIVISION_BY_ZERO
NO_ENGINE_SUBSTITUTION
```

注意，包含了 `STRICT_TRANS_TABLES`

时间有限，这一章节我们先讲到这里，我觉得 sql\_mode 还是看官方的文档来的最好


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")