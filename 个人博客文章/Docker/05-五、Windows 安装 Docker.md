虽然早期的 Docker 不支持 Windows，但是最新的版本都可以使用 `Docker ToolBox`

Docker 引擎使用的是 Linux 内核特性，所以需要在 Windows 上使用一个轻量级的虚拟机 (VM) 来运行 Docker

Windows 上安装 Docker 有两种方式

1、  如果你的电脑是 `Window 10` 且是专业版，那么可以使用官方的 `Docker for Windows`
    
    官网地址为: [https://www.docker.com/docker-windows][https_www.docker.com_docker-windows]
    
    安装包下载地址为: [https://store.docker.com/editions/community/docker-ce-desktop-windows][https_store.docker.com_editions_community_docker-ce-desktop-windows]
2、  如果是 `Window 10` 家庭版及以下版本，则可以使用 `Docker ToolBox`
    
    官网地址为: [https://docs.docker.com/toolbox/toolbox\_install\_windows/][https_docs.docker.com_toolbox_toolbox_install_windows]
    
    下载地址为: [https://download.docker.com/win/stable/DockerToolbox.exe][https_download.docker.com_win_stable_DockerToolbox.exe]
    
    阿里云提供了镜像地址: [http://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/][http_mirrors.aliyun.com_docker-toolbox_windows_docker-toolbox]

## Window 10 专业版安装 Docker ##

Window 10 上安装 Docker ，我们推荐使用 `Docker for Windows`

1、  点击下载 CE 版本的 `Docker for Windows`
    
    可以访问 [https://store.docker.com/editions/community/docker-ce-desktop-windows][https_store.docker.com_editions_community_docker-ce-desktop-windows] 页面，然后在页面的右侧有一个按钮 `Please Login To Download`
    
    ![img\_1.png][img_1.png]
    
    需要登录才能下载，不管怎么样，先注册一下，然后点击下载即可
    
    ![img\_2.png][img_2.png]
    
    如果你不想注册，可以直接点击 [https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe][https_download.docker.com_win_stable_Docker_20for_20Windows_20Installer.exe] 来下载
2、  双击下载后的 `Docker for Windows Installer.exe` ，然后一路默认 `next` 即可
    
    ![img\_3.png][img_3.png]
3、  安装成功后，`Docker` 并不会立即启动，所以需要我们手动启动一下
    
    在搜索栏输入 `Docker` 然后选择 `Docker for Windows` 并回车，会打开如下界面
    
    ![img\_4.png][img_4.png]
4、  经过一段时间后，会在右下角的通知看到 `docker` 的小图标
    
    ![img\_5.png][img_5.png]
5、  点击小图标，就会弹出一序列想到，告诉我们怎么设置 `docker`

## Window 10 家庭版、 Win 8 、Win7 安装 ToolBox ##

一般情况下，我们的电脑安装的都是 `Window 10` 的家庭版，所以只能使用 `Docker ToolBox`

#### 如果你的电脑是 `WIN8` ####

那么需要按下 `CTRL+ALT+DELETE` 键，打开任务管理器，然后切换到 `性能` 栏，看看是否已经启用 `虚拟化`

![img\_6.png][img_6.png]

我们从阿里云的镜像地址下载 [http://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/][http_mirrors.aliyun.com_docker-toolbox_windows_docker-toolbox]

1、  点击最新的版本，就会开始下载了
    
    ![img\_7.png][img_7.png]
2、  双击下载成功的 `DockerToolbox-xxxx-ce.exe`
    
    ![img\_8.png][img_8.png]
3、  然后一路 `next` 安装，到了选择组件这一步
    
    ![img\_9.png][img_9.png]
    
    > 这里一定要小心，因为它依托于 `Git For Window` 里的 `bash.exe`
    > 
    > 所以你在取消勾选的时候要先确认下 `bash.exe` 有没有存在
    
    如果你已经安装了 Git ，那么可以在这一步取消勾选 `Git For Windows`
4、  继续一路 `next` 直到安装成功

## 初始化 Docker ToolBox ##

安装完成后，桌面会出现三个小图标

![img\_10.png][img_10.png]

1、  可以双击 `Docker QuickStart` 图标来启动 Docker Toolbox 终端
    
    ![img\_11.png][img_11.png]
    
    此时需要漫长的等待，你可以做一点其它事情，偶尔要回来看看，因为中间需要你确认一下
    
    如果系统显示 User Account Control 窗口来运行 VirtualBox 修改你的电脑，选择 Yes
2、  等它下载初始化好了之后，就会是下面这样了
    
    ![img\_12.png][img_12.png]
    
    任何以 `$` 美元符号开头的行都表示我们可以输入命令

## Docker ToolBox Hello World ##

我们可以在 `$` 符号后面输入 `docker run hello-world` 来输出一个 `Hello World`

```
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1、 The Docker client contacted the Docker daemon.
 2、 The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3、 The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4、 The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

我们可以使用 `docker images hello-world` 看看刚刚运行的 `hello-world`

```
$ docker images hello-world
REPOSITORY   TAG     IMAGE ID      SIZE
hello-world  latest  e38bc07ac18e  1.85kB
```

## 镜像加速 ##

鉴于我们国内访问 `https://hub.docker.com/` 上的镜像可能速度有些慢，所以我们推荐你更改下镜像地址

目前比较推荐的是使用网易的镜像地址 [http://hub-mirror.c.163.com][http_hub-mirror.c.163.com]

1、  找到配置文件
    
    目前最新的版本，在各个操作系统上使用下面的配置文件
    
    1.  Linux: `/etc/docker/daemon.json`
    2.  Window: `%programdata%\docker\config\daemon.json`
    
    如果没有这些配置文件，那么新创建一个
2、  在该配置文件中加入
    
```
    {
        "registry-mirrors": ["http://hub-mirror.c.163.com"]
    }
```

[https_www.docker.com_docker-windows]: https://www.docker.com/docker-windows
[https_store.docker.com_editions_community_docker-ce-desktop-windows]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[https_docs.docker.com_toolbox_toolbox_install_windows]: https://docs.docker.com/toolbox/toolbox_install_windows/
[https_download.docker.com_win_stable_DockerToolbox.exe]: https://download.docker.com/win/stable/DockerToolbox.exe
[http_mirrors.aliyun.com_docker-toolbox_windows_docker-toolbox]: http://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/
[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_1.png
[img_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_2.png
[https_download.docker.com_win_stable_Docker_20for_20Windows_20Installer.exe]: https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe
[img_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_3.png
[img_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_4.png
[img_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_5.png
[img_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_6.png
[img_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_7.png
[img_8.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_8.png
[img_9.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_9.png
[img_10.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_10.png
[img_11.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_11.png
[img_12.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/5/img_12.png
[http_hub-mirror.c.163.com]: http://hub-mirror.c.163.com




希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")