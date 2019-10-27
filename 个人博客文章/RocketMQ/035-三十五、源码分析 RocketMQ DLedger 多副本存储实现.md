作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

### 本节目录 ###

 *  1、DLedger 存储相关类图
    
     *  1.1 DLedgerStore
     *  1.2 DLedgerMemoryStore
     *  1.3 DLedgerMmapFileStore
    
    2、DLedger 存储 对标 RocketMQ 存储
    
    3、DLedger 数据存储格式
    
    4、DLedger 索引存储格式
    
    5、思考

  
`RocketMQ` `DLedger` 的存储实现思路与 `RocketMQ` 的存储实现思路相似，本文就不再从源码角度详细剖析其实现，只是点出其实现关键点。我们不妨简单回顾一下 `CommitLog` 文件、`ConsumeQueue` 文件设计思想。

其文件组成形式如下：  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
正如上图所示，多个 `commitlog` 文件组成一个逻辑上的连续文件，使用 `MappedFileQueue` 表示，单个 `commitlog` 文件使用 `MappedFile` 表示。

> 温馨提示：如果想详细了解 RocketMQ 关于存储部分的讲解，可以关注笔者的《RocketMQ 技术内幕》一书。

## 1、DLedger 存储相关类图 ##

![img\_0914\_01\_2.png][img_0914_01_2.png]

### 1.1 DLedgerStore ###

存储抽象类，定义如下核心方法：

```html
 *  public abstract DLedgerEntry appendAsLeader(DLedgerEntry entry)  
    向主节点追加日志(数据)。
 *  public abstract DLedgerEntry appendAsFollower(DLedgerEntry entry, long leaderTerm, String leaderId)  
    向从节点同步日志。
 *  public abstract DLedgerEntry get(Long index)  
    根据日志下标查找日志。
 *  public abstract long getCommittedIndex()  
    获取已提交的下标。
 *  public abstract long getLedgerEndTerm()  
    获取 Leader 当前最大的投票轮次。
 *  public abstract long getLedgerEndIndex()  
    获取 Leader 下一条日志写入的下标（最新日志的下标）。
 *  public abstract long getLedgerBeginIndex()  
    获取 Leader 第一条消息的下标。
 *  public void updateCommittedIndex(long term, long committedIndex)  
    更新commitedIndex的值，为空实现，由具体的存储子类实现。
 *  protected void updateLedgerEndIndexAndTerm()  
    更新 Leader 维护的 ledgerEndIndex 和 ledgerEndTerm 。
 *  public void flush()  
    刷写，空方法，由具体子类实现。
 *  public long truncate(DLedgerEntry entry, long leaderTerm, String leaderId)  
    删除日志，空方法，由具体子类实现。
 *  public void startup()  
    启动存储管理器，空方法，由具体子类实现。
 *  public void shutdown()  
    关闭存储管理器，空方法，由具体子类实现。
```

### 1.2 DLedgerMemoryStore ###

Dledger 基于内存实现的日志存储。

### 1.3 DLedgerMmapFileStore ###

基于文件内存映射机制的存储实现。其核心属性如下：

```html
*  long ledgerBeginIndex = -1  
    日志的起始索引，默认为 -1。  
    l- ong ledgerEndIndex = -1  
    下一条日志下标，默认为 -1。
 *  long committedIndex = -1  
    已提交的日志索引。
 *  long ledgerEndTerm  
    当前最大的投票轮次。
 *  DLedgerConfig dLedgerConfig  
    DLedger 的配置信息。
 *  MemberState memberState  
    状态机。
 *  MmapFileList dataFileList  
    日志文件(数据文件)的内存映射Queue。
 *  MmapFileList indexFileList  
    索引文件的内存映射文件集合。（可对标 RocketMQ MappedFIleQueue )。
 *  ThreadLocal< ByteBuffer> localIndexBuffer  
    本地线程变量，用来缓存索引ByteBuffer。
 *  ThreadLocal< ByteBuffer> localEntryBuffer  
    本地线程变量，用来缓存数据索引ByteBuffer。
 *  FlushDataService flushDataService  
    数据文件刷盘线程。
 *  CleanSpaceService cleanSpaceService  
    清除过期日志文件线程。
 *  boolean isDiskFull = false  
    磁盘是否已满。
 *  long lastCheckPointTimeMs  
    上一次检测点（时间戳）。
 *  AtomicBoolean hasLoaded  
    是否已经加载，主要用来避免重复加载(初始化)日志文件。
 *  AtomicBoolean hasRecovered  
    是否已恢复。
```

