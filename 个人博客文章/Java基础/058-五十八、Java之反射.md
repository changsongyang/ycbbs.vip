## 反射

反射是一种功能强大且复杂的机制。它主要是动态地创建类型的实例，将类型绑定到现有对象，或从现有对象中获取类型。然后，可以调用类型的方法或访问其字段和属性。

也就是说当我们的程序在运行时，需要动态的加载一些类这些类可能之前用不到所以不用加载到`jvm`，而是在运行时根据需要才加载，这样的好处对于服务器来说不言而喻，

举个例子我们的项目底层有时是用`mysql`，有时用`oracle`，需要动态地根据实际情况加载驱动类，这个时候反射就有用了。
### 举个小例子假设
 `com.java.dbtest.myqlConnection，com.java.dbtest.oracleConnection`
这两个类我们要用，这时候我们的程序就写得比较动态化，通过`Class tc = Class.forName("com.java.dbtest.TestConnection");`通过类的全类名让`jvm`在服务器中找到并加载这个类，而如果是`oracle`则传入的参数就变成另一个了。这时候就可以看到反射的好处了，这个动态性就体现出java的特性了！

## 反射的优缺点

优点：
1、反射提高了程序的灵活性和扩展性。
2、降低耦合性，提高自适应能力。
3、它允许程序创建和控制任何类的对象，无需提前硬编码目标类。
缺点：
1、性能问题：使用反射基本上是一种解释操作，用于字段和方法接入时要远慢于直接代码。因此反射机制主要应用在对灵活性和拓展性要求很高的系统框架上，普通程序不建议使用。
2、使用反射会模糊程序内部逻辑；程序员希望在源代码中看到程序的逻辑，反射却绕过了源代码的技术，因而会带来维护的问题，反射代码比相应的直接代码更复杂。

## 代码演示
下面做一个小例子大概使用一下反射。再不写框架的情况下我们反射使用起来还是比较简单的。
需求：
1.类:`Student`
2.字段：`stuName,stuId,stuNumber`
3.构造方法：`student();student(stuName,stuId,stuNumber);`
4.普通方法：`getset`方法以及重写一个`toString`方法。

```java
public class Student {
   private String stuName;
   private int stuId;
   private String stuNumber;

   public Student() {
   }

   public Student(String stuName, int stuId, String stuNumber) {
       this.stuName = stuName;
       this.stuId = stuId;
       this.stuNumber = stuNumber;
   }

   public String getStuName() {
       return stuName;
   }

   public void setStuName(String stuName) {
       this.stuName = stuName;
   }

   public int getStuId() {
       return stuId;
   }

   public void setStuId(int stuId) {
       this.stuId = stuId;
   }

   public String getStuNumber() {
       return stuNumber;
   }

   public void setStuNumber(String stuNumber) {
       this.stuNumber = stuNumber;
   }

   public String toString() {
       return "Student{" +
               "stuName='" + stuName + '\'' +
               ", stuId=" + stuId +
               ", stuNumber='" + stuNumber + '\'' +
               '}';
   }
}
```

5.使用类名的全限定名的字符串的参数形式用反射一个无参构实例。
```java
String calssName = "reflect.Student";
try {
   //得到Student的Class的对象
   Class<?> clazz = Class.forName(calssName);
   //使用Class类的newInstance方法创建Student的实例
//            clazz.newInstance();//此方法已经过时了

   //现在需要使用Constructor类的newInstance方法来创建Student实例了
          //Constructor主要是提供Student类的单个构造方法的信息以及对它的访问权限。
   Constructor constructor = clazz.getConstructor();
   //使用的无参构造创建的实例
   Student stu = (Student) constructor.newInstance();

   System.out.println(stu);
} catch (Exception e) {
   e.printStackTrace();
}
```

