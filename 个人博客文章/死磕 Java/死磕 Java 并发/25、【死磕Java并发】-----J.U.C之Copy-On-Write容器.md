> 原文出自:[http://cmsblogs.com](http://cmsblogs.com)

----

集合在我们开发中是使用得非常多的，包括在并发环境，我们知道Map接口在高并发时可以使用ConcurrentHashMap，但是List、Set貌似没有相应的ConcurrentList、ConcurrentSet，那么怎么解决List、Set高并发环境下的使用呢？第一种使用锁。

也就是在get、remove、add等操作时增加Lock锁，如下：

```java
add(E e){
	lock.lock();
	// add操作
	lock.unlock();
}

get(int i ){
	lock.lock();
	// get操作
	lock.unlock();
}
```

这样虽然保证了线程的安全性，但是由于是所有操作都是同一个锁，那么吞吐量势必就不是想象中的那么好了，怎么做呢？按照读写锁的思想，读写分离。CopyOnWrite容器就是这样一个读写分离的并发容器，他可以在非常多的并发场景中使用，目前JDK中有CopyOnWriteArrayList和CopyOnWriteSet两种。

## CopyOnWrite容器

Copy-On-Write即写时复制，简称COW。其思想是当我们往一个容器中添加元素时，不是直接新增，而是对当前容器进行Copy，复制出一份一模一样的新容器，然后往新容器中添加元素，添加成功后，再将原容器的引用指向新的容器，在读的时候仍然是读原容器，替换引用后就改为读新容器了。这样做的一个好处就是读写分离了，读的使用可以使用并发读并且是不需要加锁的。当然这种读写分离的思想是读和写分别在不同的容器。

这里我们来看CopyOnWriteArrayList是如何实现 CopyOnWrite。

**add(E e) **

```java
    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        // 获取锁
        lock.lock();
        try {
            // 数组
            Object[] elements = getArray();
            int len = elements.length;
            // 复制出新数组
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            // 新增元素e
            newElements[len] = e;
            // 修改引用
            setArray(newElements);
            return true;
        } finally {
            // 释放锁
            lock.unlock();
        }
    }
```

整个过程清晰、明了。先获取锁，然后复制新数组并插入元素，最后设置引用释放锁。在add(E e)的时候是需要锁机制的，否则很容易会出现多分拷贝而且容器出现数据丢失。

**get(int index)**

```java
    public E get(int index) {
        return get(getArray(), index);
    }
```

get操作相对于add来说，太简单了，就是a[index]，主要是读的时候是读得就容器里面的数据不会存在数据被修改的问题，所以直接根据数组下标获取即可。

虽然CopyOnWrite容器采用读写分离的思路避免了线程安全的问题，但是它仍然存在一些缺陷：内存占用和数据不一致问题。

**内存占用**：因为在写的时候采用复制的思想，那么写的时候内存里面会同时驻扎两个数组对象的内存。如果对象占用内存空间较大，那么可能会引发频繁的Yong GC和Full GC。

**数据不一致问题**：因为时效性的问题，写的时候读不一定可以里面读取到数据，例如你add(1)，那么在get(1)的时候不一定能够得到，所以CopyOnWrite容器只能保证数据的最终一致性。

最后，CopyOnWrite容器适用于读多写少的并发场景，如果写太频繁了则会导致大量数据复制操作，这会严重影响系统的性能。
