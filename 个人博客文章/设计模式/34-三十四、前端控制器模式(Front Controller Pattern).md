前端控制器模式（`Front` `Controller` `Pattern`）是用来提供一个集中的请求处理机制，所有的请求都将由一个单一的处理程序处理

该处理程序可以做认证/授权/记录日志，或者跟踪请求，然后把请求传给相应的处理程序

前端控制器模式涉及以下实体

 *  **前端控制器（Front Controller）** \- 处理应用程序所有类型请求的单个处理程序，应用程序可以是基于 `web` 的应用程序，也可以是基于桌面的应用程序。
 *  **调度器（Dispatcher）** \- 前端控制器可能使用一个调度器对象来调度请求到相应的具体处理程序。
 *  **视图（View）** \- 视图是为请求而创建的对象。

## 实现 ##

![img\_1.png][img_1.png]

1、定义类 *`FrontController`* 、 *`Dispatcher`* 分别当作前端控制器和调度器
2、定义类 *`HomeView`* 和 *`StudentView`* 表示作为前端控制器接收到的请求而创建的视图
3、定义类 *`FrontControllerPatternDemo`* 使用 *`FrontController`* 演示前端控制器设计模式

## 范例 ##

#### 1. 创建视图 ####

*HomeView.java*

```
// author: 研发军团(www.ycbbs.vip)
// Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.

package com.ycbbs.tech.gof;


public class HomeView {
   public void show(){
      System.out.println("Displaying Home Page");
   }
}
```

*StudentView.java*

```
// author: 研发军团(www.ycbbs.vip)
// Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.

package com.ycbbs.tech.gof;


public class StudentView {
   public void show(){
      System.out.println("Displaying Student Page");
   }
}
```

#### 2. 创建调度器 Dispatcher ####

*Dispatcher.java*

```
// author: 研发军团(www.ycbbs.vip)
// Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.

package com.ycbbs.tech.gof;


public class Dispatcher {
   private StudentView studentView;
   private HomeView homeView;
   public Dispatcher(){
      studentView = new StudentView();
      homeView = new HomeView();
   }

   public void dispatch(String request){
      if(request.equalsIgnoreCase("STUDENT")){
         studentView.show();
      }else{
         homeView.show();
      } 
   }
}
```

#### 3. 创建前端控制器 FrontController ####

*Context.java*

```
// author: 研发军团(www.ycbbs.vip)
// Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.

package com.ycbbs.tech.gof;


public class FrontController {

   private Dispatcher dispatcher;

   public FrontController(){
      dispatcher = new Dispatcher();
   }

   private boolean isAuthenticUser(){
      System.out.println("User is authenticated successfully.");
      return true;
   }

   private void trackRequest(String request){
      System.out.println("Page requested: " + request);
   }

   public void dispatchRequest(String request){
      //记录每一个请求
      trackRequest(request);
      //对用户进行身份验证
      if(isAuthenticUser()){
         dispatcher.dispatch(request);
      } 
   }
}
```

#### 4. 使用 *FrontController* 来演示前端控制器设计模式 ####

*FrontControllerPatternDemo.java*

```
// author: 研发军团(www.ycbbs.vip)
// Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.

package com.ycbbs.tech.gof;


public class FrontControllerPatternDemo {
   public static void main(String[] args) {
      FrontController frontController = new FrontController();
      frontController.dispatchRequest("HOME");
      frontController.dispatchRequest("STUDENT");
   }
}
```

编译运行以上 `Java` 范例，输出结果如下

```
$ javac -d . src/main/com/ycbbs/tech/gof/FrontControllerPatternDemo.java
$ java  com.ycbbs.tech.gof.FrontControllerPatternDemo
Page requested: HOME
User is authenticated successfully.
Displaying Home Page
Page requested: STUDENT
User is authenticated successfully.
Displaying Student Page
```

[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0802/03/34/img_1.png

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")