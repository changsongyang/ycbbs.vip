## 引言

`java.nio`全称`java non-blocking IO`，是指`jdk1.4` 及以上版本里提供的新`api(New IO)`，为所有的原始类型（`boolean`类型除外）提供缓存支持的数据容器，使用它可以提供非阻塞式的高伸缩性网络。

## 目录大纲

1.阻塞和非阻塞
2.同步和异步
3.Java IO模型,NIO,BIO,AIO
4.NIO的原理
5.NIO操作流程
6.代码演示

## 1.阻塞和非阻塞

阻塞和非阻塞是进程在访问数据的时候，进程需不需要等待。

1. 阻塞
当数据没有准备好时，都会一直等待缓冲区中的数据准备就绪之后才会开始处理，否则会一直等待下去。

2.  非阻塞
当进程访问到数据缓冲区时，如果数据还未准备好，会直接返回不会一直等待，如果数据已经准备好了，也会直接返回。


## 2.同步和异步

同步和异步都是基于应用程序和操作系统处理IO事件锁采用的方式。

同步：

1. 同步是应用系统直接参与`IO`读写操作。在处理`IO`事件的时候必须阻塞在某个方法上面等待`IO`事件完成(阻塞IO事件或则通过轮询`IO`事件的方式)。
 
2. 阻塞`IO`实现方式，一般是直接阻塞到read和`write`方法。都是将读（写）方法交给线程来操作，然后阻塞线程的方式来实现，只是这样对线程开销较大。

异步：
1. 异步是所有的`IO`读写都交给了操作系统，这个时候就可以去做其它的事情并不需要去完成真正的`IO`操作,当操作完成`IO`后，系统将会给的应用程序一个通知的。

## 3.Java IO模型,NIO,BIO,AIO

### NIO:
`jdk1.4 linux`多路复用技术 (`select`模式)实现IO事件的轮询方式同步非阻塞的模式这种方式目前是主流的网络通信模式。
 
目前市面上的这种模式的框架有：`Mina, netty，mina2.0，nett5.0`--网络通信框架比直接写
`NIO`要容易些，并且代码可读性更好。

### BIO:
`JDK1.4`以前使用都是`BIO`阻塞`IO`，主要是阻塞到线程来操作的，但对于线程的开销本来就是性能的浪费。

### AIO:
`jdk1.7(NIO2)`才是实现真正的异步`aio`,学习 它的思想主要是借鉴了`linux epoll`模式。

## 4.NIO的原理

先来看一张图

![](https://gitee.com/duchaochen/gongzhonghao/raw/master/5/73-1.jpg)

网络通信中，`NIO`也提供了`SocketChannel`和`ServerSocketChannel`两种不同的套接字通道来实现，这两个类都实现了`Channel`接口。它们可以设置阻塞余非阻塞两种模式，为了实现高负载高并发都采取非阻塞的模式。

`NIO`采用缓冲区`Buffer`，实现对数据的读写操作，缓冲区是固定大小，并由内部状态记录有多少数据被放入或者取出。

与阻塞IO不同，阻塞IO采用阻塞式流（`Stream`）的方式进行读写，流是单向的只能向一个方向读数据或者写数据。

而通道是双向的，可以同时在通道上发送和读取数据，而且是非阻塞的，在没有数据可读可写时可以去做别的事情。

主要使用了`ServerSocketChannel，SocketChannel,Selector,ByteBuffer`这么几个类。
### 1.ServerSocketChannel(服务端使用类)
采用api文档解释：通过调用此类的 `open` 方法创建服务器套接字通道。新创建的服务器套接字通道已打开，但尚未绑定。

试图调用未绑定的服务器套接字通道的 `accept` 方法会导致抛出 `NotYetBoundException`。

可通过调用相关服务器套接字的某个 `bind` 方法来绑定服务器套接字通道。多个并发线程可安全地使用服务器套接字通道。

### 2.SocketChannel(客户端使用类)
采用`api`文档解释：通过调用此类的某个 `open` 方法创建套接字通道。

新创建的套接字通道已打开，但尚未连接。试图在未连接的通道上调用 `I/O` 操作将导致抛出 `NotYetConnectedException`。

可通过调用套接字通道的 `connect` 方法连接该通道；一旦连接后，关闭套接字通道之前它会一直保持已连接状态。

可通过调用套接字通道的 `isConnected` 方法来确定套接字通道是否已连接。
 
### 3. Selector(选择器)：
是 `SelectableChannle` 对象的多路复用器，Selector 可以同时监控多个 `SelectableChannel` 的 `IO` 状况，也就是说，利用 `Selector`可使一个单独的线程管理多个 Channel，`selector` 是非阻塞 `IO` 的核心。

当通道使用`register（Selector sel, int ops）`方法将通道注册选择器时，选择器对通道事件进行监听，通过第二个参数指定监听的事件类型。
其中可监听的事件类型包括以下：
　　`读 : SelectionKey.OP_READ （1）
　　写 : SelectionKey.OP_WRITE （4）
　　连接 : SelectionKey.OP_CONNECT （8）
　　接收 : SelectionKey.OP_ACCEPT （16）`

如果需要监听多个事件是：
　　`int key = SelectionKey.OP_READ | SelectionKey.OP_WRITE ;` //表示同时监听读写操作

### 4.Bytebuffer
`ByteBuffer`类是`Buffer`的子类，`Buffer`是顶层抽象类，`ByteBuffer`继承`Buffer`，也是抽象类。看看继承结构。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/5/73-2.png)

