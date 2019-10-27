## 引言

前面已经讲过线程池和`ServerSocket`的编程了，这一节讲讲怎么在`ServerSocket`中结合线程池使用。

现在服务器端采用的实现方式是：一个客户端对应一个线程。但是，每个新线程都会消耗系统资源：创建一个线程会占用CPU周期，而且每个线程都会建立自己的数据结构（如，栈），也要消耗系统内存。

另外，当一个线程阻塞时，`JVM`将保存其状态，选择另外一个线程运行，并在上下文转换（`context switch`）时恢复阻塞线程的状态。

随着线程数的增加，线程将消耗越来越多的系统资源，这将最终导致系统花费更多的时间来处理上下文转换盒线程管理，更少的时间来对连接进行服务。在这种情况下，加入一个额外的线程实际上可能增加客户端总服务的时间。

我们可以通过限制线程总数并重复使用线程来避免这个问题。我们让服务器在启动时创建一个由固定线程数量组成的线程池，当一个新的客户端连接请求传入服务器，它将交给线程池中的一个线程处理，该线程处理完这个客户端之后，又返回线程池，继续等待下一次请求。

如果连接请求到达服务器时，线程池中所有的线程都已经被占用，它们则在一个队列中等待，直到有空闲的线程可用。

下面做个线程池的小例子。

## 服务端代码创建

先创建一个`Servce`类实现`Runnable`接口，并且有读取方法
直接贴代码吧，尽量重要的注释都写在代码中

```java
import java.io.*;
import java.net.Socket;

public class Service implements Runnable {
   /**
    * 获取客户端的链接对象
    */
   private Socket client;
   public Service(Socket client) {
       this.client = client;
   }

   /**
    * 读取客户端数据
    * @return
    * @throws IOException
    */
   private ObjectInputStream readData() throws IOException {
       //还可以使用其它流对象的方法，为了代码精简所以直接使用上面的代码了
       return new ObjectInputStream(this.client.getInputStream());
   }

   /**
    * 为客户端写入数据
    * @return
    * @throws IOException
    */
   private ObjectOutputStream writeData() throws IOException {
       return new ObjectOutputStream(this.client.getOutputStream());
   }

   @Override
   public void run() {
       ObjectInputStream clientRead = null;
       ObjectOutputStream clientWrite = null;
       try {
           //获取序列化输入流
           clientRead = readData();
           //获取序列化输出流
           clientWrite = writeData();
           Object obj;
           while ((obj = clientRead.readObject()) != null) {
               //获取数据
               System.out.println(this.client.getPort()+"获取客户端数据成功!!!,数据为："+obj.toString());
               clientWrite.writeObject("server接收到了!!!");
           }
           //这里需要通知客户端一下，我要开始关闭了
           clientWrite.writeObject(null);
           System.out.println(this.client.getPort()+"开始关闭了!!!");
       } catch (ClassNotFoundException e) {
           e.printStackTrace();
       } catch (IOException e) {
           e.printStackTrace();
       }finally {
           System.out.println(this.client.getPort()+"关闭关闭了!!!");
           if (this.client != null) {
               try {
                   this.client.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
           if (clientRead != null) {
               try {
                   clientRead.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
           if (clientWrite != null) {
               try {
                   clientWrite.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
       }
   }
}
```
2.创建一个`serverThread`封装类来启动线程池的
```java
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ServerThread {
   /**
    * 获取服务器的对象
    */
   private ServerSocket serverSocket;
   private ExecutorService pool;
   private static final int PORT = 30000;

   public ServerThread() throws IOException {
       this.serverSocket = new ServerSocket(PORT);
       /**
        * 初始化线程池
        * Runtime.getRuntime().availableProcessors()
        * 表示主机有几个cpu就设置它的cpu的2倍数量的线程
        */
       int nThreads = Runtime.getRuntime().availableProcessors()*2;
       this.pool = Executors.newFixedThreadPool(nThreads);
       System.out.println("启动服务器");
   }

   public void service() {
       while (true) {
           Socket clientSocket = null;
           try {
               //这里等待客户端链接
               clientSocket = this.serverSocket.accept();
               System.out.println(clientSocket.getPort()+"链接成功!!!");
               //这里就只要客户端链接之后直接使用线程执行任务了。
               this.pool.execute(new Service(clientSocket));
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }

   public static void main(String[] args) throws IOException {
       ServerThread serverThread = new ServerThread();
       serverThread.service();
   }
}
```
这里需要注意的是千万别`serverSocket.close`写在`finally`中，我就犯过这个错误，那样将会每次接收一个链接之后运行完业务之后就会自动关闭服务端所以一直会报错的。

3.客户端就简单了和以前区别不大,直接上代码吧！

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.Socket;
import java.util.ArrayList;
import java.util.List;

public class ClientTcp {
   public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
       Socket clinet = new Socket("127.0.0.1",30000);
       //模拟发送的数据
       List<String> list = sendData();
       ObjectOutputStream out = new ObjectOutputStream(clinet.getOutputStream());
       ObjectInputStream  in = new ObjectInputStream(clinet.getInputStream());
       for (String str : list) {
           //像服务端发送数据
           out.writeObject(str);
           //获取服务端的数据
           Object object = in.readObject();
           Thread.sleep(2000);
           //打印服务端发过来的数据
           if (object == null) {
               break;
           }
           System.out.println(object);
       }
       out.close();
       in.close();
       clinet.close();
   }

   /**
    * 模拟发送的数据
    * @return
    */
   private static List<String> sendData() {
       List<String> list = new ArrayList<>();
       //要发送服务器的数据
       for (int i = 1; i <= 10; i++) {
           list.add(String.valueOf(i));
       }
       list.add(null);
      return list;
   }
}
```

好了写完了，基本上和前面章节的操作很类似，这里知识封装了一下加了一个线程池，应该还是比较简单的。初学者最好把代码全部拷贝出来在自己电脑上运行一遍。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")