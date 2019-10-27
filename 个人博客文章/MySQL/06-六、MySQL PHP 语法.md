很多语言都提供了操作 MySQL 的函数库，包括 PERL, C, C++, JAVA 和 PHP 等等。

这些语言中，尤以 PHP 与 MySQL 的结合最为广泛。

MySQL 与 PHP、Linux、Apache、Nginx 组成的 `LAMP` 和 `LNMP` 最为经典

如果你想了解 MySQL 在 PHP 中的应用，可以访问我们的 PHP MySQL 基础教程

### 范例 ###

下面的代码演示了 PHP 使用 `PDO_MySQL` 操作 MySQL

```
<?php 

/*
 * filename: main.php
 * author: 研发军团(www.ycbbs.vip)
 * 
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
 */
try {    
    $dbh = new PDO('mysql:host=127.0.0.1;dbname=souyunku', 'root', '');

    print_r($dbh);    

    $dbh = null;    
}
catch (PDOException $e) 
{    
    print "Error!: " . $e->getMessage() . "<br/>";    
    die();    
}
```

运行以上 PHP 范例，输出结果如下

```
$ php main.php
PDO Object
(
)
```

在接下来的章节中，我们会学习到更多的 PDO\_MySQL 函数


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")