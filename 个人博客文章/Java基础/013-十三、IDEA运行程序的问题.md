### IDEA视图功能介绍

`IDEA`面板的全貌如下图
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-1.png)
下面会简单介绍下一些常用的部分菜单使用，如有疑问或补充欢迎留言

1) File文件
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-2.jpg)
1. New：新建一个工程
可以新建 `project`，导入已存在的资源 `project`，从版本控制库导入工程，新建 `Module`，导入已存在的资源 `Module`，新建文件（`JS，DB，JSP，Java，CSS……`），新建FMXL文件。
2. `Open`：打开本地的文件或工程
3.` Open URL`：
4. `Open Recent`：打开最近已导入过的工程
5.` Close Project`：关闭工程
7. `Setting`：`IDEA` 配置文件
8. `Project` `Structure`：显示当前工程结构
9. `Other` `Setting`：全局默认配置
10. `Import` `Settings`：导入 `Settins` 文件
11. `Exoort` `Settings`：导出 `Settings` 文件

2) Edit编辑
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-3.jpg)
1.`Undo`：撤销
2.`Redo` `Duplicate` `Line` `or` `Selection`：重新复制行或选择。（返回撤销之前）
3.`Cut`：剪切
4.`Copy`：复制
5.`Copy` `Reference`:复制文件路径
6.`Paste`：粘贴
7.`Paste` `from` `History`…：从剪切板中选择历史复制的内容粘贴
8.`Paste`：
9.`Delete`：删除
10.`Find`:

3) 工具栏
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-4.png)
从左至右依次为：

1、打开文件（`File` -- `Open`）

2、保存全部（`Ctrl` + `S`）

3、同步：（`Ctrl+Alt+Y`）检测所有外部改变的文件并从磁盘加载

4、Undo：（`Ctrl + Z`）撤销

5、Redo：（`Ctrl + Shift + Z`）返回撤销前，防止误撤销

6、剪切：（`Ctrl + X`）

7、复制：（`Ctrl + C`）

8、粘贴：（`Ctrl + V`）

9、查找：（`Ctrl + F`）

10、替换：（`Ctrl + R`）

11、回退：（`Ctrl + Alt + 向左箭头`）

12、前进：（`Ctrl + Alt + 向右箭头`）

13、构建项目：（`Ctrl + F9`）

14、当前项目(`Run/Debug`)运行配置

15、运行项目

16、`Debug`模式运行项目

17、代码覆盖率方式运行项目

18、打开Settings配置界面（`Ctrl + Alt + S`）

19、项目结构设置（`Ctrl + Alt + Shift + S`）

20、SDK管理器

当然还有一些设置我没有显示，上面的都是经常使用的。
还有一个是中英文翻译，这个也是经常用到的，如果你想使用这个插件，可以联系小编，小编教你。由于篇幅问题，在此就不再详细写了。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-5.jpg)

4) Setting界面
打开他的方式有三种：
1.      菜单栏 - File - Settings
2.      Ctrl + Alt + s
3.      工具栏 - Sttings按钮
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-6.jpg)

5.全局JDK

 具体步骤：顶部工具栏  `File ->Other Settins -> Default Project Structure -> SDKs -> JDK`
          示例： 根据下图步骤设置JDK目录，最后点击OK保存。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-7.jpg)

6.全局Maven

具体步骤：顶部工具栏  `File ->Other Settings -> Default Settings -> Build & Tools -> Maven`

      示例： 理论上只要配置了Maven主目录即可，实际开发推荐采用User Settins file .
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-8.jpg)

7. Tomcat Server

很多小伙伴刚开始都找不到`Tomcat`的配置，其实很简单，`Tomcat`或者`Jetty`这些都是部署的容器，自然会联想到Deployment ，打开部署配置，可以看到应用服务器的配置。

配置Tomcat方法： `File -> Settings -> Deployment -> Application Servers -> Tomcat Server`

具体配置方法，如下图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-9.jpg)

8.调整字体类型和字体大小

默认的白色背景和细小的字体会影响大家的编码体验，这里特意提供了调整代码窗的快捷配置。

打开配置，搜索Font，然后再Font可以调整字体类型，Size可以调整字体大小，如图
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-10.jpg)

9.将快捷键设置为跟`Eclipse`一样

很多人可能并不习惯IDEA的快捷键，为了方便，这里我们将快捷键设置为跟 `Eclipse`一样。

具体步骤:` File -> Settings -> Keymap - > 选择 Eclipse `.
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-11.jpg)

## IDEA中建立java项目

1.进入到I`ntelliJ IDEA`启动界面，点击`Create New Project`
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-12.png)

2.这样就进入到了创建项目页面，这里可以创建好多项目，这里我们以java项目为例开始一个新的旅程，选择你下载的jdk的版本即可。 
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-13.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-14.png)

3.继续，除了标记位置，其他都是默认即可 
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-15.jpg)

4.这样你就进入了一个全新页面，这里就可以显示你创建的项目，是不是很快啊 
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-16.jpg)

5.选中src，右键新建一个包 
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-17.jpg)

6.接着在这个包下面新建一个java类 
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-18.jpg)

7.这里我们看到了熟悉的界面，编写一个简单的helloWorld.java
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-19.png)

## IDEA中如何运行Java类

有两种方式

第一种是：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-20.jpg)
第二种是
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-21.jpg)

运行结果如下
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-22.jpg)

