## 顺序结构(if)

语法：
```java
if(布尔表达式) {
//如果布尔表达式为true将执行的语句
}
```
如图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/14-1.jpg)
还可以连续写if语句
```java
if(布尔表达式) {
   ...
}
if(布尔表达式) {
   ...
}
if(布尔表达式) {
   ...
}
```
## if...else语句

if 语句后面可以跟 `else` 语句，当 `if` 语句的布尔表达式值为 `false` 时，`else` 语句块会被执行。
```java
if(布尔表达式){
//如果布尔表达式的值为true
}else{
//如果布尔表达式的值为false
}
```
> Ps：这样测款的好处除了可以提高流量款的转化外，其实也是类似绑定套餐消费模式，可以让新品亏的钱从利润款上补回来，是一种比较良性的测款。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/14-2.jpg)

`if...else if...else` 语句
`if` 语句后面可以跟 `elseif…else` 语句，这种语句可以检测到多种可能的情况。

使用 `if，else if，else` 语句的时候，需要注意下面几点：

`if` 语句至少有 `1` 个 `else` 语句，`else` 语句在所有的 `elseif` 语句之后。

`if` 语句可以有若干个 `elseif` 语句，它们必须在 `else` 语句之前。

一旦其中一个` else if` 语句检测为 `true`，其他的 `else if` 以及 `else` 语句都将跳过执行。

直接看图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/14-3.jpg)

## switch 语句

`switch` 语句判断一个变量与一系列值中某个值是否相等，每个值称为一个分支。
语法
switch 语法格式如下：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/14-4.jpg)

`switch` 语句有如下规则：

`switch` 语句中的变量类型可以是：` byte、short、int` 或者 `char`。从 `Java` `SE` `7` 开始，`switch` 支持字符串类型了，同时 `case` 标签必须为字符串常量或字面量。

`switch` 语句可以拥有多个 `case` 语句。每个 `case` 后面跟一个要比较的值和冒号。

`case` 语句中的值的数据类型必须与变量的数据类型相同，而且只能是常量或者字面常量。

当变量的值与 `case` 语句的值相等时，那么 `case` 语句之后的语句开始执行，直到 `break` 语句出现才会跳出 `switch` 语句。

当遇到 `break` 语句时，`switch` 语句终止。程序跳转到 `switch` 语句后面的语句执行。`case` 语句不必须要包含 `break` 语句。如果没有 `break` 语句出现，程序会继续执行下一条 `case` 语句，直到出现 `break` 语句。

`switch` 语句可以包含一个 `default` 分支，该分支必须是 `switch` 语句的最后一个分支。`default` 在没有 `case` 语句的值和变量值相等的时候执行。`default` 分支不需要 `break` 语句。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/14-5.jpg)

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
