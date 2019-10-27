之前我们使用到的 Git 命令都是在本地执行，如果你想通过 Git 分享代码或者与其它开发人员合作

那么就需要将数据放到一台其他开发人员能够连接的服务器上

本章节，我们将使用 [GitHub][] 作为远程仓库，演示如何操作远程仓库

如果你还没有 Github 账号，可以在官网 [https://github.com/][https_github.com] 注册

## 配置 ##

由于我们的本地 Git 仓库和 GitHub 仓库之间的传输是通过 SSH 加密的，所以我们需要配置验证信息

1、  使用以下命令生成 SSH Key
    
```
    ssh-keygen -t rsa -C "your_email@youremail.com"
```
    
    后面的 **your\_email@youremail.com** 改为你在 github 上注册的邮箱
2、  然后会出现下面的提示要求确认路径
    
```
    Enter file in which to save the key (~/.ssh/id_rsa):
```
    
    我们建议将路径改成 `~/.ssh/github`
    
    也就是最后会保存为 `github` 而不是 `id_rsa`
    
    > 这样可以避免主要的 SSH 被覆盖
3、  按回车之后，会出现下面的提示要求输入密码，我们这使用默认的一路回车就行
    
```
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again:
```
    
    > 为什么不设置呢？ 因为有些 Git 客户端软件没有设置密码的地方，省的以后每次输入密码麻烦
4、  成功的话会在 `~/.ssh` 创建文件 `github` 和 `github.pub`
5、  打开 `~/.ssh/github.pub` 文件，复制里面的内容
6、  回到 github 上，进入 Account => Settings（账户配置）
    
   ![img\_1.png][img_1.png]
7、  左边选择 **SSH and GPG keys** ，然后点击 **New SSH key** 按钮
    
   ![img\_2.png][img_2.png]
8、  进入 SSH 添加界面，title 设置标题，可以随便填，粘贴在你电脑上生成的 key
    
   ![img\_3.png][img_3.png]
    
    > 虽然 title 可以随便填写，但我建议填入邮箱
9、  点击 **Add SSH Key** 按钮，添加成功后界面如下所示
    
   ![img\_4.png][img_4.png]
10、 为了验证是否成功，输入以下命令
    
```
    ssh -T git@github.com
```
    
    如果出现下面的内容，则表示成功
    
```
    Warning: Permanently added the RSA host key for IP address '52.74.223.119' to the list of known hosts.
    Hi ijoywan! You've successfully authenticated, but GitHub does not provide shell access.
```

## Github 官方上添加仓库 ##

1、  登录到 [Github.com][GitHub] 官方后，点击右上角的 `+` 号
    
   ![img\_5.png][img_5.png]
2、  然后点击 `New repository` 会弹出如下所示
    
   ![img\_6.png][img_6.png]
    
    1.  在 **Repository name** 输入你想创建的仓库的英文名，比如 `ycbbs-git-tutorial`
    2.  其它保持默认即可，不过你也可以做一些更改
3、  填写完成后点击 "Create repository" 按钮，就成功地创建了一个新的 Git 仓库
    
   ![img\_7.png][img_7.png]
    
    上面这段信息告诉我们：既可以从这个仓库克隆出新的仓库，也可以把本地仓库的内容推送到 GitHub 仓库

## 创建本地相对应的仓库 ##

刚刚我们在 [Github.com][GitHub] 上新建了一个仓库 `ycbbs-git-tutorial`

那么我们要如何在本地创建对应的仓库呢？

有三种方式：从远程仓库 clone 、本地新建 和 本地已经存在

1、  从远程仓库 clone
    
    我们可以使用下面的命令将远程的 `ycbbs-git-tutorial` 仓库 clone 到本地
    
```
    git clone git@github.com:ijoywan/ycbbs-git-tutorial.git
```
    
    输入如下信息
    
```
    Cloning into 'ycbbs-git-tutorial'...
    Warning: Permanently added the RSA host key for IP address '13.229.188.59' to the list of known hosts.
    warning: You appear to have cloned an empty repository.
```
    
    然后使用 `cd ycbbs-git-tutorial` 进入 `ycbbs-git-tutorial` 目录
    
    最后做一些配置
    
```
    git config user.name ijoywan
    git config user.email noreply@docfor.cn
```
    
    可以使用命令 `git config --list` 检查下配置是否正确
2、  本地新建仓库
    
    这个很简单，Github.com 那里有提示
    
```
    $ mkdir ycbbs-git-tutorial  # 创建目录
    $ cd    ycbbs-git-tutorial  # 进入目录
    $ echo "# 研发军团 Github 向导" > README.md     # 创建 README.md 文件并写入内容
    $ ls                       # 查看目录下的文件
    README.md
    $ git init                 # 初始化
    Initialized empty Git repository in /Users/penglei/github/ycbbs-git-tutorial/.git/
    $ git add README.md        # 添加文件
    $ git commit -m "添加 README.md 文件"        # 提交并备注信息
    [master (root-commit) 9c81d29] 添加 README.md 文件
     1 file changed, 1 insertion(+)
     create mode 100644 README.md
```
    
    然后做一些配置
    
```
    git config user.name ijoywan
    git config user.email noreply@docfor.cn
```
    
    可以使用命令 `git config --list` 检查下配置是否正确
    
    最后提交到 Github
    
```
    $ git remote add origin git@github.com:ijoywan/ycbbs-git-tutorial.git
    $ git push -u origin master
    Counting objects: 3, done.
    Writing objects: 100% (3/3), 265 bytes | 265.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To github.com:ijoywan/ycbbs-git-tutorial.git
     * [new branch]      master -> master
    Branch master set up to track remote branch master from origin.
```
3、  如果之前本地仓库已经存在，那么就更好办了，直接使用下面的命令添加 Github 仓库
    
```
    $ git remote add origin git@github.com:ijoywan/ycbbs-git-tutorial.git
    $ git push -u origin master
```

然后我们回到 Github 上创建的仓库，就可以看到我们刚刚添加的文件 `README.md`

![img\_8.png][img_8.png]

  


  


## 查看当前的远程库 ##

要查看当前配置有哪些远程仓库，可以用命令

```
git remote
```

### 范例 ###

```
$ git remote
origin
```

如果要查看每个别名的实际链接地址，可以添加参数 `-v`

```
$ git remote -v
origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (fetch)
origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (push)
```

## 拉取远程仓库 ##

如果远程仓库他人做了更改，那么就需要从远程仓库拉取更改，合并到本地仓库

Git 有两个命令用来拉取远程仓库的更新

1、  从远程仓库下载新分支与数据
    
```
    git fetch
```
    
    该命令执行完后需要执行 `git merge` 远程分支到本地所在的分支
2、  将远程仓库拉取的更新尝试合并到当前分支
    
```
    git merge
```
    
    该命令就是在执行 git fetch 之后紧接着执行 git merge 远程分支到你所在的任意分支

假设配置好了一个远程仓库，并且你想要提取更新的数据

你可以首先执行 **git fetch \[alias\]** 告诉 Git 去获取它有你没有的数据

然后你可以执行 **git merge \[alias\]/\[branch\]** 以将服务器上的任何更新（假设有人这时候推送到服务器了）合并到你的当前分支

### 范例 ###

我们先在 Github 上点击 "README.md" 并在线修改它

![img\_9.png][img_9.png]

修改为

![img\_10.png][img_10.png]

然后我们就可以使用下面的命令拉取远程的修改更新

```
$ git fetch origin
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:ijoywan/ycbbs-git-tutorial
   9c81d29..84a307e  master     -> origin/master
```

信息 `9c81d29..84a307e master -> origin/master` 说明 master 分支已被更新

我们可以使用以下命令将更新同步到本地

```
$ git merge origin/master
Updating 9c81d29..84a307e
Fast-forward
 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

查看 README.md 文件内容

```
$ cat README.md 
# 研发军团 Github 向导

详情可以访问 [https://www.ycbbs.vip/l/penglei/git/git-basic-index.html](https://www.ycbbs.vip/l/penglei/git/git-basic-index.html)
```

## 推送到远程仓库 ##

本地修改的更新，经过自己测试成功后，就可以推送到远端的仓库里

推送新分支与数据到某个远端仓库命令

```
git push [alias] [branch]
```

上面的命令会将 **\[branch\]** 分支推送成为 **\[alias\]** 远程仓库上的 **\[branch\]**

### 范例 ###

```
$ touch demo.md      # 添加文件
$ git add demo.md    # 提交暂存区 
$ git commit -m "添加 demo.md 文件"
[master c0137c6] 添加 demo.md 文件
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 demo.md

$ git push origin master    # 推送到 Github
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 287 bytes | 287.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:ijoywan/ycbbs-git-tutorial.git
   84a307e..c0137c6  master -> master
```

点开我们在 Github 的仓库，可以看到文件 `demo.md` 已经成功提交到远程仓库上了

![img\_11.png][img_11.png]

## 删除远程仓库 ##

删除远程仓库可以使用命令

```
git remote rm [别名]
```

### 范例 ###

1、  先查看有多少远程仓库
    
```
    $ git remote -v
    origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (fetch)
    origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (push)
```
2、  添加分支 origin2
    
```
    $ git remote add origin2 git@github.com:ijoywan/ycbbs-git-tutorial.git
```
3、  再次查看远程仓库有多少分支
    
```
    $ git remote -v
    origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (fetch)
    origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (push)
    origin2 git@github.com:ijoywan/ycbbs-git-tutorial.git (fetch)
    origin2 git@github.com:ijoywan/ycbbs-git-tutorial.git (push)
```
4、  删除分支 origin2
    
```
    $ git remote rm origin2
    $ git remote -v
    origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (fetch)
    origin  git@github.com:ijoywan/ycbbs-git-tutorial.git (push)
```


[GitHub]: https://github.com
[https_github.com]: https://github.com/

[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_1.png
[img_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_2.png
[img_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_3.png
[img_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_4.png
[img_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_5.png
[img_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_6.png
[img_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_7.png
[img_8.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_8.png
[img_9.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_9.png
[img_10.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_10.png
[img_11.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/06/18/img_11.png

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")