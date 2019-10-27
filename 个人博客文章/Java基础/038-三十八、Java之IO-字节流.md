## FileInputStream

该流用于从文件读取数据，它的对象可以用关键字 new 来创建。有多种构造方法可用来创建对象，主要是读取字节的。



可以使用字符串类型的文件名来创建一个输入流对象来读取文件。
```java
InputStream f = new FileInputStream("C:/java/1.txt");
```


也可以使用一个文件对象来创建一个输入流对象来读取文件。我们首先得使用 File() 方法来创建一个文件对象
```java

File f = new File("C:/java/1.txt");
FileInputStream out = new FileInputStream(f);
```


所有方法：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/38-1.jpg)

## 下面介绍几个常用方法：
```java
public void read() throws IOException{}

public int read(int r)throws IOException{}

read(byte[] b, int off, int len) throws IOException{}
```
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/38-2.jpg)

值得一提的`native`方法
上面核心方法中为什么实现简单，因为工作量都在`native`方法里面，即JVM里面实现了。`native` 倒是不少,一一列举吧：
```java

native void open(String name) // 打开文件，为了下一步读取文件内容

native int read0() // 从文件输入流中读取一个字节

native int readBytes(byte b[], int off, int len) // 从文件输入流中读取，从off句柄开始的len个字节，并存储至b字节数组内。

native void close0() // 关闭该文件输入流及涉及的资源，比如说如果该文件输入流的FileChannel对被获取后，需要对FileChannel进行close。
　　　　
```
其它还有值得一提的就是，在jdk1.4中，新增了NIO(关于NIO后续再进阶中再讲述)包，优化了一些IO处理的速度，所以在`FileInputStream`和`FileOutputStream`中新增了`FileChannel getChannel()`的方法。即获取与该文件输入流相关的` java.nio.channels.FileChannel`对象。

## FileOutputStream

该类用来创建一个文件并向文件中写数据。

如果该流在打开文件进行输出前，目标文件不存在，那么该流会创建该文件。

有两个构造方法可以用来创建 `FileOutputStream` 对象。

使用字符串类型的文件名来创建一个输出流对象：

```java
OutputStream f = new FileOutputStream("C:/java/1.txt ")
```
也可以使用一个文件对象来创建一个输出流来写文件。



我们首先得使用`File()`方法来创建一个文件对象：
```java

File f = new File("C:/java/hello");
OutputStream f = new FileOutputStream(f);
```

操作方法与`FileReader`很类似，我就演示一个吧。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/38-3.jpg)

介绍几个常用的方法

```java
public void read() throws IOException{}
public int read(int r)throws IOException{}
read(byte[] b, int off, int len) throws IOException{}

```

操作方法与`FileWriter`很类似，我就演示一个吧。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/38-4.jpg)

以上还会有其它集成的方法，大家都可以自己测试一下就好了，在这里就不一个个演示了。



还有其它的流对象基本上用法一致，我就不一一讲解了基本都是成对成对的出现。

比如：

`InputStream，OutputStream`

## 
## 练习：


使用程序创建一个文本并且写入数据，然后在读取数据。使用什么方法和构造函数，大家随便。

在公众号输入“`FileOutputStream`作业”


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")