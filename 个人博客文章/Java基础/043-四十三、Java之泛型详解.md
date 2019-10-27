## 泛型程序设计

意味着编写的代码可以被很多不同类型的对象所重用。
例如， 我们并不希望为聚集 `String` 和 `File` 对象分别设计不同的类。

实际上，也不需要这样做，因为一个 `ArrayList` 类可以聚集任何类型的对象。这是一个泛型程序设计的实例。
 
## 类型参数的好处

在 `Java` 中增加范型类之前， 泛型程序设计是用继承实现的。`ArrayList` 类只维护一个 `Object` 引用的数组(`ArrayList`源码)
```java
public class ArrayList
{
  private Object[] elementData;
  public Object get(int i) { . . , }
  public void add(Object o) { . . . }
}

这种方法有两个问题。当获取一个值时必须进行强制类型转换。
ArrayList files = new ArrayList();
files.add("hello");
//在这里必须强转成String类型，否则是默认的Object类型,不强转就会编译不通过！
String filename = (String)files.get(0);
//这里就是String类型了，因为上面强转了
System.out.println(filename);
此外，这里没有错误检査。可以向数组列表中添加任何类的对象。
ArrayList files = new ArrayList();
//这里添加的是一个字符串
files.add("hello");
String filename1 = (String)files.get(0);
System.out.println(filename1);
//如果现在添加的是一个对象（ArrayList）也是没有问题的。
files.add(new ArrayList());
//这个元素应该是一个ArrayList数组，那么我们强转成String类型而系统也会编译通过的！
String filename2 = (String)files.get(1);
//这里就是String类型了，因为上面强转了
System.out.println(filename2);

以上编译器照样编译通过！但是当代码运行到
String filename2 = (String)files.get(1);

这行代码的时候就会报一个异常强转String类型失败！！！
Exception in thread "main" java.lang.ClassCastException: java.base/java.util.ArrayList cannot be cast to java.base/java.lang.String
at 集合.myarraylist.ArrayListTest.main(ArrayListTest.java:16)
```

那么目前泛型就可以解决这种问题了。下面我们来看看泛型怎么来解决这个问题。

## 泛型指定类型

泛型提供了一个更好的解决方案： 
类型参数 （ `type` `parameters`)。 `ArrayList` 类有一个类型参数用来指示元素的类型，也使得代码具有更好的可读性。人们一看就知道这个数组列表中包含的是 `String` 对象。

改造上面的代码：
```java
//为ArrayList指定存储的元素类型为字符串：String
ArrayList<String> files = new ArrayList<>();
//这里只能添加字符串了
files.add("hello");
//这里就不需要在转型了
String filename1 = files.get(0);
System.out.println(filename1);

//这里在添加其它类型的就不可以了，编译器就不会通过了。
files.add(new ArrayList());
```

这里就不会出现以上转型问题了。编译器也可以很好地利用这个信息。当调用get的时候， 不需要进行强制类型转换，编译器就知道返回值类型为 String，而不是Object。

然后add方法添加类型也会固定为String了，如果add其它类型是无法通过编译的。

## 泛型类

一个泛型类（generic class) 就是具有一个或多个类型变量的类。

 泛型类的声明和非泛型类的声明类似，就是在类名后面添加了类型参数声明部分，泛型类的类型参数声明部分也包含一个或多个类型参数，参数间用逗号隔开。

一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。因为它们接受一个或多个参数，这些类被称为参数化的类或参数化的类型。 

泛型语法：
```java
public class 类名称<类型参数>{
   ...
}

示例：
public class MyClass<T> {
 private T t;
 /**
  * 设置t的值
  * @param t
  */
 public void set(T t) {
     this.t = t;
 }
 /**
  * 获取t的值
  * @return
  */
 public T get() {
     return t;
 }
 public static void main(String[] args) {
     //实例化一个integerMyClass对象，泛型的参数类型为Integer
     MyClass<Integer> integerMyClass = new MyClass<Integer>();
     //实例化一个stringMyClass对象，泛型的参数类型为String
     MyClass<String> stringMyClass = new MyClass<String>();
     //设置t的值为:new Integer(10)
     integerMyClass.set(new Integer(10));
     //设置t的值为:new String("这个泛型类是指定的String类型")
     stringMyClass.set(new String("这个泛型类是指定的String类型"));
     System.out.printf("整型值为 :%d\n\n", integerMyClass.get());
     System.out.printf("字符串为 :%s\n", stringMyClass.get());
 }
}
```

