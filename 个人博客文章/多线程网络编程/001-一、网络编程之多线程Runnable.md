## Runnable

`JDK`说的：`Runnable` 接口应该由那些打算通过某一线程执行其实例的类来实现。实现类必须实现一个名称为 `run` 的无参数方法。
也有其它的说法是：如果让一个线程实现`Runnable`接口，那么当调用这个线程的对象开辟多个线程时，可以让这些线程调用同一个变量；若这个线程是由继承`Thread`类而来，则要通过内部类来实现上述功能，利用的就是内部类可任意访问外部变量这一特性。
个人理解主要是解决多继承问题。

## Runnable优点

实现多线程，有两种方式， 一种是继承`Thread`类，一种是实现`Runnable`接口。前面已经有了`Thread`类可以实现线程了为何还要使用`Runnable`接口呢？

`Runnable`只是一个接口，并不能启动一个线程，因为它没有start方法，只有一个run方法,所以并不能代表一个线程。

`Runnable`接口和线程是两个不同的概念！因为`Thread`是一个能开启线程的类，并且它继承了`Runnabl`e接口重写了`run`方法，如果现在创建一个类的继承了`Thread`类了，那么这个时候还需要继承另一个类怎么办呢，`java`是单根继承所以这个时候就可以直接实现`Runnable`接口就可以了。那么这样该怎么操作呢？

```java
class MyRunnable implements Runnable {
   @Override
   public void run() {
       System.out.println("线程名称："+Thread.currentThread().getName());
   }
}
MyRunnable myRunnable = new MyRunnable();
Thread thread1 = new Thread(myRunnable);
thread1.setName("A..");
thread1.start();
System.out.println("程序结束!!!");

结果：程序结束!!!
    线程名称：A..
```
## 数据共享

先看一段继承`Thread`类的线程，然后在来讨论数据共享。
```java
class MyRunnable extends Thread {
   int count = 5;
   @Override
   public void run() {
       while (count > 0) {
           System.out.println("线程名称："+Thread.currentThread().getName() + "，还剩票数为：" + count--);
       }
       System.out.println("==============================");
   }
}
运行代码
MyRunnable myRunnable1 = new MyRunnable();
myRunnable1.setName("A..");
MyRunnable myRunnable2 = new MyRunnable();
myRunnable2.setName("B..");
MyRunnable myRunnable3 = new MyRunnable();
myRunnable3.setName("C..");

myRunnable3.start();
myRunnable2.start();
myRunnable1.start();
System.out.println("程序结束!!!");
输出结果:
程序结束!!!
线程名称：C..，还剩票数为：5
线程名称：C..，还剩票数为：4
线程名称：C..，还剩票数为：3
线程名称：B..，还剩票数为：5
线程名称：C..，还剩票数为：2
线程名称：A..，还剩票数为：5
线程名称：B..，还剩票数为：4
线程名称：A..，还剩票数为：4
线程名称：C..，还剩票数为：1
==============================
线程名称：A..，还剩票数为：3
线程名称：A..，还剩票数为：2
线程名称：A..，还剩票数为：1
==============================
线程名称：B..，还剩票数为：3
线程名称：B..，还剩票数为：2
线程名称：B..，还剩票数为：1
==============================
```

上面输出的是每个线程都打印出了`5，4，3，2，1`。现在我们想实现一个创建多个线程执行的打印并且每个线程打印的票数不允许重复，当然也可以使用这种继承的方式实现，相对来说麻烦一点，现在我们使用实现Runnable的结果来实现一下。也就是实现一个简单的打印票据系统！！！

## 打印票据系统

