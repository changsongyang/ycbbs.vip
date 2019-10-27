## 引言

`java.net.Socket` 类代表客户端和服务器都用来互相沟通的套接字。客户端要获取一个 `Socket` 对象通过实例化 ，而 服务器获得一个 `Socket` 对象则通过` accept()` 方法的返回值。

## 构造函数

```java

public Socket(String host, int port) throws UnknownHostException, IOException.
  创建一个流套接字并将其连接到指定主机上的指定端口号。
  
public Socket(InetAddress host, int port) throws IOException
  创建一个流套接字并将其连接到指定 IP 地址的指定端口号。
  
public Socket(String host, int port, InetAddress localAddress, int localPort) throws IOException.
  创建一个套接字并将其连接到指定远程主机上的指定远程端口。
  
public Socket(InetAddress host, int port, InetAddress localAddress, int localPort) throws IOException.
  创建一个套接字并将其连接到指定远程地址上的指定远程端口。
  
public Socket()
  通过系统默认类型的 SocketImpl 创建未连接套接字
```

## public Socket(String host, int port) 

 创建一个流套接字并将其连接到指定主机上的指定端口号。
方法参数解释
`host`：服务端地址
`port`：服务端端口
现在做一个扫描主机上的从`1-1024`的端口判断是否已经被服务器监听的小例子

```java
public class TcpClient1 {
   public static void main(String[] args) {
       String host = "127.0.0.1";
       Socket socket = null;

       for (int port = 1; port <= 1024; port++) {
           try {
               socket = new Socket(host,port);
               System.out.println("端口：" + port + "已被监听!!!");
           } catch (IOException e) {
               System.out.println("=========="+port+"没有被监听!!!");
           }
           finally {
               if (socket != null) {
                   try {
                       socket.close();
                   } catch (IOException e1) {

                   }
               }
           }
       }
   }
}
//执行结果
//其它的就省略了
==========132没有被监听!!!
==========133没有被监听!!!
==========134没有被监听!!!
端口：135已被监听!!!
==========136没有被监听!!!
==========137没有被监听!!!
==========138没有被监听!!!
==========139没有被监听!!!
```

## public Socket(InetAddress host, int port) throws IOException

创建一个流套接字并将其连接到指定 `IP` 地址的指定端口号。
方法参数解释
`host`：服务端`ip`地址的对象，`IntetAddress` 的静态方法 `getByName` 可以获取 `IntetAddress` 的对象,`getByName` 的参数可以为 `ip` 也可以使用域名。
`port`：服务端端口
看代码
```java
public class TcpClient1 {
   public static void main(String[] args) throws IOException {
       final String host = "127.0.0.1";
       final int port = 30000;
       Socket socket = new Socket(InetAddress.getByName(host), port);
       System.out.println("链接成功！！！");
       socket.close();
   }
}

结果：
链接成功！！！

```
服务端代码直接使用上一节的代码

## public Socket(String host, int port, InetAddress localAddress, int localPort)
创建一个套接字并将其连接到指定远程主机上的指定远程端口。
方法参数解释：
    `host`：服务端地址
    `port`：服务端端口号
    `localAddress`：本地主机ip地址对象
    `localPort`：本地主机端口
注意：本地主机ip对象的参数是防止本地主机有多个网卡有局域网和外网的情况下这个时候就可以指定为是局域网的`ip`还是外网的`ip`了

```java
public class TcpClient1 {
  public static void main(String[] args) {
      final String host = "127.0.0.1";
      final int port = 30000;
      Socket socket = null;
      try {
          socket = new Socket(host, port, InetAddress.getByName("127.0.0.1"),8521);
          System.out.println("链接成功！！！");
      } catch (IOException e) {
          e.printStackTrace();
      }finally {
          try {
              if (socket != null) {
                  socket.close();
              }
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  }
}
```
 
## public Socket(InetAddress host, int port, InetAddress localAddress, int localPort)

创建一个套接字并将其连接到指定远程地址上的指定远程端口。
方法参数解释：
`host`：远程服务端的地址对象
`port`：远程服务端的端口号
`localAddress`：本地主机的地址对象
`localPort`：本地主机的端口号
本方法和上面的`public Socket(String host, int port, InetAddress localAddress, int localPort)`方法使用类似，只是将第一个参数的`string`类型变成了`InetAddress`对象了。

```java
public class TcpClient1 {
   public static void main(String[] args) {
       final String host = "127.0.0.1";
       final int port = 30000;
       Socket socket = null;
       try {
           socket = new Socket(InetAddress.getByName(host), port, InetAddress.getByName("127.0.0.1"),8521);
           System.out.println("链接成功！！！");
       } catch (IOException e) {
           e.printStackTrace();
       }finally {
           try {
               if (socket != null) {
                   socket.close();
               }
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
}
```

## public Socket()

通过系统默认类型的 `SocketImpl` 创建未连接套接字

