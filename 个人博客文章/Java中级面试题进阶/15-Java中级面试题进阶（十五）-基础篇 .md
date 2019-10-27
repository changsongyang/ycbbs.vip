## 1. Java集合类的总结

- 1、Iterator：Collection（值）、Map（键值对）；
- 2、Collection：Set（无序不重复）、List（有序可重复）、Queue；
- 3、Set：HashSet（基于HashMap实现）、LinkedHashSet（继承自HashSet）、TreeSet（底层基于HashMap实现，升序排列）；
- 4、List：ArrayList（基于数组实现，默认初始容量为10，增速1.5倍 + 1）、Vector（初始容量为10，增速2倍）、Stack（继承自Vector）、LinkedList（基于链表实现）；
- 5、Map：HashMap、HashTable、TreeMap（基于红黑树实现，按key值排序）；
- 6、不论Collection的实际类型如何，它都支持一个iterator()的方法，该方法返回一个迭代子，使用该迭代子即可逐一访问Collection中每一个元素。典型的用法如下：


```java
Iterator it = collection.iterator(); // 获得一个迭代子
while(it.hasNext()) {
Object obj = it.next(); // 得到下一个元素

}
```
  List还提供一个listIterator()方法，返回一个 ListIterator接口（实现Iterator），和标准的Iterator接口相比，ListIterator多了一些add()之类的方法，允许添加，删除，设定元素，还能向前或向后遍历。
  
  - 7、快速失败（Fail-Fast）机制：它是Java集合的一种错误检测机制。当多个线程对集合进行结构上的改变的操作时，有可能会产生fail-fast机制。记住是有可能，而不是一定。
  
  例如：假设存在两个线程（线程1、线程2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合A的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出 ConcurrentModificationException 异常，从而产生fail-fast机制。


## 2.Set接口与List接口
  List是有序的集合，这里的有序指的是元素插入List的顺序可以控制，Set是无序的。因此List可以允许元素重复，而Set则不可以（只能有一个null）。
  
  元素是否重复是根据equals和hashCode判断的，也就是说如果一个对象要存储在Set中，必须重写equals和hashCode方法。

## 3.ArrayList和LinkedList
  1、ArrayList和LinkedList都是非线程安全的，都允许null值。ArrayList基于数组数据结构实现，LinkedList基于链表结构实现。
  
  2、ArrayList允许对元素进行快速随机访问，但是想中间插入与移除元素的速率很慢；LinkedList插入与删除的开销不大，随机访问较慢。
  
  3、LinkedList具有下列方法：addFirst()，addLast()，getFirst()，getLast()，removeFirst()和removeLast()这些方法(没有在任何接口或基类中定义过)，这些操作使LinkedList可被用作堆栈（stack），队列（queue）或双向队列（deque）。

## 4.ArrayList和Vector
  1、ArrayList是线程不安全的，Vector是线程安全的。因此ArrayList的性能要高于Vector；
  
  2、ArrayList和Vector都采用基于数组的方式组织元素，默认初始容量都是10，如果集合中的元素的数目大于目前集合数组的长度时，Vector扩容为原数组的2倍，而ArrayList扩容为原来1.5倍。Vector还可以设置容量的增量，而ArrayList不可以。

## 5.队列与阻塞队列
  队列包含固定长度的队列和不固定长度的队列，先进先出。
  
  固定长度的队列往里放数据，如果放满了还要放，阻塞式队列就会等待，直到有数据取出，空出位置后才继续放；非阻塞式队列不能等待就只能报错了。

