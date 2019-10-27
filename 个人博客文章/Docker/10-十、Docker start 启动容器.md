对于一个已经是停止状态的容器，我们可以重复使用它们，而再次启动它们的命令就是 `docker start`

```
docker start <container_id>
```

我们先使用 `docker ps -a` 命令看一下所有容器的状态

```
[root@localhost ~]# docker ps -a
CONTAINER ID  ...  STATUS                         ...   
cf38bec0c26f  ...  Exited (0) 2 seconds ago       ...
e66458d65564  ...  Up 23 minutes                  ...  
4558b3b54da0  ...  Exited (137) About an hour ago ...   
e08201b591cd  ...  Exited (0) About an hour ago   ...
```

找到那条已经停止的，且用来输出 `Hello World` 的容器，在我这里是 `cf38bec0c26f`

我们先使用 `docker logs` 命令查看容器 `cf38bec0c26f` 的日志

```
[root@localhost ~]#  docker logs cf38bec0c26f
Hello world
```

然后我们就可以使用 `docker start` 重启这个容器 `cf38bec0c26f` 了

```
[root@localhost ~]# docker start cf38bec0c26f
cf38bec0c26f
```

`docker start` 返回的是启动的容器的 ID，至于容器的日志，会统一保存到 `Docker logs` 中

然后我们就可以使用 `docker logs` 命令查看刚刚容器的输出

```
[root@localhost ~]#  docker logs cf38bec0c26f
Hello world
Hello world
```

更多 `docker start` 命令使用方法，可以访问 docker start 命令

## docker start vs docker run ##

`docker run` 和 `docker start` 看起来好像都是启动一个容器，但它们是有本质区别的

1、  `docker run` 从一个镜像启动一个容器，多次运行同样的命令会创建多个容器
2、  `docker start` 从一个容器 ID 启动一个容器，多次启动同一个容器的 ID 的结果都一样

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")