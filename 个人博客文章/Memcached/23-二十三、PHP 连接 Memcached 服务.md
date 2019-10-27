在前面章节中我们已经介绍了如何安装 `Memcached` 服务,也介绍了 `Memcached` 的各种命令

现在我们为大家介绍 `PHP` 如何使用 `Memcached` 服务。

### PHP Memcached 客户端 ###

`PHP` `Memcached` 的客户端有两个：

1、 ` PHP-Memcache`
2、  `PHP-Memcached` 推荐

我们推荐大家使用 **PHP-Memcached**

### PHP Memcached 扩展安装 ###

`PHP` `Memcached` 扩展包下载地址： [http://pecl.php.net/package/memcached][http_pecl.php.net_package_memcached] ，你可以下载最新稳定包(`stable`)

```
#下载
wget http://pecl.php.net/get/memcached-3.0.3.tgz              

# 解压
tar -zxvf memcached-3.0.3.tgz

cd memcached-3.0.3

# 生成编译配置文件
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config

# 编译安装
make && make install
```

> **注意：** /usr/local/php/ 为php的安装路径，需要根据你安装的实际目录调整

### 如果你使用的是 PHP7 ###

如果你是 `PHP7` 版本，则需要下载指定分支：

```
git clone -b php7 https://github.com/php-memcached-dev/php-memcached.git
```

如果你的系统还未编译 `libmemcached`，则下载编译它：

[https://launchpad.net/libmemcached/+download][https_launchpad.net_libmemcached_download]

### 配置 php.ini ###

安装成功后会显示你的 **memcache.so** 扩展的位置，例如：

```
Installing shared extensions:   /usr/local/opt/php56-memcached/
```

最后需要把这个扩展添加到 `php` 中，打开你的 `php.ini` 文件在最后添加以下内容：

```
[Memcache]
extension = "/usr/local/opt/php56-memcached/memcached.so"
```

添加完后,可以用下面的命令检查下安装是否正确

```
php -i | grep memcache
```

如果返回结果里出现了 `memcache support => enabled` 字样基本就安装正确了

### 重启 php-fpm ###

重新启动 php-fpm, 如果你用的是 nginx+php-fpm 那么只要：

```
kill -USR2 `cat /usr/local/php/var/run/php-fpm.pid`
```

如果是apache的使用以下命令:

```
/usr/local/apache2/bin/apachectl restart
```

## PHP 连接 Memcached ##

```
<?php
$mc = new Memcached('mc');  //创建一个 memcached 线程池
$mc->setOption(Memcached::OPT_LIBKETAMA_COMPATIBLE, true);
$mc->addServers(array(
    array('127.0.0.1',11211)
));

$is_ok = $mc->set("site","www.ycbbs.vip");  // 设置一个 key 为 site value 为 www.ycbbs.vip 的缓存

var_dump($is_ok);

$site = $mc->get("site");        // 从缓存中取出 key 为 site 的值

var_dump($site);
```

上面代码运行结果为:

```
bool(true)
string(11) "www.ycbbs.vip"
```

更多 `PHP` 操作 `Memcached` 请参阅：

 *  [http://php.net/manual/zh/book.memcached.php][http_php.net_manual_zh_book.memcached.php] 推荐
 *  [http://php.net/manual/zh/book.memcache.php][http_php.net_manual_zh_book.memcache.php]


[http_pecl.php.net_package_memcached]: http://pecl.php.net/package/memcached
[https_launchpad.net_libmemcached_download]: https://launchpad.net/libmemcached/+download
[http_php.net_manual_zh_book.memcached.php]: http://php.net/manual/zh/book.memcached.php
[http_php.net_manual_zh_book.memcache.php]: http://php.net/manual/zh/book.memcache.php


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")