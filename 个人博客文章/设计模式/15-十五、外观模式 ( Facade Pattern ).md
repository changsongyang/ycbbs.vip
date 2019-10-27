文章永久连接：[https://tech.souyunku.com/?p=2681](https://tech.souyunku.com/?p=2681)


外观模式（Facade Pattern）隐藏系统的复杂性，并向客户端提供了一个客户端可以访问系统的接口

外观模式涉及到一个单一的类，该类提供了客户端请求的简化方法和对现有系统类方法的委托调用

外观模式属于结构型模式，它向现有的系统添加一个接口，来隐藏系统的复杂性

## 摘要 ##

1、**意图：**

为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用

2、**主要解决：**

降低访问复杂系统的内部子系统时的复杂度，简化客户端与之的接口

3、**何时使用：**

1、客户端不需要知道系统内部的复杂联系，整个系统只需提供一个"接待员"即可
2、定义系统的入口

4、**如何解决：**

客户端不与系统耦合，外观类与系统耦合

5、**关键代码：**

在客户端和复杂系统之间再加一层，这一层将调用顺序、依赖关系等处理好

6、**应用实例：**

1、去医院看病，可能要去挂号、门诊、划价、取药，让患者或患者家属觉得很复杂，如果有提供接待人员，只让接待人员来处理，就很方便
2、JAVA 的三层开发模式

7、**优点：**

1、减少系统相互依赖
2、提高灵活性
3、提高了安全性

8、**缺点：**

不符合开闭原则，如果要改东西很麻烦，继承重写都不合适

9、**使用场景：**

1、为复杂的模块或子系统提供外界访问的模块
2、子系统相对独立
3、预防低水平人员带来的风险

10、 **注意事项：**

在层次化结构中，可以使用外观模式定义系统中每一层的入口

## 实现 ##

![img\_1.png][img_1.png]

1、创建一个 *Shape* 接口和实现了 *Shape* 接口的实体类
2、定义一个外观类 *ShapeMaker*
3、定义类 *ShapeMaker* 使用实体类来代表用户对这些类的调用
4、定义类 *FacadePatternDemo* 使用 *ShapeMaker* 类来显示结果

## 范例 ##

#### 1. 创建一个接口 ####

*Shape.java*

```
// author: 搜云库技术团队(tech.souyunku.com)
// Copyright © 2015-2065 tech.souyunku.com. All rights reserved.

package com.souyunku.tech.gof;


public interface Shape {
   void draw();
}
```

#### 2. 创建实现接口的实体类 ####

*Rectangle.java*

```
// author: 搜云库技术团队(tech.souyunku.com)
// Copyright © 2015-2065 tech.souyunku.com. All rights reserved.

package com.souyunku.tech.gof;


public class Rectangle implements Shape {

   @Override
   public void draw() {
      System.out.println("Rectangle::draw()");
   }
}
```

*Square.java*

```
// author: 搜云库技术团队(tech.souyunku.com)
// Copyright © 2015-2065 tech.souyunku.com. All rights reserved.

package com.souyunku.tech.gof;


public class Square implements Shape {

   @Override
   public void draw() {
      System.out.println("Square::draw()");
   }
}
```

*Circle.java*

```
// author: 搜云库技术团队(tech.souyunku.com)
// Copyright © 2015-2065 tech.souyunku.com. All rights reserved.

package com.souyunku.tech.gof;


public class Circle implements Shape {

   @Override
   public void draw() {
      System.out.println("Circle::draw()");
   }
}
```

#### 3. 创建一个外观类 ####

*ShapeMaker.java*

```
// author: 搜云库技术团队(tech.souyunku.com)
// Copyright © 2015-2065 tech.souyunku.com. All rights reserved.

package com.souyunku.tech.gof;


public class ShapeMaker {
   private Shape circle;
   private Shape rectangle;
   private Shape square;

   public ShapeMaker() {
      circle = new Circle();
      rectangle = new Rectangle();
      square = new Square();
   }

   public void drawCircle(){
      circle.draw();
   }
   public void drawRectangle(){
      rectangle.draw();
   }
   public void drawSquare(){
      square.draw();
   }
}
```

#### 4. 使用该外观类画出各种类型的形状 ####

*FacadePatternDemo.java*

```
// author: 搜云库技术团队(tech.souyunku.com)
// Copyright © 2015-2065 tech.souyunku.com. All rights reserved.

package com.souyunku.tech.gof;


public class FacadePatternDemo {
   public static void main(String[] args) {
      ShapeMaker shapeMaker = new ShapeMaker();

      shapeMaker.drawCircle();
      shapeMaker.drawRectangle();
      shapeMaker.drawSquare();      
   }
}
```

编译运行以上 Java 范例，输出结果如下

```
$ javac -d . src/main/com/souyunku/tech/gof/FacadePatternDemo.java
$ java  com.souyunku.tech.gof.FacadePatternDemo
Circle::draw()
Rectangle::draw()
Square::draw()
```


[img_1.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/08/0802/03/15/img_1.png


## 干货推荐

[本站推荐：精选优质专栏](https://tech.souyunku.com/?page_id=2)

## 附录：设计模式：系列文章

- [一、设计模式](https://tech.souyunku.com/?p=2653)
- [二、设计模式 - 简介](https://tech.souyunku.com/?p=2655)
- [三、设计模式 - 四大类型](https://tech.souyunku.com/?p=2657)
- [四、设计模式 - 六大原则](https://tech.souyunku.com/?p=2659)
- [五、工厂模式 ( Factory Pattern )](https://tech.souyunku.com/?p=2661)
- [六、抽象工厂模式 ( Abstract Factory Pattern )](https://tech.souyunku.com/?p=2663)
- [七、单例模式 ( Singleton Pattern )](https://tech.souyunku.com/?p=2665)
- [八、建造者模式 ( Builder Pattern )](https://tech.souyunku.com/?p=2667)
- [九、原型模式 ( Prototype Pattern )](https://tech.souyunku.com/?p=2669)
- [十、适配器模式 ( Adapter Pattern )](https://tech.souyunku.com/?p=2671)
- [十一、桥接模式 ( Bridge Pattern )](https://tech.souyunku.com/?p=2673)
- [十二、过滤器模式 ( Filter Pattern )](https://tech.souyunku.com/?p=2675)
- [十三、组合模式 ( Composite Pattern )](https://tech.souyunku.com/?p=2677)
- [十四、装饰器模式 ( Decorator Pattern )](https://tech.souyunku.com/?p=2679)
- <a style="font-size: 18px;color: #FF0000; font-weight: 600;" href="https://tech.souyunku.com/?p=2681">【当前读到】十五、外观模式 ( Facade Pattern )</a>
- [十六、享元模式 ( Flyweight Pattern )](https://tech.souyunku.com/?p=2683)
- [十七、代理模式 ( Proxy Pattern )](https://tech.souyunku.com/?p=2685)
- [十八、责任链模式 ( Chain of Responsibility)](https://tech.souyunku.com/?p=2687)
- [十九、命令模式 ( Command Pattern )](https://tech.souyunku.com/?p=2689)
- [二十、解释器模式 ( Interpreter Pattern )](https://tech.souyunku.com/?p=2691)
- [二十一、迭代器模式 ( Iterator Pattern )](https://tech.souyunku.com/?p=2693)
- [二十二、中介者模式 ( Mediator Pattern )](https://tech.souyunku.com/?p=2695)
- [二十三、备忘录模式 ( Memento Pattern )](https://tech.souyunku.com/?p=2697)
- [二十四、观察者模式 ( Observer Pattern )](https://tech.souyunku.com/?p=2699)
- [二十五、状态模式 ( State Pattern )](https://tech.souyunku.com/?p=2701)
- [二十六、空对象模式 ( Null Object Pattern )](https://tech.souyunku.com/?p=2703)
- [二十七、策略模式 ( Strategy Pattern )](https://tech.souyunku.com/?p=2705)
- [二十八、模板模式 ( Template Pattern )](https://tech.souyunku.com/?p=2707)
- [二十九、访问者模式 ( Visitor Pattern )](https://tech.souyunku.com/?p=2709)
- [三十、MVC 模式](https://tech.souyunku.com/?p=2711)
- [三十一、业务代表模式(Business Delegate Pattern)](https://tech.souyunku.com/?p=2713)
- [三十二、组合实体模式 (Composite Entity Pattern)](https://tech.souyunku.com/?p=2715)
- [三十三、数据访问对象模式 ( Data Access Object )](https://tech.souyunku.com/?p=2717)
- [三十四、前端控制器模式(Front Controller Pattern)](https://tech.souyunku.com/?p=2719)
- [三十五、拦截过滤器模式 ( Intercepting Filter )](https://tech.souyunku.com/?p=2721)
- [三十六、服务定位器模式 (Service Locator Pattern)](https://tech.souyunku.com/?p=2723)
- [三十七、传输对象模式 ( Transfer Object Pattern )](https://tech.souyunku.com/?p=2725)
- [三十八、设计模式资源](https://tech.souyunku.com/?p=2727)