## 6.ArrayBlockingQueue
  一个由数组支持的有界阻塞队列。此队列按 FIFO（先进先出）原则对元素进行排序。队列的头部是在队列中存在时间最长的元素。队列的尾部是在队列中存在时间最短的元素。新元素插入到队列的尾部，队列获取操作则是从队列头部开始获得元素。
  
  这是一个典型的“有界缓存区”，固定大小的数组在其中保持生产者插入的元素和使用者提取的元素。一旦创建了这样的缓存区，就不能再增加其容量。试图向已满队列中放入元素会导致操作受阻塞；试图从空队列中提取元素将导致类似阻塞。
  
  此类支持对等待的生产者线程和使用者线程进行排序的可选公平策略。默认情况下，不保证是这种排序。然而，通过将公平性 (fairness) 设置为 true 而构造的队列允许按照FIFO 顺序访问线程。公平性通常会降低吞吐量，但也减少了可变性和避免了“不平衡性”。
  
  LinkedBlockingQueue则是一个由链接节点支持的可选有界队列。基于链表实现的阻塞队列。

## 7.PriorityQueue
  PriorityQueue是一个基于优先级堆的无界队列，它的元素是按照自然顺序（natural order）排序的。在创建的时候，我们可以给他提供一个负责给元素排序的比较器。
  
  PriorityQueue不允许null值，因为他们没有自然顺序，或者说他们没有任何的相关联的比较器。最后，PriorityQueue不是线程安全的，入队和出队的时间复杂度是O(log(n))。
  
  PriorityBlockingQueue是对PriorityQueue的再次包装的阻塞型无界优先级队列。所以在优先阻塞队列上put时是不会受阻的（但是资源耗尽的情况下回导致OutOfMemoryError）。
  
  当队列为空，那么取元素的操作take就会阻塞。

## 8.为什么集合类没有实现Cloneable和Serializable接口
  克隆（cloning）或者是序列化（serialization）的语义和含义是跟具体的实现类相关的。因此应该由集合类的具体实现来决定如何被克隆或者序列化。当然了，集合框架的接口没有实现克隆和序列化，但是具体的集合类实现了，例如ArrayList类。


## 9.Array与ArrayList的区别

### 9.1、存储内容方面：

**Array数组可以包含基本类型和对象类型；**
  ArrayList却只能包含对象类型。
  但是需要注意的是：Array数组在存放的时候一定是同种类型的元素。ArrayList就不一定了，因为ArrayList可以存储Object。

### 9.2、空间大小方面：
  Array的空间大小是固定的，空间不够时也不能再次申请，所以需要事先确定合适的空间大小。
  
  ArrayList的空间是动态增长的，如果空间不够，它会创建一个空间比原来大1.5倍的新数组，然后将所有元素复制到新数组中，接着抛弃旧数组。而且每次添加新元素的时候都会检查内部数组的空间是否足够。

### 9.3、方法操作方面：
  ArrayList作为Array的增强版，当然是在方法上比Array更多样化，比如添加全部addAll()，删除全部removeAll()，返回迭代器iterator()等。
  
  **适用场景：**
  
如果想要保存一些在整个程序运行期间都会存在而且不变的数据，我们可以将它们放进一个全局数组里。如果数据的个数在程序运行时会发生改变，则只能使用ArrayList。

但是应该注意到ArrayList增加或者删除数据的时候，都会移动数组中的元素。如果我们需要对元素进行频繁的移动或者删除，或者是处理的是超大量的数据。

那么，使用ArrayList就真的不是一个好的选择，因为它的效率很低，使用数组进行这样的动作就会很麻烦，那么，我们可以考虑选择LinkedList。

## 9.4、Arrays类
Arrays主要是用于方便操作数组的，他的主要方法有，给数组赋值：通过fill方法；对数组排序：通过sort方法，按升序；

比较数组：通过equals方法比较数组中元素值是否相等；查找数组元素：通过binarySearch方法能对排序好的数组进行二分查找法操作。


## 10.HashMap和HashTable
  相同点：HashMap和HasheTalbe都可以使用来存储key--value的数据。都实现了Map接口；
  
 **区别：**
-  1、历史原因：HashTable是基于陈旧的Dictionary类的，HashMap是Java1.2引进的Map接口的一个实现。但是两者都实现了Map接口；

