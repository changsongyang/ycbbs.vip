前面一章节中，我们使用 `docker build` 创建了一个 `ycbbs/flask_365_102` 的镜像

但是，我们创建的镜像的标签都是 `latest`

```
REPOSITORY         TAG    IMAGE ID     CREATED        SIZE
ycbbs/flask_365_102 latest 083eecd092af 27 seconds ago 922MB
```

如果我们可以将标签设置为 `365_102` 那该多好

## 设置镜像标签 ##

我们可以使用 `docker tag` 命令，为镜像添加一个新的标签

```
[root@localhost flask_365_102]# docker tag 083eecd092af ycbbs/my-flask:365-102
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
   <td align="left">083eecd092af</td> 
   <td align="left">镜像 ID</td> 
  </tr> 
  <tr> 
   <td align="left">ycbbs</td> 
   <td align="left">用户名称</td> 
  </tr> 
  <tr> 
   <td align="left">my-flask:365-102</td> 
   <td align="left">镜像源名(repository name)和新的标签名</td> 
  </tr> 
 </tbody> 
</table>

使用 docker images 命令可以看到，ID 为 083eecd092af 的镜像多一个标签

```
[root@localhost flask_365_102]# docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
ycbbs/my-flask        365-102             083eecd092af        13 minutes ago      922MB
ycbbs/flask_365_102   latest              083eecd092af        13 minutes ago      922MB
ycbbs/py365flask102   latest              aaf108c1dbdc        4 hours ago         922MB
python               3.6.5               29d2f3226daf        3 weeks ago         912MB
nginx                latest              ae513a47849c        4 weeks ago         109MB
ubuntu               latest              452a96d81c30        4 weeks ago         79.6MB
ubuntu               17.10               e4422b8da209        4 weeks ago         99.2MB
hello-world          latest              e38bc07ac18e        7 weeks ago         1.85kB
jcdemo/flaskapp      latest              084bae02af1b        4 months ago        98.9MB
ubuntu               17.04               fe1cc5b91830        5 months ago        95.6MB
ubuntu               16.10               7d3f705d307c        10 months ago       107MB
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")