## java异常和错误


异常是程序中的一些错误，但并不是所有的错误都是异常，也就是在的程序中，不存在所谓的“异常”，只存在错误并且错误有时候是可以避免的。



错误会导致我们的程序在运行期无法继续运行比如说，你的代码少了一个分号，那么会出现编译不通过。



如果一个无法处理接收到除数为零时的情况， `System.out.println(11/0)`，那么是因为你用0做了除数，就会抛出 `java.lang.ArithmeticException` 的异常，我无法处理，在这个时候就可以抛出一个异常，然后将它交给你处理（不然程序就中止了）。



到这里，大家大概知道了Java中的异常和问题的关系了，`java` 为了解决出现的问题时发出的一个通知，这个通知到达它该到达的地方，然后在这个地方得到解决，然后使程序能够继续运行。



异常发生的原因有很多，通常包含以下几大类：

用户输入了非法数据。

要打开的文件不存在。

网络通信时连接中断，或者 `JVM` 内存溢出。

这些异常有的是因为用户错误引起，有的是程序错误引起的，还有其它一些是因为物理错误引起的。


## Java异常处理


要理解 `Java` 异常处理是如何工作的，你需要掌握以下三种类型的异常：

检查性异常：最具代表的检查性异常是用户错误或问题引起的异常，这是程序员无法预见的。



例如要打开一个不存在文件时，一个异常就发生了，这些异常在编译时不能被简单地忽略。



运行时异常： 运行时异常是可能被程序员避免的异常。与检查性异常相反，运行时异常可以在编译时被忽略。



错误： 错误不是异常，而是脱离程序员控制的问题。它们在编译期也检查不到的。例如，当栈溢出时，一个错误就发生了。

## 异常的分类和类结构图

`Java` 标准裤内建了一些通用的异常，这些类以 `Throwable` 为顶层父类。

`Throwable` 又派生出 `Error` 类和 `Exception` 类。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/40-1.jpg)


## Error

`Error` 用来表示编译时和系统错误，一般我们不用关心，这里再重申一个概念，异常就是发生错误时被抛出的一个通知，所以 `Error` 是在编译时和系统错误时被抛出的异常。



## Exception

这是我们需要关心的异常，因为当这些异常抛出时，说明了我们程序出现了问题，我们需要处理这些问题。Exception又分为不检查异常和检查异常：

```java
public void method(String s) {
 if(s == null) throw new NullPointerException();
}
```
不检查异常

当出现使用了指向空的引用时，系统会自动帮你抛出，所以我们不用手动抛出所有 `RuntimeException` 类型（它的子类）的异常。



我们不需要去捕获它，所以这类异常被称为不检查异常。往往我们的程序的问题就是出现在这些不被检查的异常，如 `NullPointerException`，这个也属于运行时的异常，不需要捕获。



这个也是很多新手容易发生的问题，经常在一些群中发现新手贴出空指针异常的问题。

检查异常。



这一类异常抛出时，我们必须去捕获它，如 `SQLException`、`XMLStreamException` 等异常。至于说如何处理，根据具体的业务逻辑来编写处理的代码了。


## 异常捕获

语法：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/40-2.jpg)

`try { // 有可能发生的运行错误代码； }`

如果在方法内部抛出了异常或者在方法内部调用的其他方法抛出了异常，这方法将在抛出异常后终止，如果不希望方法就此终止，那么在方法内设置一个特殊的块来捕获异常，`所以称为try` 块，这也属于是异常监控区域。

`catch(异常类型 异常对象引用){ // 用于处理异常的代码 }`

一个 `try` 代码块后面跟随多个 `catch` 代码块的情况就叫多重捕获。



例如：

```java
try{ // 程序代码 }

catch(异常类型1 异常的变量名1){ // 程序代码 }

catch(异常类型2 异常的变量名2){ // 程序代码 }

catch(异常类型2 异常的变量名2){ // 程序代码 }
```



再次强调一下，不被检查的异常编译时不会强制让我们捕获，所以需要大家自己注意这些异常。



