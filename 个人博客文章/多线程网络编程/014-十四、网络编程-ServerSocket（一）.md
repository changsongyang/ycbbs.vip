## 引言

上一节讲解`socket`的基本使用，这一节来顺带着讲讲`socket`的一个自认为重要的设置选项的方法，哈哈,多多少少会在今后的开发中使用到。还是比较好用的。

## public void setsolingen( boolean on, int seconds) 

设置该选项: `public void setsolingen( boolean on, int seconds) throws Socketexception`
每次`socket`调用了`close`方法之后，其实默认情况下底层的socket并不是立即关闭，而是会等待剩余的数据发送完毕后才会真正的关闭底层`socket`和断开与服务器的链接。

那么现在需要调用了 `socket,close `方法后要立即关闭底层`socket`怎么办呢？
这个时候就可以使用本方法来`socket. setsolinger(true, 0)`设置一下，在执行 `Socket`的`close`方法即可马上关闭底层`socket`了,但是所有未发送完的剩余数据被丢弃。

值得注意的是,在以上情况下,当 `closed`方法返回后,底层的 `Socket`会被关闭,断开连接。此外, `setsolinger( boolean on, int seconds)`方法中的 `seconds`参数以秒为单位,而不是以毫秒为单位。
代码演示一下
服务端代码
```java
public class TcpServer2 {
   public static void main(String[] args) throws IOException, InterruptedException {
       final int port = 30000;
       //创建ServerSocket对象server
       ServerSocket server = null;
       server = new ServerSocket(port);
       //监听所有客户端，等待客户端链接请求socket对象
       Socket socket = server.accept();
       //为了演示效果这里休眠5秒
       Thread.sleep(5000);
       InputStream inputStream = socket.getInputStream();
       //定义缓冲区
       ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
       byte[] bytes = new byte[1024];
       int len = -1;
       do{
           //在这一步就会出现异常了
           len = inputStream.read(bytes);
           if (len != -1) {
               byteArrayOutputStream.write(bytes,0,len);
           }
       }while (len != -1);
       //输出客户端传来的数据
       System.out.println(new String(byteArrayOutputStream.toByteArray()));
   }
}
//客户端代码
public class TcpClient2 {
   public static void main(String[] args) throws IOException {
       Socket client = new Socket("127.0.0.1",30000);
       //设置调用close就立即停止与服务器链接
       client.setSoLinger(true,0);
       //发送数据
       client.getOutputStream().write("我是客户端".getBytes());
       System.out.println("开始关闭");
       client.close();
       System.out.println("关闭结束");
   }
}
//运行结果

客户端显示：
开始关闭
关闭结束

//服务端显示：
Exception in thread "main" java.net.SocketException: Connection reset
 at java.net.SocketInputStream.read(SocketInputStream.java:210)
 at java.net.SocketInputStream.read(SocketInputStream.java:141)
 at java.net.SocketInputStream.read(SocketInputStream.java:127)
 at a004.TcpServer2.main(TcpServer2.java:21)
```

这样就强制关闭底层`socket`了，也断开服务器的链接，如果需要等数据放送完毕在断开的话，还可以设置为：`setsolinger（true,2000）`,第二个参数是秒为单位，就是会阻塞`2000`秒在关闭底层`socket`和断开服务器链接了。也可以去掉本方法也是会默认等数据发送完毕在关闭链接的。大家可以自己测试下。

## ServerSocket

在客户/服务器通信模式中,服务器端需要创建监听特定端口的 `Serversocket,Serversocke`t负责接收客户连接请求。`java`提供了一个`ServerSocket`类表示服务器`Socket`。

服务器`Socket`在服务器上运行，监听入站ftp连接。每个服务器Socket监听服务器上的一个特定端口。当远程注解上的一个客户端尝试这个端口时，服务器就会被唤醒，协商建立客户端与服务器端的连接，并返回一个常规的`Socket`对象，表示2台主机之间的`Socket`。

也是就说服务器端`Socket`接受到客户端`Socket`发送过来的连接时，服务器端会生成一个常规的Socket对象，用于向客户端发送数据，数据总是通过常规socket进行传输。

## ServerSocket生命周期

`ServerSocket`服务器的基本生命周期包含以下几个:
1.使用一个`ServerSocket（）`构造函数在一个特定的端口创建一个新的`ServerSocket`对象。
2.`ServerSocket`使用他的`accept（）`方法来监听这个端口的入站连接。`accept`会一直阻塞，直到一个客户端尝试与服务器建立连接，此时`accept`将返回一个连接客户端和服务器`Socket`对象。
3.根据服务器的类型，会调用`Socket`对象的`getInputStream`或`getOutputStream`方法，或者这两个方法都调用，以获得客户端通信的输入和输出流。
4.服务器和客户端根据已经协商的协议交互，直到要关闭连接。
5.服务器或客户端关闭连接。
5服务器返回到第2步`accept`，等待下一次连接

## 构造方法
```java
ServerSocket(int port) 
          创建绑定到特定端口的服务器套接字。
ServerSocket(int port, int backlog) 
          创建服务器套接字，backlog为连接请求队列的长度。
ServerSocket(int port, int backlog, InetAddress bindAddr) 
          使用指定的端口、侦听 backlog 和要绑定到的本地 IP 地址创建服务器。
ServerSocket() 
          创建非绑定服务器套接字。
```

## ServerSocket(int port) 

