## Java 中的ArrayList

`ArrayList`是基于动态数组实现的，数组具有按索引查找的特性，所以访问很快，适合经常查询的数据。其实就是对数组的操作。首先分析一下`ArrayList`的几个常用方法。
```javascript

1. add(E e) ：将指定的元素添加到此列表的尾部。

2. indexOf(Object o) 返回此列表中首次出现的指定元素的索引，或如果此列表不包含元素，则返回 -1。

3. contains(Object o) ： 如果此列表中包含指定的元素，则返回 true。

4. isEmpty() ：如果此列表中没有元素，则返回 true

5. size()：返回此列表中的元素数。

6. toArray()：按适当顺序（从第一个到最后一个元素）返回包含此列表中所有元素的数组

7. get(int index) ：返回此列表中指定位置上的元素。

8. set(int index, E element)： 用指定的元素替代此列表中指定位置上的元素。

9. remove(int index) ： 移除此列表中指定位置上的元素。

10. clear() ： 移除此列表中的所有元素

11. iterator()：返回以恰当顺序在此列表的元素上进行迭代的迭代器。
```

这些大致是ArrayList很常用的方法，我们就一个个的来模仿这些方法，自定义个自己的MyArrayList类。

## 自定义的ArrayList

下面我们来自定义一个`MyArrayList`类。

要自定义 `MyArrayList` 类我们就要关心一下 `ArrayList` 有默认 `Object` 数组 `elementData`，和默认数组的长度 `default_length`，还有一个记录数组元素的数量 `size`，并且至少有2个构造方法，一个无参构造方法和一个含有长度的有参构造方法。

另外还有一个数组长度达到上线之后扩容。动态扩容也是 `ArrayList` 的核心吧。

代码演示

好了废话少说，现在开始代码演示，我不一定完全按照 `ArrayList` 源码来实现。

根据个人思想来实现,尽量减少逻辑判断，并且使用泛型实现，肯定不是最好的，仅供广大初学者参考吧。
```java
public class MyArrayList<T> {
/**
* ArrayList主要就是操作数组所以首先需要定义一个数组
*/
private Object[] elementData;
/**
* 数组的默认长度为3,方便测试
*/
private int defaultLength = 3;
/**
* 记录数组元素的个数
*/
private int size;
/**
* 默认构造
*/
public MyArrayList() {
  /**
   * 调用了有参构造,传入了数组的默认3个空间
   */
  this(3);
}
/**
* @param defaultLength
*/
public MyArrayList(int defaultLength) {
  if (defaultLength <= 0){
      defaultLength = this.defaultLength;
  }
  elementData = new Object[defaultLength];
}
}
```

下面开始实现各个方法了。

## public boolean add(T t) 方法和扩容方法


将指定的元素添加到此列表的尾部。下面的我就直接在代码中写注释讲解了。
```java
/**
* 添加方法,将指定的元素添加到此列表的尾部。
*
* @param t
* @return
*/
public boolean add(T t) {
  /**
   * 添加之前必须检查是否需要扩容
   * 这里为什么需要size+1？当size到了3之后就需要扩容了。
   */
  expansion(size + 1);
  /**
   * size:表示元素的索引,当前索引位置更新值之后需要自增一次,下次添加的时候也是如此
   */
  elementData[size++] = t;
  return true;
}
/**
* 扩容方法
* 写得比较简单
* @param minLength
*/
private void expansion(int minLength) {
  /**
   * 首先判断目前最小长度是否超过默认长度了
   * 我就不弄很复杂的算法了。直接向右移1个位加上目前的长度
   */
   if (minLength > this.defaultLength) {
  /**
   * 得到新的长度
   */
   this.defaultLength = minLength + (this.defaultLength >> 1);
  /**
   * 现在我们需要拷贝一个新的数组出来，可以使用2个方法
   * 1.System.arraycopy
   * 2.Arrays.copyOf：这个方法简单，其实底层也是使用的System.arraycopy方法
   * 拷贝elementData数据的元素到elementData，且为elementData设置一个新长度
   */
   elementData = Arrays.copyOf(elementData, this.defaultLength);
  }
}
```

## public int indexOf(T t)方法

