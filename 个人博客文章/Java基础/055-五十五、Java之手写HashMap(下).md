## entrySet() 

返回此映射所包含的映射关系的 `Set` 视图。

该方法实现起来还是比较麻烦的。
1.由于本方法需要返回一个`Set<Map.Entry<K,V>>`类型，所以需要借用一下`jdk`中的`TreeSet`集合。
```java
/**
* 仿造entrySet方法需要定义一个set集合
*/
private Set<Map.Entry<K,V>> entrySet;
```
2.在外部类的构造方法中实例化一个`TreeHast`集合，现在改造外部类的有参构造方法。
```java
/**
* 有参构造
* @param defaultLength
*/
public MyHashMap(int defaultLength) {
  this.entries = new Entry[defaultLength];
  //这里需要先实例化一个hashset集合
  this.entrySet = new TreeSet<>();
}
```
3.定一个`public Set<Map.Entry<K,V>>`` entrySet()`方法
```java
/**
*  返回此映射所包含的映射关系的 Set 视图。
* @return
*/
public Set<Map.Entry<K,V>> entrySet() {
  return this.entrySet;
}
```
4.那么上面只是定义和返回了`set`集合，现在该怎么将数据添加到`set`中呢，现在使用一个偷懒的办法。纯属自己的想法。

就是每次`put`的时候，顺便将元素添加到`set`集合中就可以了。那么现在需要改造的地方太多了，首先改造外部类的`put`方法。
在普通添加的时候加上这句代码 `entrySet.add(entry);`//在put的时候顺带将entry添加到set中
```java
/**
* 新增
* @param entry
*/
private void put(Entry<P,Q> entry) {
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
          //在put的时候顺带将entry添加到set中
          entrySet.add(entry);
          size++;//这里也表示为新增一个元素
      } else {//递归
          this.next.put(entry);
      }
  }
}
```

5.改造内部类的类型。
    a.需要将内部类的泛型类型继承外部类的泛型类型
```java
private final class Entry<P extends K,Q extends V>
```
不继承的话，内部类的K,V类型会和外部类的K,V重叠，因为作用域的关系会自动覆盖外部类的K,V。

b. 需要实现Comparable接口的public int compareTo(Object o)方法，因为使用TreeSet的排序的时候用到。
```java
/**
* 因为使用的TreeSet集合所以需要实现Comparable的compareTo方法
* @param o
* @return
*/
@Override
public int compareTo(Entry<P, Q> o) {
  return (Objects.equals(this.key, o.key) && this.hash == o.hash) ? 0 : 1;
}
```
c.需要实现Map.Entry<K,V>接口的
                    public P getKey()
                    public Q getValue()
                    public Q setValue(V value)
        这3个方法必须实现。因为迭代的时候需要使用到。
```java
/**
* 必须实现Map.Entry<K,V>的方法
* 使用迭代器时会使用到
* @return
*/
@Override
public P getKey() {
  return this.key;
}

@Override
public Q getValue() {
  return this.value;
}

@Override
public Q setValue(V value) {
  return (this.value = (Q) value);
}

```