创建绑定到特定端口的服务器套接字。
这个构造方法前面已经使用过了，下面看语法。
`ServerSocket server = new ServerSocket(10000);`
需要注意几点
1. 端口号千万不要指定已经被服务器进程已经占用的端口
2. 尽量不要指定端口为`1-1023`之间的端口，因为在某些操作系统中，如果不是管理员身份运行的该服务程序的话，那么操作系统是拒绝绑定`1-1023`之间的端口的。
3. 如果把`port`设置为`0`时，属于匿名端口，也就是系统会随机分配一个端口的，一般不建议这么使用。并且匿名端口是有着特殊的意义与用图，后期再来讲解。

## ServerSocket(int port, int backlog)

创建服务器套接字，`backlog`为连接请求队列的长度。
管理客户连接请求的任务是由操作系统来完成的。操作系统把这些连接请求存储在一个先进先出的队列中。许多操作系统限定了队列的最大长度,一般为50。

当队列中的连接请求达到了队列的最大容量时,服务器进程所在的主机会拒绝新的连接请求。只有当服务器进程通过`Serversocket`的` accept`方法从队列中取出连接请求,使队列腾出空位时,队列才能继续加入新的连接请求。

对于客户端进程,如果它发出的连接请求被加入到服务器的队列中,那么就与服务器的连接建立成功了。

如果客户端进程发出的连接请求被服务器拒绝, `Socket`构造方法就会抛出`Connection Exception，Serversocket`构造方法的 `backlog`参数用来显式设置连接请求队列的长度,它将覆盖操作系统限定的队列的最大长度。

值得注意的是,在以下几种情况中,仍然会采用操作系统限定的队列的最大长度
1.`backlog`参数的值大于操作系统限定的队列的最大长度
2.`backlog`参数的值小于或等于0;
3.在 `Server Socket`构造方法中没有设置 `backlog`参数。
代码演示
```java
//服务端代码
public class TestServerSocket {
   public static void main(String[] args) {
       try {
           /**
            * 设置了队列为5的长度，只允许有5个链接能建立成功
            */
           final int port = 30000;
           ServerSocket serverSocket = new ServerSocket(port,5);
           Thread.sleep(60000*5);
       } catch (Exception e) {
           e.printStackTrace();
       }
   }
}

//客户端代码
public class TestSocket {
   public static void main(String[] args) throws IOException, InterruptedException {

       Socket[] sockets = new Socket[10];
       final String ip = "127.0.0.1";
       final int port = 30000;
       for (int i = 0; i < 10; i++) {
           //这里与服务端建立10个连接
           sockets[i] = new Socket(ip,port);
       }
       Thread.sleep(3000);
       for (int i = 0; i < 10; i++) {
           sockets[i].close();
       }
   }
}

运行结果：
Exception in thread "main" java.net.ConnectException: Connection refused: connect
 at java.net.DualStackPlainSocketImpl.connect0(Native Method)
 at java.net.DualStackPlainSocketImpl.socketConnect(DualStackPlainSocketImpl.java:79)
 at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:350)
 at java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:206)
 at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:188)
 at java.net.PlainSocketImpl.connect(PlainSocketImpl.java:172)
 at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
 at java.net.Socket.connect(Socket.java:589)
 at java.net.Socket.connect(Socket.java:538)
 at java.net.Socket.<init>(Socket.java:434)
 at java.net.Socket.<init>(Socket.java:211)
 at a005.TestSocket.main(TestSocket.java:14)
```

解释一下上面的代码，在运行的时候服务端代码,`ServerSocket`设置了请求队列的长度为`5`，又休眠了`10`分钟，并且永远都没有 `accept` 取出连接，所以在客户端循环到第`6`个`socket`连接建立的时候就报上面异常了。
现在加上`accept`来取出连接再来看看,修改服务端代码。

```java
public class TestServerSocket {
   public static void main(String[] args) {
       try {
           /**
            * 设置了队列为5的长度，只允许有5个链接能建立成功
            */
           final int port = 30000;
           ServerSocket serverSocket = new ServerSocket(port,5);
           while (true) {
               Socket accept = serverSocket.accept();
               System.out.println(accept.getPort()+"连接已取出...");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
   }
}

执行结果：
59162连接已取出...
59163连接已取出...
59164连接已取出...
59165连接已取出...
59166连接已取出...
59167连接已取出...
59168连接已取出...
59169连接已取出...
59170连接已取出...
59171连接已取出...
```

## ServerSocket(int port, int backlog, InetAddress bindAddr) 

使用指定的端口、侦听 `backlog` 和要绑定到的本地 IP 地址创建服务器。
这个构造方法和上面一个构造方法类似使用，只多了一个本地ip的参数，一般情况下是本机有多个网卡的情况下使用。也就是固定一个`ip`当作服务端`ip`
```java
InetAddress address = InetAddress.getByName("192.168.105.126")；
ServerSocket server=new ServerSocket(3000,5,address);
```

## ServerSocket() 

创建非绑定服务器套接字。
`Serversocket`有一个不带参数的默认构造方法。通过该方法创建的 `ServerSocket`不与任何端口绑定,接下来还需要通过`bind`方法与特定端口绑定。

这个默认构造方法的用途是允许服务器在绑定到特定端口之前,先设置`ServerSocket`的一些选项。因为一旦服务器与特定端口绑定,有些选项就不能再改变了在以下代码中,先把 `ServerSocke`t的 `SO_EUSEADDR`选项设为`true`,然后再绑定端口
```java
ServerSocket server = new ServerSocket();
server.setReuseAddress(true);
server.bind(new InetSocketAddress(8000));
```
如果在实例化`ServerSocket`对象的时候在`setReuseAddress（true）`就没有效果了。

## 常用方法

`accept() `
          侦听并接受到此套接字的连接。
本方法主要是等待客户端连接的，如果没有客户端连接它将一直等待。前面基本上都使用到了，其它的方法基本上和`socket`里面的方法一模一样的使用这里就不过多的讲解了。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")