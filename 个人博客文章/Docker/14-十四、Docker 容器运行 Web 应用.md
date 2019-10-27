前面我们运行的容器并没什么大作用，这次，我们尝试使用 docker 构建一个 web 应用程序

我们将在 Docker 容器中运行一个 Python Flask 应用来运行一个 web 应用

我特意去 [Docker Hub][] 找了一圈，发现这个 `jcdemo/flaskapp` 是最好的，小，而且刚好满足我们的需求

## 拉取 `jcdemo/flaskapp` 镜像 ##

使用下面的命令将 `jcdemo/flaskapp` 镜像拉取到本地

```
[root@localhost ~]# docker pull jcdemo/flaskapp
latest: Pulling from jcdemo/flaskapp
81033e7c1d6a: Pull complete 
9b61101706a6: Pull complete 
35b21c1a8b97: Pull complete 
4856f5aeeec4: Pull complete 
ce9545e7847c: Pull complete 
c0a17c8bb7b9: Pull complete 
c8d70066c010: Pull complete 
Digest: sha256:c0c7fb38dfa6dc371e8c04827eb83ff265401ddc25b624ed58d05b1cce0026f8
Status: Downloaded newer image for jcdemo/flaskapp:latest
f6a0e149983a29294abd76c141f44e8da59bcbaf1d283be63fca803a486f9582
```

## Docker 容器运行 Web 应用 ##

我们使用下面的命令运行我们的 Web 应用容器 `jcdemo/flaskapp`

```
[root@localhost ~]# docker run -d -P jcdemo/flaskapp
d07b599f11e473c3112002f9b697efd4f01b0f7563490db65cfc11ba8d816bbb
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
   <td align="left">-d</td> 
   <td align="left">让容器在后台运行</td> 
  </tr> 
  <tr> 
   <td align="left">-P</td> 
   <td align="left">将容器内部使用的网络端口映射到我们使用的主机上</td> 
  </tr> 
 </tbody> 
</table>

## 查看 WEB 应用容器 ##

使用 docker ps 来查看我们正在运行的容器 (字段有删除)

```
[root@localhost ~]# docker ps
CONTAINER ID  STATUS         PORTS
d07b599f11e4  Up 3 minutes   0.0.0.0:32768->5000/tcp
```

可以看到，想比较于之间的容器，这个容器的 `PORTS` 字段是有值的

```
PORTS
0、0、0.0:32768->5000/tcp
```

Docker 开放了 5000 端口（默认 Python Flask 端口）映射到主机端口 32768 上

这样我们就可以通过 **http://localhost:32768/** 来访问 Web 应用

![img\_1.png][img_1.png]


[Docker Hub]: https://hub.docker.com

[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/14/img_1.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")