-  2、同步性：HashTable是线程安全的，也就是说同步的，这个类中的一些方法包装了HashTable中的对象是线程安全的。而HashMap则是线程异步的，即不同步的，因此HashMap中的对象并不是线程安全的。因为同步的要求会影响执行效率，所以HashMap的性能要高于HashTable；

-  3、HashMap可以将空值（null）做为key和value，而HashTable是不能放入空值的（value为空也不行）；

-  4、HashMap是快速失败机制，HashTable不是；

-  5、HashTable使用Enumeration，HashMap使用Iterator；

-  6、HashTable直接使用对象的hashCode值，而HashMap重新计算hash值，并且用“与”运算代替求模。

-  7、HashMap的初始容量为16，HashTable的初始容量为11。

## 11.Hashtable 和ConcurrentHashMap
效率低下的HashTable容器：HashTable容器使用synchronized来保证线程安全，但在线程竞争激烈的情况下HashTable的效率非常低下。

因为当一个线程访问HashTable的同步方法时，其他线程访问HashTable的同步方法时，可能会进入阻塞或轮询状态。

如线程1使用put进行添加元素，线程2不但不能使用put方法添加元素，并且也不能使用get方法来获取元素，所以竞争越激烈效率越低。

**锁分段技术**
  HashTable容器在竞争激烈的并发环境下表现出效率低下的原因是所有访问HashTable的线程都必须竞争同一把锁，那假如容器里有多把锁，每一把锁用于锁容器其中一部分数据。
  
  那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是 ConcurrentHashMap所使用的锁分段技术。
  
  首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。
  
  java5中新增了ConcurrentMap接口和它的一个实现类 ConcurrentHashMap。ConcurrentHashMap提供了和Hashtable以及SynchronizedMap中所不同的锁机制。
  
  Hashtable中采用的锁机制是一次锁住整个hash表，从而同一时刻只能由一个线程对其进行操作；而ConcurrentHashMap中则是一次锁住一个桶。
  
  ConcurrentHashMap默认将hash表分为16个桶，诸如put，remove等常用操作只锁当前需要用到的桶。这样，原来只能一个线程进入，现在却能同时有16个写线程执行，并发性能的提升是显而易见的。
  
  而读操作大部分时候都不需要用到锁（读到null值时会上锁）。只有在size等操作时才需要锁住整个hash表。
  
  在迭代方面，ConcurrentHashMap使用了一种不同的迭代方式。在这种迭代方式中，当iterator被创建后集合再发生改变就不再是抛出ConcurrentModificationException，取而代之的是在改变时new新的数据从而不影响原有的数据，iterator完成后再将头指针替换为新的数据，这样iterator线程可以使用原来老的数据，而写线程也可以并发的完成改变。

## 12.为什么我们需要ConcurrentHashMap和CopyOnWriteArrayList
  同步的集合类（Hashtable和Vector），同步的封装类（使用Collections.synchronizedMap()方法和Collections.synchronizedList()方法返回的对象）可以创建出线程安全的Map和List。
  
  但是有些因素使得它们不适合高并发的系统。它们仅有单个锁，对整个集合加锁，以及为了防止ConcurrentModificationException异常经常要在迭代的时候要将集合锁定一段时间，这些特性对可扩展性来说都是障碍。
  
  ConcurrentHashMap和CopyOnWriteArrayList保留了线程安全的同时，也提供了更高的并发性。ConcurrentHashMap和CopyOnWriteArrayList并不是处处都需要用，大部分时候你只需要用到HashMap和ArrayList，它们用于应对一些普通的情况。

