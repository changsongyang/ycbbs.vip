### 1.Math.round(11.5)等于多少? Math.round(-11.5)等于多少？
A.11—— 11
B.11 ——11
C.12——12
D.12 ——11
答案： D
解析：
Math.round(11.5)==12;Math.round(-11.5)==-11;round方法返回与参数最接近的长整数，参数加1/2后求其floor


### 2.代码如下
```java
1.Float pi = new Float(3.14f);
2. if (pi > 3) {
3. System.out.print("pi is bigger than 3. ");
4. }
5. else {
6. System.out.print("pi is not bigger than 3. ");
7. }
8. finally {
9. System.out.println("Have a nice day.");
10. }
```
输出结果是（）
 A. 编译失败。
B. pi is bigger than 3.
C. 在运行时发生n个异常。
D. pi is bigger than 3. Have a nice day.
E. pi is not bigger than 3. Have a nice day.
答案: A
解析：
finally不是用在这种地方，仅仅在try/catch/finally内使用。


### 3.数组有没有length()这个方法? String有没有length()这个方法
A. 有 没有
B. 有 有
C. 没有 没有
D. 没有 有
答： D
解析：
数组没有length()这个方法，有length的属性。String有有length()这个方法


### 4.List, Set, Map哪一个不是继承自Collection接口？
A. List
B. Set
C. Map
D. 都继承自Collection接口
答： C
解析：
List，Set继承自Collection接口，Map不是。


### 5.是否可以从一个static方法内部发出对非static方法的调用？
A. 是
B. 否
答： B
解析：
不可以,如果其中包含对象的method()；不能保证对象初始化. 


### 6.下列哪个叙述是正确的()
A. 创建File对象可能发生异常。 
B. BufferedRead流可以指向FileInputStream流 
C. BufferedWrite流可以指向FileWrite流 
D.RadomAccessFile流一旦指向文件，就会刷新该文件
答案：C 
解析：
创建File对象不会发生异常，创建新文件的时候会发生IOException异常。 
BufferedRead流只能指向FileRead流，它们都是以字符读取数据的。 
RadomAccessFile流指向文件时，不刷新文件。


### 7.为了向文件hello.txt尾加数据，下列哪个是正确创建指向hello.txt的流？ 
```java
A. try{ OutputStream out = new FileOutputStream(“hello.txt”); 
} 
catch(IOException e){} 

B. try { OutputStream out = new FileOutputStream(“hello.txt”,true); 
} 
catch(IOException e){} 

C.try{ OutputStream out = new FileOutputStream(“hello.txt”,false); 
} 
catch(IOException e){} 

D.try{ OutputStream out = new OutputStream(“hello.txt”,true); 
} 
catch(IOException e){}
```
答案：B 
FileOutputStream类是文件字节输出流，它是OutputStream类的子类，所以FileOutputStream的实例方法都是从OutputStream类继承来的，那么在这道题中需要使用上转型对象才能调用重写父类的方法，排除D。ABC在于FileOutpurStream的构造方法的不同，题目要求向文件尾加数据，A选项是具有刷新功能的构造方法，也就是会覆盖掉原来文件的数据，B选项参数为true表示不会刷新所指向的文件，而是从文件的末尾开始向文件写入数据，取值为false，输出流将刷新所指向的文件。


### 8.下面关于Java类的构造方法的说法中正确的是：（ ）
A. 一个类至少有一个构造方法
B. 构造方法的返回值类型必须是void
C. 构造方法的可见性修饰符必须是public
D. 构造方法必须显式的定义
正确答案: A
解析：
构造方法是一种特殊的方法：它必须具备和所在类相同的名字；没有返回值类型，甚至连void也没有；构造方法是在创建一个对象使用new操作符时调用的，作用是初始化对象。
造方法可以默认提供而不显式定义。
简单的说就是，每个类至少有一个构造方法，即使没有写出来，即可以不用显示定义。她没有返回值，连void都没有，但是也可以使用private修饰构造方法。


### 9.为什么构造方法里 this 或者 super 函数调用必须放在第一行且无法共存？
答：super 方法在构造函数的第一行原因是子类有可能访问了父类对象，比如在构造函数中使用父类对象的成员函数和变量，在成员初始化使用了父类，在代码块中使用了父类等，所以放在第一行可以保证在子类可以访问父类对象之前完成对父类对象的初始化。

this 方法在构造函数的第一行原因是为保证父类对象初始化的唯一性，因为假设类 B 是类 A 的子类，如果 this 方法可以在构造函数的任意行使用则首先程序运行到构造函数 B() 的第一行发现没有调用 this() 和 super()，就自动在第一行补齐了 super() 方法（这是 java 默认的机制），以此完成了对父类对象的初始化，然后返回子类的构造函数继续执行，当运行到构造函数 B() 的 this(参数) 调用行时, 调用 B 类对象的另一个构造方法 B(参数)，在 B(参数) 中还会对父类对象再次初始化，这就造成了对资源的浪费，也有可能造成某些意想不到的结果，所以 this 方法不能出现在构造方法除第一行以外的其他行。

这也就解释了为啥在构造方法里面 this 与 super 方法不能同时存在的原因。

### 10.Java 构造方法能否被重写和重载？
答：重写是子类方法重写父类的方法，重写的方法名不变，而类的构造方法名必须与类名一致，假设父类的构造方法如果能够被子类重写则子类类名必须与父类类名一致才行，所以 Java 的构造方法是不能被重写的。而重载是针对同一个的，所以构造方法可以被重载。


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")