抛出的异常必须要得到处理。当发生异常时（监控区域抛出异常），`Java` 异常处理机制将负责搜寻 `catch` 中与异常类型相匹配的第一个处理程序，进入这个 `catch` 块。



也就是说只有匹配的catch字句才能执行，即便是下面还有匹配的类型为什么下面还会存在匹配的类型，因为类继承原因，下面还存在父类的异常），也不会执行，具有唯一匹配性。



Java的这种异常捕获、处理的模式，可以很好的将正常的代码和出现问题时处理的代码分开，而不是混在一起。



代码演示一个简单的下标越界的例子：

```java
public class Test1 {
  public static void main(String[] args) {
      try{
          int a[] = new int[2];
          System.out.println("获取数组第三个元素 :" + a[3]);
      }catch(ArrayIndexOutOfBoundsException e){
          System.out.println("异常信息  :" + e);
      }
  }
}
```

## Finally关键字

finally 关键字用来创建在 `try` 代码块后面执行的代码块。无论是否发生异常，`finally` 代码块中的代码总会被执行。



在 `finally` 代码块中，把资源恢复到它们的初始状态，如：已打开的文件或网络链接等，总言之，就是与外界“世界”的某个开关。
```java
try{
// 程序代码
}
catch(异常类型2 异常的变量名2){
// 程序代码
}

finally{
// 程序代码
}
```

代码演示
```java
public class Test1 {
  public static void main(String[] args) {
      int a[] = new int[2];
      try{
          System.out.println("获取数组第三个元素 :" + a[3]);
      }catch(ArrayIndexOutOfBoundsException e){
          System.out.println("异常信息  :" + e);
      }
      finally{
          a[0] = 6;//为数组的第一个元素赋值
          System.out.println("打印第一个元素 " +a[0]);
      }
  }
}
```
## throws/throw关键字


如果一个方法没有捕获一个检查性异常，那么该方法必须使用 `throws` 关键字来声明。`throws` 关键字放在方法与方法体中间的部分。

也可以使用 `throw` 关键字抛出一个异常，无论它是新实例化的还是刚捕获到的。

下面方法的声明抛出一个 `RemoteException` 异常：
```java
public void deposit(double amount) throws RemoteException//声明
{
  // 抛出异常
  throw new RemoteException();
}
```
一个方法可以声明抛出多个异常，多个异常之间用逗号隔开。

例如：
```java
public void withdraw(double amount) throws RemoteException, Exception//声明多个异常
 {
     // Method implementation
 }
```
## 声明自定义异常


在 `Java` 中你可以自定义异常。编写自己的异常类时需要记住下面的几点。

1．所有异常都必须是 `Throwable` 的子类。

2. 如果希望写一个检查性异常类，则需要继承 Exception 类。

3. 如果你想写一个运行时异常类，那么需要继承 RuntimeException 类。



可以像下面这样定义自己的异常类：

```java
class MyException extends Exception{ }
```

只继承 `Exception` 类来创建的异常类是检查性异常类。



代码演示:

以下实例是一个银行账户的模拟，通过银行卡的号码完成识别，可以进行存钱和取钱的操作。
```java
public class MyException extends Exception {
  /**
   * 此处的amount用来储存当出现异常（取出钱多于余额时）所缺乏的钱
   */
  private int amount;
  public MyException(int amount)
  {
      this.amount = amount;
  }
  public int getAmount()
  {
      return amount;
  }
}
```

为了展示如何使用我们自定义的异常类，

在下面的 `ExceptionTest` 类中包含一个 `withdraw()` 方法抛出一个 `MyException` 异常。
```java

public class ExceptionTest{
  //balance为余额，number为卡号
  private int balance;
  private int number;
  public ExceptionTest(int number){        
      this.number = number;   
  }
  public void deposit(int amount){   
       balance += amount;   
  } //方法：存钱
  public void withdraw(int amount) throws MyException{    //方法：取钱
      if(amount <= balance)
      {
          balance -= amount;
      }
      else
      {
          int needs = amount - balance;
          throw new MyException(needs);
      }
  }
  public int getBalance()    { //方法：返回余额
      return balance;
  }
  public int getNumber()    {//方法：返回卡号
      return number;
  }
}
```