`ByteBuffer`是在`javaNIO`中常使用的一个缓冲区类，使用`ByteBuffer`可以进行高效的IO操作。通过 `ByteBuffer`提供很多读写的方法`put()，get(),`并且还包含四个很重要的属性。

容量（`capacity`）
`capacity`指的是缓冲区能够容纳元素的最大数量，这个值在缓冲区创建时被设定，而且不能够改变，如下，我们创建了一个最大容量为10的字节缓冲区；
`ByteBuffer bf = ByteBuffer.allocate(10);`

上界（`limit`）
limit指的是缓冲区中第一个不能读写的元素的数组下标索引，也可以认为是缓冲区中实际元素的数量；

位置（`position`）
`position`指的是下一个要被读写的元素的数组下标索引，该值会随get()和put()的调用自动更新；

标记（`mark`）
一个备忘位置，调用`mark()`方法的话，`mark`值将存储当前`position`的值，等下次调用`reset()`方法时，会设定`position`的值为之前的标记值；

四个属性值之间的关系
根据以上四个属性的定义，我们可以总结出它们之间的关系如下：
`0 <= mark <= position <= limit <= capacity`

## 5.NIO操作流程
1. 服务端对象：`ServerSocketChannel`
2. 客户端对象：`SocketChannel`
3. 选择器：`Selector selector= Selector.open();`//这样就打开了选择器
4. 获得选择器中的事件集合：`Set<SelectionKey> key= selector.selectedKeys()`
5. `Selectionkey`:可以通过它来判断`IO`事件是否已经就绪
```java
key. isAccptable:是否可以接受客户端的连接
key, isConnctionable:是否可以连接服务端
key, isReadableo:缓冲区是否可读
key, isWriteableo:缓冲区是否可写
```


### 6.如何注册

```java
channel.regist(selector, Selectionkey.OP_WRITE)//注册写事件
channel.regist(Selector, Selectionkey.OP_READ);//注册读事件
channel.regist(Selector, Selectionkey.OP_CONNECT);//注册连接事件
channel.regist(Selector, Selectionkey.OP_ACCEPT);//注册请求事件
```
以上注册成功之后就可以通过SelectionKey来判断io事件是否就绪，然后可以进行后续操作。

## 6.代码演示

现在来演示一下网络编程中的`nio`使用，使用网络编程`nio`的编程就需要使用到`Channel`(管道)、`Selector`(事件选择器) 、`Bytebuffer`(缓冲区)。

演示代码一般会使用服务端代码和客户端代码分别书写。我会尽量在代码中把注释写详细一些。

