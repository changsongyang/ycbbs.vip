### 1.一个以”.java”为后缀的源文件，正确的是（）
A. 只能包含一个类，类名必须与文件名相同
B. 只能包含与文件名相同的类以及其中的内部类
C. 只能有一个与文件名相同的类，可以包含其他类
D. 可以包含任意类
答案: C
解析：一个.java文件中，只能存在一个类是用public修饰的，并且这个类必须与类名一致，文件中其他的类不能是public权限的，但可以有很多个类。

### 2.Which is not Java primitive types?
A. short
B. Boolean
C. byte
D. float
答案: B 
解析：java规定类名首字母必须大写，这里可以直观的看出来Boolean是一个引用类型，不是基本数据类型。
java中的基本数据类型都对应一个引用类型，如Float是float的引用类型，Integer是int的引用类型。

### 3.多个线程可同时操作一个数据，为了保证该数据的准确性，可将操作该数据的部分改为 ()
A:类型定义机制;
B:数据封装机制;
C:类型定义机制和数据封装机制
D:上述都不对;

答案: A
解析：
对该数据加锁，放在同步代码块中
synchronize(){
}
### 4.下面有关List接口、Set接口和Map接口的描述，错误的是？
A. 他们都继承自Collection接口
B. List是有序的Collection，使用此接口能够精确的控制每个元素插入的位置
C. Set是一种不包含重复的元素的Collection
D. Map提供key到value的映射。一个Map中不能包含相同的key，每个key只能映射一个value

答案: A
解析：
1、List接口和Set接口都继承自Collection接口，Collection接口继承Iterable接口（Iterable有一个Iterator方法），即可迭代的；Collection只能存储引用类型，并且是单个存储；
2、List接口存储元素特点：有序（存进去什么顺序取出来还什么顺序），可重复；Set接口存储元素特点：无序，不可重复
3、实现List接口主要的类包括ArrayList，LinkedList，Vector；实现Set的主要类包括：hashSet，另外还有一个TreeSet接口继承它（自动排序）
4、Map接口以键值对方式存储元素，键无序不可重复，Map和Collection没有任何关系

### 5.在基本JAVA类型中，如果不明确指定，整数型的默认是什么类型？带小数的默认是什么类型？
A. int float
B. int double
C. long float
D. long double
答案: B 
解析：
Java中，如果对整数不指定类型，默认时int类型，对小数不指定类型，默认是double类型
如果要指定长整型，最好写为long a = 100000000L,如果要指定为单精度最好写为float a= 12.34F


### 6.下面有关webservice的描述，错误的是？
A. Webservice是跨平台，跨语言的远程调用技术
B. Webservice通信机制实质就是json数据交换
C. Webservice采用了soap协议（简单对象协议）进行通信
D. WSDL是用于描述 Web Services 以及如何对它们进行访问
答案: B
解析：Web service顾名思义是基于web的服务，它是一种跨平台，跨语言的服务。
我们可以这样理解它，比如说我们可以调用互联网上查询天气信息的web服务，把它嵌入到我们的B/S程序中，当用户从我们的网点看到天气信息时，会认为我们为他提供很多的服务，但其实我们什么也没做，只是简单的调用了一下服务器上的一端代码而已。Web service 可以将你的服务发布到互联网上让别人去调用，也可以调用别人发布的web service，和使用自己的代码一样。
它是采用XML传输格式化的数据，它的通信协议是SOAP(简单对象访问协议).


### 7.从下面四段（A，B，C，D）代码中选择出正确的代码段（）
```java
A．abstract class Name {
    private String name;
public abstract boolean isStupidName(String name) {}
                                        }
B．public class Something {
void doSomething () {
private String s = ?”;
int l = s.length();
}
}
C．public class Something {
public static void main(String[] args) {
Other o = new Other();
new Something().addOne(o);
}
public void addOne(final Other o) {
o.i++;
}
}
class Other {
    public int i;
}
D．public class Something {
public int addOne(final int x) {
return ++x; }
}
```
答案：C
解析：
A..抽象方法不能有方法体
B．方法中定义的是局部变量，不能用类成员变量修饰符private
D．final修饰为常量，常量的值不能被改变


### 8.选择下面代码的运行结果：（）。
```java
public class Test{
public void method()
{
for(int i = ; i < 3; i++)
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
解析：i变量的作用范围是整个for循环

### 9.Java 创建对象的方式有哪几种？
有4种显式地创建对象的方式：
1.用new语句创建对象，这是最常用的创建对象的方式。
2.运用反射手段，调用Java.lang.Class或者java.lang.reflect.Constructor类的newInstance()实例方法。
3.调用对象的clone()方法。
4.运用反序列化手段，调用java.io.ObjectInputStream对象的readObject()方法.


### 10.问：抽象类（abstract class）和接口（interface）有什么区别？
解答：由于本试题的答案详解篇幅较长比较详细，大家可以通过留言，小编会将答案发给你们个人。


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")