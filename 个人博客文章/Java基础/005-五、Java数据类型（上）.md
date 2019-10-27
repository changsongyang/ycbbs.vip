## Java是一种强类型语言，每个变量都必须声明其类型。

1) `Java`的数据类型分为两大类：基本类型和引用类型

2) 在客户碰到困难的时候施以援手，雪中送炭比锦上添花更让人感动；
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-1.png)

## 整数型（byte,short,int,long）
大家来看一个图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-2.png)

1) 举例一个byte其它以此类推：
`byte` 是一个字节保存的,有`8`个位,即`8`个`0、1`。
`8` 位的第一个位是符号位， 也就是说 `0000` `0001` 代表的是数字1
`1000` `0000` 代表的就是`-1`，所以正数最大位 `0111` `1111`，也就是数字127 
负数最大为 `1111` `1111`，也就是数字`-128`，所以 `Java` 中的一个 `byte` 是 `1` 个字节，其范围是` -128~127`
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-3.png)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-4.png)
下面看看错误的，大家比对一下。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-5.png)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-6.png)
2) Java 语言整型常数的三种表示形式：
    十进制整数，如：`100`, `-500`, `0`。
    八进制整数，要求以 `0` 开头，如：`011`。
十六进制数，要求 `0x` 或 `0X` 开头，如：`0x15` 。
如图：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-7.png)
`Java`语言的整型常数默认为`int`型，声明long型常量可以后加‘ `l` ’或‘` L` ’ ，如：
   `long a = 10000000; ` //不出错，在`Int`表示的范围内(`21`亿内)。
  ` long b = 10000000000;`//必须要加10000000000L，要不然就会报错错误: 过大的整数: `10000000000`
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-8.jpg)

## 浮点型

![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-9.png)
`float`类型又被称作单精度类型，尾数可以精确到 `7` 位有效数字，在很多情况下，`float` 类型的精度很难满足需求。

`double` 表示这种类型的数值精度是 `float` 类型的两倍，又被称作双精度，绝大部分应用程序都采用 `double` 类型。

`Java` 浮点类型常量有两种表示形式

十进制数形式，例如:

`3.14 `      `314.0 `    ` 0.314`

科学记数法形式，如

`314e2`      `314E2`      `314E-2`

```java
   double f = 314e2;  //314*10^2-->31400.0

   double f2 = 314e-2; //314*10^(-2)-->3.14
```

`Float`类型的数值有一个后缀`F/f `，没有后缀`F/f`的浮点数值默认为`double`类型。也可以在浮点数值后添加后缀`D/d`, 以明确其为`double`类型：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/5-10.jpg)
浮点类型`float, double`的数据不适合在计算或者比较中使用，因为会丢失精度。
        如果需要精确数字计算，需要使用 `BigDecimal` 类。

注意：主要理由：由于字长有限，浮点数能够精确表示的数是有限的，因而也是离散的。浮点数一般都存在舍入误差，很多数字无法精确表示(例如`0.1`)，其结果只能是接近， 但不等于。

>PS：官方文档中好像说float指数的取值范围为-126~127，double指取的取值数范围为-1022~1023

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")