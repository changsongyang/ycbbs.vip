如果你对 `Docker` 有那么一点了解，就知道 `Docker` 是一个开源的应用容器引擎

那么它是一个什么的应用引擎，它之前，又是怎么做的呢？

## 虚拟化 ##

在阿里云等还没横空出世前，我们用的最多的是什么？ 虚拟主机

想必早期的站长都知道，那个时候买的虚拟主机，是大家的网站都放在一台电脑上，不同的目录下而已，说直白一点，买虚拟主机，其实就是买了一个固定大小的文件夹

因为一台比较好的服务器是很贵的，如果只运行一个网站，资源也太浪费了，如果是多个网站，那么多个网站之间就要相互隔离，不然就可以相互访问对方的数据了

相互隔离的方式有很多中，比如现在常见的

1、  `vmware`
2、  `virtualbox`
3、  `kvm`
4、  `xen`

这四种软件，又称之为虚拟化，说的直白一点，就是隔离操作系统各个应用程序，使得它们之间相互独立

这几种虚拟化技术是怎么做到的呢？ 看一张图

![img\_1.png][img_1.png]

每一个应用程序都自带一个小操作系统，和系统类库，这样就有点资源浪费了 ( 虽然硬盘不值几个钱 )

Docker 出现了之后，这种状况就改变了，因为它采用了下面这种方式

![img\_2.png][img_2.png]

由 `Docker` 来屏蔽各个应用程序

## Docker 工作模式 ##

`Docker` 使用客户端-服务器 ( C/S ) 架构模式，使用远程 `API` 来管理和创建 `Docker` 容器

`Docker` 容器通过 `Docker` 镜像来创建

容器与镜像的关系类似于面向对象编程中的对象与类

<table> 
 <thead> 
  <tr> 
   <th align="left">Docker</th> 
   <th align="left">面向对象</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">容器</td> 
   <td align="left">对象</td> 
  </tr> 
  <tr> 
   <td align="left">镜像</td> 
   <td align="left">类</td> 
  </tr> 
 </tbody> 
</table>

![img\_3.png][img_3.png]

<table> 
 <thead> 
  <tr> 
   <th align="left">名词</th> 
   <th align="left">解释</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">Docker 镜像(Images)</td> 
   <td align="left">Docker 镜像是用于创建 Docker 容器的模板</td> 
  </tr> 
  <tr> 
   <td align="left">Docker 容器(Container)</td> 
   <td align="left">容器是独立运行的一个或一组应用</td> 
  </tr> 
  <tr> 
   <td align="left">Docker 客户端(Client)</td> 
   <td align="left">Docker 客户端通过命令行或者其他工具使用 <a href="https://docs.docker.com/reference/api/docker_remote_api" rel="nofollow">Docker API</a> 与 Docker 的守护进程通信</td> 
  </tr> 
  <tr> 
   <td align="left">Docker 主机(Host)</td> 
   <td align="left">一个物理或者虚拟的机器用于执行 Docker 守护进程和容器</td> 
  </tr> 
  <tr> 
   <td align="left">Docker 仓库(Registry)</td> 
   <td align="left">Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库<br><a href="https://hub.docker.com" rel="nofollow">Docker Hub</a> 提供了庞大的镜像集合供使用</td> 
  </tr> 
  <tr> 
   <td align="left">Docker Machine</td> 
   <td align="left">Docker Machine 是一个简化 Docker 安装的命令行工具<br>通过一个简单的命令行即可在相应的平台上安装 Docker<br>比如 VirtualBox、 Digital Ocean、Microsoft Azure</td> 
  </tr> 
 </tbody> 
</table>

[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/2/img_1.png
[img_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/2/img_2.png
[img_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/2/img_3.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