### 1.服务端代码
```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;

public class Server {
   /**
    * 服务器端口
    */
   private static final int PORT = 30000;
   /**
    * 设置服务器address对象
    */
   private static final InetSocketAddress ADDRESS = new InetSocketAddress(PORT);
   /**
    * 设置读/取缓冲区的字节大小
    */
   private static final int SIZE = 1024;
   /**
    * 定义一个发送数据的缓冲区对象
    */
   private static ByteBuffer sendBuffer = ByteBuffer.allocate(SIZE);
   /**
    * 定义一个接收数据的缓冲区对象
    */
   private static ByteBuffer receiveBuffer = ByteBuffer.allocate(SIZE);
   /**
    * 定义一个事件选择器对象
    */
   private static Selector selector;

   public Server() throws IOException {
       /**
        * 定义一个socket服务端管道对象
        */
       ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
       //设置成非阻塞
       serverSocketChannel.configureBlocking(false);
       //绑定端口
       serverSocketChannel.bind(ADDRESS);
       //创建一个事件选择器对象
       selector = Selector.open();
       //注册请求事件（个人理解是将请求事件与管道绑定关系的意思）)
       serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
       System.out.println("服务器已经开启...");
   }

   public void listen() throws IOException {
       /**
        * 因为服务器一般都是接收所有的客户端请求的所以这里使用一个死循环
        */
       while (true) {
           //查询是否有事件，如果没有将停顿一秒后在执行后续代码
           selector.select(1000);
           //获取所有事件键值
           Set<SelectionKey> selectionKeys = selector.selectedKeys();
           Iterator<SelectionKey> iterator = selectionKeys.iterator();
           //开始循环事件
           while (iterator.hasNext()) {
               SelectionKey key = iterator.next();
               //删除已处理的事件，防止重复处理
               iterator.remove();
               handler(key);
           }
           //清空注册事件
           selectionKeys.clear();
       }
   }

   /**
    * 业务处理
    * @param key
    */
   private void handler(SelectionKey key) throws IOException {
       if (key.isAcceptable()) {
           //获取请求事件
           accept(key);
       } else if (key.isReadable()) {
           //获取读取事件
           read(key);
       } else if (key.isWritable()) {
           write(key);
       }
   }

   /**
    * 写入事件
    * @param key
    */
   private void write(SelectionKey key) throws IOException {
       //获取客户端
       SocketChannel client = (SocketChannel) key.channel();
       //清空读的缓冲区
       sendBuffer.clear();
       //将数据写入缓冲区
       sendBuffer.put("我是服务器".getBytes("UTF-8"));
       //将缓冲区复位
       sendBuffer.flip();
       //发送数据到客户端
       client.write(sendBuffer);
       //注册读事件
       client.register(selector,SelectionKey.OP_READ);
   }

   /**
    * 读取事件
    * @param key
    * @throws IOException
    */
   private void read(SelectionKey key) throws IOException {
       //获取客户端
       SocketChannel client = (SocketChannel) key.channel();
       //清空读的缓冲区
       receiveBuffer.clear();
       //读取客户端传过来的书籍
       int len = client.read(receiveBuffer);
       //复位缓冲区
       receiveBuffer.flip();
       //解析数据
       System.out.println("服务端接收客户端传过来的数据:"+new String(receiveBuffer.array(),0,len));
       //注册写入事件
       client.register(selector,SelectionKey.OP_WRITE);
   }

   private void accept(SelectionKey key) throws IOException {
       //获取服务器管道
       ServerSocketChannel server = (ServerSocketChannel) key.channel();
       //获取客户端请求
       SocketChannel client = server.accept();
       //将客户端对象设置为非阻塞模式
       client.configureBlocking(false);
       //注册读的事件
       client.register(selector,SelectionKey.OP_READ);
   }

   public static void main(String[] args) throws IOException {
       Server server = new Server();
       server.listen();
   }
}
```

### 2.客户端代码

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;

public class Clinet {
   private static final int PORT = 30000;
   /**
    * 设置服务器address对象
    */
   private static final InetSocketAddress ADDRESS = new InetSocketAddress("127.0.0.1",PORT);
   /**
    * 设置读/取缓冲区的字节大小
    */
   private static final int SIZE = 1024;
   /**
    * 获取数据的缓冲区对象
    */
   private static ByteBuffer receiveByteBuffer = ByteBuffer.allocate(SIZE);
   /**
    * 写入数据的缓冲区对象
    */
   private static ByteBuffer sendByteBuffer = ByteBuffer.allocate(SIZE);
   /**
    * 事件选择器
    */
   private Selector selector;

