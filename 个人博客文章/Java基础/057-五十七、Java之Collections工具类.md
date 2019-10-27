## collections

此类完全由在 `collection` 上进行操作或返回 `collection` 的静态方法组成。它包含在 `collection` 上操作的多态算法，即“包装器”，包装器返回由指定 `collection` 支持的新 `collection`，以及少数其他内容。 
如果为此类的方法所提供的 `collection` 或类对象为 `null`，则这些方法都将抛出
`NullPointerException`。

## collection .addAll方法

```json
addAll(Collection<? super T> c, T... elements)
将所有指定元素添加到指定 collection 中。
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");
list.add("3");
list.add("4");
/*addAll(Collection<? super T> c, T... elements)
将所有指定元素添加到指定 collection 中。*/
Collections.addAll(list,"5","6","7");
for (String str : list) {
   System.out.print(str + "\t");
}

结果：1  2  3  4  5  6  7
```

## collection.binarySearch方法
```java
binarySearch(List<? extends Comparable<? super T>> list, T key)
              使用二分搜索法搜索指定列表，以获得指定对象
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");
list.add("3");
list.add("4");
int index = Collections.binarySearch(list,"2");
System.out.println("2的索引为：" + index);

结果：2的索引为：1
```

## collection.copy方法

`copy(List<? super T> dest, List<? extends T> src)`
          将所有元素从一个列表复制到另一个列表。
```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");

List<String> list2 = Arrays.asList(new String[list1.size()]);
Collections.copy(list2,list1);

for (String str : list2) {
   System.out.print(str + "\t");
}
```
解释下上面代码：由于`Collections.copy`方法的`list2`需要一个和`list1`一样的容量，否则会抛出异常的。一般直接实例化的`ArrayList`容量都是0，所以我们采用了另一个工具类1来来为1设置一个有容量的对象。

## Collections.fill方法
`fill(List<? super T> list, T obj) `
          使用指定元素替换指定列表中的所有元素。

```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");

Collections.fill(list1,"将list集合中所有的元素都替换");

for (String str : list1) {
   System.out.println(str + "\t");
}
```

## Collections.max方法

`max(Collection<? extends T> coll) `
          根据元素的自然顺序，返回给定 collection 的最大元素。
```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");

String max = Collections.max(list1);
System.out.println("最大值为："+max);

结果：最大值为：4
```
## Collection.min方法

`min(Collection<? extends T> coll) `
          根据元素的自然顺序 返回给定 `collection` 的最小元素。
```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");

String min = Collections.min(list1);
System.out.println("最小值为："+min);

结果：最小值为：1
```

## Collections.replaceAll
`replaceAll(List<T> list, T oldVal, T newVal) `
          使用另一个值替换列表中出现的所有某一指定值。

```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");
list1.add("3");

Collections.replaceAll(list1,"3","修改了");

for (String str : list1) {
   System.out.print(str + "\t");
}

结果：1  2  修改了  4  修改了
```

## Collections.reverse方法

`reverse(List<?> list) `
          反转指定列表中元素的顺序。
```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");

Collections.reverse(list1);//将list1原有顺序反过来

for (String str : list1) {
   System.out.print(str + "\t");
}

结果：4  3  2  1
```
## Collections.reverseOrder方法

`reverseOrder()`
          返回一个比较器，它强行逆转实现了 `Comparable` 接口的对象 `collection` 的自然顺序。
可以使用在集合的排序上。

```java
List<String> list1 = new ArrayList<>();
list1.add("3");
list1.add("1");
list1.add("4");
list1.add("2");
//Collections.reverseOrder()是一个比较器，使用在list的排序上
list1.sort(Collections.reverseOrder());


for (String str : list1) {
   System.out.print(str + "\t");
}

结果：4  3  2  1
```

## Collections.sort方法

`sort(List<T> list) `
          根据元素的自然顺序 对指定列表按升序进行排序。

```java
List<String> list1 = new ArrayList<>();
list1.add("3");
list1.add("1");
list1.add("4");
list1.add("2");
//排序
Collections.sort(list1);


for (String str : list1) {
   System.out.print(str + "\t");
}

结果：1  2  3  4
```
## 对其他类型泛型的List进行排序