附IDEA常用快捷键
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/13-23.jpg)

1.实用快捷键:

`Ctrl+/` 或 `Ctrl+Shift+/ `注释（`//` 或者`/*…*/` ）

`Ctrl+D` 复制行

`Ctrl+X` 删除行

快速修复 `alt+enter`` (modify/cast)`

代码提示 `alt+/`

`ctr+G` 定位某一行

`Shift+F6` 重构-重命名

`Ctrl+R` 替换文本

`Ctrl+F` 查找文本

`Ctrl+E` 最近打开的文件

`Ctrl+J `自动代码

组织导入 `ctr+alt+O`

格式化代码 `ctr+alt+L`

大小写转化 `ctr+shift+U`

————————–



2.IntelliJ Idea 常用快捷键列表

Alt+回车 导入包,自动修正

Ctrl+N   查找类

Ctrl+Shift+N 查找文件

Ctrl+Alt+L  格式化代码

Ctrl+Alt+O 优化导入的类和包

Alt+Insert 生成代码(如get,set方法,构造函数等)

Ctrl+E或者Alt+Shift+C  最近更改的代码

Ctrl+R 替换文本

Ctrl+F 查找文本

Ctrl+Shift+Space 自动补全代码

Ctrl+空格 代码提示

Ctrl+Alt+Space 类名或接口名提示

Ctrl+P 方法参数提示

Ctrl+Shift+Alt+N 查找类中的方法或变量

Alt+Shift+C 对比最近修改的代码

Shift+F6  重构-重命名

Ctrl+Shift+先上键

Ctrl+X 删除行

Ctrl+D 复制行

Ctrl+/ 或 Ctrl+Shift+/  注释（// 或者/*…*/ ）

Ctrl+J  自动代码

Ctrl+E 最近打开的文件

Ctrl+H 显示类结构图

Ctrl+Q 显示注释文档

Alt+F1 查找代码所在位置

Alt+1 快速打开或隐藏工程面板

Ctrl+Alt+ left/right 返回至上次浏览的位置

Alt+ left/right 切换代码视图

Alt+ Up/Down 在方法间快速移动定位

Ctrl+Shift+Up/Down 代码向上/下移动。

F2 或Shift+F2 高亮错误或警告快速定位

代码标签输入完成后，按Tab，生成代码。

选中文本，按Ctrl+Shift+F7 ，高亮显示所有该文本，按Esc高亮消失。

Ctrl+W 选中代码，连续按会有其他效果

选中文本，按Alt+F3 ，逐个往下查找相同文本，并高亮显示。

Ctrl+Up/Down 光标跳转到第一行或最后一行下

Ctrl+B 快速打开光标处的类或方法



3. 查询快捷键

CTRL+N   查找类

CTRL+SHIFT+N  查找文件

CTRL+SHIFT+ALT+N 查找类中的方法或变量

CIRL+B   找变量的来源

CTRL+ALT+B  找所有的子类

CTRL+SHIFT+B  找变量的类

CTRL+G   定位行

CTRL+F   在当前窗口查找文本

CTRL+SHIFT+F  在指定窗口查找文本

CTRL+R   在 当前窗口替换文本

CTRL+SHIFT+R  在指定窗口替换文本

ALT+SHIFT+C  查找修改的文件

CTRL+E   最近打开的文件

F3   向下查找关键字出现位置

Ctrl+F12 打开当前类中的方法

SHIFT+F3  向上一个关键字出现位置

F4   查找变量来源

CTRL+ALT+F7  选中的字符查找工程出现的地方

CTRL+SHIFT+O  弹出显示查找内容



4. 自动代码

ALT+回车  导入包,自动修正

CTRL+ALT+L  格式化代码

CTRL+ALT+I  自动缩进

CTRL+ALT+O  优化导入的类和包

ALT+INSERT  生成代码(如GET,SET方法,构造函数等)

CTRL+E 最近更改的代码

CTRL+SHIFT+SPACE 自动补全代码

CTRL+空格  代码提示

CTRL+ALT+SPACE  类名或接口名提示

CTRL+P   方法参数提示

CTRL+J   自动代码

CTRL+ALT+T  把选中的代码放在 TRY{} IF{} ELSE{} 里



5. 复制快捷方式

CTRL+D   复制行

CTRL+X   剪切,删除行  

6. 其他快捷方式

CIRL+U   大小写切换

CTRL+Z   倒退

CTRL+SHIFT+Z  向前

CTRL+ALT+F12  资源管理器打开文件夹

ALT+F1   查找文件所在目录位置

SHIFT+ALT+INSERT 竖编辑模式

CTRL+/   注释//  

CTRL+SHIFT+/  注释/*…*/

CTRL+W   选中代码，连续按会有其他效果

CTRL+B   快速打开光标处的类或方法

ALT+ ←/→  切换代码视图

CTRL+ALT ←/→  返回上次编辑的位置

ALT+ ↑/↓  在方法间快速移动定位

SHIFT+F6  重构-重命名

CTRL+H   显示类结构图

CTRL+Q   显示注释文档

ALT+1   快速打开或隐藏工程面板

CTRL+SHIFT+UP/DOWN 代码向上/下移动。

CTRL+UP/DOWN  光标跳转到第一行或最后一行下

ESC   光标返回编辑框

SHIFT+ESC  光标返回编辑框,关闭无用的窗口

F1   帮助千万别按,很卡!

CTRL+F4   非常重要下班都用


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")