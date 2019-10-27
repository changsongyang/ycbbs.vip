## Properties 介绍

`Properties` 是在`java.util`包中，该类表示了一个持久的属性集。`Properties` 可保存在流中或从流中加载。属性列表中每个键及其对应值都是一个字符串。

一个属性列表可包含另一个属性列表作为它的“默认值”；如果未能在原有的属性列表中搜索到属性键，则搜索第二个属性列表。

因为 `Properties` 继承于 `Hashtable`，所以可对 `Properties` 对象应用 `put` 和 `putAll` 方法。但不建议使用这两个方法，因为它们允许调用者插入其键或值不是 `String` 的项。

相反，应该使用`setProperty` 方法。如果在“不安全”的 `Properties` 对象（即包含非 `String` 的键或值）上调用 `store` 或 `save` 方法，则该调用将失败。

类似地，如果在“不安全”的 `Properties` 对象（即包含非 `String` 的键）上调用`propertyNames `或 `list` 方法，则该调用将失败。一般`Properties` 文件是一个配置文件。

## 构造方法

` Properties()`
创建一个无默认值的空属性列表。
`Properties(Properties defaults)`
创建一个带有指定默认值的空属性列表。

## 常用方法

`load(InputStream inStream)`
从输入流中读取属性列表（键和元素对）。

`setProperty(String key, String value)`
调用 `Hashtable` 的方法 `put`。

`store(OutputStream out, String comments)`
以适合使用 `load(InputStream)` 方法加载到 `Properties` 表中的格式，将此 `Properties` 表中的属性列表（键和元素对）写入输出流。


`getProperty(String key)`
用指定的键在此属性列表中搜索属性。

`clear()`
将此哈希表清空，使其不包含任何键。

创建`temp.properties`文件

在掩饰方法之前我们需要先创建一个后缀为`properties`的文件，文件名就叫做`temp.properties`吧。这个时候需要用到`File`类了。
```java
/**
* 在程序目录下创建一个temp.properties文件
*/
File dirPath = new File(".");
//创建一个储存文件temp.properties文件的绝对路径
String filePath = dirPath.getCanonicalPath() + "\\src\\temp.properties";
//开始创建文件
File file = new File(filePath);
//创建文件
boolean bool = file.createNewFile();
System.out.println(bool);

//在当前程序下的src文件夹下成功创建了一个temp.properties文件
结果：true
```
## load,setProperty,store

在写入配置文件内容需要使用到`load，setProperty,store`方法
```java
**
* 在程序目录下创建一个temp.properties文件
*/
File dirPath = new File(".");
//创建一个储存文件temp.properties文件的绝对路径
String filePath = dirPath.getCanonicalPath() + "\\src\\temp.properties";

//创建properties对象
Properties properties = new Properties();
//加载temp.properties文件
properties.load(new FileInputStream(filePath));
//设置数据
properties.setProperty("1","zhangsan");
properties.setProperty("2","lisi");
properties.setProperty("3","wangwu");
//将数据保存到文件中
properties.store(new FileOutputStream(filePath),"This is properties file!!!");

//temp.properties文件中的内容。
#This is properties file!!!
#Fri Apr 13 14:31:53 CST 2018
3=wangwu
2=lisi
1=zhangsan
```

### getProperty(String key)
用指定的键在此属性列表中搜索属性。
```java

System.out.println(properties.get("1"));

结果：zhangsan
```

### clear()
将此哈希表清空，使其不包含任何键。
```java
properties.clear();//清空数据
System.out.println(properties.get("1"));//再次获取就是null了

结果：null
```

## 对象的序列化与反序列化

序列化为将对象的信息存储到文件中,反序列化为将文件存储的对象信息读取出来。


## ObjectOutputStream(对象序列化)

`ObjectOutputStream` 将 `Java` 对象的基本数据类型和图形写入 `OutputStream`。可以使用 `ObjectInputStream` 读取（重构）对象。通过在流中使用文件可以实现对象的持久存储。

如果流是网络套接字流，则可以在另一台主机上或另一个进程中重构对象。只能将支持 `java.io.Serializable` 接口的对象写入流中。

每个 `serializable` 对象的类都被编码，编码内容包括类名和类签名、对象的字段值和数组值，以及从初始对象中引用的其他所有对象的闭包。 

## ObjectInputStream(对象反序列化)


`ObjectInputStream` 确保从流创建的图形中所有对象的类型与 `Java` 虚拟机中显示的类相匹配。使用标准机制按需加载类。

只有支持 `java.io.Serializable` 或 `java.io.Externalizable` 接口的对象才能从流读取。 

下面我们就不再讲述了，前面章节已经讲过io流的一些类的使用了。大致api的使用都差不多。这里就直接代码演示了。


## 代码演示

首先创建一个实体类实现`Serializable`接口,该接口只是一个序列化的标记接口。
```java
class Person implements Serializable {
  private String name;
  private int id;

  public Person() {
  }

  public Person(String name, int id) {
      this.name = name;
      this.id = id;
  }

  public String getName() {
      return name;
  }

  public void setName(String name) {
      this.name = name;
  }

  public int getId() {
      return id;
  }

  public void setId(int id) {
      this.id = id;
  }
}
```
然后实现序列化和反序列化的代码。
```java
**
* 在程序目录下创建一个temp.properties文件
*/
File dirPath = new File(".");
//创建一个储存文件temp.properties文件的绝对路径
String filePath = dirPath.getCanonicalPath() + "\\src\\temp.properties";

//创建一个序列化对象
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filePath));
//这里需要序列化对象，所以使用writeObject方法
oos.writeObject(new Person("张三",1));//写入第一个对象
oos.writeObject(new Person("李四",2));//写入第二个对象
oos.writeObject(new Person("王五",3));//写入第三个对象
//关闭资源
oos.close();

//创建一个反序列化对象
ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filePath));
Person p1 = (Person) ois.readObject();//读取第一个对象
Person p2 = (Person) ois.readObject();//读取第二个对象
Person p3 = (Person) ois.readObject();//读取第三个对象
//关闭资源
ois.close();
System.out.println(p1);
System.out.println(p2);
System.out.println(p3);
结果：

Person{name='张三', id=1}
Person{name='李四', id=2}
Person{name='王五', id=3}
```

大家看这个应该能看懂了吧，下面我顺便复习下前面的集合在演示个集合存取。
```java
/**
* 在程序目录下创建一个temp.properties文件
*/
File dirPath = new File(".");
//创建一个储存文件temp.properties文件的绝对路径
String filePath = dirPath.getCanonicalPath() + "\\src\\temp.properties";


//要存储的数据集合
List<Person> writeList = new ArrayList<>();
writeList.add(new Person("张三",1));
writeList.add(new Person("李四",2));
writeList.add(new Person("王五",3));

//创建一个序列化对象
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filePath));
//这里需要序列化对象，所以使用writeObject方法
oos.writeObject(writeList);
//关闭资源
oos.close();

//创建一个反序列化对象
ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filePath));
List<Person> readList = (List<Person>) ois.readObject();
//关闭资源
ois.close();

//遍历集合
Iterator<Person> iterator = readList.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}
//结果：

Person{name='张三', id=1}
Person{name='李四', id=2}
Person{name='王五', id=3}
```

写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")