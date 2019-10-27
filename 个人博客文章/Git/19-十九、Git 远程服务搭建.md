我们可能需要与别人共享代码，协作开发

这时候我们就需要一台 `Git` 服务器作为远程 `Git` 仓库

下面我们就以在 `Centos7` 上安装 `Git` 服务器为例学习如何配置远程 `Git` 仓库

### 1. 安装 Git 依赖 ###

```
[root@localhost ~]# yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
```

### 2. 安装 Git ###

```
[root@localhost ~]# yum install git
```

### 3. 创建 git 用户和用户组 ###

创建一个 `git` 用户组和用户，用来运行 `Git` 服务

```
[root@localhost ~]# groupadd git
[root@localhost ~]# adduser git -g git
```

### 4. 创建 ssh 登录证书 ###

收集所有需要登录的用户的公钥，公钥位于 `~/.ssh/id\_rsa.pub` 文件中

把每个开发者的公钥导入到 `/home/git/.ssh/authorized\_keys` 文件里，一行一个

如果没有 `/home/git/.ssh/authorized\_keys` 文件则使用下面的命令创建

```
[root@localhost ~]# cd /home/git/
[root@localhost git]# mkdir .ssh
[root@localhost git]# chmod 700 .ssh
[root@localhost git]# touch .ssh/authorized_keys
[root@localhost git]# chmod 600 .ssh/authorized_keys
```

### 5、初始化 Git 仓库 ###

首先我们选定一个目录作为 `Git` 仓库的根目录，一般情况下我们会在 `git` 用户目录下创建 `repo` 目录作为 `Git` 服务的根目录

首先切换到 `git` 用户

```
[root@localhost git]# su git
```

```
[git@localhost ~]$ mkdir /home/git/repo
[git@localhost ~]$ chown /home/git/repo git:git
```

然后在 `repo` 目录下创建我们的 `Git` 项目目录，比如 `git-demo.git`

> 目录名最好以 `.git` 结尾，这样在 git clone 或者 push 的时候 URL 路径更直观

```
[git@localhost ~]$ cd /home/home/git/repo
[git@localhost ~]$ mkdir git-demo.git
[git@localhost ~]$ chown git:git git-demo.git
[git@localhost ~]$ cd git-demo.git

[git@localhost ~]$ git init --bare .
Initialized empty Git repository in /home/git/repo/git-demo.git/
```

### 5. 禁用 git 用户登录 ###

编辑 `/etc/passwd` 文件完成，找到有 `git` 那行

```
git:x:503:503::/home/git:/bin/bash
```

改为：

```
git:x:503:503::/home/git:/sbin/nologin
```

### 6. 克隆仓库 ###

现在我们就可以克隆这个远程仓库了，假设我们服务器的 `IP` 是 `192.168.1.8`

```
$ git clone git@192.168.1.8:~/repo/git-demo.git
Cloning into 'git-demo'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
```

如果出现类似的 `Clone` `into` 语句，则说明我们的 远程 `Git` 服务配置完成了


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")