### 1.以下哪些定义的变量是正确的（）
A int 1abc;
B char abc1;
C long #abc;
D boolean $abc;
E：String_123456asdfghjklqwertyuiopzmnbvasdf12345;
答案 B D
解析：
因为java中变量名必须是一个以字母开头并由字母或者数字构成的序列。
字母包括 A-Z,a-z,_,$或在某种语言中表示的字母的任何Unicode字符。变量名中所有的字符都是有意义的，并且大小写敏感，变量名基本上没有限制.


### 2.代码如下
```java
public class Test {
    public final String a;
        public Test() {
            a = "10";
        }
    public void setA1(){
               a = "20";
        }
     public static void main(String[] args) {
            Test t = new Test();
            t.setA1();
            System.out.println(t.a);
        }
}
```
t.a的值为（）
A “10”
B “20”
C “”
D  以上都不对，并且编译错误
答案 D
解析 ：
因为a被final（只能在初始化时赋值一次，一旦被赋值后，就不能再更改了，习惯上常量名使用全大写）修饰了，所以在setA1中再次赋值了，直接导致编译错误。

### 3.代码如下  
```java
double a = 15.0;
     int b=2;
      system.out.println(a+b);
```
打印出来的数值类型为（）
A int
B float
C String
D double
答案 D
解析：
如果两个操作数中有一个是double类型，另一个操作数就会自动转换为double类型。

### 4.以private修饰的元素的作用域的是（）
A. 当前类
B. 同一个package 内
C. 子孙类中
D. 其他的package 内
答案 A
解析：
共有四个访问修饰符public protected friendly private
public 的作用域有当前类，同一个package，子孙类和其他的package
protected的作用域有当前类，同一个package，和子孙类
friendly的作用域有当前类和同一个package
private 的作用域只有当前类


### 5.String s =new String ("xyz");这句一共创建了几个String Object？ 
A. 1
B. 2
C. 3
D. 4
答案:B
解析：
一共创建了两个对象，一个是"xyz"，为缓冲区对象。另一个是new出来的String 对象。这两个对象的值相同，但不是同一个对象。


### 6.String 是最基本的数据类型吗？()
A：是
B：不是
答案：A
解析
A存储在数据源（通常是一个关系数据库）中的数据的 API
B java.awt是一个软件包，包含用于创建用户界面和绘制图形图像的所有分类
C是提供利用 Java 编程语言进行程序设计的基础类
D Swing是一个用于开发Java应用程序用户界面的开发工具包

### 7.选择下面代码的运行结果：（）。
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
public static String valueOf(int i) { 
return Integer.toString(i); 
}

后者则是调用如下部分： 
public String(String original) { 
this.value = original.value; 
this.hash = original.hash; 
} 
最后我们的变量都存储在一个char数组中 
private final char value[];

### 10.Java I/O中字符流和字节流的区别 ?
字节流是传递0~255的整数而Java中有一些(如unicode代码)不能使用字符流，是特殊的字节流。


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")