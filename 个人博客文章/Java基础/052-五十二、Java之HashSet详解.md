## HashSet原理

该类实现了`Set`接口，不允许出现重复元素，不保证集合中元素的顺序，允许包含值为`null`的元素，但最多只能一个。

对于 `HashSet` 而言，`HashSet` 继承自`AbstractSet`，它是基于 `HashMap` 实现的，`HashSet` 底层使用 `HashMap` 来保存所有元素。

因此 `HashSet` 的实现比较简单，相关 `HashSet` 的操作，基本上都是直接调用底层 `HashMap` 的相关方法来完成，我们应该为保存到 `HashSet` 中的对象覆盖` hashCode()` 和 `equals()`

## 构造方法
```java
HashSet()
          构造一个新的空 set，其底层 HashMap 实例的默认初始容量是 16，加载因子是 0.75。

HashSet(Collection<? extends E> c)
          构造一个包含指定 collection 中的元素的新 set。

HashSet(int initialCapacity)
          构造一个新的空 set，其底层 HashMap 实例具有指定的初始容量和默认的加载因子（0.75）。

HashSet(int initialCapacity, float loadFactor)
          构造一个新的空 set，其底层 HashMap 实例具有指定的初始容量和指定的加载因子。
```

## 常用方法

```java
size() 
          返回此 set 中的元素的数量（set 的容量）。

isEmpty()
          如果此 set 不包含任何元素，则返回 true。

add(E e)
          如果此 set 中尚未包含指定元素，则添加指定元素。

iterator()
          返回对此 set 中元素进行迭代的迭代器。

remove(Object o)
          如果指定元素存在于此 set 中，则将其移除。

contains(Object o)
          如果此 set 包含指定元素，则返回 true。

clear()
          从此 set 中移除所有元素。

size() 
```
返回此 `set` 中的元素的数量（`set` 的容量）。
```java
HashSet<String> set = new HashSet<>();
set.add("zhangsan1");
System.out.println("set的元素个数:" + set.size());

结果：
set的元素个数:1
```

## isEmpty()
如果此 `set` 不包含任何元素，则返回 `true`。
```java
HashSet<String> set = new HashSet<>();
set.add("zhangsan1");
System.out.println("isEmpty方法:" + set.isEmpty());

//false表示为不为空
结果为：isEmpty方法:false
```
## add(E e)

如果此 `set` 中尚未包含指定元素，则添加指定元素。
```java
HashSet<String> set = new HashSet<>();
//添加一个元素
set.add("zhangsan1");
System.out.println("set的元素个数:" + set.size());

结果：
set的元素个数:1
```

## iterator()
返回对此 `set` 中元素进行迭代的迭代器。
```java
HashSet<String> set = new HashSet<>();
set.add("zhangsan1");
set.add("zhangsan2");
set.add("zhangsan3");
set.add("zhangsan4");

/**
* 创建迭代器
*/
Iterator<String> iterator = set.iterator();
while (iterator.hasNext()) {
 //打印每个元素
 System.out.println(iterator.next());
}

结果：
zhangsan1
zhangsan3
zhangsan2
zhangsan4

```

## contains(Object o)

如果此 `set` 包含指定元素，则返回 `true`。
```java
HashSet<String> set = new HashSet<>();
set.add("zhangsan1");
set.add("zhangsan2");
set.add("zhangsan3");
set.add("zhangsan4");
/**
* 判断set中是否包含zhangsan2,true表示有，false表示没有
*/
System.out.println(set.contains("zhangsan2"));


结果：true
```
 
## clear()

```java
从此 set 中移除所有元素。
HashSet<String> set = new HashSet<>();
set.add("zhangsan1");
set.add("zhangsan2");
set.add("zhangsan3");
set.add("zhangsan4");

System.out.println("清空之前：" +set.size());
/**
* 清空set所有数据
*/
set.clear();
System.out.println("清空之后：" +set.size());

结果：清空之前：4
   清空之后：0
```

## 总结
`HashSet`

1.实现原理，基于哈希表（`hashmap`）实现。

2. 不允许重复键存在，但可以有`null`值。

3. 哈希表存储是无序的。

4. 添加元素时把元素当作`hashmap`的`key`存储，`HashMap`的 `value` 是存储的一个固定值 `object`

5. 排除重复元素是通过 `equals` 检查对象是否相同。

6. 判断2个对象是否相同，先根据2个对象的 `hashcode` 比较是否相等（如果两个对象的 `hashcode` 相同，它们也不一定是同一个对象，如果不同，那一定不是同一个对象）如果不同，则两个对象不是同一个对象，如果相同，在将2个对象进行equals检查来判断是否相同，如果相同则是同一个对象，不同则不是同一个对象。

7. 如果要完全判断自定义对象是否有重复值，这个时候需要将自定义对象重写对象所在类的 `hashcode` 和 `equals` 方法来解决。

8. .哈希表的存储结构就是：数组+链表，数组的每个元素都是以链表的形式存储的。

以上就是 `HashSet` 的常用方法，还有一些继承过来的方法，大家可以自行测试一下。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")