d.需要改造内部类的所有方法的泛型类型，以及在`put`方法中添加元素到`set`集合中。添加这句代码 `entrySet.add(entry);`，并且需要把参数的泛型类型修改为内部类的泛型类型P,Q。
```java
/**
* 新增
* @param entry
*/
private void put(Entry<P,Q> entry) {
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
          //在put的时候顺带将entry添加到set中
          entrySet.add(entry);
          size++;//这里也表示为新增一个元素
      } else {//递归
          this.next.put(entry);
      }
  }
}
```
这里基本上重点要改造的方法就改造完了，还有其它的地方改造就是内部类的类型了，这里就直接看源码吧！！！
```java
**
* 这是一个成员内部类
* 储存hashmap元素的key和value
* 要仿造entrySet方法需要实现 Comparable, Map.Entry<K,V>
*/
private final class Entry<P extends K,Q extends V> implements Comparable<Entry<P,Q>>, Map.Entry<K,V> {
  /**
   * key 键
   */
  private P key;
  /**
   * value 值
   */
  private Q value;
  /**
   * hash码,比较key是否重复使用
   */
  private long hash;
  /**
   * 数组+链表,所以就需要提供下个节点
   */
  private Entry<P,Q> next;

  /**
   * 构造器
   * @param key
   * @param value
   */
  private Entry(P key, Q value) {
      this.key = key;
      this.value = value;
      this.hash = this.key.hashCode();
  }

  /**
   * 新增
   * @param entry
   */
  private void put(Entry<P,Q> entry) {
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
              //在put的时候顺带将entry添加到set中
              entrySet.add(entry);
              size++;//这里也表示为新增一个元素
          } else {//递归
              this.next.put(entry);
          }
      }
  }
  /**
   * 获取数据
   * @param key
   * @return
   */
  private Entry<P,Q> get(P key) {
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

  /**
   * 必须实现Map.Entry<K,V>的方法
   * 使用迭代器时会使用到
   * @return
   */
  @Override
  public P getKey() {
      return this.key;
  }

  @Override
  public Q getValue() {
      return this.value;
  }

  @Override
  public Q setValue(V value) {
      return (this.value = (Q) value);
  }

  /**
   * 因为使用的TreeSet集合所以需要实现Comparable的compareTo方法
   * @param o
   * @return
   */
  @Override
  public int compareTo(Entry<P, Q> o) {
      return (Objects.equals(this.key, o.key) && this.hash == o.hash) ? 0 : 1;
  }
```
测试代码
```java
MyHashMap<String,String> map = new MyHashMap<>();
map.put("1","zhangsan1");
map.put("2","zhangsan2");
map.put("3","zhangsan3");
map.put("4","zhangsan4");
map.put("5","zhangsan5");
map.put("6","zhangsan6");
map.put("7","zhangsan7");
map.put("8","zhangsan8");
System.out.println(map.containsKey("100"));
System.out.println(map.get("6"));
System.out.println(map.isEmpty());
System.out.println(map.size());
System.out.println("========================");
/**
* 使用iterator遍历
* */
Set<Map.Entry<String, String>> entries = map.entrySet();
Iterator<Map.Entry<String, String>> iterator = entries.iterator();
while (iterator.hasNext()) {
  Map.Entry<String,String> entry = iterator.next();
  System.out.print(entry.getKey() + "\t");
  System.out.println(entry.getValue() + "\t");
}
```
测试结果：
```java
1  zhangsan1  
2  zhangsan2  
3  zhangsan3  
4  zhangsan4  
5  zhangsan5  
6  zhangsan6  
7  zhangsan7  
8  zhangsan8
```

## keySet() 
返回此映射中所包含的键的 `Set` 视图。
这里使用了一个匿名内部类。

```java
/**
* 返回一个set类型，该方法使用了一个匿名内部类
* 并且重写了迭代器
* @return
*/
public Set<K> keySet(){
  return new AbstractSet<K>() {
      @Override
      public Iterator<K> iterator() {
          return new Iterator<K>() {
              private Iterator<Map.Entry<K,V>> iterator = entrySet.iterator();

              @Override
              public boolean hasNext() {
                  return iterator.hasNext();
              }

              @Override
              public K next() {
                  return iterator.next().getKey();
              }

              @Override
              public void remove() {
                  iterator.remove();
              }
          };
      }

      /**
       * 数据的长度
       * @return
       */
      @Override
      public int size() {
          return size;
      }
  };
}

//测试

MyHashMap<String,String> map = new MyHashMap<>();
map.put("1","zhangsan1");
map.put("2","zhangsan2");
map.put("3","zhangsan3");
map.put("4","zhangsan4");
map.put("5","zhangsan5");
map.put("6","zhangsan6");
map.put("7","zhangsan7");
map.put("8","zhangsan8");
/**
* 使用iterator遍历
* */
Set<String> entries = map.keySet();
Iterator<String> iterator = entries.iterator();
while (iterator.hasNext()) {
  System.out.print(iterator.next() + "\t");
}

//结果

1  2  3  4  5  6  7  8
```
## values()
返回此映射所包含的值的 `Collection` 视图。
这个方法与entrySet方法类似。需要一个集合 `Collection` 也需要在 `put` 时直接添加到集合中就好了。
```java
/**
* 仿造 values方法时使用，和仿造entryset方法原理一样
* 也是在put方法中直接将value存入即可.
*/
private Collection<V> list;
```
改造构造方法
```java
/**
* 有参构造
* @param defaultLength
*/
public MyHashMap(int defaultLength) {
  this.entries = new Entry[defaultLength];
  //这里需要先实例化一个hashset集合
  this.entrySet = new TreeSet<>();
  //这里需要先实例化一个ArrayList
  this.list = new ArrayList<>();
}
```

