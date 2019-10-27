## 简述：

顺序结构的程序语句只能被执行一次。如果您想要同样的操作执行多次,，就需要使用循环结构。

`Java`中有三种主要的循环结构：
`while` 循环
`do…while` 循环
`for` 循环
在`Java5`中引入了一种主要用于数组的增强型for循环。

## While循环

while是最基本的循环，它的结构流程图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/15-1.png)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/15-2.jpg)

在循环刚开始时，会计算一次“布尔表达式”的值，若条件为真，执行循环体。而对于后来每一次额外的循环，都会在开始前重新计算一次。

如图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/15-3.jpg)

语句中应有使循环趋向于结束的语句，否则会出现无限循环–––"死"循环。

`do…while` 循环

对于 `while` 语句而言，如果不满足条件，则不能进入循环。但有时候我们需要即使不满足条件，也至少执行一次。

`do…while` 循环和 `while` 循环相似，不同的是，`do…while` 循环至少会执行一次。

看流程图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/15-4.jpg)
语句事例：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/15-5.jpg)
看图演示：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/15-6.jpg)
> PS `While`和`do-While`的区别：

`while`先判断后执行。`do...while`是先执行后判断！

`do...while`总是保证循环体会被至少执行一次！这是它们的主要差别。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击此处[扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "扫下面二维码关注微信公众号")