6.在使用反射一个有参实例
```java
String calssName = "reflect.Student";
try {
   //得到Student的Class的对象
   Class<?> clazz = Class.forName(calssName);
   /**
    * 现在需要使用Constructor类的newInstance方法来创建Student实例了
    * 1.在得到Constructor类的同时需要传入构造方法的参数类型
    * 2.也同时需要在newInstance方法加上参数
    */
   Constructor constructor = clazz.getConstructor(String.class,int.class,String.class);
   //使用的无参构造创建的实例,调用有参构造需要传参
   Student stu = (Student) constructor.newInstance("zhangsan",1,"NO001");
   System.out.println(stu);
} catch (Exception e) {
   e.printStackTrace();
}
结果：
Student{stuName='zhangsan', stuId=1, stuNumber='NO001'}

```
7.使用反射找出类中的所有字段名称以及字段声明类型。
```java
String calssName = "reflect.Student";
//得到Student的Class的对象
Class<?> clazz = Class.forName(calssName);
//获取所有方法
   Field[] fields = clazz.getDeclaredFields();
for (Field field : fields) {
   //打印所有方法字段
   System.out.println("字段名称为：" + field.getName() + ",字段的声明类型为：" + field.getGenericType().getTypeName() + "\t");
}
结果
字段名称为：stuName,字段的声明类型为：java.lang.String  
字段名称为：stuId,字段的声明类型为：int  
字段名称为：stuNumber,字段的声明类型为：java.lang.String
```

8.使用反射获取类中的所有的方法以及返回类型。
```java
String calssName = "reflect.Student";
//得到Student的Class的对象
Class<?> clazz = Class.forName(calssName);
//获取所有方法
Method[] methods = clazz.getDeclaredMethods();
for (Method method : methods) {
   //打印所有方法字段
   System.out.println("方法名称为：" + method.getName() + ",方法返回类型为：" + method.getGenericReturnType().getTypeName() + "\t");
}
结果
方法名称为：toString,方法返回类型为：java.lang.String  
方法名称为：getStuName,方法返回类型为：java.lang.String  
方法名称为：setStuName,方法返回类型为：void  
方法名称为：getStuId,方法返回类型为：int  
方法名称为：setStuId,方法返回类型为：void  
方法名称为：setStuNumber,方法返回类型为：void  
方法名称为：getStuNumber,方法返回类型为：java.lang.String
```

9.利用反射来为私有字段`stuName`赋值,并且调用`getStuName`方法。
```java
String calssName = "reflect.Student";
try {
   //得到Student的Class的对象
   Class<?> clazz = Class.forName(calssName);
   /**
    * 现在需要使用Constructor类的newInstance方法来创建Student实例了
    * 1.在得到Constructor类的同时需要传入构造方法的参数类型
    * 2.也同时需要在newInstance方法加上参数
    */
   Constructor constructor = clazz.getConstructor();
   //使用的无参构造创建的实例,调用有参构造需要传参
   Object stu =  constructor.newInstance();
   
   //获取stuName字段
   Field field = clazz.getDeclaredField("stuName");
   //这里需要设置一下允许为修改私有变量的值
   field.setAccessible(true);
   //设置值
   field.set(stu,"王五");
   
   //打印该字段的值
   System.out.println(field.get());

method.invoke(stu));

} catch (Exception e) {
   e.printStackTrace();
}

结果：王五
```
10.使用所有的`set`方法为字段赋值，并且使用`get`方法获取字段的新值。
```java
String calssName = "reflect.Student";
try {
   //得到Student的Class的对象
   Class<?> clazz = Class.forName(calssName);
   /**
    * 现在需要使用Constructor类的newInstance方法来创建Student实例了
    * 1.在得到Constructor类的同时需要传入构造方法的参数类型
    * 2.也同时需要在newInstance方法加上参数
    */
   Constructor constructor = clazz.getConstructor();
   //使用的无参构造创建的实例,调用有参构造需要传参
   Object stu =  constructor.newInstance();

   //设置setStuName的值
   Method stuNameMethod = clazz.getDeclaredMethod("setStuName",String.class);
   stuNameMethod.invoke(stu,"王五");
   //使用getNameMethod获取刚刚设置的值
   Method getNameMethod = clazz.getDeclaredMethod("getStuName");
   System.out.println(getNameMethod.invoke(stu));

   //设置setStuId的值
   Method setStuIdMethod = clazz.getDeclaredMethod("setStuId",int.class);
   setStuIdMethod.invoke(stu,1);
   //使用getStuId获取刚刚设置的值
   Method getStuIdMethod = clazz.getDeclaredMethod("getStuId");
   System.out.println(getStuIdMethod.invoke(stu));


   //设置stuNumber的值
   Method setStuNumberMethod = clazz.getDeclaredMethod("setStuNumber",String.class);
   setStuNumberMethod.invoke(stu,"No0001");
   //使用getStuNumber获取刚刚设置的值
   Method getStuNumberMethod = clazz.getDeclaredMethod("getStuNumber");
   System.out.println(getStuNumberMethod.invoke(stu));

} catch (Exception e) {
   e.printStackTrace();
}
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")