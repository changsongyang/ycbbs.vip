## HashMap

`HashMap` 是一个散列表，它存储的内容是键值对(`key-value`)映射。

该类实现了 `Map` 接口，根据键的 `HashCode` 值存储数据，具有很快的访问速度，最多允许一条记录的键为 `null`，不支持线程同步。

## 散列表（Hash table，也叫哈希表）

是根据关键码值(`Key` `value`)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。

这个映射函数叫做散列函数，存放记录的数组叫做散列表。

## HashMap实现原理
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/51-1.png)

`HashMap` 主要是以数组和链表实现的。每个列表被称为桶要想査找表中对象的位置， 就要先计算它的散列码， 然后与桶的总数取余， 所得到的结果就是保存这个元素的桶的索引。

解释：`hashmap` 是以一个数组和链表储存的。那么现在加入数组有 `10` 个长度，比方说现在需要 `add` 的一个`key=1`，`vallue=“张三”`的元素

 散列表数组的下标`=1.hashcode()%`散列表数组`.length`，这个就是数组的下标。

 将 `key`,`value` 存进索引下的链表中，当然，有时候会遇到桶被占满的情况， 这也是不可避免的。

这种现象被称为散列冲突（ `hashcollision`) o 这时， 需要用新对象与桶中的所有对象进行比较，査看这个对象是否已经存在。

如果散列码是合理且随机分布的， 桶的数目也足够大， 需要比较的次数就会很少。

## 构造方法
```java
HashMap() 
          构造一个具有默认初始容量 (16) 和默认加载因子 (0.75) 的空 HashMap。

HashMap(int initialCapacity) 
          构造一个带指定初始容量和默认加载因子 (0.75) 的空 HashMap。

HashMap(int initialCapacity) 
          构造一个带指定初始容量和默认加载因子 (0.75) 的空 HashMap。

HashMap(Map<? extends K,? extends V> m) 
          构造一个映射关系与指定 Map 相同的新 HashMap。
```


## HashMap常用方法
```java
size()
            返回此映射中的键-值映射关系数。

put(K key, V value)
          在此映射中关联指定值与指定键。

putAll(Map<? extends K,? extends V> m)
          将指定映射的所有映射关系复制到此映射中，这些映射关系将替换此映射目前针对指定映射中所有键的所有映射关系。

get(Object key)
          返回指定键所映射的值；如果对于该键来说，此映射不包含任何映射关系，则返回 null。

remove(Object key)
          从此映射中移除指定键的映射关系（如果存在）。

isEmpty()
          如果此映射不包含键-值映射关系，则返回 true。

keySet()
          返回此映射中所包含的键的 Set 视图。

containsKey(Object key)
          如果此映射包含对于指定键的映射关系，则返回 true。

containsValue(Object value)
          如果此映射将一个或多个键映射到指定值，则返回 true。

values()
          返回此映射所包含的值的 Collection 视图。

entrySet()
          返回此映射所包含的映射关系的 Set 视图。

clear()
          从此映射中移除所有映射关系。

size()

 返回此映射中的键-值映射关系数。
```

 
```java
HashMap<String,String> map = new HashMap<>();
System.out.println(map.size());

结果：0
```

## put(K key, V value)

```java
在此映射中关联指定值与指定键。就是一个添加元素的方法。

HashMap<String,String> map = new HashMap<>();
map.put("1","zhangsan1");
map.put("2","zhangsan2");
map.put("3","zhangsan3");
map.put("4","zhangsan4");
System.out.println(map.size());

结果：4
```

## putAll(Map<? extends K,? extends V> m)
```java
将指定映射的所有映射关系复制到此映射中，这些映射关系将替换此映射目前针对指定映射中所有键的所有映射关系。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");

HashMap<String,String> map2 = new HashMap<>();
map2.put("5","zhangsan5");
map2.put("6","zhangsan6");
map2.put("7","zhangsan7");
map2.put("8","zhangsan8");
//将map2的元素添加到map1中
map1.putAll(map2);
System.out.println(map1.size());

结果：8
```

## get(Object key)
```java
返回指定键所映射的值；如果对于该键来说，此映射不包含任何映射关系，则返回 null。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");

System.out.println(map1.get("2"));

结果：zhangsan2
```

根据 `put` 的 `key` 值来获取实际值。

## remove(Object key)
```java
从此映射中移除指定键的映射关系（如果存在）。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
System.out.println("删除前："+ map1.size());
//删除
map1.remove("3");

System.out.println("获取key=3的值：" + map1.get("3"));

System.out.println("删除后："+ map1.size());

结果: 删除前：4
   获取key=3的值：null
   删除后：3
```
          

## isEmpty()
```java
如果此映射不包含键-值映射关系，则返回 true。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
//表示不为空
System.out.println(map1.isEmpty());

```

结果：false

## keySet()
返回此映射中所包含的键的 Set 视图。
```java
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");


//这里需要返回一个set集合
Set<String> strings = map1.keySet();
//然后遍历，这个set集合后期章节会讲到
Iterator<String> iterator = strings.iterator();
//这样就循环出所有的key了
while (iterator.hasNext()) {
  System.out.print(iterator.next() + "\t");
}

结果： 1 2 3 4
```
这里需要配合set集合来使用，后期章节会讲解set

## containsKey(Object key)
```java
如果此映射包含对于指定键的映射关系，则返回 true。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
//查询map中的keys中是否包含2
System.out.println(map1.containsKey("2"));

结果：true
```

## containsValue(Object value)

```java
如果此映射将一个或多个键映射到指定值，则返回 true。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
//查询map中的keys中是否包含2
System.out.println(map1.containsValue("zhangsan3"));

//包含zhangsan3
结果：true
```

## values()
返回此映射所包含的值的 Collection 视图。
```java
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
/**
* 转换为Collection数组，然后用iterator来遍历
*/
Collection<String> values =  map1.values();
Iterator<String> iterator = values.iterator();
while (iterator.hasNext()) {
 System.out.println(iterator.next());
}

结果
zhangsan1
zhangsan2
zhangsan3
zhangsan4
```

## entrySet()
```java
//返回此映射所包含的映射关系的 Set 视图。
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
/**
* 转换为Set数组，然后用iterator来遍历
*/
Set<Map.Entry<String, String>> strings = map1.entrySet();
Iterator<Map.Entry<String, String>> iterator = strings.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果：

1=zhangsan1

2=zhangsan2

3=zhangsan3

4=zhangsan4
```

这个返回的是一个`set`的集合，`set`的元素是`map`的形式

## clear()

从此映射中移除所有映射关系。
```java
HashMap<String,String> map1 = new HashMap<>();
map1.put("1","zhangsan1");
map1.put("2","zhangsan2");
map1.put("3","zhangsan3");
map1.put("4","zhangsan4");
//全部移出了
map1.clear();
System.out.println("map的元素个数:" + map1.size());

结果： map的元素个数:0
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")