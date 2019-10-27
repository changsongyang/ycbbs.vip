## 引言
`JAVA` 可以通过 `jedis` 包访问 `Redis`

## 安装 jedis 包 ##

`JAVA` 语言访问 `Redis` 需要先安装 `Redis` 服务和 `jedis` 包

如果你还未安装 `JAVA`，可以移步我们的 `JAVA` 开发环境配置

然后通过以下地址下载 **jedis** 包：[ **jedis 2.9.0** ][_jedis 2.9.0_]

把下载好的 **jedis.jar** 放在 **CLASSPATH**\* 目录下

## JAVA 连接到 Redis 服务 ##

```
/*
 * filename: JavaRedisDemo.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import redis.clients.jedis.Jedis;

public class JavaRedisDemo 
{
    public static void main(String[] args) 
    {
        //连接本地的 Redis 服务
        Jedis jedis = new Jedis("localhost");
        System.out.println("连接成功");

        //查看服务是否运行
        System.out.println("服务正在运行: "+jedis.ping());
    }
}
```

编译运行以上 `JAVA` 范例，输出结果如下

```
$ javac JavaRedisDemo.java && java JavaRedisDemo
连接成功
服务正在运行: PONG
```

## Java 存储/获取 Redis 字符串( String ) ##

```
/*
 * filename: JavaRedisString.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import redis.clients.jedis.Jedis;

public class JavaRedisString 
{
    public static void main(String[] args) 
    {
        //连接本地的 Redis 服务
        Jedis jedis = new Jedis("localhost");
        System.out.println("连接成功");

        //设置 redis 字符串数据
        jedis.set("site", "www.ycbbs.vip");

        // 获取存储的数据并输出
        System.out.println("redis 存储的字符串为: "+ jedis.get("site"));
    }
}
```

编译运行以上 `JAVA` 范例，输出结果如下

```
$ javac JavaRedisString.java && java JavaRedisString
连接成功
redis 存储的字符串为: www.ycbbs.vip
```

## JAVA 访问 Redis 列表( List ) ##

```
/*
 * filename: JavaRedisList.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import java.util.List;
import redis.clients.jedis.Jedis;

public class JavaRedisList
{
    public static void main(String[] args)
    {
        //连接本地的 Redis 服务
        Jedis jedis = new Jedis("localhost");
        System.out.println("连接成功");

        //存储数据到列表中
        jedis.lpush("language", "Python2");
        jedis.lpush("language", "C++");
        jedis.lpush("language", "Perl");

        // 获取存储的数据并输出
        List<String> list = jedis.lrange("language", 0 ,2);

        for(int i=0; i<list.size(); i++) 
        {
            System.out.println("列表项为: "+list.get(i));
        }
    }
}
```

编译运行以上 `JAVA` 范例，输出结果如下

```
$ javac JavaRedisList.java && java JavaRedisList
连接成功
列表项为: Python2
列表项为: C++
列表项为: Perl
```

## Java 访问 Redis 键 ( Keys ) ##

```
/*
 * filename: JavaRedisKey.java
 * author: 研发军团(www.ycbbs.vip)
 * Copyright © 2015-2065 www.ycbbs.vip. All rights reserved.
*/

import java.util.Iterator;
import java.util.Set;
import redis.clients.jedis.Jedis;

public class JavaRedisKey
{
    public static void main(String[] args)
    {
        //连接本地的 Redis 服务
        Jedis jedis = new Jedis("localhost");
        System.out.println("连接成功");

        // 获取数据并输出
        Set<String> keys = jedis.keys("*"); 
        Iterator<String> it=keys.iterator() ;   
        while(it.hasNext())
        {   
            String key = it.next();   
            System.out.println(key);   
        }
    }
}
```

编译运行以上 `JAVA` 范例，输出结果如下

```
$ javac JavaRedisList.java && java JavaRedisList
连接成功
site
language
```
[_jedis 2.9.0_]: https://mvnrepository.com/artifact/redis.clients/jedis


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")