## 2、DLedger 存储 对标 RocketMQ 存储 ##

存储部分主要包含存储映射文件、消息存储格式、刷盘、文件加载与文件恢复、过期文件删除等，由于这些内容在 `RocketMQ` 存储部分都已详细介绍，故本文点到为止，其对应的参考映射如下：  
![img\_0914\_01\_3.png][img_0914_01_3.png]  
在 `RocketMQ` 中使用 `MappedFile` 来表示一个物理文件，而在 `DLedger` 中使用 `DefaultMmapFIle` 来表示一个物理文件。

在 `RocketMQ` 中使用 `MappedFile` 来表示多个物理文件(逻辑上连续)，而在 `DLedger` 中则使用 `MmapFileList`。

在 `RocketMQ` 中使用 `DefaultMessageStore` 来封装存储逻辑，而在 `DLedger` 中则使用 `DLedgerMmapFileStore` 来封装存储逻辑。

在 `RocketMQ` 中使用 CommitlogF l u s h C o m m i t L o g S e r v i c e 来 实 现 c o m m i t l o g 文 件 的 刷 盘 ， 而 在 D L e d g e r 中 则 使 用 D L e d g e r M m a p F i l e S t o r e `FlushCommitLogService` 来实现 commitlog 文件的刷盘，而在 `DLedger` 中则使用 `DLedgerMmapFileStoreFlushCommitLogService` 来实现commitlog文件的刷盘，而在DLedger中则使用 `DLedgerMmapFileStoreFlushDataService` 来实现文件刷盘。

在 RocketMQ 中使用 DefaultMessageStoreC l e a n C o m m i t l o g S e r v i c e 来 实 现 c o m m i t l o g 过 期 文 件 的 删 除 ， 而 D L e d g e r 中 则 使 用 D L e d g e r M m a p F i l e S t o r e `CleanCommitlogService` 来实现 `commitlog` 过期文件的删除，而 `DLedger` 中则使用 `DLedgerMmapFileStoreCleanCommitlogService`来实现 `commitlog` 过期文件的删除，而DLedger中则使用 `DLedgerMmapFileStoreCleanSpaceService` 来实现。

由于其实现原理相同，上述部分已经在《`RocketMQ` 技术内幕》第4章中详细剖析，故这里就不重复分析了。

## 3、DLedger 数据存储格式 ##

![img\_0914\_01\_4.png][img_0914_01_4.png]  
存储格式字段的含义如下：

```html
*  magic  
    魔数，4字节。
 *  size  
    条目总长度，包含 Header(协议头) + 消息体，占4字节。
 *  entryIndex  
    当前条目的 index，占8字节。
 *  entryTerm  
    当前条目所属的 投票轮次，占8字节。
 *  pos  
    该条目的物理偏移量，类似于 commitlog 文件的物理偏移量，占8字节。
 *  channel  
    保留字段，当前版本未使用，占4字节。
 *  chain crc  
    当前版本未使用，占4字节。
 *  body crc  
    body 的 CRC 校验和，用来区分数据是否损坏，占4字节。
 *  body size  
    用来存储 body 的长度，占4个字节。
 *  body  
    具体消息的内容。
```

源码参考点：`DLedgerMmapFileStore\#recover、DLedgerEntry、DLedgerEntryCoder`。

## 4、DLedger 索引存储格式 ##

![img\_0914\_01\_5.png][img_0914_01_5.png]  
即一个索引条目占32个字节。

## 5、思考 ##

`DLedger` 存储相关就介绍到这里，为了与大家增加互动，特提出如下两个思考题，欢迎与作者互动，这些问题将在该系列的后面文章专题探讨。

1、`DLedger` 如果整合 `RocketMQ` 中的 `commitlog` 文件，使之支持多副本？  
2、从老版本如何升级到新版本，需要考虑哪些因素呢？

尊敬的读者朋友们，都阅读到这里了，麻烦帮忙点个赞鼓励一下我，谢谢。


[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/35/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/35/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/35/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/35/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/35/img_0914_01_5.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")