   /**
    * 键盘输入缓存区对象
    */
   private BufferedReader reader;

   public Clinet(BufferedReader reader) throws IOException {
       //创建客户端管道对象
       SocketChannel client = SocketChannel.open();
       //使用非阻塞
       client.configureBlocking(false);
       //连接
       client.connect(ADDRESS);
       //创建事件选择器
       selector = Selector.open();
       //注册连接事件
       client.register(selector, SelectionKey.OP_CONNECT);
       this.reader = reader;
   }

   public void serice() throws IOException {

       while (true) {
           selector.select(1000);
           Set<SelectionKey> selectionKeys = selector.selectedKeys();
           Iterator<SelectionKey> iterator = selectionKeys.iterator();
           while (iterator.hasNext()) {
               SelectionKey key = iterator.next();
               //删除已处理事件，防止重复处理
               iterator.remove();
               handler(key);
           }
           //清空事件
           selectionKeys.clear();
       }
   }

   private void handler(SelectionKey key) throws IOException {
       //连接事件判断
       if (key.isConnectable()) {
           connect(key);
       }
       //读取事件判断
       else if (key.isReadable()) {
           read(key);
       }
       //写入事件判断
       else if (key.isWritable()) {
           write(key);
       }
   }

   private void write(SelectionKey key) throws IOException {
       //客户端管道
       SocketChannel client = (SocketChannel) key.channel();
       //清空缓冲区
       sendByteBuffer.clear();
       //将数据填充到缓冲区
       sendByteBuffer.put(("客户端输入数据为："+this.reader.readLine()).getBytes("UTF-8"));
       //这里一定要flip一下，这个是复位缓冲区的意思
       sendByteBuffer.flip();
       //发送数据
       client.write(sendByteBuffer);
       //注册读取事件
       client.register(selector,SelectionKey.OP_READ);
   }

   private void read(SelectionKey key) throws IOException {
       //客户端管道
       SocketChannel client = (SocketChannel) key.channel();
       //清空缓冲区
       receiveByteBuffer.clear();
       //读取服务端传数据写进缓冲区
       int len = client.read(receiveByteBuffer);
       //每次都要调用一个flip方法
       receiveByteBuffer.flip();
       //打印获取服务端的数据
       System.out.println(new String(receiveByteBuffer.array(),0,len));
       //注册写入事件
       client.register(selector,SelectionKey.OP_WRITE);
   }

   private void connect(SelectionKey key) throws IOException {
       //客户端管道
       SocketChannel client = (SocketChannel) key.channel();
       if (client.isConnectionPending()) {
           //完成与服务端连接
           client.finishConnect();
           //清空缓冲区
           sendByteBuffer.clear();
           //将数据填充到缓冲区
           sendByteBuffer.put("我是客户端来了".getBytes("UTF-8"));
           //这里一定要flip一下，这个是复位缓冲区的意思
           sendByteBuffer.flip();
           //发送数据
           client.write(sendByteBuffer);
       }
       //注册读取事件
       client.register(selector,SelectionKey.OP_READ);
   }

   public static void main(String[] args) throws IOException {
       //实例化一个键盘输入流缓冲区
       BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));

       Clinet clinet = new Clinet(reader);
       clinet.serice();
   }
}
```
先运行服务端程序会显示“服务器已经开启”的字样，然后运行客户端程序会在连接成功时发送"我是客户端来了"的字样，服务端就会接收到客户端的数据并且打印。

然后客户端输入“`111`”，紧接着服务端就会打印“服务端接收客户端传过来的数据:客户端输入数据为：`111`”的字样，然后在客户端继续输入`222`，服务端就会打印出“服务端接收客户端传过来的数据:客户端输入数据为：`222`”
如下所示：

```java
服务端显示：
服务器已经开启...
服务端接收客户端传过来的数据:我是客户端来了
服务端接收客户端传过来的数据:客户端输入数据为：111
服务端接收客户端传过来的数据:客户端输入数据为：222

客户端显示
我是服务器
111
我是服务器
222
我是服务器

```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")