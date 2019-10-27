## 为什么要用LinkedList?

我们在现实开发中我们都是会大量使用到数组以及动态的`ArrayList`类。然而，数组和数组列表都有一个重大的缺陷。

这就是从数组的中间位置删除一个元素要付出很大的代价，其原因是数组中处于被删除元素之后的所有元素都要向数组的前端移动。在数组中间的位置上插入一个元素也是如此。

那么`LinkedList`(链表)就能解决了这个问题尽管数组在连续的存储位置上存放对象引用，但链表却将每个对象存放在独立的节点中。每个节点还存放着序列中下一个结点的引用。

## LinkedList

`LinkedList`是一个实现了 `List` 接口和 `Deque` 接口的双向链表。

有关索引的操作可能从链表头开始遍历到链表尾部，也可能从尾部遍历到链表头部，这取决于看索引更靠近哪一端。

`LinkedList`不是线程安全的，如果想使`LinkedList`变成线程安全的，可以使用如下方式：
```java
List list=Collections.synchronizedList(new LinkedList(...));
```

如下图：

以上图可以看出，双向链表是一个和现在的动车组类似，2端都是可以当作头来使用，意思就是说可以从前往后面查找，也可以从后往前查找。

## LinkedList构造方法

`LinkedList()  `
    构造一个空列表

`LinkedList(Collection<? extends E> c)`
          构造一个包含指定 collection 中的元素的列表，这些元素按其 `collection` 的迭代器返回的顺序排列。

## LinkedList常用方法介绍

`public boolean add(Object element)`
   向链表末尾添加一个新节点，该节点中的数据是参数 `element` 指定的对象。
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
 System.out.println(iterator.next());
}

结果：
hello1
hello2
hello3
hello4
```

## public void add(int index,Object element)

向链表指定位置添加一个新节点，该节点中的数据是参数 `element` 指定的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
list.add(1,"我是新插入的");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果
hello1
我是新插入的
hello2
hello3
hello4
```

## public void addFirist(Object element)
向链表表头添加一个新节点，该节点中的数据是参数 `element` 指定的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
list.addFirst("addFirst");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果
addFirst
hello1
hello2
hello3
hello4
```

## public void addLast(Object element)

向链表表尾添加一个新节点，该节点中的数据是参数 `element` 指定的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
list.addLast("addLast");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果
hello1
hello2
hello3
hello4
addLast
```

## public  Object removeFirst()

删除第一个节点并返回这个节点中的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
String str = list.removeFirst();//删除第一个节点
System.out.println("删除第一个节点的数据为："+str);

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果
删除第一个节点的数据为：hello1
hello2
hello3
hello4
```
## public  Object removeLast()

删除最后一个节点并返回这个节点中的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
String str = list.removeLast();//删除最后节点
System.out.println("删除最后节点的数据为："+str);

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果
删除最后节点的数据为：hello4
hello1
hello2
hello3
```

## public Object remove(int index)

```java
删除指定位置的节点
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
String str = list.remove(1);//删除索引为1的节点
System.out.println("删除索引为1的节点数据为："+str);

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果
删除索引为1的节点数据为：hello2
hello1
hello3
hello4
```
## public Object get(int index)

得到指定位置的节点
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
String str = list.get(1);//获取索引为1的值
System.out.println("获取索引为1的值："+str);

结果
获取索引为1的值：hello2
```

## public Object getFirst()

得到链表第一个节点的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
String str = list.getFirst();//获取链表第一个元素的值
System.out.println("获取链表第一个元素的值："+str);

结果
获取链表第一个元素的值：hello1
```



## public Object getLast()

得到链表最后一个节点的对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
String str = list.getLast();//获取链表最后一个元素的值
System.out.println("获取链表最后一个元素的值："+str);

结果
获取链表最后一个元素的值：hello4
```

## int indexOf(Object element)
```
返回节点对象element在链表中首次出现的位置，如果链表中无此节点的对象则返回-1
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
//返回节点对象element在链表中首次出现的位置，如果链表中无此节点的对象则返回-1
int index = list.indexOf("hello2");
System.out.println("返回节点对象element在链表中首次出现的位置："+index);

结果
返回节点对象element在链表中首次出现的位置：1
```


## public int lastIndexOf(Object element)

返回节点对象`element`在链表中最后出现的位置，如果链表中无此节点的对象则返回-1
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
//返回节点对象element在链表中最后出现的位置，如果链表中无此节点的对象则返回-1
int index = list.lastIndexOf("hello3");
System.out.println("返回节点对象element在链表中最后出现的位置："+index);

结果
返回节点对象element在链表中最后出现的位置：2
```
## public Object set(int index,Object element)

将当前链表index位置节点中的对象替换成参数element指定的对象，返回被替换对象
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
// 将当前链表index位置节点中的对象替换成参数element指定的对象，返回被替换对象
list.set(1,"我把索引为1的值改变了");
System.out.println("替换链表中索引为1的值："+list.get(1));

结果
替换链表中索引为1的值：我把索引为1的值改变了
```
## public int size()

返回链表的长度即节点个数
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
System.out.println("链表中的元素数量为："+list.size());

结果
链表中的元素数量为：4
```
## public boolean contains(Object element)

判断链表节点对象中是否含有element
```java
LinkedList<String> list = new LinkedList<>();
list.add("hello1");
list.add("hello2");
list.add("hello3");
list.add("hello4");
//判断链表节点对象中是否含有element
System.out.println("hello2是否在链表中存在："+list.contains("hello2"));

结果
hello2是否在链表中存在：true
```
## ArrayList/Vector/LinkedList原理总结
### ArrayList:
1、实现原理，采用了动态对象数组实现，默认构造函数创建了一个空的对象数组
2、第一次添加元素，扩展了10个位置的容量。
3、扩充算法，原来数组的大小+原来数组大小的一半
4、不适合进行删除或插入操作
5、为了防止数组动态扩充次数较多，建议大家创建ArrayList的时候给定默认初始容量
6、线程不安全，适合单线程访问时使用。
### Vector:
1、实现原理，采用了动态对象数组实现，默认构造函数创建了一个大小为10的对象数组
2、扩充算法，当增量为0时，扩充为原来容量的2倍，当增量大小大于0时，扩充为原来的大小+增量大小
3、不适合进行删除或插入操作
4、线程安全，适合在多线程访问时使用，效率较低。

### LinkedList:
1、实现原理，使用双向列表结构实现。
2、适合插入，或者修改，性能高

## 备注

以上为`LinkedList`的常用方法，初学者朋友学会了没有，下一节我们讲自定义链表，敬请期待。。。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")