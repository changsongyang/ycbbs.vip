## for循环

虽然所有循环结构都可以用 `while` 或者 `do...while`表示，但 `Java` 提供了另一种语句 —— `for` 循环，使一些循环结构变得更加简单。

for循环执行的次数是在执行前就确定的。语法格式如下：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-1.jpg)

关于 `for` 循环有以下几点说明：

最先执行初始化步骤。可以声明一种类型，但可初始化一个或多个循环控制变量，也可以是空语句。

然后，检测布尔表达式的值。如果为 `true`，循环体被执行。如果为`false`，循环终止，开始执行循环体后面的语句。

执行一次循环后，更新循环控制变量。

再次检测布尔表达式。循环执行上面的过程。

for循环的流程图
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-2.jpg)
【注意】for循环在执行条件测试后，先执行程序部分，再执行跟表达式。

代码演示：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-3.jpg)

## Java 增强 for 循环

`Java5` 引入了一种主要用于数组的增强型 for 循环。
`Java` 增强 `for` 循环语法格式如下:
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-4.jpg)
声明语句：声明新的局部变量，该变量的类型必须和数组元素的类型匹配。其作用域限定在循环语句块，其值与此时数组元素的值相等。

表达式：表达式是要访问的数组名，或者是返回值为数组（下一节讲解数组）的方法。

看图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-5.jpg)

## break 关键字

`break` 主要用在循环语句或者 `switch` 语句中，用来跳出整个语句块。

`break` 跳出最里层的循环，并且继续执行该循环下面的语句。

语法
`break` 的用法很简单，就是循环结构中的一条语句：`break`;
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-6.jpg)

## continue 关键字

`continue` 适用于任何循环控制结构中。作用是让程序立刻跳转到下一次循环的迭代。

在 `for` 循环中，`continue` 语句使程序立即跳转到更新语句。

在 `while` 或者 `do…while` 循环中，程序立即跳转到布尔表达式的判断语句。 

语法。

`continue` 就是循环体中一条简单的语句：`continue`;
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/16-7.jpg)


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")