## 13.Comparable接口和Comparator接口的比较
  在实际应用中，我们往往有需要比较两个自定义对象大小的地方。而这些自定义对象的比较，就不像简单的整型数据那么简单，它们往往包含有许多的属性，我们一般都是根据这些属性对自定义对象进行比较的。
  
  所以Java中要比较对象的大小或者要对对象的集合进行排序，需要通过比较这些对象的某些属性的大小来确定它们之间的大小关系。
  
  一般，Java中通过接口实现两个对象的比较，比较常用就是Comparable接口和Comparator接口。
  
  首先类要实现接口，并且使用泛型规定要进行比较的对象所属的类，然后类实现了接口后，还需要实现接口定义的比较方法（compareTo方法或者compare方法），在这些方法中传入需要比较大小的另一个对象，通过选定的成员变量与之比较，如果大于则返回1，小于返回-1，相等返回0。
  
- 1、Comparable和Comparator都是用来实现集合中元素的比较、排序的。
- 2、Comparable是在类内部定义的方法实现的排序，位于java.lang下。
- 3、Comparator是在类外部实现的排序，位于java.util下。
- 4、实现Comparable接口需要覆盖compareTo方法，实现Comparator接口需要覆盖compare方法。

## 14.Comparable接口
### 1、什么是Comparable接口
  此接口强行对实现它的每个类的对象进行整体排序。此排序被称为该类的自然排序，类的 compareTo方法被称为它的自然比较方法 。
  
  实现此接口的对象列表（和数组）可以通过 Collections.sort（和 Arrays.sort ）进行自动排序。实现此接口的对象可以用作有序映射表中的键或有序集合中的元素，无需指定比较器。
  
  如String、Integer自己就实现了Comparable接口，可完成比较大小操作。自定义类要在加入list容器中后能够排序，也可以实现Comparable接口，在用Collections类的sort方法排序时若不指定Comparator，那就以自然顺序排序。所谓自然顺序就是实现Comparable接口设定的排序方式。
### 2、实现什么方法
  **int compareTo(T o)**
  比较此对象与指定对象的顺序。如果该对象小于、等于或大于指定对象，则分别返回负整数、零或正整数。
  
  参数：o - 要比较的对象。
  
  返回：负整数、零或正整数，根据此对象是小于、等于还是大于指定对象。
  
  抛出：ClassCastException - 如果指定对象的类型不允许它与此对象进行比较。

## 15.Comparator接口
  Comparator是一个专用的比较器，当这个对象不支持自比较或者自比较函数不能满足要求时，可写一个比较器来完成两个对象之间大小的比较。
  
  Comparator体现了一种策略模式(strategy design pattern)，就是不改变对象自身，而用一个策略对象(strategy object)来改变它的行为。
  
  **与上面的Comparable接口不同的是：**
  - 1）、Comparator位于包java.util下，而Comparable位于包java.lang下。
  
-   2）、Comparable接口将比较代码嵌入需要进行比较的类的自身代码中，而Comparator接口在一个独立的类中实现比较。

-   3）、如果前期类的设计没有考虑到类的Compare问题而没有实现Comparable接口，后期可以通过Comparator接口来实现比较算法进行排序，并且为了使用不同的排序标准做准备，比如：升序、降序。

-   4）、Comparable接口强制进行自然排序，而Comparator接口不强制进行自然排序，可以指定排序顺序。



## 16.快速失败与安全失败的区别
### 16.1快速失败（fail—fast）
  在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。
  
  **原理：** 
  迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。
  
  每当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。
  
  >注意：这里异常的抛出条件是检测到 modCount！=expectedmodCount 这个条件。如果集合发生变化时修改modCount值刚好又设置为了expectedmodCount值，则异常不会抛出。因此，不能依赖于这个异常是否抛出而进行并发操作的编程，这个异常只建议用于检测并发修改的bug。
  
  **JDK中详细的描述：**
  注意，此实现不是同步的。如果多个线程同时访问一个哈希映射（或者其他集合类），而其中至少一个线程从结构上修改了该映射，则它必须保持外部同步。
  
  （结构上的修改是指添加或删除一个或多个映射关系的任何操作；仅改变与实例已经包含的键关联的值不是结构上的修改。）这一般通过对自然封装该映射的对象进行同步操作来完成。
  
  如果不存在这样的对象，则应该使用 Collections.synchronizedMap 方法来“包装”该映射。最好在创建时完成这一操作，以防止对映射进行意外的非同步访问，如下所示：
  
