## 引言

其实 `Option` 类在 `Java 8` 中就引入了，用于避免 `null` 检查和 `NullPointerException` 指针问题

`Java 9` 中，又为该类添加了三个方法来改进它的功能

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">stream()</td> 
   <td align="left">返回包含值的流，如果值不存在，则返回空流</td> 
  </tr> 
  <tr> 
   <td align="left">ifPresentOrElse()</td> 
   <td align="left">如果值存在则对值执行一些操作，否则执行另一个操作</td> 
  </tr> 
  <tr> 
   <td align="left">or()</td> 
   <td align="left">如果值存在，则返回用于描述该值的 <code>Option</code>，如果不存在则生成一个值</td> 
  </tr> 
 </tbody> 
</table>

## steam() 方法 ##

`steam()` 方法的原型如下

```
public Optional<T> or(Supplier<? extends Optional<? extends T>> supplier)
```

如果值存在，则返回包含值的有序的流，如果值不存在，则返回一个空流

### 范例 ###

在我们的工作目录，创建一个文件 `OptionStreamTester.java` 并输入以下内容

```
import java.util.Arrays;
import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class OptionStreamTester {
public static void main(String[] args) {
   List<Optional<String>> list = Arrays.asList (
      Optional.empty(), 
      Optional.of("A"), 
      Optional.empty(), 
      Optional.of("B"));

      //filter the list based to print non-empty values

      //if optional is non-empty, get the value in stream, otherwise return empty
      List<String> filteredList = list.stream()
         .flatMap(o -> o.isPresent() ? Stream.of(o.get()) : Stream.empty())
         .collect(Collectors.toList());

      //Optional::stream method will return a stream of either one 
      //or zero element if data is present or not.
      List<String> filteredListJava9 = list.stream()
         .flatMap(Optional::stream)
         .collect(Collectors.toList());

      System.out.println(filteredList);
      System.out.println(filteredListJava9);
   }  
}
```

运行结果如下

```
[penglei@www.ycbbs.vip java9]$ javac OptionStreamTester.java && java OptionStreamTester
[A, B]
[A, B]
```

在这个范例中，我们先创建了一个字符串列表，这一个可空的字符串列表。

而且我们使用了 Java 9 中的 `stream()` 方法和非 `stream()` 方法来输出列表中的不为空的元素

大家可以对比下，使用 `Option::steam()` 方法的确简练了很多

## ifPresentOrElse() 方法 ##

`ifPresentOrElse()` 方法的原型如下

```
public void ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)
```

如果值存在则对值执行一个动作 `action`，如果值不存在则执行另一个动作 `emptyAction`

## 范例 ##

在我们的工作目录，创建一个文件 `OptionIfPresentOrElseTester.java` 并输入以下内容

```
import java.util.Optional;

public class OptionIfPresentOrElseTester {
   public static void main(String[] args) {
      Optional<Integer> optional = Optional.of(1);

      optional.ifPresentOrElse( x -> System.out.println("Value: " + x),() -> 
         System.out.println("Not Present."));

      optional = Optional.empty();

      optional.ifPresentOrElse( x -> System.out.println("Value: " + x),() -> 
         System.out.println("Not Present."));
   }  
}
```

运行结果如下

```
[penglei@www.ycbbs.vip java9]$ javac OptionIfPresentOrElseTester.java && java OptionIfPresentOrElseTester
Value: 1
Not Present.
```

范例很简单，就是如果值存在则输出，不存在则输出 `Not Present.`

## or() 方法 ##

`or()` 方法的原型如下

```
public Optional<T> or(Supplier<? extends Optional<? extends T>> supplier)
```

如果值存在，则返回一个描述该值的 `Option` ，否则使用 `supplier` 生成一个值

## 范例 ##

在我们的工作目录，创建一个文件 `OptionOrTester.java` 并输入以下内容

```
import java.util.Optional;
import java.util.function.Supplier;

public class OptionOrTester {
   public static void main(String[] args) {
      Optional<String> optional1 = Optional.of("Mahesh");

      Supplier<Optional<String>> supplierString = () -> Optional.of("Not Present");

      optional1 = optional1.or( supplierString);

      optional1.ifPresent( x -> System.out.println("Value: " + x));

      optional1 = Optional.empty();    

      optional1 = optional1.or( supplierString);

      optional1.ifPresent( x -> System.out.println("Value: " + x));  
   }  
}
```

运行结果如下

```
[penglei@tech.souyunku.com java9]$ javac OptionOrTester.java && java OptionOrTester 
Value: Mahesh
Value: Not Present
```


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")