### 1.已知表达式 int m [ ] = {9，1，2，3，4，5，6}；
下面哪个表达式的值与数组下标量总数相等？（ ）
A .m.length()
B.m.length
C.m.length()+1
D.m.length+1

答案：B
解析：数组下标是从零开始的，但是数据下标的总量和数据长度相同。


### 2.下面能让线程停止执行的有（多选）( )
A. sleep();
B. stop();
C. notify();
D. synchronized();
E. yield();
F. wait();
G. notifyAll();
答案：ABDEF
解析：sleep：导致此线程暂停执行指定时间
stop: 这个方法将终止所有未结束的方法，包括run方法。
notify();唤醒线程
synchronized():对象锁
yield：当前正在被服务的线程可能觉得cpu的服务质量不够好，于是提前退出，这就是yield。
wait：当前正在被服务的线程需要睡一会，醒来后继续被服务
notifyAll();唤醒所有线程


### 3.下列说法正确的有（）
A． 在类方法中可用this来调用本类的类方法
B． 在类方法中调用本类的类方法时可直接调用
C． 在类方法中只能调用本类中的类方法
D． 在类方法中绝对不能调用实例方法
答案：B 
解析：
A.在类方法中不能使用this关键字
C．在类方法中可以调用其它类中的类方法
D．在类方法中可以通过实例化对象调用实例方法


### 4.关于Java语言，下列描述正确的是（多选）
A. switch 不能够作用在String类型上
B. List， Set， Map都继承自Collection接口
C. Java语言支持goto语句
D. GC是垃圾收集器，程序员不用担心内存管理
答案：AD
解析：
B. Map没有继承Collection接口
C．java不支持goto语句

### 5.Which two demonstrate a ?has a” relationship(Choose two)?()
```java
A. public interface Person { }
public class Employee extends Person{ }
B. public interface Shape { }
public interface Rectandle extends Shape { }
C. public interface Colorable { }
public class Shape implements Colorable
{ }
D. public class Species{ }
public class Animal{private Species species;}
E. interface Component{ }
class Container implements Component{
private Component[] children;
}
```
答案：D
解析：
“has a”是关联关系，关联分双向关联和单向关联，双向关联是A，B类分别持有对方的引用(有是对方的属性).
单向关联是一方持另一方的引用.


## 6.Given the folowing classes which of the following will compile without error?()
```java
interface IFace{}
class CFace implements IFace{}
class Base{}
public class ObRef extends Base{
public static void main(String argv[]){
ObRef ob = new ObRef();
Base b = new Base();
Object o1 = new Object();
IFace o2 = new CFace();
}
}
```
A. o1=o2;
B. b=ob;
C. ob=b;
D. o1=b;
答案：B
解析：
b和ob对应的类之间没有任何关系，要想b=ob成立要么是父子关系，要么是接口实现类的关系


## 7.问：简单说说 Array 和 ArrayList 的区别？
答：这题相当小儿科，Array 可以包含基本类型和对象类型，ArrayList 只能包含对象类型；Array 的大小是固定的，ArrayList 的大小是动态变化的；ArrayList 提供了更多的方法和特性，譬如 addAll()、removeAll()、iterator() 等。


## 8.问：为什么 HashMap 中 String、Integer 这样的包装类适合作为 key 键，即为什么使用它们可以减少哈希碰撞？
答：因为 String、Integer 等包装类是 final 类型的，具有不可变性，而且已经重写了 equals() 和 hashCode() 方法。不可变性保证了计算 hashCode() 后键值的唯一性和缓存特性，不会出现放入和获取时哈希码不同的情况且读取哈希值的高效性，此外官方实现的 equals() 和 hashCode() 都是严格遵守相关规范的，不会出现错误。


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")