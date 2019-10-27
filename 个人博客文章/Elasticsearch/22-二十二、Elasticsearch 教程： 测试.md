`Elasticsearch` 还提供了一个 `jar` 文件，用于测试 `Elasticsearch` 相关的代码

我们可以将它添加到任何 `Java` `IDE` 中，接着使用 `Elasticsearch` 提供的框架的进行一系列的测试，例如

1、单元测试
2、集成测试
3、随机测试

开始测试前，我们需要将 `Elasticsearch` 测试依赖项添加到我们的程序中

如果你使用 `maven` 来管理项目，那么可以在 `pom.xml` 中添加以下内容

```
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>6.3.0</version>
</dependency>
```

接着初始化 `EsSetup`，然后就可以用它来启动启动和停止 `Elasticsearch` 节点并创建索引

```
EsSetup esSetup = new EsSetup();
```

最后可以调用 `esSetup.execute()` 方法并传递 `createIndex` 参数和其它配置、类型、数据等参数来创建索引

## 单元测试 ##

可以使用 `JUnit` 和 `Elasticsearch` 测试框架来运行单元测试

使用 `Elasticsearch` 类创建节点和索引，并且可以使用 `ESTestCase`和 `ESTokenStreamTestCase` 类来执行测试

## 集成测试 ##

集成测试需要使用 `ESIntegTestCase` 类，而且可以使用集群中的多个节点

Elasticsearch 提供了大量的方法用于集成测试

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">refresh()</td> 
   <td align="left">重新更新集群中的所有索引</td> 
  </tr> 
  <tr> 
   <td align="left">ensureGreen()</td> 
   <td align="left">确保集群处于健康的运行状态</td> 
  </tr> 
  <tr> 
   <td align="left">ensureYellow()</td> 
   <td align="left">确保集群处于亚健康的黄色运行状态</td> 
  </tr> 
  <tr> 
   <td align="left">createIndex(name)</td> 
   <td align="left">使用传递的参数 <code>name</code> 创建一个索引，该索引使用默认配置</td> 
  </tr> 
  <tr> 
   <td align="left">flush()</td> 
   <td align="left">更新集群中的所有索引</td> 
  </tr> 
  <tr> 
   <td align="left">flushAndRefresh()</td> 
   <td align="left">顺序调用 <code>flush()</code> 和 <code>refresh()</code> 方法</td> 
  </tr> 
  <tr> 
   <td align="left">indexExists(name)</td> 
   <td align="left">判断 <code>name</code> 索引是否存在</td> 
  </tr> 
  <tr> 
   <td align="left">clusterService()</td> 
   <td align="left">返回集群服务 Java 类</td> 
  </tr> 
  <tr> 
   <td align="left">cluster()</td> 
   <td align="left">返回集群测试类</td> 
  </tr> 
 </tbody> 
</table>

### 提供的用于测试集群的方法 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">ensureAtLeastNumNodes(n)</td> 
   <td align="left">确保集群中的节点数量的最小值大于等于参数 <code>n</code></td> 
  </tr> 
  <tr> 
   <td align="left">ensureAtMostNumNodes(n)</td> 
   <td align="left">确保集群中节点数量的最大值是否小于等于参数 <code>n</code></td> 
  </tr> 
  <tr> 
   <td align="left">stopRandomNode()</td> 
   <td align="left">随机停止集群中的某个节点</td> 
  </tr> 
  <tr> 
   <td align="left">stopCurrentMasterNode()</td> 
   <td align="left">停止当前主节点</td> 
  </tr> 
  <tr> 
   <td align="left">stopRandomNonMaster()</td> 
   <td align="left">随机停止集群中不是主节点的其它节点</td> 
  </tr> 
  <tr> 
   <td align="left">buildNode()</td> 
   <td align="left">创建一个新的节点</td> 
  </tr> 
  <tr> 
   <td align="left">startNode(settings)</td> 
   <td align="left">使用配置 <code>settings</code> 开始一个新的节点</td> 
  </tr> 
  <tr> 
   <td align="left">nodeSettings()</td> 
   <td align="left">可以重写该方法来更改节点配置</td> 
  </tr> 
 </tbody> 
</table>

### 提供的用于访问客户端的方法 ###

客户端用于访问群集中的不同节点并执行某些操作

可以用 `ESIntegTestCase.client()` 方法随机获取一个客户端

Elasticsearch 还提供了访问客户端的其它方法，可以使用 `ESIntegTestCase.internalCluster()` 方法的返回值来调用这些方法

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">iterator()</td> 
   <td align="left">用于迭代访问所有的可用客户端</td> 
  </tr> 
  <tr> 
   <td align="left">masterClient()</td> 
   <td align="left">返回一个可以跟主节点进行通讯的客户端</td> 
  </tr> 
  <tr> 
   <td align="left">nonMasterClient()</td> 
   <td align="left">返回一个不可以跟主节点进行通讯的客户端</td> 
  </tr> 
  <tr> 
   <td align="left">clientNodeClient()</td> 
   <td align="left">返回客户端节点中的当前客户端</td> 
  </tr> 
 </tbody> 
</table>

## 随机测试 ##

如果需要测试用户的代码和每个可能的数据，以便将来使用任何类型的数据都不会失败，那么随机数据是执行此测试的最佳选择

### 生成随机数据 ###

这种测试方法中，可以使用 `RandomizedTest` 的实例来实例化一个`Random` 类的对象，然后通过该对象上的不同方法来获取不同类型的数据

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">返回值</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">getRandom()</td> 
   <td align="left">随机返回一个类的实例</td> 
  </tr> 
  <tr> 
   <td align="left">randomBoolean()</td> 
   <td align="left">随机返回一个布尔值 ( boolean )</td> 
  </tr> 
  <tr> 
   <td align="left">randomByte()</td> 
   <td align="left">随机返回一个字节数据 ( byte )</td> 
  </tr> 
  <tr> 
   <td align="left">randomShort()</td> 
   <td align="left">随机返回一个短整数 ( short )</td> 
  </tr> 
  <tr> 
   <td align="left">randomInt()</td> 
   <td align="left">随机返回一个整形 ( integer )</td> 
  </tr> 
  <tr> 
   <td align="left">randomLong()</td> 
   <td align="left">随机返回一个长整型 ( long )</td> 
  </tr> 
  <tr> 
   <td align="left">randomFloat()</td> 
   <td align="left">随机返回一个浮点数 ( float )</td> 
  </tr> 
  <tr> 
   <td align="left">randomDouble()</td> 
   <td align="left">随机返回一个双精度浮点数 ( double )</td> 
  </tr> 
  <tr> 
   <td align="left">randomLocale()</td> 
   <td align="left">随机返回一个地区 ( locale )</td> 
  </tr> 
  <tr> 
   <td align="left">randomTimeZone()</td> 
   <td align="left">随机返回一个时区 ( time zone )</td> 
  </tr> 
  <tr> 
   <td align="left">randomFrom()</td> 
   <td align="left">随机返回数组中的一个元素</td> 
  </tr> 
 </tbody> 
</table>

## 断言 ( Assertions) ##

`ElasticsearchAssertions` 和 `ElasticsearchGeoAssertions` 类包含一些可以在测试时进行常见检查的断言

例如

```
SearchResponse seearchResponse = client().prepareSearch();
assertHitCount(searchResponse, 6);
assertFirstHit(searchResponse, hasId("6"));
assertSearchHits(searchResponse, "1", "2", "3", "4",”5”,”6”);
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")