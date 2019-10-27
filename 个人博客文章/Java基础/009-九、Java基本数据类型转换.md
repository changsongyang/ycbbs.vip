总结一句话就是：容量小的数据类型可以自动转换为容量大的数据类型。

下面看一张表
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/9-1.jpg)

最小的 `byte`，最大容量为 `double`，容量可以从上面的标数范围查看下面我们做一个实例
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/9-2.png)
再来对比一个错误的实例
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/9-3.jpg)

## 强制转换 — （显示转换）
```java
int a = 200;
	byte b = (int) a;
```

我们来看看一个实例来对比下
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/9-4.png)

以上图中显示没有强转就会报出一个错误。那么这种情况我们该怎么解决呢，这个时候就需要来将`int`类型强制（显示）转换成 `byte` 类型
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/9-5.png)

通过 `(int)`可以强制转型 , 但是这样会丢失精度 , 比如`a`如果超过了`b`的范围 ,就会被截断成为一个完全不同的值。

不能在布尔类型和任何数值类型之间做强制类型转换。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")