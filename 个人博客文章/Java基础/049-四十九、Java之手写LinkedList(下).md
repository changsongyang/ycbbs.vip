## public T get(int index)

得到指定位置的节点。
昨天粘贴代码掉了一部分改造的代码，所以今天重新把这个代码贴一下。
```java
/**
* 得到指定位置的节点,
* @param index
* @return
*/
public T get(int index) {
  //下标越界提醒
  arrayIndexOutOfBoundsException(index);
  /**
   * 以上条件都不满足，那么就开始递归查询
   * 为什么大家都说LinkedList的get效率低呢？
   * 主要是因为get的时候需要逐个遍历来匹配获取数据，这样效率就低很多 了。
   * ArrayList是直接操作数组的，get也是直接在数组里面根据索引获取的。
   *
   * 因为linkedList是没有index属性的，所以需要一个临时变量,那么直接传入一个0进入方法即可
   * 因为需要逐个递归需要和索引比配上才能找到对应的元素
   * 根据index获取对应的节点
   * 这里改造
   */
  Node<T> node = this.first.get(index,0);
  return node.data;
}
/**
* 下标越界异常
* @param index
*/
private void arrayIndexOutOfBoundsException(int index){
  /**
   * size==0表示链表中没有数据,直接抛出异常
   * index>=size或者index<0，表示index索引已经越界，直接抛出异常
   */
  if (size == 0 || index >= size || index < 0)
  {
      throw new ArrayIndexOutOfBoundsException("下标越界!!!");
  }
}


/**
* 获取index索引的节点
* @param index
* @return
*/
private Node<T> get(int index,int tempIndex) {
  /**
   * 匹配就直接返回了
   */
  if (index == tempIndex) {
      return this;
  }
  /**
   * 如果传入索引和临时索引不匹配将递归到下一个节点在进行匹配
   * 以此类推
   */
  return this.next.get(index,++tempIndex);
}
```

以上代码就不再做解释了，上2节已经说过了！

## public T getFirst()

得到链表第一个节点的对象
```java
/**
* 获取第一个节点
* @return
*/
public T getFirst() {
 /**
  * 只要first值不为null就直接返回了
  */
 if (Objects.isNull(this.first)) {
     throw new NullPointerException("空指针异常，因为容器里还没有任何元素");
 }
 return this.first.data;
}
MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
System.out.println(list.getFirst());

结果：a
```

## public Object getLast()

得到链表最后一个节点的对象
```java
/**
* 得到链表最后一个节点的对象
* @return
*/
public T getLast() {
  /**
   * 只要last值不为null就直接返回了
   */
  if (Objects.isNull(this.last)) {
      throw new NullPointerException("空指针异常，因为容器里还没有任何元素");
  }
  return this.last.data;
}
MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
System.out.println(list.getLast());
结果：d
```
## int indexOf(T t)

返回节点对象element在链表中首次出现的位置，如果链表中无此节点的对象则返回-1
```java
/**
* 返回节点对象element在链表中首次出现的位置，如果链表中无此节点的对象则返回-1
* @param t
* @return
*/
public int indexOf(T t) {
  /**
   * 首先判断first==null。如果为null那么表示没有元素
   * 在判断first==t。如果为t直接返回
   */
  if (Objects.isNull(this.first)) {
      return -1;
  }
  else {
      /**
       * 如果链表中有数据就开始递归匹配
       */
      return this.first.index(t,0);
  }
}
```

## 内部类方法
```java
/**
* 查询
* @param t
* @param index
* @return
*/
private int index(T t, int index) {
  /**
   * 如果值相当就直接返回
   */
  if (this.data == t) {
      return index;
  }
  /**
   * 当递归前判断一下this.next==null，如果为null就不需要在递归了
   * 表示没有该元素，直接返回-1
   */
  else if(Objects.isNull(this.next)) {
      return -1;
  }
  else {
      return this.next.index(t,++index);
  }
}

//调用执行

MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
System.out.println(list.indexOf("c"));

结果：2
```

## public int lastIndexOf(T t)

返回节点对象`element`在链表中最后出现的位置，如果链表中无此节点的对象则返回-1。

