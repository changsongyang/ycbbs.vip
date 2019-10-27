## 数据类型包装类


`Java` 语言是一个面向对象的语言，但是 `Java` 中的基本数据类型却是不面向对象的，这在实际使用时存在很多的不便，为了解决这个不足，在设计类时为每个基本数据类型设计了一个对应的类进行代表，这样八个和基本数据类型对应的类统称为包装类(`Wrapper` `Class`)，有些地方也翻译为外覆类或数据类型类。



包装类均位于 `java.lang` 包，包装类和基本数据类型的对应关系如下表所示：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/35-1.jpg)

## 包装类的用途

对于包装类说，这些类的用途主要包含两种：
  a、作为和基本数据类型对应的类类型存在，方便涉及到对象的操作。
  b、包含每种基本数据类型的相关属性如最大值、最小值等，以及相关的操作方法（这些操作方法的作用是在基本类型数据、包装类对象、字符串之间提供转化！）。

所有的包装类(`Wrapper` `Class`)都有共同的方法。


int和Integer类之间的转换

在实际转换时，使用Integer类的构造方法和Integer类内部的intValue方法实现这些类型之间的相互转换，实现的代码如下：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/35-2.jpg)

## Integer类内部的常用方法


在 `Integer` 类内部包含了一些和int操作有关的方法，下面介绍一些比较常用的方法： 

1.`parseInt`方法

 ```java
public static int parseInt(String s)
```

该方法的作用是将数字字符串转换为`int`数值。在以后的界面编程中，将字符串转换为对应的`int`数字是一种比较常见的操作。



使用示例如下：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/35-6.jpg)

则`int`变量n的值是`123`，该方法实际上实现了字符串和int之间的转换，如果字符串都包含的不是都是数字字符，则程序执行将出现异常。(说明：异常的概念将在后期进行讲述)



另外一个`parseInt`方法：

  ```java
 public static int parseInt(String s, int radix)
```

则实现将字符串按照参数radix指定的进制转换为int。



使用示例如下：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/35-7.jpg)

这样可以实现更灵活的转换。

1.`toString`方法
```java
public static String toString(int i) 该方法的作用是将int类型转换为对应的String类型。使用示例代码如下： 
```
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/35-8.jpg)
则字符串`s`的值是`”1000”`。


自动拆箱和装箱


自动装箱的过程：每当需要一种类型的对象时，这种基本类型就自动地封装到与它相同类型的包装中。


自动拆箱的过程：每当需要一个值时，被装箱对象中的值就被自动地提取出来，没必要再去调用`intValue()`和`doubleValue()`方法。



自动装箱与拆箱的功能事实上是编译器来帮您的忙，编译器在编译时期依您所编写的语法，决定是否进行装箱或拆箱动作。



例如：

`Integer i = 100;`

相当于编译器自动为您作以下的语法编译：

`Integer i = new Integer(100);`



所以自动装箱与拆箱的功能是所谓的“编译器蜜糖”(`Compiler` `Sugar`)，虽然使用这个功能很方便，但在程序运行阶段您得了解Java的语义。



例如下面的程序是可以通过编译的：

```java
Integer i = null;

int j = i;
```

这样的语法在编译时期是合法的，但是在运行时期会有错误。

因为这种写法相当于：
```java
Integer i = null;

int j = i.intValue();
```

null表示i没有参考至任何的对象实体，它可以合法地指定给对象参考名称。



由于实际上i并没有参考至任何的对象，所以也就不可能操作`intValue()`方法，这样上面的写法在运行时会出现`NullPointerException`错误。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")