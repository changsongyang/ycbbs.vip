## Scanner介绍

一个可以使用正则表达式来解析基本类型和字符串的简单文本扫描器。

`Scanner` 使用分隔符模式将其输入分解为标记，默认情况下该分隔符模式与空白匹配。然后可以使用不同的 `next` 方法将得到的标记转换为不同类型的值。
 
以上为api里面的，意思是：这个类最实用的地方表现在获取控制台输入，尽管`Java API`文档中列举了大量的API方法，但是都不怎么使用。

## Scanner常用构造方法

`Scanner(InputStream source)`
构造一个新的 `Scanner`，它生成的值是从指定的输入流扫描的。

## Scanner常用方法

`next() `
  查找并返回来自此扫描器的下一个完整标记。

`nextLine() `
  此扫描器执行当前行，并返回跳过的输入信息。

`hasNext()`
          如果此扫描器的输入中有另一个标记，则返回 true。

`close()`
      关闭此扫描器。该方法会在每个演示当中都会使用。

## next方法
```java
import java.util.Scanner;
public class Test1 {
  public static void main(String[] args) {
      Scanner scan = new Scanner(System.in);
      System.out.println("请输入数据：");
// 从键盘接收数据
  String next = scan.next();
  System.out.println("输入的数据为：" + next);
  can.close();
  }
}
//打印结果
请输入数据：
hello
输入的数据为：hello
```

## nextLine方法
```java
import java.util.Scanner;
public class Test1 {
  public static void main(String[] args) {
      Scanner scan = new Scanner(System.in);
      System.out.println("请输入数据：");
// 从键盘接收数据
  String next = scan.nextLine();
  System.out.println("输入的数据为：" + next);
  scan.close();
  }
}
//打印结果
请输入数据：
hello
输入的数据为：hello
```


## hasNext方法
```java
import java.util.Scanner;
public class Test1 {
  public static void main(String[] args) {
      Scanner scan = new Scanner(System.in);
      System.out.println("请输入数据：");
      String next = scan.nextLine();
      System.out.println("输入的数据为：" + next);
      /**
       * 是否有下一条数据输入
       */
      if (scan.hasNext()) {
          next = scan.nextLine();
      }
      System.out.println("输入的数据为：" + next);
      scan.close();
  }
}
结果
请输入数据：
hello
输入的数据为：hello
hello1
输入的数据为：hello1
```


## next() 与 nextLine() 区别

`next()`:
· 1、一定要读取到有效字符后才可以结束输入。
· 2、对输入有效字符之前遇到的空白，`next()` 方法会自动将其去掉。
· 3、只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。
· `next()` 不能得到带有空格的字符串。

`nextLine()`：
· 1、以Enter为结束符,也就是说 `nextLine()`方法返回的是输入回车之前的所有字符。
· 2、可以获得空白。

如果要输入 `int` 或 `float` 类型的数据，在 `Scanner` 类中也有支持，但是在输入之前最好先使用 `hasNextXxx()` 方法进行验证，再使用 `nextXxx()` 来读取：
```java
import java.util.Scanner;
public class Test1 {
  public static void main(String[] args) {
      Scanner scan = new Scanner(System.in);
      // 从键盘接收数据
      int i = 0;
      float f = 0.0f;
      System.out.print("输入整数：");
      if (scan.hasNextInt()) {
          // 判断输入的是否是整数
          i = scan.nextInt();
          // 接收整数
          System.out.println("整数数据：" + i);
      } else {
          // 输入错误的信息
          System.out.println("输入的不是整数！");
      }
      System.out.print("输入小数：");
      if (scan.hasNextFloat()) {
          // 判断输入的是否是小数
          f = scan.nextFloat();
          // 接收小数
          System.out.println("小数数据：" + f);
      } else {
          // 输入错误的信息
          System.out.println("输入的不是小数！");
      }
      scan.close();
  }
}
结果
输入整数：1
整数数据：1
输入小数：1.1
小数数据：1.1
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")