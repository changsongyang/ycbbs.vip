## 字符流的缓冲区

缓冲区的出现，提高了对数据的读写效率对应的类：`BufferedWriter`,`BufferedReader`

缓冲区要结合流才可以使用缓冲区是在流的基础上对流的功能进行增强


## BufferedWriter

将文本写入到字符输出流中，缓冲字符，以便提供对单个字符、数组和字符串的有效写入。



可以指定缓冲区大小，也可以接受默认大小。默认是足够大的用于大多数目的。



提供了一种`newline()`方法，利用平台自身观念的行分隔符由系统性`line.separator`定义。并不是所有的平台都使用换行符`（'\n'）`。



调用此方法终止各输出线因此宁愿写一个换行符直接。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/39-1.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/39-2.jpg)
所有的类用法几乎都很类似，下面直接写案例了。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/39-3.jpg)

## BufferedReader


从一个字符输入流中读取文本，缓冲字符，以便提供字符、数组和行的有效读取。

可以指定缓冲区大小，也可以使用默认大小。默认是足够大的用于大多数目的。



在一般情况下，每一个读的读者提出的要求导致相应的读请求是由底层字符或字节流。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/39-4.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/39-5.jpg)


这个类和BufferedWriter使用方式完全一样就演示几个常用的，下面直接写案例了。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/39-6.jpg)

`BufferedInputStream, BufferedOutputStream`这一对用法也是一模一样的。大家下去可以自己试试。

## 练习

使用程序创建一个文本并且写入数据，然后在读取数据。使用，大家随便`BufferedInputStream, BufferedOutputStream`和`BufferedReader，BufferedWriter`，2对类各写一个写入和读取。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")