备注：其实大家也可以把泛型类看作普通类的工厂。

## 泛型方法

前面已经介绍了如何定义一个泛型类。实际上，还可以定义一个带有类型参数的简单方法，这就是泛型方法。泛型方法可以定义在普通类中，也可以定义在泛型类中。

泛型方法定义：
```java
public static <T> T getMiddle(T... a)
{
return a[a.length / 2];
}
```

所有泛型方法声明都有一个类型参数声明部分（由尖括号分隔），该类型参数声明部分在方法返回类型之前。

每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开。一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。

类型参数能被用来声明返回值类型，并且能作为泛型方法得到的实际参数类型的占位符。

泛型方法体的声明和其他方法一样。注意类型参数只能代表引用型类型，不能是原始类型（像`int,double,char`的等）。

```java
示例：
public class TestArrayList {
  /**
   * 泛型方法printArray1:只能传一个参数的泛型方法
   * @param inputArray
   * @param <E>
   */
  private <E> void printArray1(E inputArray)
  {
      //输出数组元素
      System.out.printf( "printArray1：%s ", inputArray);
      System.out.println();
  }

  /**
   * 泛型方法printArray1:可以传入多个参数的泛型方法
   * @param inputArray
   * @param <E>
   */
  private static <E> void printArray2(E... inputArray )
  {
      System.out.print("printArray2：");
      //输出数组元素
      for ( E element : inputArray ){
          System.out.printf( "%s ", element);
      }
      System.out.println();
  }

  public static void main(String[] args) {
      /**
       * 这里在调用方法，在方法名前的尖括号中放人具体的类型
       * 但是往往这个类型我们是可以去掉的
       */
      new TestArrayList().<String>printArray1("zhangsan");
      printArray2("wangwu",1,1.3f);
  }
}

运行的结果
printArray1：zhangsan 
printArray2：wangwu 1 1.3
```

## 类型变量的限定

有时，类或方法需要对类型变量加以约束。我们来看看下面的例子再来讲解。
```java
public static <T> T min(T[] a) 
{
 if (a == null || a.length == 0) {
    return null;
 }
 T smallest = a[0] ;
 for (int i = 1; i < a.length; i ++){
  if (smallest.compareTo(a[i]) > 0){
       smallest = a[i];
   }
 }
  return smallest;
}
```

这里该方法本身就会编译不通过但是又想传入的类型都包含`compareTo`方法怎么办？

现在该方法变量 `smallest` 类型为 `T`, 这也意味着它可以是任何一个类的对象。怎么才能确信T所属的类有 `compareTo` 方法呢？

那么这个时候我们就可以通过对类型变量`T`设置限定解决。
```java
public static <T extends Comparable> T min(T[] a)
```

这样泛型方法限定了必须传入的参数都是实现了 `Comparable` 接口的类或者子类，如果传入其它对象的话 `min` 将会产生一个编译错误。

## 类型通配符

1、类型通配符一般是使用?代替具体的类型参数。例如 `List<?>` 在逻辑上是 `List<String>,List<Integer>` 等所有` List<具体类型实参>` 的父类。

```java
示例：
public class TestArrayList {
 public static void main(String[] args) {
     List<String> name = new ArrayList<String>();
     List<Integer> age = new ArrayList<Integer>();
     List<Number> number = new ArrayList<Number>();

     name.add("icon");
     age.add(18);
     number.add(314);

     getData(name);
     getData(age);
     getData(number);
 }
 /**
  * 类型通配符就是<？>这个?代表这任意类型
  * @param data
  */
 public static void getData(List<?> data) {
     System.out.println("data :" + data.get(0));
 }
}
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以[点击此处扫下面二维码关注微信公众号](https://www.ycbbs.vip?p=28 "点击此处扫下面二维码关注微信公众号")