这个时候只需要从后往前面找了,`last`就是最后一个元素，`size-1`就是最后一个元素的索引了这样就比较好找多了
```java
/**
* 返回节点对象t在链表中最后出现的位置，如果链表中无此节点的对象则返回-1
* @param t
* @return
*/
public int lastIndexOf(T t) {
  /**
   * 这个时候只需要从后往前面找了
   * last就是最后一个元素，size-1就是最后一个元素的索引了
   * 这样就比较好找多了
   */
  if (Objects.isNull(this.last)) {
      return -1;
  }
  return this.last.lastIndexOf(t,--this.size);

}
```
## 
## 内部类递归方法
```java
/**
* 递归链表上节点方式查找
* @param t
* @param index
* @return
*/
private int lastIndexOf(T t, int index) {
  if (this.data == t) {
      return index;
  }
  /**
   * 表示没有上个节点了，就是没有元素了直接返回-1
   */
  else if (Objects.isNull(this.prev) || index - 1 < 0) {
      return -1;
  }
  /**
   * 递归继续向上查找
   */
  else {
      return this.prev.lastIndexOf(t, --index);
  }
}
//调用
MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("b");
list.add("d");
System.out.println(list.lastIndexOf("b"));

结果：3
```

## public Object set(int index,T t)

将当前链表`index`位置节点中的对象替换成参数`element`指定的对象，返回被替换对象。
```java
/**
* 将当前链表index位置节点中的对象替换成参数t指定的对象，返回被替换对象
* @param index
* @param t
* @return
*/
public T set(int index,T t) {
  /**
   * 获取需要替换的node节点，然后直接替换节点的data即可
   */
  Node<T> node = this.first.get(index,0);
  //保存被替换的值
  T temp = node.data;
  //替换新的值
  node.data = t;
  return temp;
}
MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
System.out.println("old:"+list.set(2,"aaaa"));
System.out.println("new:"+list.get(2));

结果：
old:c
new:aaaa
```
## public boolean contains(T t)

判断链表节点对象中是否含有t。
```java
/**
* 判断链表节点对象中是否含有t
* @param t
* @return
*/
public boolean contains(T t) {
  return this.indexOf(t) != -1;
}

MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
System.out.println("new:"+list.contains("c"));

结果：
new:true
```
## public Iterator<E> iterator()

返回在此列表中的元素上进行迭代的迭代器（按适当顺序）。此实现仅返回列表的一个列表迭代器。

这个就简单了，我们回顾一下匿名内部类，这里就使用匿名内部类写下。
```java
/**
* 返回在此列表中的元素上进行迭代的迭代器（按适当顺序）。
* 此实现仅返回列表的一个列表迭代器。
* @return
*/
public Iterator<T> iterator() {
  /**
   *  这里我们回顾一下匿名内部类
   */
      return new Iterator<T>() {
          private int index;
          @Override
          public boolean hasNext() {
              return this.index < size;
          }

          @Override
          public T next() {
              return get(index++);
          }
      };
}


MyLinkedList<String> list = new MyLinkedList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

结果：
a
b
c
d
```

