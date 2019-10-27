### 1.对于以下，有int x = 5, y =6, z;
z = ++x + y++;
z的结果下面正确是是（）

A：10
B:8
C:11
D:12

答案：D
解析：
++x ，y++ 将 x = 5, y =6带入里面 得到  6 ，6 所以结果为12 

### 2.代码如下 
```java
int a=5;
int b=++a;
int c=a+(++b)
```
C的正确结果是（）

A：11
B：13
C：10
D：12

答案 B
解析：
 a=5;  b=++a; // 相当于a=a+1;b=a;  结果是a=6，b=6
int c=a+(++b)   //先计算 ++b =6+1=7;  c=6+7=13;
这个主要是考察 自增的运算， ++在变量前面就是先让变量+1 在运算；++在变量的后面就是 先运算。

### 3.下列运算符合法的是（）
A．&&     B．<>      C．if      D．:=
答案：A
解析：
Java 运算符可以分成以下
算术运算符，关系运算符，位运算符，逻辑运算符，赋值运算，其他运算符
经常提到的四大类为算数运算符、关系运算符、逻辑运算符、位运算符。
算数运算符(9)：+  -  *  /  %  ++  --
关系运算符(6)：==  !=  >  >=  <  <=
逻辑运算符(6)：&&  ||  !  ^  &  |
位运算符(7)：&  |  ~  ^  >>  <<  >>>
&&是在编程中使用的一种符号。是逻辑（条件）与。
java 中没有<> := 这种运算符，if else不算运算符

### 4.下列哪种说法是正确的（ ）
A． 实例方法可直接调用超类的实例方法
B． 实例方法可直接调用超类的类方法
C． 实例方法可直接调用其他类的实例方法
D． 实例方法可直接调用本类的类方法
答案 D
解析:
AC选项 如果方法是private 则不可以;
B选项类父类的类方法是属于父类本身的;
D选项虽然类方法可以类名.类方法来调用 但是new 该类 的实例方法去调也不不会报错  ;
记住一句话：实例方法只能直接调用本类的方法，其他类的方法得需要引用调用

### 5.  计算2 << 3，下面正确的是（）
A：6
B:54
C:8
D:16

答案D
解析：
2<<3； 表示 2向左移 3位：2 * 2^3(2的3次方) = 2 * 8；
因为一个数左移 n位，就相当于 2的 n次方，那么一个数乘以 8只要将其左移 3位即可，而为运算符的效率最高，所以 2乘以 8等于几的最有效方法是 2<<3。

### 6：提供Java存取数据库能力的包是（）
A．java.sql
B．java.awt
C．java.lang
D．java.swing

答案：A
解析
A存储在数据源（通常是一个关系数据库）中的数据的 API
B java.awt是一个软件包，包含用于创建用户界面和绘制图形图像的所有分类
C是提供利用 Java 编程语言进行程序设计的基础类
D Swing是一个用于开发Java应用程序用户界面的开发工具包

### 7选择下面代码的运行结果：（）。
```java
public class Test{
    public void method()
    {
        for(int i = ; i < 5; i++)
    {
            System.out.print(i);
    }
            System.out.print(i);
    }
}
```

A．122
B．123
C．编译错误
D．没有任何输出
答案：C
解析：
本试题属于  本身该试题就有严重的语法程序错误，并且没有主方法。


### 8.下面程序的运行结果是（）
String str1 = "banalan";
String str2 = "bana" + new String("lan");
System.err.println(str1 == str2);
A：true   
B：false
C：不确定
答案：B
解析
对于字符串，==比较引用对象的内存地址，equals比较的是字符串的内容
String str1 = "banalan"; //一个对象
String str2 = "bana" + new String("lan"); //new了一个对象，总的就是2个对象 System.err.println(str1 == str2); // ==是比较引用，两个对象的地址能一样么？只有重写equals比较内容（值）才能为TRUE

### 9.String能被继承吗？为什么？
不可以，因为String类有final修饰符，而final修饰的类是不能被继承的，实现细节不允许改变。平常我们定义的String str=”a”;其实和String str=new String(“a”)还是有差异的。

前者默认调用的是String.valueOf来返回String实例对象，至于调用哪个则取决于你的赋值，比如String num=1,调用的是 
```java
public static String valueOf(int i) { 
return Integer.toString(i); 
}
```

后者则是调用如下部分： 
```java
public String(String original) { 
this.value = original.value; 
this.hash = original.hash; 	
} 
```
最后我们的变量都存储在一个char数组中 
private final char value[];

### 10.String, Stringbuffer, StringBuilder 的区别?
String 字符串常量(final修饰，不可被继承)，String是常量，当创建之后即不能更改。(可以通过StringBuffer和StringBuilder创建String对象(常用的两个字符串操作类)。) 
StringBuffer 字符串变量（线程安全）,其也是final类别的，不允许被继承，其中的绝大多数方法都进行了同步处理，包括常用的Append方法也做了同步处理(synchronized修饰)。其自jdk1.0起就已经出现。其toString方法会进行对象缓存，以减少元素复制开销。 
```java
public synchronized String toString() { 
if (toStringCache == null) { 
toStringCache = Arrays.copyOfRange(value, 0, count); 
} 
return new String(toStringCache, true); 
}
```

StringBuilder 字符串变量（非线程安全）其自jdk1.5起开始出现。与StringBuffer一样都继承和实现了同样的接口和类，方法除了没使用synch修饰以外基本一致，不同之处在于最后toString的时候，会直接返回一个新对象。 
```java
public String toString() { 
// Create a copy, don’t share the array 
return new String(value, 0, count); 
}
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")