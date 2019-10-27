上一章节中我们下载了 `Elasticsearch` 的最新版本，也安装和配置了 `Java` 环境。接下来，我们将尝试在 `Windows` 系统上安装 `Elasticsearch`

`Windows` 上安装 `Elasticsearch` 的过程非常简单

1、下载完成后，选中`elasticsearch-6.3.0.zip`

![img\_1.png][img_1.png]
2、点右键，选择解压到当前目录，不用管当前目录是哪个，先解压

![img\_2.png][img_2.png]

解压完成后显示如下

![img\_3.png][img_3.png]
3、然后将解压后的目录 `elasticsearch-6.3.0` 移动到你的开发环境目录，比如 `d:/devops` 目录

![img\_4.png][img_4.png]
4、打开 `命令行提示符` 或者 `Windows PowerShell`

如果你的电脑是 `Window 8` 或以上版本，在左下脚的搜索框里输入 `Power shell` 就可以看到 `Windows PowerShell` ，点击它打开 `PowerShell`

如果你的电脑是 `Window 8` 以下版本，则点击开始菜单，选择 **程序 -> 所有程序 -> 附件** 点击命令行提示符

![img\_5.png][img_5.png]
5、接下来先输入 `d:\` 进入 `d:` 盘目录，然后输入 `cd devops\elasticsearch-6.3.0\bin` 进入 `elasticsearch-6.3.0` 目录下的 `bin` 目录

![img\_6.png][img_6.png]
6、接下来输入 `elasticsearch.bat --version` 查看是否有版本输出

![img\_7.png][img_7.png]

我们可以看到当前版本为 `6.3.0` ，刚好是我们下载的版本，所以，配置正确

## 首次运行 ##

还是在刚刚那个 `PowerShell` 中，输入 `elasticsearch.bat` 就会使用 `elasticsearch-6.3.0\config` 下的 `elasticsearch.yml` 配置文件来启动项目

![img\_8.png][img_8.png]

在经过一系列的初始化之后，就会启动成功

![img\_9.png][img_9.png]

## 检查是否成功启动 ##

当启动成功后，就可以通过浏览器访问 [http://localhost:9200/][http_localhost_9200] 来查看是否成功启动

![img\_10.png][img_10.png]

可以看到 `version` 键下有当前 `Elasticsearch` 的相关信息，比如版本号为 `6.3.0`

## 停止 ##

如果想要停止 `elasticsearch` 可以直接按下 `CTRL + C` 组合键，就可以停止服务了

![img\_11.png][img_11.png]


[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_1.png
[img_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_2.png
[img_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_3.png
[img_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_4.png
[img_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_5.png
[img_6.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_6.png
[img_7.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_7.png
[img_8.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_8.png
[img_9.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_9.png
[http_localhost_9200]: http://localhost:9200/
[img_10.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_10.png
[img_11.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/01/4/img_11.png


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")