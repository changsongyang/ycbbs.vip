## StringBuffer和 StringBuilder 类



当对字符串进行修改的时候，需要使用 `StringBuffer` 和 `StringBuilder` 类以及 `String` 类不同的是，`StringBuffer` 和 `StringBuilder` 类的对象能够被多次的修改，并且不产生新的未使用对象。



`StringBuilder` 类在 `Java 5` 中被提出，它和 `StringBuffer` 之间的最大不同在于 `StringBuilder` 的方法不是线程安全的（不能同步访问）。



由于 `StringBuilder` 相较于 `StringBuffer` 有速度优势，所以多数情况下建议使用 `StringBuilder` 类。



然而在应用程序要求线程安全的情况下，则必须使用 `StringBuffer` 类。

2个类的用法基本是一模一样的下面就接受其中一个吧。

## StringBuffer


代码示例：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/36-1.jpg)

下面介绍StringBuffer的几个常用方法。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/36-2.jpg)

示例
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/36-3.jpg)
下面的列表里的方法和 String 类的方法类似：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/36-4.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/36-5.jpg)
注意：

`Java` 中 `StringBuffer` 和 `String` 是有一定的区别的，首先，`String` 是被 `final` 修饰的，他的长度是不可变的，就算调用 `String` 的 `concat` 方法，那也是把字符串拼接起来并重新创建一个对象，把拼接后的 `String` 的值赋给新创建的对象。

而 StringBuffer 的长度是可变的，调用StringBuffer 的 append 方法，来改变 StringBuffer 的长度，并且，相比较于 StringBuffer，String 一旦发生长度变化，是非常耗费内存的！

## 面试题的回答


`StringBuilder` 与 `StringBuffer` 的区别，`StringBuilder` 与 `String` 的区别。

1）`StringBuilder` 效率高，线程不安全，`StringBuffer` 效率低，线程安全。



2）`String` 是不可变字符串，`StringBuilder` 是可变字符串。为什么有这样的差异，可以深入源码去解析，比如 `String` 类内的 `priver` `final`  `char`  `value[]` 等方法的原因。



3）如果是简单的声明一个字符串没有后续过多的操作，使用 `String`,`StringBuilder` 均可，若后续对字符穿做频繁的添加，删除操作,或者是在循环当中动态的改变字符穿的长度应该用 `StringBuilder`。使用 `String` 会产生多余的字符串，占用内存空间。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")