## Java 多态


在 `java` 程序设计中，对象变量是多态的，多态是同一个行为具有多个不同表现形式或形态的能力。



也是 `OOP` 中的一个重要特性，主要是用来实现动态联编的，换句话说，就是程序的最终状态只有在执行过程中才被决定而非在编译期间就决定了。



这对于大型系统来说能提高系统的灵活性和扩展性。



多态性是对象多种表现形式的体现。就是同一个接口，使用不同的实例而执行不同操作，如图所示：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-1.jpg)

## 多态的优点
1. 消除类型之间的耦合关系
2. 可替换性

3. 可扩充性

4. 接口性

5. 灵活性

6. 简化性


## 多态存在的三个必要条件


继承

重写

父类引用指向子类对象

比如：`Parent p = new Child();`



## 对象的转型


为什么需要强制转换类型？

 引用变量只能调用它编译类型的方法，不能调用它运行类型的方法。这时，我们就需要进行类型的强制转换！



一个父类的引用类型变量可以“指向”其子类的对象。



一个父类的引用不可以访问其子类对象新增加的成员（属性和方法）。



可以使用引用变量 `instanceof` 类名来判断该引用型变量所“指向”的对象是否属于该类或该类的子类。



子类的对象可以当作基类的对象来使用称作向上转型（`upcasting`），反之称为向下转型（`downcasting`）



当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误；如果有，再去调用子类的同名方法。



多态的好处：可以使程序有良好的扩展，并可以对所有类的对象进行通用处理。



以下是一个多态实例的演示，详细说明请看注释：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-2.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-3.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-4.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-5.jpg)

## 虚方法


我们将介绍在 `Java` 中，当设计类时，被重写的方法的行为怎样影响多态性。



我们已经讨论了方法的重写，也就是子类能够重写父类的方法。



当子类对象调用重写的方法时，调用的是子类的方法，而不是父类中被重写的方法。



要想调用父类中被重写的方法，则必须使用关键字 `super`。



如图看看下个代码演示:



父类
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-6.jpg)
子类
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-7.jpg)
测试类
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-8.jpg)

例子解析：

实例中，实例化了两个 `Salary` 对象：一个使用 `Salary` 引用 `s`，另一个使用 `Employee` 引用 `e`。当调用 `s.mailCheck()` 时，编译器在编译时会在 `Salary` 类中找到 `mailCheck()`，执行过程 `JVM` 就调用 `Salary` 类的 `mailCheck()`。

 

因为 `e` 是 `Employee` 的引用，所以调用 `e` 的 `mailCheck() `方法时，编译器会去 `Employee` 类查找 `mailCheck()` 方法 。



在编译的时候，编译器使用 Employee 类中的 `mailCheck()` 方法验证该语句， 但是在运行的时候，Java虚拟机(`JVM`)调用的是 `Salary` 类中的 `mailCheck()` 方法，然而`salary`类中的`mailCheck`方法又使用了`super.mailCheck()`，这里又调用了父类的`mailCheck`方法。



以上整个过程被称为虚拟方法调用，该方法被称为虚拟方法。



Java中所有的方法都能以这种方式表现，因此，重写的方法能在运行时调用，不管编译的时候源代码中引用变量是什么数据类型。



## 多态的实现方式


方式一：重写：

这个内容已经在上一章节详细讲过，就不再阐述，详细可访问：`Java` 重写(`Override`)与重载(`Overload`)。

 

方式二：接口 （后期将会讲到）

1. 生活中的接口最具代表性的就是插座，例如一个三接头的插头都能接在三孔插座中，因为这个是每个国家都有各自规定的接口规则，有可能到国外就不行，那是因为国外自己定义的接口类型。



2. java中的接口类似于生活中的接口，就是一些方法特征的集合，但没有方法的实现。具体可以看 java接口 这一章节的内容。

 

方式三：抽象类和抽象方法

详情请看 下个章节。



## 练习题:


1. 设计一个动物类，有个跑的方法sout（“四只脚落地跑”）

2. 设计2个子类，老虎和袋鼠，都重写了跑的方法，在老虎类跑的方法中调用父类的跑的方法。袋鼠跑的方法sout（“一蹦一蹦的跑”）

3. 在测试类中使用动物类实例化一个老虎类调用跑的方法。

在测试类中使用动物类实例化一个袋鼠类调用跑的方法。

![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/29-9.jpg)

答案在公众号回复“多态”

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")