需求就是客运站有多个窗口卖票，那么票数为30张，一共有5个窗口买票。出票肯定不能重复。这个时候就可以使用`Thread(Runnable target) `构造方法了，这个构造方法支持一个`Runnable`的接口。
```java
class MyRunnable implements Runnable {
   /**
    * 一共有30张票
    */
   int count = 30;
   @Override
   public void run() {
       try {
           while (count > 0) {
               int time = (int) (Math.random()*1000);
               Thread.sleep(time);
               System.out.println("窗口名称："+Thread.currentThread().getName() + "，还剩票数为：" + count--);
           }
         } catch (InterruptedException e) {
           e.printStackTrace();
         }
   }
}
为了方便看到效果，加了一个随机数的休眠。
MyRunnable myRunnable = new MyRunnable();
//设置5个窗口卖票
Thread thread1 = new Thread(myRunnable);
thread1.setName("1窗口");
Thread thread2 = new Thread(myRunnable);
thread2.setName("2窗口");
Thread thread3 = new Thread(myRunnable);
thread3.setName("3窗口");
Thread thread4 = new Thread(myRunnable);
thread4.setName("4窗口");
Thread thread5 = new Thread(myRunnable);
thread5.setName("5窗口");

//开始卖票
thread1.start();
thread2.start();
thread3.start();
thread4.start();
thread5.start();

运行的结果已经测试出来count数据在所有线程都可以共享了.
窗口名称：3窗口，还剩票数为：28
窗口名称：2窗口，还剩票数为：30
窗口名称：1窗口，还剩票数为：26
窗口名称：4窗口，还剩票数为：29
窗口名称：5窗口，还剩票数为：27
窗口名称：3窗口，还剩票数为：25
窗口名称：1窗口，还剩票数为：24
窗口名称：3窗口，还剩票数为：23
窗口名称：5窗口，还剩票数为：22
窗口名称：5窗口，还剩票数为：21
窗口名称：4窗口，还剩票数为：20
窗口名称：1窗口，还剩票数为：19
窗口名称：2窗口，还剩票数为：18
窗口名称：4窗口，还剩票数为：17
窗口名称：3窗口，还剩票数为：16
窗口名称：2窗口，还剩票数为：15
窗口名称：5窗口，还剩票数为：14
窗口名称：3窗口，还剩票数为：13
窗口名称：3窗口，还剩票数为：12
窗口名称：1窗口，还剩票数为：11
窗口名称：4窗口，还剩票数为：10
窗口名称：2窗口，还剩票数为：9
窗口名称：5窗口，还剩票数为：8
窗口名称：3窗口，还剩票数为：7
窗口名称：4窗口，还剩票数为：6
窗口名称：2窗口，还剩票数为：5 //这里重复
窗口名称：4窗口，还剩票数为：5 //这里重复
窗口名称：4窗口，还剩票数为：4
窗口名称：5窗口，还剩票数为：3
窗口名称：1窗口，还剩票数为：2
窗口名称：5窗口，还剩票数为：1
窗口名称：4窗口，还剩票数为：0
窗口名称：3窗口，还剩票数为：-1//负数
窗口名称：1窗口，还剩票数为：-2//负数
窗口名称：2窗口，还剩票数为：-3//负数
```

从结果中还是有重复的数据并且还有负数出现了，不可能卖票的还有负数出现这个就是线程不安全，现在该怎么办呢？这个时候就需要使用锁（`synchronized`）来处理了同步了。

## synchronized（锁）

`synchronized`表示锁，是一个关键字，只要synchronized的代码块都是同步的，其它线程要执行这段代码必须要先拿到锁（`synchronized`）之后才能执行这段代码的。
那么怎么锁才能让共享的数据打印出来没有重复并且没有负数呢？我们只需要改造一下 `MyRunnable` 类的`run`方法即可。
```java
@Override
public void run() {
   try {
       while (count > 0) {
           int time = (int) (Math.random()*10);
           Thread.sleep(time);
           synchronized (Object.class) {//加锁
               if (count > 0) {
                   System.out.println("窗口名称："+Thread.currentThread().getName() + "，还剩票数为：" + count--);
               }
           }
       }
   } catch (InterruptedException e) {
       e.printStackTrace();
   }
}
```
解释一下上面代码,为什么在 `synchronized` 之后还需要再次判断一下 `count` 是否大于0呢？因为所有的线程都只是停留在 `synchronized` 代码块外面，而不是在 `while` 外面，所以这里只是解决了相同数据出现，有可能到这里本来是目前有3个线程到达了 `synchronized` 这段代码块的时候，目前 `count` 是1，当前面的线程执行完毕了就会 `count--`，这个时候就变成0了，然后再进去一个线程又会关闭锁，然后打印的是`0`，完毕之后又 `count--`，这个时候就变成了`-1`，以此内推。

结果打印
```java
窗口名称：3窗口，还剩票数为：30
窗口名称：1窗口，还剩票数为：29
窗口名称：2窗口，还剩票数为：28
窗口名称：5窗口，还剩票数为：27
窗口名称：4窗口，还剩票数为：26
窗口名称：4窗口，还剩票数为：25
窗口名称：4窗口，还剩票数为：24
窗口名称：2窗口，还剩票数为：23
窗口名称：1窗口，还剩票数为：22
窗口名称：3窗口，还剩票数为：21
窗口名称：3窗口，还剩票数为：20
窗口名称：2窗口，还剩票数为：19
窗口名称：4窗口，还剩票数为：18
窗口名称：5窗口，还剩票数为：17
窗口名称：3窗口，还剩票数为：16
窗口名称：5窗口，还剩票数为：15
窗口名称：1窗口，还剩票数为：14
窗口名称：2窗口，还剩票数为：13
窗口名称：1窗口，还剩票数为：12
窗口名称：4窗口，还剩票数为：11
窗口名称：3窗口，还剩票数为：10
窗口名称：5窗口，还剩票数为：9
窗口名称：4窗口，还剩票数为：8
窗口名称：2窗口，还剩票数为：7
窗口名称：1窗口，还剩票数为：6
窗口名称：3窗口，还剩票数为：5
窗口名称：2窗口，还剩票数为：4
窗口名称：1窗口，还剩票数为：3
窗口名称：1窗口，还剩票数为：2
窗口名称：3窗口，还剩票数为：1
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")