返回此列表中首次出现的指定元素的索引，或如果此列表不包含元素，则返回 -1。
```java
/**
* 返回此列表中首次出现的指定元素的索引，或如果此列表不包含元素，则返回 -1。
*
* @param t
* @return
*/
public int indexOf(T t) {
  for (int i = 0; ; i++) {
      /**
       * 保证退出的判断
       */
      if (i == size) {
          return -1;
      }
      /**
       * 为什么需要判断空呢，因为MyArrayList是可以存储null值的。
       * 并且null值使用equals方法时会报java.lang.NullPointerException异常。
       */
      if (t == null && elementData[i] == null) {
          return i;
      }
      /**
       * 注意这里千万不要使用elementData[i].equals(o)
       * 因为elementData是可以储存null值的
       * 判断时会引发java.lang.NullPointerException异常。
       */
      else if (t.equals(elementData[i])) {
          return i;
      }
  }
}
```

## public boolean contains(T t)方法

如果此列表中包含指定的元素，则返回 true。
这个方法就简单了，基本上就是调用了IndexOf方法。
```java
/**
* 如果此列表中包含指定的元素，则返回 true。
* @param t
* @return
*/
public boolean contains(T t){
  return indexOf(t) > -1;
}

public boolean isEmpty()
 
如果此列表中没有元素，则返回 true
/**
* 如果此列表中没有元素，则返回 true
* @return
*/
public boolean isEmpty(){
  return this.size == 0;
}

public int size()方法

返回此列表中的元素数。
/**
* 获取数组的所有用户添加的元素数量，不是所有容量的长度
* @return
*/
public int size(){
  return this.size;
}

pubilc Object[] toArray()方法

按适当顺序（从第一个到最后一个元素）返回包含此列表中所有元素的数组
/**
* 按适当顺序（从第一个到最后一个元素）返回包含此列表中所有
* @return
*/
public Object[] toArray(){
  return Arrays.copyOf(elementData,this.size);
}
public T get(int index)方法

返回此列表中指定位置上的元素。
/**
* 返回此列表中指定位置上的元素
* @param index
* @return
*/
public T get(int index) {
  arrayIndexOutOfBoundsException(index);
  return this.getElement(index);
}
/**
* 有两处调用该方法，为了严谨所以封装一下
* @param index
* @return
*/
private T getElement(int index){
  return (T) this.elementData[index];
}
/**
* 主要是索引越界发出异常的,多处使用
* @param index
*/
private void arrayIndexOutOfBoundsException(int index){
  /**
   * 在这里判断一下是想提示比较中文化。没有其它的。
   */
  if(index >= size || index < 0){
      throw new ArrayIndexOutOfBoundsException("数组越界！！！");
  }
}
```

## public void set(int index, T t)

用指定的元素替代此列表中指定位置上的元素。
```java
/**
* 用指定的元素替代此列表中指定位置上的元素。
* @param index
* @param t
*/
public void set(int index, T t){
  arrayIndexOutOfBoundsException(index);
  this.elementData[index] = t;
}
```

## public T remove(int index)

移除此列表中指定位置上的元素。
```java
/**
* 移除此列表中指定位置上的元素。
* 删除玩之后需要--size,并且全部都得移位
* @param index
*/
public T remove(int index) {
  arrayIndexOutOfBoundsException(index);
  /**
   * 保存删除的元素
   */
  T t = this.getElement(index);
  for (int i = index; i < this.size-1; i++) {
      /**
       * /index索引开始以后的所有元素向前移动
       */
      this.elementData[i] = this.elementData[i + 1];
  }
  //设置最后一个元素为空,并且size减1
  this.elementData[--this.size] = null;
  return t;
}
```

## public void clear()方法

移除此列表中的所有元素
```java
/**
* 移除此列表中的所有元素
*/
public void clear(){
  for (int i = 0; i < this.size; i++) {
      /**
       * 将所有元素赋null值
       */
       this.elementData[i] = null;
  }
  this.size = 0;
}
```

## 备注

以上都是写了大部分了，以上可能有问题希望大家看出来了多多指正，也可以私聊小编交流。还差一个`iterator()`方法了留在下一节再讲。

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")

