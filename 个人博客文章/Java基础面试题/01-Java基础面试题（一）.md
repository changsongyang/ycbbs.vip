### 1. Byte类型可以存储多少个字节和取值范围？（）
A  1个字节 ， -128 ~ 127  
B  2个字节， 32 768 - 32 767   
C 4字节  -2 147 483 648 - 2 147 483 647 (正好超过 20 亿)
D 8个字节  -9 223 372 036 854 775 808 - 9 223 372 036 854 775 807 
答案：A
解析：
byte是一个字节保存的，有8个位，即8个0、1。
8位的第一个位是符号位， 
也就是说0000 0001代表的是数字1 
1000 0000代表的就是-1 
所以正数最大位0111 1111，也就是数字127 
负数最大为1111 1111，也就是数字-128
所以Java中的一个byte是1个字节，其范围是-128~127

### 2. short类型可以存储多少个字节和取值范围？（）
A  1个字节 ， -128 ~ 127  
B  2个字节， 32 768 - 32 767   
C 4字节  -2 147 483 648 - 2 147 483 647 (正好超过 20 亿)
D 8个字节  -9 223 372 036 854 775 808 - 9 223 372 036 854 775 807 
答案 B
解析：
与byte类似道理，16位,（-32768~32767） (-2的15次方到2的15次方-1)需要2个字节存储所以答案为B


### 3.long类型可以存储多少个字节和取值范围？（）
A  1个字节 ， -128 ~ 127  
B  2个字节， 32 768 - 32 767   
C 4字节  -2 147 483 648 - 2 147 483 647 (正好超过 20 亿)
D 8个字节  -9 223 372 036 854 775 808 - 9223 372 036 854 775 807 
答案 D
解析：
与byte类似道理,64位，（-9223372036854774808~9223372036854774807）（-2的63次方到2的63次方-1）需要4个字节存储。

### 4.下面能通过编译运行出Hello World的是()
```java
A:public class Hello {
public static void main(String[]args) {
System.out.println("Hello World");
    }
}

B:.public class Hello {
public static void main(String[] args) {
system.out.println("Hello World");
    }
}

C:public class Hello {
public static void main(String[] args) {
System.out.println("Hello World");
    }
}

D:public class Hello {
public static  main(String[] args) {
System.out.println("Hello World");
    }
}
```

答案：A C
解析：
这个题主要考察的是对基础知识的掌握程度，还有你得慧眼。
这道题错误率100%


### 5.java源文件和编译后得文件拓展名分别为（）
A:.class和.java  B: .java 和.class
C:.class和.class  D:.java和.javac
答案：B
解析：
源文件就是程序员们所编写出来的java代码  
类文件则是通过java虚拟机编译后没有语法错误生成的.class文件

### 6.如下哪些不是java的关键字？（ ）
A.const
B.NULL
C.false
D.this
E.native
答案：BC
解析：
虽然null false 还有true不是java的关键字，但是都有特殊用途，不建议作为标识符。

### 7.下面哪个表达式的值与数组下标量总数相等？（ ）
A .m.length()
B.m.length
C.m.length()+1
D.m.length+1
答案：B  
数组下标是从零开始的，但是数据下标的总量和数据长度相同。

### 8.如下代码
```java
public class Sortof {
        public static void main(String[] args) {
            ArrayList<Integer> a=new ArrayList<>();
             a.add(1);a.add(5);a.add(3);
            Collections.sort(a);
            a.add(2);
            Collections.reverse(a);
            System.out.println(a);
        }
}
```
下面哪一个是正确的（）
A[1,2,3,5)]
B.[2 ,1,3,5]
C.[2,5,3,1]
D [5, 3,2, 1]
E [1,3 ,5,2)
F Compilation fails.
G An exception is thrown at runtime
答案：C
解析：
Collections.sort(a);默认是升序排列，所以变成了1，3，5然后在加上2，就会变成1，3，5，2，
而reverse方法是反转集合中的所有元素，所以反转之后就成了2，5，3，1.

### 9.如下代码
```java
 public class Alpha{
         public void foo(){
                System.out.print("Afoo ");
           }
｝
     public class Beta extends Alpha{
          public void foo() {
                 System.out.print("Bfoo" );
           }
    public static void main(String[] args){
           Alpha a = new Beta();
           Beta b=(Beta)a;
           a.foo();
           b.foo();
      }
｝
```
下面哪一个是正确的（）
A.Afoo Afoo 
B. Afoo Bfoo 
C. Bfoo Afoo
D. Bfoo Bfoo 
E. Compilation fails 
F.An exception is thrown at runtime 
答案:D 
考察多态性，编译器认为a是Alpha 类型，b 是Beta 类型，但是虚拟机知道a 和b 的真实类型是Beta，所以调用Beta 类的函数。如果还是不了解，建议大家看看面向对象的多态。


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")