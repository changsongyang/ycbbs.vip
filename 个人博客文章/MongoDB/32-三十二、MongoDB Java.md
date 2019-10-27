JAVA 语言使用 `MongoDB` `Java` `Driver` 来访问 `MongoDB` 数据库

本章教程使用 `mongodb-driver 3.5.0` 版本，其它版本也类似

### MongoDB Java Driver 环境配置 ###

JAVA 程序中如果访问 MongoDB，需要安装了 JAVA 环境及 MongoDB Java 驱动

如果你还未安装 JAVA 环境，可以移步 JAVA 基础教程

现在，我们来安装配置 `JAVA` `MongoDB` 驱动

1、  首先下载 mongodb-driver-3.5.0.jar [http://mongodb.github.io/mongo-java-driver/][http_mongodb.github.io_mongo-java-driver]
    
    ![img\_1.png][img_1.png]
2、  然后将 `mongo-java-driver-3.5.0.jar` （或其它版本）放到 **CLASSPATH** 中

你也可以从地址下载 mongodb-driver jar：[http://central.maven.org/maven2/org/mongodb/mongo-java-driver/][http_central.maven.org_maven2_org_mongodb_mongo-java-driver]

### Gradle 构建 JAVA MongoDB 运行环境 ###

因为我实在不想把 `jar` 添加到 `CLASSPATH` 中，又不想用 `MAVEN` 这种 `XML` 配置方式，所以就使用了 `Gradle` 构建工具

配置步骤如下

```
$ mkdir hello
$ cd hello
$ mkdir -p src/main/java/com/ycbbs/tech
$ touch src/main/java/com/ycbbs/tech/HelloWorld.java
$ touch build.gradle
```

然后把以下内容复制到 `build.gradle` 文件中

```
apply plugin: 'java'
apply plugin: 'application'

mainClassName = 'com.ycbbs.tech.HelloWorld'

repositories {
    mavenLocal()
    mavenCentral()
}

jar {
    baseName = 'helloworld-gradle'
    version =  '0.1.0'
}

task wrapper(type: Wrapper) {
    gradleVersion = '1.11'
}

dependencies {
    compile 'org.mongodb:mongodb-driver:3.5.0'
  }
```

然后运行以下命令

```
$ gradle wrapper
$ gradlew build
$ gradlew run
```

## MongoDB Java 连接数据库 ycbbs ##

连接 `MongoDB` 数据库，需要指定数据库名称，如果指定的数据库不存在，`MongoDB` 会自动创建数据库

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import org.bson.Document;
import com.mongodb.MongoClient;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Filters;

public class HelloWorld 
{
   public static void main( String args[] )
   {
        try
        {   
            // 连接到 mongodb 服务
            MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

            // 连接到数据库
            MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
            System.out.println("Connect to database ycbbs successfully");

      }catch(Exception e){
            System.err.println( e.getClass().getName() + ": " + e.getMessage() );
     }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database ycbbs successfully
```

上面的范例，`MongoDB` 数据库不需要使用密码验证，如果需要使用密码验证，可以使用以下代码

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import java.util.ArrayList;  
import java.util.List;  
import com.mongodb.MongoClient;  
import com.mongodb.MongoCredential;  
import com.mongodb.ServerAddress;  
import com.mongodb.client.MongoDatabase;  

public class HelloWorld {  
    public static void main(String[] args)
    {  
        try {  
            //连接到MongoDB服务 如果是远程连接可以替换“localhost”为服务器所在IP地址  
            //ServerAddress()两个参数分别为 服务器地址 和 端口  
            ServerAddress serverAddress = new ServerAddress("localhost",27017);  
            List<ServerAddress> addrs = new ArrayList<ServerAddress>();  
            addrs.add(serverAddress);  

            //MongoCredential.createScramSha1Credential()三个参数分别为 用户名 数据库名称 密码  
            MongoCredential credential = MongoCredential.createScramSha1Credential("penglei", "ycbbs", "123abc321".toCharArray());  
            List<MongoCredential> credentials = new ArrayList<MongoCredential>();  
            credentials.add(credential);  

            //通过连接认证获取MongoDB连接  
            MongoClient mongoClient = new MongoClient(addrs,credentials);  

            //连接到数据库 ycbbs 
            MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
            System.out.println("Connect to database successfully");  
        } catch (Exception e) {  
            System.err.println( e.getClass().getName() + ": " + e.getMessage() );  
        }  
    }  
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database successfully
```

## 创建集合 ##

`com.mongodb.client.MongoDatabase` 类中的` createCollection()` 方法可以用来创建集合

下面的代码我们创建集合 **language**

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import com.mongodb.MongoClient;
import com.mongodb.client.MongoDatabase;

public class HelloWorld
{
   public static void main( String args[] )
   {
      try{   
      // 连接到 mongodb 服务
      MongoClient mongoClient = new MongoClient( "localhost" , 27017 );


      // 连接到数据库
      MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
      System.out.println("Connect to database successfully");
      mongoDatabase.createCollection("language");
      System.out.println("集合创建成功");

      }catch(Exception e){
        System.err.println( e.getClass().getName() + ": " + e.getMessage() );
     }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
集合创建成功
```

## 获取集合 ##

`com.mongodb.client.MongoDatabase` 类的 `getCollection()` 方法可以用来获取一个集合

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import org.bson.Document;
import com.mongodb.MongoClient;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;

public class HelloWorld {
   public static void main( String args[] )
   {
      try{   
       // 连接到 mongodb 服务
         MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

         // 连接到数据库
         MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
       System.out.println("Connect to database successfully");

       MongoCollection<Document> collection = mongoDatabase.getCollection("language");
       System.out.println("集合 language 选择成功");
      }catch(Exception e){
        System.err.println( e.getClass().getName() + ": " + e.getMessage() );
     }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database successfully
集合 language 选择成功
```

## 插入文档 ##

`com.mongodb.client.MongoCollection` 类 的 `insertMany()` 方法可以插入一个文档

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import java.util.ArrayList;
import java.util.List;
import org.bson.Document;

import com.mongodb.MongoClient;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;

public class HelloWorld
{
   public static void main( String args[] )
   {
      try{   
         // 连接到 mongodb 服务
         MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

         // 连接到数据库
         MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
         System.out.println("Connect to database successfully");

         MongoCollection<Document> collection = mongoDatabase.getCollection("language");
         System.out.println("集合 language 选择成功");

         //插入文档  
         /** 
         * 1. 创建文档 org.bson.Document 参数为key-value的格式 
         * 2. 创建文档集合List<Document> 
         * 3. 将文档集合插入数据库集合中 mongoCollection.insertMany(List<Document>) 插入单个文档可以用 mongoCollection.insertOne(Document) 
         * */

         Document document = new Document("name", "JAVA").  
         append("description", "language").  
         append("likes", 10000000).  
         append("by", "penglei");  
         List<Document> documents = new ArrayList<Document>();  
         documents.add(document);  
         collection.insertMany(documents);  
         System.out.println("文档插入成功");  
      }catch(Exception e){
         System.err.println( e.getClass().getName() + ": " + e.getMessage() );
      }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database successfully
集合 language 选择成功
文档插入成功
```

## 检索所有文档 ##

`com.mongodb.client.MongoCollection` 类中的` find()` 方法可以获取集合中的所有文档

此方法返回一个游标，因此我们需要遍历这个游标

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import org.bson.Document;
import com.mongodb.MongoClient;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;

public class HelloWorld
{
   public static void main( String args[] ){
      try{   
         // 连接到 mongodb 服务
         MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

         // 连接到数据库
         MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
         System.out.println("Connect to database successfully");

         MongoCollection<Document> collection = mongoDatabase.getCollection("language");
         System.out.println("集合 language 选择成功");

         //检索所有文档  
         /** 
         * 1. 获取迭代器FindIterable<Document> 
         * 2. 获取游标MongoCursor<Document> 
         * 3. 通过游标遍历检索出的文档集合 
         * */  
         FindIterable<Document> findIterable = collection.find();  
         MongoCursor<Document> mongoCursor = findIterable.iterator();  
         while(mongoCursor.hasNext()){  
            System.out.println(mongoCursor.next());  
         }  

      }catch(Exception e){
         System.err.println( e.getClass().getName() + ": " + e.getMessage() );
      }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database successfully
集合 language 选择成功
Document{{_id=59ef06274844491a17679515, name=JAVA, description=language, likes=10000000, by=penglei}}
```

## 更新文档 ##

`com.mongodb.client.MongoCollection` 类中的 `updateMany()` 方法可以更新集合中的文档

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import org.bson.Document;
import com.mongodb.MongoClient;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Filters;

public class HelloWorld {
   public static void main( String args[] ){
      try{   
         // 连接到 mongodb 服务
         MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

         // 连接到数据库
         MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
         System.out.println("Connect to database successfully");

         MongoCollection<Document> collection = mongoDatabase.getCollection("language");
         System.out.println("集合 language 选择成功");

         //更新文档   将文档中likes=10000000的文档修改为likes=15000000   
         collection.updateMany(Filters.eq("likes", 10000000), new Document("$set",new Document("likes",15000000)));  
         //检索查看结果  
         FindIterable<Document> findIterable = collection.find();  
         MongoCursor<Document> mongoCursor = findIterable.iterator();  
         while(mongoCursor.hasNext()){  
            System.out.println(mongoCursor.next());  
         }  

      }catch(Exception e){
         System.err.println( e.getClass().getName() + ": " + e.getMessage() );
      }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database successfully
集合 language 选择成功
Document{{_id=59ef06274844491a17679515, name=JAVA, description=language, likes=15000000, by=penglei}}
```

## 删除第一个文档 ##

要删除集合中的第一个文档

首先要使用 `com.mongodb.DBCollection` 类中的 `findOne()` 方法来获取第一个文档

然后使用 `remove` 方法删除

```
package com.ycbbs.tech;

/*
 * filename: HelloWorld.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import org.bson.Document;
import com.mongodb.MongoClient;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Filters;

public class HelloWorld {
   public static void main( String args[] ){
      try{   
         // 连接到 mongodb 服务
         MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

         // 连接到数据库
         MongoDatabase mongoDatabase = mongoClient.getDatabase("ycbbs");  
         System.out.println("Connect to database successfully");

         MongoCollection<Document> collection = mongoDatabase.getCollection("language");
         System.out.println("集合 language 选择成功");

         //删除符合条件的第一个文档  
         collection.deleteOne(Filters.eq("likes", 15000000));  
         //删除所有符合条件的文档  
         collection.deleteMany (Filters.eq("likes", 15000000));  
         //检索查看结果  
         FindIterable<Document> findIterable = collection.find();  
         MongoCursor<Document> mongoCursor = findIterable.iterator();  
         while(mongoCursor.hasNext()){  
           System.out.println(mongoCursor.next());  
         }  

      }catch(Exception e){
        System.err.println( e.getClass().getName() + ": " + e.getMessage() );
     }
   }
}
```

使用 **gradlew run** 运行以上 `JAVA` 代码，输出结果如下

```
$ gradlew run
Connect to database successfully
集合 language 选择成功
```

## 延伸阅读 ##

1、  更多操作可以参考： [https://docs.mongodb.com/][https_docs.mongodb.com]
2、  参考文档：[http://blog.csdn.net/ererfei/article/details/50857103][http_blog.csdn.net_ererfei_article_details_50857103]


[http_mongodb.github.io_mongo-java-driver]: http://mongodb.github.io/mongo-java-driver/
[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/01/32/img_1.png
[http_central.maven.org_maven2_org_mongodb_mongo-java-driver]: http://central.maven.org/maven2/org/mongodb/mongo-java-driver/
[https_docs.mongodb.com]: https://docs.mongodb.com/
[http_blog.csdn.net_ererfei_article_details_50857103]: http://blog.csdn.net/ererfei/article/details/50857103


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")