线程发生死锁可能性很小，即使看似可能发生死锁的代码，在运行时发生死锁的可能性也是小之又小。

发生死锁的原因一般是两个对象的锁相互等待造成的。

在《[Java线程：线程的同步与锁](https://www.ycbbs.vip/?p=2548 "Java线程：线程的同步与锁")》一文中，简述死锁的概念与简单例子，但是所给的例子是不完整的，这里给出一个完整的例子。
```java
public class Test { 
        public static void main(String[] args) { 
                DeadlockRisk dead = new DeadlockRisk(); 
                MyThread t1 = new MyThread(dead, 1, 2); 
                MyThread t2 = new MyThread(dead, 3, 4); 
                MyThread t3 = new MyThread(dead, 5, 6); 
                MyThread t4 = new MyThread(dead, 7, 8); 

                t1.start(); 
                t2.start(); 
                t3.start(); 
                t4.start(); 
        } 

} 

class MyThread extends Thread { 
        private DeadlockRisk dead; 
        private int a, b; 


        MyThread(DeadlockRisk dead, int a, int b) { 
                this.dead = dead; 
                this.a = a; 
                this.b = b; 
        } 

        @Override 
        public void run() { 
                dead.read(); 
                dead.write(a, b); 
        } 
} 

class DeadlockRisk { 
        private static class Resource { 
                public int value; 
        } 

        private Resource resourceA = new Resource(); 
        private Resource resourceB = new Resource(); 

        public int read() { 
                synchronized (resourceA) { 
                        System.out.println("read():" + Thread.currentThread().getName() + "获取了resourceA的锁！"); 
                        synchronized (resourceB) { 
                                System.out.println("read():" + Thread.currentThread().getName() + "获取了resourceB的锁！"); 
                                return resourceB.value + resourceA.value; 
                        } 
                } 
        } 

        public void write(int a, int b) { 
                synchronized (resourceB) { 
                        System.out.println("write():" + Thread.currentThread().getName() + "获取了resourceA的锁！"); 
                        synchronized (resourceA) { 
                                System.out.println("write():" + Thread.currentThread().getName() + "获取了resourceB的锁！"); 
                                resourceA.value = a; 
                                resourceB.value = b; 
                        } 
                } 
        } 
}
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")