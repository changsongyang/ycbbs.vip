## 引言

网络编程是指编写运行在多个设备（计算机）的程序，这些设备都通过网络连接起来。
`java.net` 包中 `J2SE` 的 `API` 包含有类和接口，它们提供低层次的通信细节。你可以直接使用这些类和接口，来专注于解决问题，而不用关注通信细节。
`java.net` 包中提供了两种常见的网络协议的支持：
`TCP：TCP` 是传输控制协议的缩写，它保障了两个应用程序之间的可靠通信。通常用于互联网协议，被称 `TCP / IP`。
`UDP：UDP `是用户数据报协议的缩写，一个无连接的协议。提供了应用程序之间要发送的数据的数据包。
以上`2`中都是客户端/服务器通信模式
`UDP`上一节已经讲解了比较简单。现在来讲解`TCP`。

## TCP协议及端口

IP协议在发送数据包时,途中会遇到各种事情。例如,可能路由器突然崩溃,使包丢失。又如一个包可能沿低速链路移动,而另一个包可能沿高速链路移动而超过前面的包,最后使得包的顺序搞乱。
`TCP` 协议使两台主机上的进程顺利通信,不必担心包丢失或包顺序搞乱。`TCP` 跟踪包顺序,并且在包顺序搞乱时按正确顺序重组包。如果包丢失,则 `TCP` 会请求源主机重发包。

现在来看一张图
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/5/70-1.jpg)
上图两台主机上都会运行许多进程。当主机`A`上的进程`A1`向主机`B`上的进程`B1`发送数据时,`IP`协议根据主机`B`的`IP`地址,把进程`A1`发送的数据送达主机`B`。接下来`TCP`需要决定把数据发送到主机`B`中的哪个进程。

`TCP`采用端口来区分进程。端口不是物理设备,而是用于标识进程的逻辑地址,更确切地说,是用于标识`TCP`连接的端点的逻辑地址。当两个进程进行一次通信,就意味着建立了一个`TCP`连接,`TCP`连接的两个端点用端口来标识。

在图中,进程`A1`与进程`B1`之间建立了一个`TCP`连接,进程`BI`的端口为`80`,因此进程`B1`的地址为主机`B:80`。

进程 `A1` 的端口为 `1000`,因此进程A1的地址为主机`A`:`1000`。每个进程有了唯一的地址,`TCP`就能保证把数据顺利送达特定的进程。

## 客户端/服务端通讯模式

`TCP/UDP`协议推动了客户服务器通信模式的广泛运用。在通信的两个进程中，一个进程为客户进程,另一个进程为服务器进程。客户进程向服务器进程发出要求某种服务的请求,服务器进程响应该请求。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/5/70-2.jpg)
如上图所示：通常一个服务器进程会同时为多个客户进程服务,图中服务器进程B同时为客户进程`A1、A2`和`B2`提供服务。
好了说了这么多了都是理论，写个小例子吧！！！

## 客户端/服务端代码演示

写代码之前先介绍下服务端和客户端的编写步骤
服务端步骤：
1. 创建`ServerSocket`对象`server`+服务端的端口
2. 监听所有客户端，等待客户端链接请求socket对象
3.使用`socket`对象的`getinputStream`方法获取客户端发送过来的数据的流对象
    或者使用`socket`对象的`getOutputStream`方法向客户端发送数据的流对象
4.获取数据或者发送数据
5.关闭`server`资源

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class TcpServer {
   public static void main(String[] args){
       final int port = 30000;
       /**
        1. 创建ServerSocket对象server
        2. 监听所有客户端，等待客户端链接请求socket对象
        3.使用socket对象的getOutputStream方法获取客户端发送过来的数据的流对象
        或者使用socket对象的getinputStream方法向客户端发送数据的流对象
        4.获取数据或者发送数据
        5.关闭server资源
        */
       //创建ServerSocket对象server
       ServerSocket server = null;
       try {
           server = new ServerSocket(port);
           while (true) {
               //监听所有客户端，等待客户端链接请求socket对象
               Socket socket = server.accept();
               System.out.println("客户端ip："+socket.getInetAddress().getHostName() + "端口为："+  socket.getPort()+"链接成功。。。");
               //使用socket对象的getOutputStream方法获取客户端发送过来的数据
               ObjectInputStream objectInputStream = new ObjectInputStream(socket.getInputStream());
               //读取数据
               Object object = objectInputStream.readObject();
               System.out.println("server获取数据为：" + object);
               //使用socket对象的getinputStream方法向客户端发送数据
               ObjectOutputStream objectOutputStream = new ObjectOutputStream(socket.getOutputStream());

```

客户端步骤：

1. 创建`socke`t对象，指定服务器ip和端口
2. 使用`socket`对象的`getinputStream`方法获取客户端发送过来的数据的流对象
    或者使用`socket`对象的`getOutputStream`方法向客户端发送数据的流对象
3.获取数据
4.关闭`socket`资源

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.Socket;

public class TcpClient {

   public static void main(String[] args) {
       /**
        1. 创建socket对象，指定服务器ip和端口
        2. 使用socket对象的getinputStream方法获取客户端发送过来的数据的流对象
        或者使用socket对象的getOutputStream方法向客户端发送数据的流对象
        3.获取数据
        4.关闭socket资源
        */
       final int port = 30000;
       Socket client = null;
       try {
           //创建socket对象，指定服务器ip和端口
           client = new Socket("127.0.0.1",port);
           //使用socket对象的getOutputStream方法向客户端发送数据的流对象
           ObjectOutputStream objectOutputStream = new ObjectOutputStream(client.getOutputStream());
           objectOutputStream.writeObject("我是客户端");

           //使用socket对象的getinputStream方法获取客户端发送过来的数据的流对象
           ObjectInputStream objectInputStream = new ObjectInputStream(client.getInputStream());
           //获取数据
           Object object = objectInputStream.readObject();
           System.out.println("客户端显示服务端传过来的数据："+object);
       } catch (Exception e) {
           e.printStackTrace();
       }finally {
           if (client != null) {
               try {
                   //关闭socket资源
                   client.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
       }
   }
}
运行结果
服务端结果
客户端ip：127.0.0.1端口为：52499链接成功。。。
server获取数据为：我是客户端

客户端结果：
客户端显示服务端传过来的数据：我是服务器
```
注意：服务端在接收数据时客户端一定不能关闭，如果关闭会报`java.net.SocketException: Software caused connection abort: socket write error`异常


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")