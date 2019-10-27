使用 `Elasticsearch` 的第一步，就是先把它装起来，而得益于使用 `Java` 作为开发语言，安装 `Elasticsearch` 就非常简单

因为 `Java` 可以跨平台运行，所以 `Elasticsearch` 天生就是可以跨平台的。

## 安装 Java 环境 ##

因为 `Elasticsearch` 对 `Java` 的版本有要求，最低版本必须是 `Java 7` ，因此，你可以在命名行提示符 ( `Windows` ) 或 `Powere` `Shell` ( `Windows` ) 或终端 ( `Linux`、`macOS` ) 中使用下面的命令来检查你的 `Java` 版本

```
java -version
```

因为我的电脑安装的是 `Java 8` 所以，显示结果如下

```
$ java -version
java version "1.8.0_101"
Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)
```

当然了，如果你还未安装 **Java 环境** ，那么可以参考 `Java` 开发环境配置 先安装 `Java` 环境

## 下载 Elasticsearch ##

Elasticsearch 的官方下载地址为 [https://www.elastic.co/downloads/elasticsearch][https_www.elastic.co_downloads_elasticsearch]，截止 2018 年 6 月 27 日最新的版本为 6.3.0

![img\_1.png][img_1.png]

从下载的页面中我们可以看到，官方提供了丰富的下载包

<table> 
 <thead> 
  <tr> 
   <th align="left">包</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">ZIP 或 TAR</td> 
   <td align="left">两种跨平台的通用的压缩格式</td> 
  </tr> 
  <tr> 
   <td align="left">DEB</td> 
   <td align="left">Debian 软件包，是 Ubuntu 和 Debian 系统下的软件安装包格式</td> 
  </tr> 
  <tr> 
   <td align="left">RPM</td> 
   <td align="left">Redhat 和 CentOS 下通用的软件安装包格式</td> 
  </tr> 
  <tr> 
   <td align="left">MSI</td> 
   <td align="left">Windows 系统下软件安装包格式</td> 
  </tr> 
 </tbody> 
</table>

你可以根据自己的操作系统类型选择合适的安装包

1、`windows` 系统下载 `ZIP`
2、`UNIX` 系统下载 `TAR`
3、`Debian` 系统和 `Ubuntu` 系统下载 `DEB`
4、`Red` `Hat` 和其它衍生系统，比如 `CentOS` 下载 RPN

本教程为了跨平台通用原则，使用了 `ZIP` 的包

点击 `ZIP`，就会开始下载名为 `elasticsearch-6.3.0.zip` 的 `Elasticsearch` 压缩包


[https_www.elastic.co_downloads_elasticsearch]: https://www.elastic.co/downloads/elasticsearch
[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/3/img_1.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")