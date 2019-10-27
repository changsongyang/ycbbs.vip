Docker 镜像只有运行起来，才会创建一个容器，才能提供对外服务

这里我们先不解释什么是镜像，我们只解释它们之间的关系和类比

我们先说镜像，镜像有点类似于已经打包好了，放在腾讯服务器上的，但还没有装在电脑上的 QQ 安装程序

那么容器，就相当于一个已经在用户电脑上安装好的 QQ 软件，只要输入 QQ 号和密码，就可以使用了

它们之间一样么？ 不一样的

1、  QQ 安装程序，人人都可以下，但是只要不安装，它就不能使用，它只是一个安装程序而已
2、  已经安装好的 QQ 程序，这时候就不是人人都能用了，只有电脑的主人才能用，而且这时候可以登录然后聊天了

相比较于 Docker ，我们只有把一个镜像 `run` 起来，它才能对外提供服务

将一个镜像转换成容器，使用的就是 `docker run` 命令

## Docker Hello World ##

`docker run` 命令可以从一个镜像创建一个容器，并运行一个应用程序

比如下面的 `docker run` 命令从 `ubuntu:17.10` 镜像创建一个容器，并运行 `echo "Hello world"` 服务输出 Hello world

```
[root@localhost ~]# docker run ubuntu:17.10 /bin/echo "Hello world"
Unable to find image 'ubuntu:17.10' locally
17、10: Pulling from library/ubuntu
68393378db12: Pull complete 
9e3366501e0e: Pull complete 
156ec05da9a5: Pull complete 
281cba1133d9: Pull complete 
0acdc2cc8ed1: Pull complete 
Digest: sha256:20ed22f88148889c94970cb325b5341f455c820aa6d3289ce4b721b14c78c242
Status: Downloaded newer image for ubuntu:17.10
Hello world
```

<table> 
 <thead> 
  <tr> 
   <th align="left">参数</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">docker</td> 
   <td align="left">Docker 的二进制执行文件</td> 
  </tr> 
  <tr> 
   <td align="left">run</td> 
   <td align="left">与前面的 docker 组合来运行一个容器</td> 
  </tr> 
  <tr> 
   <td align="left">ubuntu:17.10</td> 
   <td align="left">指定要运行的镜像，Docker 首先从本地主机上查找镜像是否存在<br>如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像</td> 
  </tr> 
  <tr> 
   <td align="left">/bin/echo "Hello world</td> 
   <td align="left">在启动的容器里执行的命令</td> 
  </tr> 
 </tbody> 
</table>

更多 `docker start` 命令使用方法，可以访问 docker start 命令

## 运行交互式的容器 ##

使用 `docker run` 如果添加了 `-i` 和 `-t` 或者 `-it` 参数，那么 Docker 就会创建一个交互式的容器，可以实现应答模式

```
[root@localhost ~]# docker run -i -t ubuntu:17.10 /bin/bash
root@1bc08b56d08f:/#  
```

> 这次执行的时候没有从远程拉取 ubuntu:17.10 镜像了，而是使用的本地版本

<table> 
 <thead> 
  <tr> 
   <th align="left">参数</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">-t</td> 
   <td align="left">在新容器内指定一个伪终端或终端</td> 
  </tr> 
  <tr> 
   <td align="left">-i</td> 
   <td align="left">允许对容器内的标准输入 ( STDIN ) 进行交互</td> 
  </tr> 
 </tbody> 
</table>

当出现 `root@1bc08b56d08f:/#` 之后，就进入了 `ubuntu 17.10` 系统的容器

我们可以在 `root@1bc08b56d08f:/#` 之后输入一些 Linux 命令 ，比如使用 `cat /proc/version` 和 `ls` 分别查看当前系统的版本信息和当前目录下的文件列表

```
root@1bc08b56d08f:/# cat /proc/version
Linux version 4.9.87-linuxkit-aufs (root@95fa5ec30613) (gcc version 6.4.0 (Alpine 6.4.0) ) #1 SMP Wed Mar 14 15:12:16 UTC 2018
root@1bc08b56d08f:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

如果要推出交互式容器，可以运行 `exit` 命令或者使用 `CTRL+D` 组合键

## 后台模式 ##

跟所有的 `Linux` 应用程序一样，如果不使用后台模式，那么它们一直会独占 **终端**，如果退出了终端，那么容器就会自动退出

所以，一般情况下我们都希望使用后台模式运行一个容器，可以添加 `-d` 参数

```
[root@localhost ~]# docker run -d ubuntu:17.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
4558b3b54da0ff34fa4c4bc6fb2c3303b2b3f602fad046b205b342b9babc21d6
```

使用后台模式，日志就不会输出到标准输出了，也就是不会显示在 **终端** 上了

这时候显示出来的，当前容器的 ID。

每个容器的 ID 都是唯一的，我们可以通过容器的 ID 来查看容器的状态和容器的所有信息

## 查看容器运行状况 ##

如果想要查看容器的运行状况，可以使用 `docker ps` 命令

```
[root@localhost ~]# docker ps
CONTAINER ID  IMAGE        ...  STATUS        ...
4558b3b54da0  ubuntu:17.10 ...  Up 2 minutes  ...
```

`docker ps` 命令只会显示所有正在运行的容器

如果想要查看所有的容器，那么需要添加 `-a` 参数

```
[root@localhost ~]# docker ps -a
CONTAINER ID  ...  STATUS                    ...
4558b3b54da0  ...  Up 2 minutes              ...
e08201b591cd  ...  Exited (0) 15 minutes ago ...
6801e4604a32  ...  Exited (0) 15 minutes ago ...
```

更多 `docker ps` 命令使用方法，可以访问 docker ps 命令

## 查看容器的运行日志 ##

后台容器的输出不会显示在 **终端** 上，而是被 `Docker` 收集起来了

我们可以通过 `docker logs` 命令查看容器的日志

```
docker logs <container_id>
```

例如想要查看容器 `4558b3b54da0` 的日志，可以使用下面的命令

```
[root@localhost ~]# docker logs 4558b3b54da0
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
......
```

更多 `docker logs` 命令使用方法，可以访问 docker logs 命令

## 停止容器 ##

如果想要主动停止容器，可以使用 `docker stop` 命令

```
docker stop <container_id>
```

我们可以使用下面的命令来停止容器 `4558b3b54da0`

```
[root@localhost ~]# docker stop 4558b3b54da0
4558b3b54da0
```

`docker stop` 命令的返回值是停止的容器 ID

现在通过 `docker ps -a` 查看，容器已经停止工作

```
[root@localhost ~]# docker ps -a
CONTAINER ID  ... STATUS                     ...
6801e4604a32  ... Exited (0) 15 minutes ago  ...
```

更多 `docker stop` 命令使用方法，可以访问 docker stop 命令

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")