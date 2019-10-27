## MyLinkedList
`jdk`中的 `LinkedList` 的实现原理是使用双向链表实现，我们自定义为了简单适合新手入门链表实现。首先看看我们需要仿造的方法吧。
```javascript
public boolean add(T t)
    向链表末尾添加一个新节点，该节点中的数据是参数element指定的对象

public int size()
   返回链表的长度即节点个数

public Object get(int index)
   得到指定位置的节点

public void addFirist(Tt)
   向链表表头添加一个新节点，该节点中的数据是参数element指定的对象

public void add(int index,T t)
   向链表指定位置添加一个新节点，该节点中的数据是参数element指定的对象

public void addLast(T t)
   向链表表尾添加一个新节点，该节点中的数据是参数element指定的对象

public  Object removeFirst()
   删除第一个节点并返回这个节点中的对象

public  Object removeLast()
   删除最后一个节点并返回这个节点中的对象

public Object remove(int index)
   删除指定位置的节点

public Object getFirst()
   得到链表第一个节点的对象

public Object getLast()
   得到链表最后一个节点的对象

int indexOf(T t)
   返回节点对象element在链表中首次出现的位置，如果链表中无此节点的对象则返回-1

public int lastIndexOf(T t)
   返回节点对象element在链表中最后出现的位置，如果链表中无此节点的对象则返回-1

public Object set(int index,T t)
   将当前链表index位置节点中的对象替换成参数element指定的对象，返回被替换对象

public boolean contains(T t)
   判断链表节点对象中是否含有element

```
## AbstractSequentialList继承过来的方法

`public Iterator<E> iterator()`
返回在此列表中的元素上进行迭代的迭代器（按适当顺序）。此实现仅返回列表的一个列表迭代器。

## 单向链表

首先我们来看一张图吧。
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/4/47-1.jpg)

链表其实就是节点的管理，每个节点都会有一个下级节点的引用，也有上一节点的引用，这样就是双向链表了，如上图显示a1的有a0的引用也有a2的引用，以此类推。

## public boolean add(T t)

向链表末尾添加一个新节点，该节点中的数据是参数element指定的对象。

首先要在外部类中添加第一个节点之前会判断有没有first节点，如果没有那么就证明是第一个节点那么这个节点既是first也是last。

刚刚说道了链表就是节点管理，那么节点就是内部类。好了开始实现代码吧！！！注释会在清楚一点。
```java
/**
* @author dcc
*/
public class MyLinkedList<T> {
  /**
   * 链表元素的长度
   */
  private int size;
  /**
   * 链表的第一个节点
   */
  private Node<T> first;

  /**
   * 链表的最后一个节点
   */
  private Node<T> last;

  /**
   *向链表末尾添加一个新节点，该节点中的数据是参数t指定的对象
   * @param t
   * @return
   */
  public boolean add(T t){
     /**
      * 每次在添加的时候就创建一个节点
      */
      final Node<T> node = new Node<>(t);
     /**
      * 当第一次添加的时候就将节点赋值给first,表示第一个节点的引用
      */
     if(this.first == null){
         //第一个节点
         this.first = node;
         //第一次第一个节点和最后一个节点相同
         this.last = this.first;
     }else {
         /**
          *  因为每次add的时候都会add到最后一个节点上，
          *  那么这个时候last需要赋值给previousNode
          *  这个时候previousNode节点就变成倒数第二个节点了
          *  也就是说新的node节点变成last节点了
          *  并且previousNode变成了last节点的上一个节点了
          *  这样就证明LinkedList添加操作效率就比ArrayList操作效率高多了。
          */
         Node<T> previousNode = this.last;
         this.last = node;
         this.last.prev = previousNode;
         previousNode.next = this.last;
     }
     this.size++;
     return true;
  }

  /**
   * 这个内部类就是每次添加的节点
   * @param
   */
  private class Node<T>{
      /**
       * 要添加的数据
       */
      private T data;
      /**
       * 下个节点的引用
       */
      private Node<T> next;
      /**
       * 上一个节点引用
       */
      private Node<T> prev;

      /**
       * 每次构造的时候index加1
       * @param t
       */
      private Node(T t) {
          this.data = t;
      }
  }
}
//测试添加
MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
以上就是一个简单的添加。
```

## public int size()

返回链表的长度即节点个数。
这个就简单了，看代码。
```java
/**
* 获取数组长度
* @return
*/
public int size(){
 return this.size;
}

直接获取全局变量的size属性就可以了。
MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
System.out.println(list.size());

结果：4

```
## public void get(int index)

向链表指定位置添加一个新节点，该节点中的数据是参数element指定的
为什么大家都说 `LinkedList` 的 `get` 效率低呢？

主要是因为 `get` 的时候需要逐个遍历来匹配获取数据，这样效率就低很多 了。
`ArrayList` 是直接操作数组的，`get` 也是直接在数组里面根据索引获取的。

看代码：
```java
/**
* 得到指定位置的节点,
* @param index
* @return
*/
public T get(int index) {
  /**
   * size==0表示链表中没有数据,直接抛出异常
   * index>=size或者index<0，表示index索引已经越界，直接抛出异常
   */
  if (size == 0 || index >= size || index < 0) {
      throw new ArrayIndexOutOfBoundsException("下标越界!!!");
  }
  /**
   * 以上条件都不满足，那么就开始递归查询
   * 为什么大家都说LinkedList的get效率低呢？
   * 主要是因为get的时候需要逐个遍历来匹配获取数据，这样效率就低很多 了。
   * ArrayList是直接操作数组的，get也是直接在数组里面根据索引获取的。
   *
   * 因为linkedList是没有index属性的，所以需要定义一个临时索引
   * 因为需要逐个递归需要和索引比配上才能找到对应的元素
   */
  int tempIndex = 0;
  return first.get(index,tempIndex);
}
```

2.然后在内部类中也添加一个add方法来递归查找
```java
/**
* 获取index索引的节点
* @param index
* @return
*/
private T get(int index,int tempIndex) {
  /**
   * 匹配就直接返回了
   */
  if (index == tempIndex) {
      return this.data;
  }
  /**
   * 如果传入索引和临时索引不匹配将递归到下一个节点在进行匹配
   * 以此类推
   */
  return this.next.get(index,++tempIndex);
}
```
## public void addFirist(T t)

向链表表头添加一个新节点，该节点中的数据是参数t指定的对象

首先将 `first` 的引用保存在一个临时变量 `oldFirst` 中,将这个节点存放在 `first` 节点上,最后需要将 `oldFirst` 节点也就是老的 `first` 节点，添加到新的 `first` 节点上
```java
/**
* 向链表表头添加一个新节点，该节点中的数据是参数element指定的对象
* @param t
*/
public void addFirist(T t) {
  /**
   * 首先将first的引用保存在一个临时变量oldFirst中
   */
  Node oldFirst = this.first;
  /**
   * 将这个节点存放在first节点上
   */
  this.first = new Node<>(t);
  /**
   * 在这里需要将oldFirst节点也就是老的first节点，添加到新的first节点上
   */
  this.first.next = oldFirst;
  /**
   * 然后将first的引用赋值给oldFirst.prev就好了
   */
  oldFirst.prev = this.first;
  this.size++;
}
```

以上就是添加了，这样看出来和 `ArrayList` 的添加有何不同了么吧！！！

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")