```java
Map m = Collections.synchronizedMap(new HashMap(...));
```
  由所有此类的“collection 视图方法”所返回的迭代器都是快速失败的：在迭代器创建之后，如果从结构上对映射进行修改，除非通过迭代器本身的 remove 方法，其他任何时间任何方式的修改，迭代器都将抛出 ConcurrentModificationException。
  
  因此，面对并发的修改，迭代器很快就会完全失败，而不冒在将来不确定的时间发生任意不确定行为的风险。
  
  注意，迭代器的快速失败行为不能得到保证，一般来说，存在非同步的并发修改时，不可能作出任何坚决的保证。
  
  快速失败迭代器尽最大努力抛出 ConcurrentModificationException。因此，编写依赖于此异常的程序的做法是错误的，正确做法是：迭代器的快速失败行为应该仅用于检测程序错误。
  
  场景：java.util包下的集合类都是快速失败的，不能在多线程下发生并发修改（迭代过程中被修改）。

### 16.2安全失败（fail—safe）
  采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。
  
  **原理：**由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发ConcurrentModificationException。
  
  **缺点：**基于拷贝内容的优点是避免了ConcurrentModificationException，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。
  
  **场景：**java.util.concurrent包下的容器都是安全失败，可以在多线程下并发使用，并发修改。
  
  ConcurrentHashMap中的迭代器主要包括entrySet、keySet、values方法。它们大同小异，这里选择entrySet解释。当我们调用entrySet返回值的iterator方法时，返回的是EntryIterator，在EntryIterator上调用next方法时，最终实际调用到了HashIterator.advance()方法。这个方法在遍历底层数组。
  
  在遍历过程中，如果已经遍历的数组上的内容变化了，迭代器不会抛出ConcurrentModificationException异常。如果未遍历的数组上的内容发生了变化，则有可能反映到迭代过程中。
  
  这就是ConcurrentHashMap迭代器弱一致的表现。ConcurrentHashMap的弱一致性主要是为了提升效率，是一致性与效率之间的一种权衡。
  
  要成为强一致性，就得到处使用锁，甚至是全局锁，这就与Hashtable和同步的HashMap一样了。


## 17.Enumeration接口和Iterator接口的区别
```java
package java.util;

public interface Enumeration<E> {
    boolean hasMoreElements();
    E nextElement();
}

public interface Iterator<E> {
    boolean hasNext();
    E next();
    void remove();
}
```
  **(01)、函数接口不同**
  Enumeration 只有2个函数接口。通过Enumeration，我们只能读取集合的数据，而不能对数据进行修改。
  
  Iterator 只有3个函数接口。Iterator除了能读取集合的数据之外，也能对数据进行删除操作。
  
  **(02)、Iterator 支持 fail-fast 机制，而 Enumeration 不支持**
  Enumeration 是JDK 1.0添加的接口。使用到它的函数包括Vector、Hashtable等类，这些类都是JDK1.0中加入的，Enumeration存在的目的就是为它们提供遍历接口。
  
  Enumeration本身并没有支持同步，而在Vector、Hashtable实现Enumeration时，添加了同步。
  
  而Iterator 是JDK 1.2才添加的接口，它也是为了HashMap、ArrayList等集合提供遍历接口。
  
  Iterator是支持fail-fast机制的：当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。
  
  总结：Enumeration速度是Iterator的2倍，同时占用更少的内存。但是，Iterator远远比Enumeration安全，因为其他线程不能够修改正在被iterator遍历的集合里面的对象。
  
  同时，Iterator允许调用者删除底层集合里面的元素，这对Enumeration来说是不可能的。


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")