在内部类和外部类的`put`方法中添加元素是加上一下代码，代码位置与`entrySet.add`的代码位置一样。

```java
//仿造values方法，将value值直接储存到list集合中
this.list.add(entry.value);


/**
* 仿造这个方法也可以使用偷懒的方式了
* @return
*/
public Collection<V> values() {
  //在put的同时就储存了这个时候直接就返回了
  return this.list;
}

//测试代码

MyHashMap<String,String> map = new MyHashMap<>();
map.put("1","zhangsan1");
map.put("2","zhangsan2");
map.put("3","zhangsan3");
map.put("4","zhangsan4");
map.put("5","zhangsan5");
map.put("6","zhangsan6");
map.put("7","zhangsan7");
map.put("8","zhangsan8");

/**
* 使用iterator遍历
* */
Collection<String> entries = map.values();
Iterator<String> iterator = entries.iterator();
while (iterator.hasNext()) {
  System.out.print(iterator.next() + "\t");
}

//测试结果：

zhangsan1  zhangsan2  zhangsan3  zhangsan4  zhangsan5  zhangsan6  zhangsan7  zhangsan8
```

## containsValue(K value)
如果此映射将一个或多个键映射到指定值，则返回 `true`。
```java
/**
* 如果此映射将一个或多个键映射到指定值，则返回 true。
* @param value
* @return
*/
public boolean containsValue(V value) {
  return this.list.contains(value);
}

//测试代码

MyHashMap<String,String> map = new MyHashMap<>();
map.put("1","zhangsan1");
map.put("2","zhangsan2");
map.put("3","zhangsan3");
map.put("4","zhangsan4");
map.put("5","zhangsan5");
map.put("6","zhangsan6");
map.put("7","zhangsan7");
map.put("8","zhangsan8");

System.out.println(map.containsValue("zhangsan6"));


输出结果：true
```

## MyHashMap源码

