## Java 封装


在面向对象程式设计方法中，封装是指一种将抽象性函式接口的实现细节部份包装、隐藏起来的方法。也就是说我要看电视，只需要按一下开关和换台就可以了。



有必要了解电视机内部的结构吗？有必要碰碰显像管吗？



## 封装的优点


1. 良好的封装能够减少耦合。

2. 类内部的结构可以自由修改。

3. 可以对成员变量进行更精确的控制。

4. 隐藏信息，实现细节。



现在书写一个`Student`类包含1个成员变量 `name`，将它们封装起来，让外界改变`name`值和获取`name`值该怎么做呢？



案例：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/28-1.jpg)

其实就是讲`name`属性私有化使用`private`修饰变量，暴露出一个设置`name`属性的方法和一个获取`name`属性的方法。



并且采用 `this` 关键字是为了解决实例变量（`private String name`）和局部变量（`setName`(`String` `name`)中的`name`变量）之间发生的同名的冲突。



以上实例中`public`方法是外部类访问该类成员变量的入口。通常情况下，`getName`和`setName`方法被称为`getter`和`setter`方法。



因此，任何要访问类中私有成员变量的类都要通过这些`getter`和`setter`方法。





## 练习题


已知一个类 `Student` 代码如下： 
```java

class Student{
     String address;
  String zipCode;
}
```


要求：1、把 `Student` 的属性都作为私有，并提供`get/set`方法以及适当的构造方法。


2、为 `Student` 类添加一个 `getPostAddress` 方法，要求返回 `Student` 对象的地址和邮编

以下面为例子。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/28-2.jpg)


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")