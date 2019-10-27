Java 9 这个版本对进程管理方面的改进也是相当大的。在为数不多的几次 Java 项目中，有偶尔用到多线程，但对多进程和进程方面的了解还真是太少。

我想，大部分人应该跟我一样，在编程之外知道有进程的东西的存在，在 Java 中反而会忽视，因为多线程和并发 ( Concurrency ) 的存在感更强吧。

这次 Java 9 对进程管理的改进主要是提供了 `ProcessHandle` 类

## ProcessHandle 类 ##

该类在 `java.lang` 包中，且处于 `java.base` 模块中。

`ProcessHandle` 可以用于获取进程信息，监听和检查进程的状态，并且可以监听进程的退出

主要提供了以下几个方法

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">static allProcesses()</td> 
   <td align="left">返回当前进程可见的所有进程的快照</td> 
  </tr> 
  <tr> 
   <td align="left">static current()</td> 
   <td align="left">返回当前进程的 ProcessHandle 实例</td> 
  </tr> 
  <tr> 
   <td align="left">static of(long pid)</td> 
   <td align="left">返回现有本机进程的 <code>Optional <ProcessHandle></code></td> 
  </tr> 
  <tr> 
   <td align="left">children()</td> 
   <td align="left">返回进程的当前直接子进程的快照</td> 
  </tr> 
  <tr> 
   <td align="left">compareTo(ProcessHandle other)</td> 
   <td align="left">比较两个进程</td> 
  </tr> 
  <tr> 
   <td align="left">descendants()</td> 
   <td align="left">返回当前进程后代的快照</td> 
  </tr> 
  <tr> 
   <td align="left">destroy()</td> 
   <td align="left">请求杀死当前进程</td> 
  </tr> 
  <tr> 
   <td align="left">destroyForcibly()</td> 
   <td align="left">强制杀死该进程</td> 
  </tr> 
  <tr> 
   <td align="left">equals(Object other)</td> 
   <td align="left">如果 <code>other</code> 对象为非 <code>null</code>，且具有相同的实现，并且表示相同的系统进程，则返回 true; 否则返回 false</td> 
  </tr> 
  <tr> 
   <td align="left">hashCode()</td> 
   <td align="left">返回此 ProcessHandle 的哈希值</td> 
  </tr> 
  <tr> 
   <td align="left">info()</td> 
   <td align="left">返回有关该进程的信息的快照</td> 
  </tr> 
  <tr> 
   <td align="left">isAlive()</td> 
   <td align="left">测试此 ProcessHandle 表示的进程是否处于活动状态</td> 
  </tr> 
  <tr> 
   <td align="left">onExit()</td> 
   <td align="left">当进程终止时返回 <code>CompletableFuture <ProcessHandle></code></td> 
  </tr> 
  <tr> 
   <td align="left">parent()</td> 
   <td align="left">返回当前进程的父进程 <code>Optional<ProcessHandle></code> ，因为当前进程可能是初始进程，所以父进程不一定存在</td> 
  </tr> 
  <tr> 
   <td align="left">pid()</td> 
   <td align="left">返回当前进程的系统进程的 id</td> 
  </tr> 
  <tr> 
   <td align="left">supportsNormalTermination()</td> 
   <td align="left">如果 <code>destroy()</code> 正常终止进程，则返回 true</td> 
  </tr> 
 </tbody> 
</table>

`ProcessHandle` 类用于标识并提供对 `native` 进程的控制，可以监控每个单独的进程的活跃度，列出其子进程 ( 线程 ) ，获取有关进程的信息或将其销毁。

而很早就存在的 `Process` 类，它的实例由当前进程启动，只提供了对进程输入，输出和错误流的访问

native 进程 ID 是操作系统分配给进程的标识号，这个 ID 值的范围取决于操作系统，例如，嵌入式系统可能使用 16 位值。

关于这个 ProcessHandle 类，还有很多信息，如果你对此感兴趣，可以访问 [官方 API 文档： Interface ProcessHandle][API _ Interface ProcessHandle]

## 范例 ##

我们写一个范例来演示下 `ProcessHandle` 如何使用，在当前工作区创建一个文件 `ProcessHandleTester.java`，并输入以下内容

```
import java.time.ZoneId;
import java.util.stream.Stream;
import java.util.stream.Collectors;
import java.io.IOException;

public class ProcessHandleTester {
   public static void main(String[] args) throws IOException {

      // Windows 有效
      ProcessBuilder pb = new ProcessBuilder("notepad.exe");
      String np = "Not Present";
      Process p = pb.start();
      ProcessHandle.Info info = p.info();
      System.out.printf("Process ID : %s%n", p.pid());
      System.out.printf("Command name : %s%n", info.command().orElse(np));
      System.out.printf("Command line : %s%n", info.commandLine().orElse(np));

      System.out.printf("Start time: %s%n",
         info.startInstant().map(i -> i.atZone(ZoneId.systemDefault())
         .toLocalDateTime().toString()).orElse(np));

      System.out.printf("Arguments : %s%n",
         info.arguments().map(a -> Stream.of(a).collect(
         Collectors.joining(" "))).orElse(np));

      System.out.printf("User : %s%n", info.user().orElse(np));
   } 
}
```

运行该范例，输出结果如下

```
[penglei@tech.souyunku.com java9]$ javac ProcessHandleTester.java && java ProcessHandleTester
Process ID : 5800
Command name : C:\Windows\System32\notepad.exe
Command line : Not Present
Start time: 2017-11-04T21:35:03.626
Arguments : Not Present
User: administrator
```


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")