## public Iterator<T> iterator()

返回以恰当顺序在此列表的元素上进行迭代的迭代器。
```java
/**
* 创建迭代器
* @return
*/
public Iterator<T> iterator(){
  /**
   * 创建一个局部内部类，实现Iterator
   */
  class MyIterator implements Iterator<T>{
      /**
       * 数组的索引
       */
      private int index;
      /**
       * 检查是否有下一个元素
       * @return
       */
      @Override
      public boolean hasNext() {
          return index < size ? true : false;
      }
      @Override
      public T next() {
          return getElement(this.index++);
      }
  }
  return new MyIterator();
}
```

## MyArrayList仿造类的全部源码
```java
import java.util.Arrays;
import java.util.Iterator;

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
  /**
   * 如果此列表中包含指定的元素，则返回 true。
   * @param t
   * @return
   */
  public boolean contains(T t){
      return indexOf(t) > -1;
  }
  /**
   * 如果此列表中没有元素，则返回 true
   * @return
   */
  public boolean isEmpty(){
      return this.size == 0;
  }
  /**
   * 获取数组的所有用户添加的元素数量，不是所有容量的长度
   * @return
   */
  public int size(){
      return this.size;
  }
  /**
   * 按适当顺序（从第一个到最后一个元素）返回包含此列表中所有
   * @return
   */
  public Object[] toArray(){
      return Arrays.copyOf(elementData,this.size);
  }
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
  /**
   * 用指定的元素替代此列表中指定位置上的元素。
   * @param index
   * @param t
   */
  public void set(int index, T t){
      arrayIndexOutOfBoundsException(index);
      this.elementData[index] = t;
  }
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
  /**
   * 创建迭代器
   * @return
   */
  public Iterator<T> iterator(){
      /**
       * 创建一个局部内部类，实现Iterator
       */
      class MyIterator implements Iterator<T>{
          /**
           * 数组的索引
           */
          private int index;
          /**
           * 检查是否有下一个元素
           * @return
           */
          @Override
          public boolean hasNext() {
              return index < size ? true : false;
          }
          @Override
          public T next() {
              return getElement(this.index++);
          }
      }
      return new MyIterator();
  }
}
```


## 备注

以上就是所有的`ArrayList`的常用方法都在自定义`MyArrayList`的仿造类中一一实现了，可能有些许问题希望大家看出来的多多指正，也可以私聊小编交流。



写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")