## MyLinkedList源码
```java
import java.util.Iterator;
import java.util.Objects;

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
     this.size++;//表示元素的总个数
     return true;
  }

  /**
   * 得到指定位置的节点,
   * @param index
   * @return
   */
  public T get(int index) {
      //下标越界提醒
      arrayIndexOutOfBoundsException(index);
      /**
       * 以上条件都不满足，那么就开始递归查询
       * 为什么大家都说LinkedList的get效率低呢？
       * 主要是因为get的时候需要逐个遍历来匹配获取数据，这样效率就低很多 了。
       * ArrayList是直接操作数组的，get也是直接在数组里面根据索引获取的。
       *
       * 因为linkedList是没有index属性的，所以需要一个临时变量,那么直接传入一个0进入方法即可
       * 因为需要逐个递归需要和索引比配上才能找到对应的元素
       */
      /**
       * 根据index获取对应的节点
       * 这里改造
       */
      Node<T> node = this.first.get(index,0);
      return node.data;
  }

  /**
   * 向链表表头添加一个新节点，该节点中的数据是参数element指定的对象
   * @param t
   */
  public void addFirist(T t) {
      Node<T> newNode = new Node<>(t);
      /**
       * 第一步需要判断first是否为null，为null就添加
       * 在这里需要将oldFirst节点也就是老的first节点，添加到新的first节点上
       */
      if (this.first != null) {
          /**
           * 首先将first的引用保存在一个临时变量oldFirst中
           */
          Node oldFirst = this.first;
          /**
           * 将这个节点存放在first节点上
           */
          this.first = newNode;
          this.first.next = oldFirst;
          /**
           * 然后将first的引用赋值给oldFirst.prev就好了
           */
          oldFirst.prev = this.first;
          if (this.last == null) {
              this.last = this.first;
          }
      } else {//表示为第一次添加
          //第一个节点
          this.first = newNode;
          //第一次第一个节点和最后一个节点相同
          this.last = this.first;
      }
      this.size++;
  }

  /**
   *  向链表指定位置添加一个新节点，该节点中的数据是参数element指定的对象
   * @param index
   * @param t
   */
  public void add(int index,T t) {
      //下标越界提醒
      arrayIndexOutOfBoundsException(index);
      /**
       * 1、获取要插入的节点和节点位置。
       */
      Node<T> oldNode = first.get(index,0);
      /**
       * 每次在添加的时候就创建一个节点
       */
      final Node<T> newNode = new Node<>(t);
      /**
       * 如果oldNode.prev为null就表示为first节点
       * 反过来就是不等于null的话那么就需要将新节点(newNode.prev)的引用值等于老节点oldNode.prev
       * =oldNode.prev
       */
      if (oldNode.prev != null) {
          newNode.prev = oldNode.prev;
          //并且需要将newNode节点的上级(prev)节点的next值等于newNode
          newNode.prev.next = newNode;
      }
      //然后改变oldNode.prev=newNode
      oldNode.prev = newNode;
      //将oldNode节点引用存放到新插入的节点next下
      newNode.next = oldNode;

      //元素总量增加1
      this.size++;
  }

  /**
   * 向链表表尾添加一个新节点，该节点中的数据是参数 t 指定的对象
   * @param t
   */
  public void addLast(T t) {
      /**
       * 这个就简单了，外部类直接就有最有一个节点的引用，直接互换就可以了。
       */
      Node<T> oldLast = this.last;
      /**
       * 每次在添加的时候就创建一个节点
       */
      final Node<T> newNode = new Node<>(t);
      //直接互换位置即可
      this.last = newNode;
      this.last.prev = oldLast;
      oldLast.next = this.last;
      this.size++;
  }

  /**
   * 删除第一个节点并返回这个节点中的对象
   * @return
   */
  public T removeFirst() {
      /**
       * 如果first直为空，表示没有数据可删除,直接返回null
       */
      if (this.first == null) {
          return null;
      }
      Node<T> oldFirst = this.first;
      /**
       * 直接将第二个节点替换成first节点
       * 然后将first节点的prev值设置为null就好了
       * 不过首先需要判断一下first.next是否为null
       * 如果为null就表示只有一个元素
       */
      if (this.first.next != null) {
          this.first = this.first.next;
          this.first.prev = null;
      }
      this.size--;
      return oldFirst.data;
  }

  /**
   * 删除最后一个节点并返回这个节点中的对象
   * @return
   */
  public T removeLast() {
      /**
       * 如果last直为null，表示没有数据可删除,直接返回null
       */
      if (this.last == null) {
          return null;
      }
      Node<T> oldLast = this.last;
      /**
       * 直接将倒数第二个节点替换成last节点
       * 然后将last节点的next值设置为null就好了
       * 首先也的判断一下last的上一个节点是否为null
       * 如果是那么就表示只有数据
       */
      this.last = this.last.prev;
      this.last.next = null;
      this.size--;
      return oldLast.data;
  }

  /**
   * 删除指定位置的节点
   * @param index
   * @return
   */
  public T remove(int index) {
      //下标越界提醒
      arrayIndexOutOfBoundsException(index);
      /**
       * 获取要删除的节点保存到临时变量中
       */
      Node<T> removeNode = this.first.get(index,0);
      /**
       * 删除中间节点
       * 如果节点prev不为null表示不是first节点
       * 如果节点next不为null表示不是last节点
       * 如果找到了直接互换perv和next即可
       */
      if (removeNode.prev != null && removeNode.next != null) {
          removeNode.prev.next = removeNode.next;
          removeNode.next.prev = removeNode.prev;
      }
      /**
       * 删除first节点
       * 表示为first节点
       * 直接将第二个节点移位到first节点
       * 并且将first.prev=null即可
       */
      else if (removeNode.prev == null && removeNode.next != null) {
          this.first = removeNode.next;
          this.first.prev = null;
      }
      /**
       * 删除last节点
       * 直接将倒数第二个节点next=null即可
       */
      else if (removeNode.prev != null) {
          this.last = removeNode.prev;
          this.last.next = null;
      }
      this.size--;
      return removeNode.data;
  }

  /**
   * 获取第一个节点
   * @return
   */
  public T getFirst() {
      /**
       * 只要first值不为null就直接返回了
       */
      if (Objects.isNull(this.first)) {
          throw new NullPointerException("空指针异常，因为容器里还没有任何元素");
      }
      return this.first.data;
  }

  /**
   * 得到链表最后一个节点的对象
   * @return
   */
  public T getLast() {
      /**
       * 只要last值不为null就直接返回了
       */
      if (Objects.isNull(this.last)) {
          throw new NullPointerException("空指针异常，因为容器里还没有任何元素");
      }
      return this.last.data;
  }

  /**
   * 返回节点对象t在链表中首次出现的位置，如果链表中无此节点的对象则返回-1
   * @param t
   * @return
   */
  public int indexOf(T t) {
      /**
       * 首先判断first==null。如果为null那么表示没有元素
       * 在判断first==t。如果为t直接返回
       */
      if (Objects.isNull(this.first)) {
          return -1;
      }
      else {
          /**
           * 如果链表中有数据就开始递归匹配
           */
          return this.first.index(t,0);
      }
  }

  /**
   * 返回节点对象t在链表中最后出现的位置，如果链表中无此节点的对象则返回-1
   * @param t
   * @return
   */
  public int lastIndexOf(T t) {
      /**
       * 这个时候只需要从后往前面找了
       * last就是最后一个元素，size-1就是最后一个元素的索引了
       * 这样就比较好找多了
       */
      if (Objects.isNull(this.last)) {
          return -1;
      }
      return this.last.lastIndexOf(t,--this.size);
  }

  /**
   * 将当前链表index位置节点中的对象替换成参数t指定的对象，返回被替换对象
   * @param index
   * @param t
   * @return
   */
  public T set(int index,T t) {
      /**
       * 获取需要替换的node节点，然后直接替换节点的data即可
       */
      Node<T> node = this.first.get(index,0);
      //保存被替换的值
      T temp = node.data;
      //替换新的值
      node.data = t;
      return temp;
  }

  /**
   * 判断链表节点对象中是否含有t
   * @param t
   * @return
   */
  public boolean contains(T t) {
      return this.indexOf(t) != -1;
  }

  /**
   * 下标越界异常
   * @param index
   */
  private void arrayIndexOutOfBoundsException(int index){
      /**
       * size==0表示链表中没有数据,直接抛出异常
       * index>=size或者index<0，表示index索引已经越界，直接抛出异常
       */
      if (size == 0 || index >= size || index < 0)
      {
          throw new ArrayIndexOutOfBoundsException("下标越界!!!");
      }
  }

  /**
   * 获取数组长度
   * @return
   */
  public int size(){
     return this.size;
  }

  /**
   * 返回在此列表中的元素上进行迭代的迭代器（按适当顺序）。
   * 此实现仅返回列表的一个列表迭代器。
   * @return
   */
  public Iterator<T> iterator() {
      /**
       *  这里我们回顾一下匿名内部类
       */
          return new Iterator<T>() {
              private int index;
              @Override
              public boolean hasNext() {
                  return this.index < size;
              }

              @Override
              public T next() {
                  return get(index++);
              }
          };
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
      /**
       * 获取index索引的节点
       * @param index
       * @return
       */
      private Node<T> get(int index,int tempIndex) {
          /**
           * 匹配就直接返回了
           */
          if (index == tempIndex) {
              return this;
          }
          /**
           * 如果传入索引和临时索引不匹配将递归到下一个节点在进行匹配
           * 以此类推
           */
          return this.next.get(index,++tempIndex);
      }

      /**
       * 查询
       * @param t
       * @param index
       * @return
       */
      private int index(T t, int index) {
          /**
           * 如果值相当就直接返回
           */
          if (this.data == t) {
              return index;
          }
          /**
           * 当递归前判断一下this.next==null，如果为null就不需要在递归了
           * 表示没有该元素，直接返回-1
           */
          else if(Objects.isNull(this.next)) {
              return -1;
          }
          else {
              return this.next.index(t,++index);
          }
      }

      /**
       * 递归链表上节点方式查找
       * @param t
       * @param index
       * @return
       */
      private int lastIndexOf(T t, int index) {
          if (this.data == t) {
              return index;
          }
          /**
           * 表示没有上个节点了，就是没有元素了直接返回-1
           */
          else if (Objects.isNull(this.prev) || index - 1 < 0) {
              return -1;
          }
          /**
           * 递归继续向上查找
           */
          else {
              return this.prev.lastIndexOf(t, --index);
          }
      }
  }
}
```
以上就是MyLinkedList的全部源码，基本LinkedList的常用方法都仿造到了。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")