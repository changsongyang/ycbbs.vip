## TreeMap 简介

1. 是一个有序的`key-value`集合，它是通过红黑树实现的。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 `Comparator` 进行排序，具体取决于使用的构造方法。
2.是继承了AbstractMap，也是以`key-value`集合存储。实现了`NavigableMap`接口，可以支持一系列的导航方法。

比如返回有序的`key`集合。实现了`Cloneable`克隆接口。实现了`java.io.Serializable`序列化接口。另外，`TreeMap`是非同步的。 

## 构造方法

```java
TreeMap()
          使用键的自然顺序构造一个新的、空的树映射。

TreeMap(Comparator<? super K> comparator)
          构造一个新的、空的树映射，该映射根据给定比较器进行排序。

TreeMap(Map<? extends K,? extends V> m)
          构造一个与给定映射具有相同映射关系的新的树映射，该映射根据其键的自然顺序 进行排序。

TreeMap(SortedMap<K,? extends V> m)
          构造一个与指定有序映射具有相同映射关系和相同排序顺序的新的树映射。
```
## 常用方法

### size() 

返回此映射中的键-值映射关系数。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");
/**
* 测试size方法，打印应该是4
*/
System.out.println(tree.size());
结果：4
```
### put(K key, V value) 

将指定值与此映射中的指定键进行关联。
这个`put`方法上面已经测试过了就不需要在演示了。

### putAll(Map<? extends K,? extends V> map)

将指定映射中的所有映射关系复制到此映射中。
```java
Map<String,String> map = new HashMap<>();
map.put("map1","map1");
map.put("map2","map2");
map.put("map3","map3");

TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");
/**
* 测试putAll方法
*/
tree.putAll(map);
//将map的3个元素也添加进来了，打印的是7
System.out.println(tree.size());

结果：7
```
### remove(Object key)
如果此 `TreeMap` 中存在该键的映射关系，则将其删除。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

System.out.println("删除前："+tree.size());
/**
* 测试remove方法
*/
tree.remove("1");

System.out.println("删除后："+tree.size());

结果：
删除前：4
删除后：3
```
### values()
返回此映射包含的值的 `Collection` 视图。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

/**
* values返回值为Collection的集合可以直接使用iterator遍历
*/
Collection<String> values = tree.values();

Iterator<String> iterator = values.iterator();
while (iterator.hasNext()) {
  System.out.print(iterator.next() + "\t");
}

结果：zhangsan1  zhangsan2   zhangsan3  zhangsan4
```

### ceilingEntry(K key)
返回一个键-值映射关系，它与大于等于给定键的最小键关联；如果不存在这样的键，则返回 `null`。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");
/**
* ceilingEntry的返回值为：Map.Entry<String, String>
*  这个类中有getKey(),getValue
*/
Map.Entry<String, String> stringStringEntry = tree.ceilingEntry("1");
//获取元素的key
System.out.println("getKey:"+ stringStringEntry.getKey());
//获取元素的value值
System.out.println("getValue:"+ stringStringEntry.getValue());

结果：
getKey:1

getValue:zhangsan1
```

### containsKey(Object key)
如果此映射包含指定键的映射关系，则返回 `true`。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

System.out.println("key为2的在集合中是否存在："+tree.containsKey("2"));

结果：key为2的在集合中是否存在：true
```

### containsValue(Object value)
如果此映射为指定值映射一个或多个键，则返回 `true`。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

System.out.println("value为zhangsan3的在集合中是否存在："+tree.containsValue("zhangsan3"));

结果：value为zhangsan3的在集合中是否存在：true
```

### entrySet()
返回此映射中包含的映射关系的 `Set` 视图。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

/**
* entrySet方法返回值也是set,也可以使用
*/
Set<Map.Entry<String, String>> set = tree.entrySet();

/**
* 使用iterator遍历
*/
Iterator<Map.Entry<String, String>> iterator = set.iterator();
while (iterator.hasNext()) {
  System.out.print(iterator.next() + "\t");
}

结果：1=zhangsan1  2=zhangsan2  3=zhangsan3  4=zhangsan4
```

### firstEntry()
返回一个与此映射中的最小键关联的键-值映射关系；如果映射为空，则返回 `null`。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

/**
* firstEntry方法返回值Map.Entry
*/
Map.Entry<String, String> entry = tree.firstEntry();
//获取键
System.out.println(entry.getKey());
//获取值
System.out.println(entry.getValue());
结果：

1
zhangsan1
```

### get(Object key)
返回指定键所映射的值，如果对于该键而言，此映射不包含任何映射关系，则返回 `null`。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");

//根据键来获取值
System.out.println(tree.get("1"));

结果：zhangsan1
```

### keySet()
返回此映射包含的键的 `Set` 视图。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");
/**
* 返回也是set集合，输出都是key
*/
Set<String> set = tree.keySet();
/**
* 使用iterator遍历
* */
Iterator<String> iterator = set.iterator();
while (iterator.hasNext()) {
  System.out.print(iterator.next() + "\t");
}

结果：1 2 3 4
```

### lastEntry()
返回与此映射中的最大键关联的键-值映射关系；如果映射为空，则返回 `null`。
```java
TreeMap<String,String> tree = new TreeMap<>();
tree.put("1","zhangsan1");
tree.put("2","zhangsan2");
tree.put("3","zhangsan3");
tree.put("4","zhangsan4");
      
/**
* 返回Map.Entry类型，输出的一个元素
*/
Map.Entry<String, String> entry = tree.lastEntry();
System.out.print("key:" + entry.getKey());
System.out.println("value:" + entry.getValue());

结果：key:4   value:zhangsan4
```

## TreeMap排序习题！

以上都是使用的字符串，而 `String` 类已经即实现了比较的方法
【`public int compareTo(String anotherString)`】
，所以不会出问题，大家可以试试使用自定义一个Cat类
属性：`int id`, `String name`,然后添加到 `treemap` 中去，看看会出现什么问题？

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")