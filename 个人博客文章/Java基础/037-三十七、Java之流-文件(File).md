## Java 流(Stream)、文件(File)和IO

`Java.io` 包几乎包含了所有操作输入、输出需要的类。所有这些流类代表了输入源和输出目标。



`Java.io `包中的流支持很多种格式，比如：基本类型、对象、本地化字符集等等。一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。



`Java` 为 `I/O` 提供了强大的而灵活的支持，使其更广泛地应用到文件传输和网络编程中。但本节讲述最基本的和流与 `I/O` 相关的功能。



看看流的结构图:
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-1.jpg)

## File类


`java`文件类以抽象的方式代表文件名和目录路径名。该类主要用于文件和目录的创建、文件的查找和文件的删除等。



`File`对象代表磁盘中实际存在的文件和目录。通过以下构造方法创建一个`File`对象。



1. File(String pathname)通过将给定路径名字符串转换成抽象路径名来创建一个新File实例。

使用下面方法获取路径名称:`public` `String` `getPath()` 将此抽象路径名转换为一个路径名字符串。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-2.jpg)

`File(File parent, String child);` 通过给定的父抽象路径名和子路径名字符串创建一个新的File实例。

## File类的常用方法

1.  `public String getName()`返回由此抽象路径名表示的文件或目录的名称。



2. `public String getParent()` 返回此抽象路径名的父路径名的路径名字符串，如果此路径名没有指定父目录，则返回 `null`。



3. `public File getParentFile()`返回此抽象路径名的父路径名的抽象路径名，如果此路径名没有指定父目录，则返回 `null`。



4. `public String getPath()`将此抽象路径名转换为一个路径名字符串。



5. `public String getAbsolutePath()`返回抽象路径名的绝对路径名字符串。



6. `public boolean canRead()`测试应用程序是否可以读取此抽象路径名表示的文件。



7. `public boolean canWrite()`测试应用程序是否可以修改此抽象路径名表示的文件。



8. `public boolean exists()`测试此抽象路径名表示的文件或目录是否存在。



9.` public boolean isDirectory()`测试此抽象路径名表示的文件是否是一个目录。



10. `public boolean isFile()`测试此抽象路径名表示的文件是否是一个标准文件。



11. `public long lastModified()`返回此抽象路径名表示的文件最后一次被修改的时间。



12.` public long length()`返回由此抽象路径名表示的文件的长度。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-3.jpg)

13.  `public boolean createNewFile() throws IOException`当且仅当不存在具有此抽象路径名指定的名称的文件时，原子地创建由此抽象路径名指定的一个新的空文件。



14.    `public boolean delete()` 删除此抽象路径名表示的文件或目录



15.   `public void deleteOnExit()`在虚拟机终止时，请求删除此抽象路径名表示的文件或目录。

![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-4.jpg)

![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-5.jpg)
16. `public String[] list()`返回由此抽象路径名所表示的目录中的文件和目录的名称所组成字符串数组。



17. `public File[] listFiles()`  返回一个抽象路径名数组，这些路径名表示此抽象路径名所表示目录中的文件。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-6.jpg)

18. `public boolean mkdir()`创建此抽象路径名指定的目录。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-7.jpg)

19. `public boolean renameTo(File dest)` 重新命名此抽象路径名表示的文件。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-8.jpg)

20. `public String toString()` 返回此抽象路径名的路径名字符串。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/37-9.jpg)
以上就是file的基本方法操作。大家可以把以上方法都每个都测试一下

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")