```java
import java.util.*;

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
  private Entry<K,V>[] entries;

  /**
   * 仿造entrySet方法需要定义一个set集合
   */
  private Set<Map.Entry<K,V>> entrySet;

  /**
   * 仿造 values方法时使用，和仿造entryset方法原理一样
   * 也是在put方法中直接将value存入即可.
   */
  private Collection<V> list;
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
      //这里需要先实例化一个hashset集合
      this.entrySet = new TreeSet<>();
      //这里需要先实例化一个ArrayList
      this.list = new ArrayList<>();
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
   * 判断是否有元素
   * @return
   */
  public boolean isEmpty() {
      return this.size > 0;
  }

  /**
   * 返回指定键所映射的值；如果对于该键来说，此映射不包含任何映射关系，则返回 null。
   * @param key
   * @return
   */
  public V get(K key) {
      int index = this.hash(key);
      if (Objects.isNull(this.entries[index])) {
          throw new NullPointerException("没有找到数据");
      }
      //获取节点
      Entry<K,V> entry = this.entries[index].get(key);
      return entry == null ? null : entry.value;
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
          //在put的时候顺带将entry插入到set中
          this.entrySet.add(entry);
          //仿造values方法，将value值直接储存到list集合中
          this.list.add(entry.value);

          this.size++;//这里表示新增一个元素
      }
      else
      {
          //添加到链表了
          this.entries[index].put(entry);
      }
      return value;
  }

  /**
   * 数组下标算法
   * @param key
   * @return
   */
  private int hash(K key) {
      return key.hashCode() % this.entries.length;
  }

  /**
   * 如果此映射包含对于指定键的映射关系，则返回 true。
   * @param key
   * @return
   */
  public boolean containsKey(K key) {
      int index = this.hash(key);
      return this.entries[index].get(key) != null;
  }

  /**
   * 如果此映射将一个或多个键映射到指定值，则返回 true。
   * @param value
   * @return
   */
  public boolean containsValue(V value) {
      return this.list.contains(value);
  }

  /**
   *  返回此映射所包含的映射关系的 Set 视图。
   * @return
   */
  public Set<Map.Entry<K,V>> entrySet() {
      return this.entrySet;
  }

  /**
   * 返回一个set类型，该方法使用了一个匿名内部类
   * 并且重写了迭代器
   * @return
   */
  public Set<K> keySet(){
      return new AbstractSet<K>() {
          @Override
          public Iterator<K> iterator() {
              return new Iterator<K>() {
                  private Iterator<Map.Entry<K,V>> iterator = entrySet.iterator();

                  @Override
                  public boolean hasNext() {
                      return iterator.hasNext();
                  }

                  @Override
                  public K next() {
                      return iterator.next().getKey();
                  }

                  @Override
                  public void remove() {
                      iterator.remove();
                  }
              };
          }

          /**
           * 数据的长度
           * @return
           */
          @Override
          public int size() {
              return size;
          }
      };
  }

  /**
   * 仿造这个方法也可以使用偷懒的方式了
   * @return
   */
  public Collection<V> values() {
      //在put的同时就储存了这个时候直接就返回了
      return this.list;
  }

  /**
   * 这是一个成员内部类
   * 储存hashmap元素的key和value
   * 要仿造entrySet方法需要实现 Comparable, Map.Entry<K,V>
   */
  private final class Entry<P extends K,Q extends V> implements Comparable<Entry<P,Q>>, Map.Entry<K,V> {
      /**
       * key 键
       */
      private P key;
      /**
       * value 值
       */
      private Q value;
      /**
       * hash码,比较key是否重复使用
       */
      private long hash;
      /**
       * 数组+链表,所以就需要提供下个节点
       */
      private Entry<P,Q> next;

      /**
       * 构造器
       * @param key
       * @param value
       */
      private Entry(P key, Q value) {
          this.key = key;
          this.value = value;
          this.hash = this.key.hashCode();
      }

      /**
       * 新增
       * @param entry
       */
      private void put(Entry<P,Q> entry) {
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
                  //在put的时候顺带将entry添加到set中
                  MyHashMap.this.entrySet.add(entry);

                  //仿造values方法，将value值直接储存到list集合中
                  MyHashMap.this.list.add(entry.value);

                  size++;//这里也表示为新增一个元素
              } else {//递归
                  this.next.put(entry);
              }
          }
      }
      /**
       * 获取数据
       * @param key
       * @return
       */
      private Entry<P,Q> get(P key) {
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

      /**
       * 必须实现Map.Entry<K,V>的方法
       * 使用迭代器时会使用到
       * @return
       */
      @Override
      public P getKey() {
          return this.key;
      }

      @Override
      public Q getValue() {
          return this.value;
      }

      @Override
      public Q setValue(V value) {
          return (this.value = (Q) value);
      }

      /**
       * 因为使用的TreeSet集合所以需要实现Comparable的compareTo方法
       * @param o
       * @return
       */
      @Override
      public int compareTo(Entry<P, Q> o) {
          return (Objects.equals(this.key, o.key) && this.hash == o.hash) ? 0 : 1;
      }
  }
}
```

## 备注

以上都是 `HashMap` 的方法基本上都模仿玩了还有些继承的方法就不写了，以上可能不是完全按照jdk的源码做的。

主要是让广大初学者知道 `hashmap` 的原理是数组+链表实现的就足够了，好了集合就写到这里了，其它的集合基本上用法都很类似，大家根据`api`来一个个测试就差不多会使用了。


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")