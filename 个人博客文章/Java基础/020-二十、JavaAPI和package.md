## 引言
`API`（`Application Programming Interface`,应用程序编程接口）是一些预先定义的函数，目的是提供应用程序与开发人员基于某软件或硬件得以访问一组例程的能力，而又无需访问源码，或理解内部工作机制的细节。

如图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-1.jpg)

## Java 包(package)

为了更好地组织类，`Java` 提供了包机制，用于区别类名的命名空间。

包的作用

1、把功能相似或相关的类或接口组织在同一个包中，方便类的查找和使用。

2、如同文件夹一样，包也采用了树形目录的存储方式。同一个包中的类名字是不同的，不同的包中的类的名字是可以相同的，当同时调用两个不同包中相同类名的类时，应该加上包名加以区别。因此，包可以避免名字冲突。

3、包也限定了访问权限，拥有包访问权限的类才能访问某个包中的类。

`Java` 使用包（`package`）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（`class`）、接口、枚举（`enumerations`）和注释（`annotation`）等。

包语句的语法格式为：
```java

package java.util;
public class Something{ ... }
```

那么它的路径应该是`java/util/Something.java` 这样保存的。 `package`(包) 的作用是把不同的 `java` 程序分类保存，更方便的被其他 `java` 程序调用。

我们来演示一下创建包,在idea上创建一个com.my.utils包的流程如图:
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-2.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-3.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-4.jpg)

我们在com.my.utils包下创建一个StudentUtils类看看
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-5.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-6.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-7.jpg)

这样就创建好了！！！

PS：注意包名里面不能单独写package作为包的名称。

## JDK中的主要包

1.`java.lang`－包含一些`Java`语言的核心类，如`String、Math、Integer、System`和`Thread`，提供常用功能。

2.`java.awt`－包含了构成抽象窗口工具集（`abstract` `window` `toolkits`）的多个类，这些类被用来构建和管理应用程序的图形用户界面(`GUI`)。

3.`java.net`－包含执行与网络相关的操作的类。

4.`Java.io`－包含能提供多种输入/输出功能的类。

5.`java.util`－包含一些实用工具类，如定义系统特性、使用与日期日历相关的函数。


## import 关键字


为了能够使用某一个包的成员，我们需要在 `Java` 程序中明确导入该包。使用 `"import"` 语句可完成此功能。

在 `java` 源文件中 `import` 语句应位于 `package` 语句之后，所有类的定义之前，可以没有，也可以有多条，其语法格式为：
```java
import package1[.package2…].(classname|*);
```
如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略。

案例

下面的 `payroll` 包已经包含了 `Employee` 类，接下来向 `payroll` 包中添加一个 `Boss` 类。`Boss` 类引用 `Employee` 类的时候可以不用使用 `payroll` 前缀，`Boss`类的实例如下。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-8.jpg)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-9.jpg)
大家仔细看看2个类（在面向对象章节详细讲解）的package名称一致，那么在同级包名下调用Employee类是不需要引用包名的。

如果 `Boss` 类不在 `payroll` 包中又会怎样？`Boss` 类必须使用下面几种方法之一来引用其他包中的类。

使用类全名描述，例如： `payroll.employee.Employee`

那么可以使用 `import` 关键字引入，使用通配符 `"*":import payroll.*;`也可以直接使用 `import` 关键字引入 `Employee` 类:`import payroll.employee.Employee`;

如图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/20-10.jpg)

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")