下面的 BankDemo `程序示范了如何调用ExceptionTest` 类的 `deposit()` 和 `withdraw() `方法。
```java

public class BankDemo {
  public static void main(String[] args) {
      ExceptionTest c = new ExceptionTest(101);
      System.out.println("存入 500块...");
      c.deposit(500);
      try
      {
          System.out.println("\n取100...");
          c.withdraw(100);//还剩400
          System.out.println("\n取600...");
          c.withdraw(600);//引发异常
      }catch(MyException e)
      {
          System.out.println("异常信息提示，余额不足，差200块." + e.getAmount());
          System.out.println("======================");
          System.out.println(e);
      }
  }
}
```
## 常用异常类列表


`ArithmeticException：`

当出现异常的运算条件时，抛出此异常。例如，一个整数“除以零”时，抛出此类的一个实例。



`ArrayIndexOutOfBoundsException：`

用非法索引访问数组时抛出的异常。如果索引为负或大于等于数组大小，则该索引为非法索引。



`ArrayStoreException:`

试图将错误类型的对象存储到一个对象数组时抛出的异常。



例如，以下代码可生成一个 `ArrayStoreException`：
```java
Object x[] = new String[3];
x[0] = new Integer(0);
```



`ClassCastException`:

当试图将对象强制转换为不是实例的子类时，抛出该异常。

例如，以下代码将生成一个 `ClassCastException`：
```java
 Object x = new Integer(0);
System.out.println((String)x);
```

`ClassNotFoundException`:

当应用程序试图使用以下方法通过字符串名加载类时，抛出该异常：

`Class` 类中的 `forName` 方法。



`ClassLoader` 类中的 `findSystemClass` 方法。



`ClassLoader` 类中的 `loadClass` 方法。



但是没有找到具有指定名称的类的定义。



`CloneNotSupportedException`:

当调用 `Object` 类中的 `clone` 方法复制对象，但该对象的类无法实现 Cloneable 接口时，抛出该异常。



重写 `clone` 方法的应用程序也可能抛出此异常，指示不能或不应复制一个对象。



`IllegalAccessException`:

当应用程序试图反射性地创建一个实例（而不是数组）、设置或获取一个字段，或者调用一个方法，但当前正在执行的方法无法访问指定类、字段、方法或构造方法的定义时，抛出 `IllegalAccessException`。



`IllegalArgumentException`:

抛出的异常表明向方法传递了一个不合法或不正确的参数。



`IndexOutOfBoundsException`:

指示某排序索引（例如对数组、字符串或向量的排序）超出范围时抛出。

应用程序可以为这个类创建子类，以指示类似的异常。



`InstantiationException`:

当应用程序试图使用 `Class` 类中的 `newInstance` 方法创建一个类的实例，而指定的类对象无法被实例化时，抛出该异常。实例化失败有很多原因。



包括但不仅限于以下原因：

类对象表示一个抽象类、接口、数组类、基本类型、`void`

类没有非 `null` 构造方法



`NullPointerException`:

当应用程序试图在需要对象的地方使用 `null` 时，抛出该异常。这种情况包括：

调用 `null` 对象的实例方法。



访问或修改 `null` 对象的字段。

将 `null` 作为一个数组，获得其长度。

将 `null` 作为一个数组，访问或修改其时间片。

将 `null` 作为 `Throwable` 值抛出。



应用程序应该抛出该类的实例，指示其他对 `null` 对象的非法使用。



`NumberFormatException`:

当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出该异常。



`StringIndexOutOfBoundsException`:

此异常由 String 方法抛出，指示索引或者为负，或者超出字符串的大小。对诸如 `charAt` 的一些方法，当索引等于字符串的大小时，也会抛出该异常。



`TypeNotPresentException:`

当应用程序试图使用表示类型名称的字符串对类型进行访问，但无法找到带有指定名称的类型定义时，抛出该异常。



此异常与 `ClassNotFoundException` 不同，`ClassNotFoundException` 是一个经过检查的异常，而此异常是未经检查的。



注意，当访问未定义的类型变量时，以及当加载类型（例如，类、接口或注释类型）时，可以使用此异常。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")