```java

public class TcpClient1 {
  public static void main(String[] args) throws IOException {
      final String host = "127.0.0.1";
      final int port = 30000;
      Socket socket = new Socket();
      socket.connect(new InetSocketAddress(host,port));
      System.out.println("链接成功！！！");
      socket.close();
  }
}
结果显示
链接成功！！！
```

## 常用方法

```java
1   public void connect(SocketAddress host, int timeout) throws IOException
将此套接字连接到服务器，并指定一个超时值。

2   public InetAddress getInetAddress()
返回套接字连接的地址。

3   public int getPort()
返回此套接字连接到的远程端口。

4   public int getLocalPort()
返回此套接字绑定到的本地端口。

5   public SocketAddress getRemoteSocketAddress()
返回此套接字连接的端点的地址，如果未连接则返回 null。

6   public InputStream getInputStream() throws IOException
返回此套接字的输入流。

7   public OutputStream getOutputStream() throws IOException
返回此套接字的输出流。

8   public void close() throws IOException
关闭此套接字。
```
以上构造方法和常用方法已经列出来了，现在我们来一个个的逐个测试。
服务端的代码还是上一节的代码

```java
public class TcpClient1 {
   public static void main(String[] args) throws IOException, InterruptedException, ClassNotFoundException {
       final String host = "127.0.0.1";
       final int port = 30000;
       Socket socket = new Socket();
       socket.connect(new InetSocketAddress(host,port));
       //返回套接字连接的地址。
       System.out.println(socket.getInetAddress().getHostName());
       //返回此套接字连接到的远程端口。
       System.out.println(socket.getPort());
       //返回此套接字绑定到的本地端口。
       System.out.println(socket.getLocalPort());
       //返回此套接字连接的端点的地址，如果未连接则返回 null。
       System.out.println(socket.getRemoteSocketAddress());
       //返回此套接字的输入流。socket.getOutputStream()
       new ObjectOutputStream(socket.getOutputStream()).writeObject("我是客户端");
       //返回此套接字的输出流。socket.getInputStream()
       ObjectInputStream objectInputStream = new ObjectInputStream(socket.getInputStream());
       Object object = objectInputStream.readObject();
       //打印服务端传过来的数据
       System.out.println(object);
       System.out.println("链接成功！！！");
       socket.close();
   }
}
```

注意：如果服务端接收的是使用序列化流，那么客户端也需要序列化写入。

## socket编程异常与解决

### 1.java.net.SocketTimeoutException
出现原因:这个异常表示很常见，原因就是Socket超时。

解决方案:一般会有2个地方会抛出这个异常，一个是在`Connect`的时候，由`connect(SocketAddress endpoint,int timeout)`中的后者来决定；另外一个就是`setSoTimeout(int timeout)`，这个是设定读取的超时时间。它们设置成0均表示无限大。

### 2.java.net.BindException:Address already in use
JVM_Bind出现原因:该异常发生在服务端进行`New ServerSocket(Port)`或者`Socket.bind(bingPort)`操作的时候，原因就是与Port一样的一个端口已经被启动，并进行监听。
解决方案:此时呢，我们可以用`netstat -an`的命令，可以监听到一个`Listending`状态的端口。

只需要找一个没有被占用的端口就能解决问题。或者，我们在使用端口前，优先去查看哪些端口不能使用。(注:`Port`值为`0-65536`的整型值)

### 3.java.net.ConnectException: Connection refused: connect
出现原因:该异常发生在客户端进行`new Socket(Ip,Port)`或`socket.connect(address,timeout)`操作时，原因就是指定的ip地址不能被找到，或者说`ip`地址存在，但是找不到对应的端口进行监听。

解决方案:首先检查客户端的`ip`和`port`是否写错了，假如正确可以测试客户端和服务器端时候可以`ping`通，如果可以`ping`通，则在服务端重新找一个可以用的端口；如果`ping`不通，则需要另外想办法了。

### 4.java.net.SocketException: Socket is closed
出现原因:该异常在客户端和服务器端均可能发生，原因就是，客户端或者服务器端主动关闭了链接，Spcket的close方法，随后再次对网络链接进行一系列操作。
解决方案:首先我们要弄清楚主动关闭链接的原因，杜绝以后再次被关闭的可能性；然后我们重启客户端和Server端，重新建立通讯即可。

### 5.java.net.SocketException:Connection reset 或者 Connect reset by peer:Socket write error出现原因

该异常在客户端和服务器端均可能发生，引发该异常有两个原因:

①如果一端的`Socket`被关闭(主动或者异常引起的关闭)后，另一方还在继续放松数据，发送的第一个数据包机会引发异常`Connect reset by peer`；

②另一个是端退出，但退出时为关闭链接，另一端从连接中读取数据则抛出异常`Connection reset`.总结一下便是，因为由链接断开后的读和写操作引起的。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")