首先定一个`Student`类
```java
class Student {
   private String name;
   private int id;

   public Student(String name, int id) {
       this.name = name;
       this.id = id;
   }

   public String getName() {
       return name;
   }

   public int getId() {
       return id;
   }

   @Override
   public String toString() {
       return "Student{" +
               "name='" + name + '\'' +
               ", id=" + id +
               '}';
   }
}
```
## 排序代码
```java
List<Student> list = new ArrayList<>();
list.add(new Student("张三5",5));
list.add(new Student("张三1",1));
list.add(new Student("张三4",4));
list.add(new Student("张三6",6));
list.add(new Student("张三3",3));
list.add(new Student("张三2",2));
//
Collections.sort(list, new Comparator<Student>() {
   @Override
   public int compare(Student o1, Student o2) {
       return o1.getName().compareTo(o2.getName());
   }
});

for (Student stu : list) {
   System.out.println(stu + "\t");
}
//结果：
Student{name='张三1', id=1}
Student{name='张三2', id=2}
Student{name='张三3', id=3}
Student{name='张三4', id=4}
Student{name='张三5', id=5}
Student{name='张三6', id=6}
```

## Collections.swap

` swap(List<?> list, int i, int j) `
  在指定列表的指定位置处交换元素。

```java
List<String> list1 = new ArrayList<>();
list1.add("3");
list1.add("1");
list1.add("4");
list1.add("2");
System.out.println("互换前的数据：");
for (String str : list1) {
   System.out.print(str + "\t");
}
//将第1个元素和第3个元素互换
Collections.swap(list1,0,2);
System.out.println("互换后的数据：");
for (String str : list1) {
   System.out.print(str + "\t");
}
结果
互换前的数据：
3  1  4  2  
互换后的数据：
4  1  3  2
```
## 支持同步方法

`ArrayLIst,HashMap，HashSet`都是线程不安全的，现在使用工具类的下面介绍的方法就可以返回一个同步的对象，简单的演示一下怎么转换吧。
`synchronizedList(List<T> list) `
          返回指定列表支持的同步（线程安全的）列表。
`synchronizedMap(Map<K,V> m) `
          返回由指定映射支持的同步（线程安全的）映射
`synchronizedSet(Set<T> s) `
          返回指定 set 支持的同步（线程安全的）`set`。

```java
//转成线程安全的list
List<String> list = Collections.synchronizedList(new ArrayList<>());
//转成线程安全的map
Map<String,String> map = Collections.synchronizedMap(new HashMap<>(10));
//转成线程安全的
Set<String> set = Collections.synchronizedSet(new HashSet<>());
现在就可以当做可以线程安全的集合使用了。
```

## Arrays

此类包含用来操作数组（比如排序和搜索）的各种方法。此类还包含一个允许将数组作为列表来查看的静态工厂。
除非特别注明，否则如果指定数组引用为 `null`，则此类中的方法都会抛出 `NullPointerException`。 
大致上的方法都类似，简单讲几个吧！！！

## Array.copyOf方法

copyOf(T[] original, int newLength) 
     复制指定的数组，截取或用 null 填充（如有必要），以使副本具有指定的长度。
```java
String[] strings = {"1","2","3","4"};
//拷贝数组
String[] newArray = Arrays.copyOf(strings,strings.length);

for (String str : newArray) {
   System.out.print(str + "\t");
}

结果：1  2  3  4
```
## Arrays.toString方法

`toString(Object[] a) `
          返回指定数组内容的字符串表示形式。
```java
String[] strings = {"1","2","3","4"};
System.out.print(Arrays.toString(strings) + "\t");
```
## Arrays.sort

`sort(T[] a, Comparator<? super T> c) `
          根据指定比较器产生的顺序对指定对象数组进行排序。
```java
String[] strings = {"5","2","1","6"};
//排序
Arrays.sort(strings, new Comparator<String>() {
   @Override
   public int compare(String o1, String o2) {
       return o1.compareTo(o2);
   }
});
System.out.print(Arrays.toString(strings) + "\t");

结果：[1, 2, 5, 6]
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")