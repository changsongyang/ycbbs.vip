## 自定义HashMap

前面手写了`MyArrayList`以及`MyLinkedList`了，现在写的`hashmap`主要小编只采取数组加链表的方式来写了，那么现在就废话不多说了，还是和以前一样，现在就开始仿造HashMap里面的一部分方法吧。

## size()
返回此映射中的键-值映射关系数。
```java
private int size;

/**
* 返回此映射中的键-值映射关系数。
* 这个方法最简单了，直接定一个全局的size变量
* 然后直接在size方法中获取即可
* @return
*/
public int size() {

  return this.size;
}
```

## put(K key, V value)

在此映射中关联指定值与指定键。

这个方法会在代码中将注释写清晰一点。当需要`put`的时候，肯定就跟元素挂钩了，那么我们说过`hashmap`是数组+链表组成的。

现在第一步需要有一个数组了，首先在创建数组之前我们都知道`hashmap`是键（`key`）值（`value`）队的形式储存的， 这个时候就需要创建一个储存`key`和`value`的类，这里使用内部类。
```java
import java.util.Objects;
/**
* @author dcc
* 自定义hashmap,暂时按照自己的思路写个最简单的HashMap,适合新手。
*/
public class MyHashMap<K,V> {
  /**
   * 元素的总个数
   */
  private int size;
  /**
   * 数组默认长度
   */
  private static final int DEFAULT_LENGTH = 4;
  /**
   * 储存元素的数组
   */
  private Entry[] entries;
  /**
   * 默认构造
   */
  public MyHashMap() {
      this(DEFAULT_LENGTH);
  }
  /**
   * 有参构造
   * @param defaultLength
   */
  public MyHashMap(int defaultLength) {
      this.entries = new Entry[defaultLength];
  }

  /**
   * 返回此映射中的键-值映射关系数。
   * 这个方法最简单了，直接定一个全局的size变量
   * 然后直接在size方法中获取即可
   * @return
   */
  public int size() {
      return this.size;
  }

  /**
   * 当需要put的时候，肯定就跟元素挂钩了
   * 那么我们说过hashmap是数组+链表组成的。
   * 现在第一步需要有一个数组了
   * 首先在创建数组之前我们都知道hashmap是键（key）值（value）队的形式储存的
   * 这个时候就需要创建一个储存key和value的类，这里使用内部类
   * @param key
   * @param value
   * @return
   */
  public V put(K key, V value) {
      /**
       * 创建需要添加的元素
       */
      Entry<K,V> entry = new Entry<>(key,value);
      /**
       * 添加元素之前需要有一个数组，那么需要在全局定一个内部类的数组
       * 现在需要使用算法(key.hashcode()%entries.length)算出数据存在那个索引下。
       */
      int index = this.hash(key);
      /**
       * 判断数组该下标下是否有元素,如果有那么在判断是否key值一致
       * 一致则覆盖，否则就添加
       */
      if (Objects.isNull(this.entries[index])) {
          //表示没有数据，可以直接保存
          this.entries[index] = entry;
          this.size++;//这里表示新增一个元素
      }
      else
      {
          //添加到链表了
          this.entries[index].put(entry);
      }
      return value;
  }

  private int hash(K key) {
      return key.hashCode() % this.entries.length;
  }

  /**
   * 这是一个成员内部类
   * 储存hashmap元素的key和value
   */
  private class Entry<K,V> {
      /**
       * key 键
       */
      private K key;
      /**
       * value 值
       */
      private V value;
      /**
       * hash码,比较key是否重复使用
       */
      private long hash;
      /**
       * 数组+链表,所以就需要提供下个节点
       */
      private Entry<K,V> next;

      private Entry() {
      }

      /**
       * 构造器
       * @param key
       * @param value
       */
      private Entry(K key, V value) {
          this.key = key;
          this.value = value;
          this.hash = this.key.hashCode();
      }

      /**
       * 新增
       * @param entry
       */
      private void put(Entry<K, V> entry) {
          /**
           * 表示已经有了那么需要判断key是否一致
           */
          if (Objects.equals(this.key, entry.key) && this.hash == entry.hash) {
              //表示一致直接替换value值即可
              this.value = entry.value;
          } else {//表示可以新增了
              /**
               * 如果next中没有元素直接赋值了
               */
              if (Objects.isNull(this.next)) {
                  this.next = entry;
                  size++;//这里也表示为新增一个元素
              } else {//递归
                  this.next.put(entry);
              }
          }
      }
  }
}
```

## isEmpty()

如果此映射不包含键-值映射关系，则返回 `true`。
```java
/**
* 判断是否有元素
* @return
*/
public boolean isEmpty() {
  return this.size > 0;
}
```

## get(Object key)

返回指定键所映射的值；如果对于该键来说，此映射不包含任何映射关系，则返回 `null`。

### 外部类：
```java
/**
* 返回指定键所映射的值；如果对于该键来说，此映射不包含任何映射关系，则返回 null。
* @param key
* @return
*/
public V get(K key) {
  int index = this.hash(key);
  if (Objects.isNull(this.entries[index])) {
      return null;
  }
  Entry<K,V> entry = this.entries[index].get(key);
  return entry == null ? null : entry.value;
}
```

### 内部类
```java
/**
* 获取数据
* @param key
* @return
*/
private Entry<K,V> get(K key) {
  //如果key都匹配直接返回当前value
  if (Objects.equals(key, this.key)) {
      return this;
  }
  //如果当前key不匹配则判断next节点是否为null，如果为null直接返回null
  if (Objects.isNull(this.next)) {
      return null;
  }
  //以上都不匹配就递归
  return this.next.get(key);
}


containsKey(K key)

          如果此映射包含对于指定键的映射关系，则返回 true。
/**
* 如果此映射包含对于指定键的映射关系，则返回 true。
* @param key
* @return
*/
public boolean containsKey(K key) {
  int index = this.hash(key);
  return this.entries[index].get(key) != null;
}
```

以上都是写了大部分了，以上可能有问题希望大家看出来了多多指正, 其它方法了留在下一节再讲。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")