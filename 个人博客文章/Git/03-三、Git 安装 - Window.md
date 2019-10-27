# git是什么？

`git`是当前最受欢迎，使用最多的分布式版本控制系统。

[git下载地址](http://rogerdudler.github.io/git-guide/index.zh.html)

这个地址包含了对`git`的一些基本介绍，图文并茂，非常奈斯！

`windows`安装`2.16.2`版本

1.首先下载`git`版本，找到适合自己电脑的版本。

下载完成后是这样的：

![][img_1]

2.打开后是这样子的：

![][img_2]

3.点击next：

![][img_3]

4.点击next:

![][img_4]

5.next:

![][img_5]

这一步中，第一个表示使用`git`自带的命令行工具（`git Bash`），第二个表示使用`Windows`的命令行工具(`cmd`),第三个表示两者都行。但是第三种方式需要修改一些设置，一般选择第一种方式就够了.

6 next:

![][img_6]

传输协议，第一个为`https`协议，第二个为`ssl`协议。

7next:

![][img_7]

第一个选择，表示跨`windows`平台的项目；

第二个表示`unix`平台的项目；

第三个表示非跨平台的项目。

8 点击下一步，

![][img_8]

使用`MinTTY`：

`Git`的`Windows`客户端，会自带一个叫`MinGW`的`Linux`命令行工具，可以执行简单的`shell`命令，与`cygwin`功能类似，仅此而已。

而在`MinGW`命令行窗口中，执行命令“`mintty`”既可以打开`mintty`窗口，再次可以使用`ssh`命令登录`linux`服务器。

9.下一步：

![][img_9]

默认配置项，默认选择就行。

10 点击安装

![][img_10]

11安装完成：

![][img_11]

点击`finish`即可。

判断是否安装完成

安装好`git`的客户端后，我们会发现开始栏会多出下面三项：

![][img_12]

我们点击`git` `bash` 出现下面的窗口，则表示安装成功了。

![][img_13]

配置`git`账号和邮箱

因为`git`是分布式版本控制系统，所以需要我们填写用户名和邮箱作为一个标识。

![][img_14]

```shell
git config --global user.name "xxxx"   设置用户名

git config --global user.email "xxxx"    设置邮箱

git config user.name     查看用户名

git config user.email    查看邮箱

git config --global user.name "xxxx" 修改用户名

git config --global user.email "xxxx@xxx.com" 修改邮箱
```

[img_1]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-1.png
[img_2]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-2.png
[img_3]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-3.png
[img_4]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-4.png
[img_5]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-5.png
[img_6]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-6.png
[img_7]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-7.png
[img_8]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-8.png
[img_9]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-9.png
[img_10]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-10.png
[img_11]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-11.png
[img_12]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-12.png
[img_13]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-13.png
[img_14]:https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Git/images/git-14.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")