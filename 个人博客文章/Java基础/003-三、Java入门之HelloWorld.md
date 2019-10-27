## 书写第一个java程序

1) 先创建一个`HelloWorld.java`文件,将文件放在一个电脑固定的一个位置。至于放在什么位置根据个人喜好来定。小编将在`D:\javase`文件夹中创建。

注意：文件夹名称和`.java`文件尽量使用英文名称。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/01.jpg)
2) 使用[editplus下载地址](https://pan.baidu.com/s/1dtT0JymSIBBQR160J-QuNQ "editplus下载地址")
3)  然后开始编写程序
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/02.jpg)
现在程序就写好了。以上也写出了每行代码的解释，初学者一般喜欢忘记写分号。
注意：类名一定要与文件名一致大小写都必须一致，并且`java`语言是严重区分大小写的。

## 编译文件和执行文件操作步骤。

1) 进入到你保存`HelloWorld.java`的目录下然后回车

命令：`cd D:\javase` 如下图
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/03.jpg)

2) 然后使用`javac`命令来编译`HelloWorld.java`
命令：`(javac HelloWorld.java)`
如下图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/04.jpg)


3) 现在就会编译出一个可执行文件`HelloWorld.class`如图
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/05.jpg)

4) 执行`HelloWorld.class`文件
命令：`java HelloWorld`
如图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/06.jpg)

注意：每次修改程序时都必须重新编译一次在执行即可。并且一个 `java` 文件对应多个类文件。每个类文件中只能有一个类的修饰符为 `public`。
总结
 计算机的高级编程语言类型: 编译型 ，解释型。
`Java` 语言是两种类型的结合； 
1）编译
利用编译器（`javac`）将源程序编译成字节码文件，文件名：源文件名.`class`
2）运行
利用虚拟机（解释器，`java`）解释执行 `class` 字节码文件。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")