作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

关于主从同步最新理解：[RocketMQ 主从同步若干问题答疑][RocketMQ]

RocketMQ在消息拉取时是如何根据消息消费队列 `MessageQueue` 来选择 `Broker` 的呢？消息消费队列如图所示：  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
`RocketMQ` 根据 `MessageQueue` 查找 `Broker` 地址的唯一依据便是 `brokerName`，从 `RocketMQ` 的 `Broker` 组织实现来看，同一组`Broker(M-S)`服务器，其 `brokerName` 相同，主服务器的brokerId为0，`从服务器的brokerId` 大于0，那 `RocketMQ` 根据 `brokerName` 如何定位到哪一台 `Broker` 上来呢？

PullAPIWrapper\#pullKernelImpl

```
FindBrokerResult findBrokerResult =
            this.mQClientFactory.findBrokerAddressInSubscribe(mq.getBrokerName(),
                this.recalculatePullFromWhichNode(mq), false);
```

`RocketMQ` 的 `MQClientInstance` 类提供了根据 `brokerName`、`brokerId` 查找 `Broker` 地址的方法，返回值如图：  
![img\_0914\_01\_2.png][img_0914_01_2.png]  
`MQClientInstance\#findBrokerAddressInSubscribe`

```
public FindBrokerResult findBrokerAddressInSubscribe(
        final String brokerName,
        final long brokerId,
        final boolean onlyThisBroker
    ) {
        String brokerAddr = null;
        boolean slave = false;
        boolean found = false;

        HashMap<Long/* brokerId */, String/* address */> map = this.brokerAddrTable.get(brokerName);
        if (map != null && !map.isEmpty()) {
            brokerAddr = map.get(brokerId);
            slave = brokerId != MixAll.MASTER_ID;
            found = brokerAddr != null;

            if (!found && !onlyThisBroker) {
                Entry<Long, String> entry = map.entrySet().iterator().next();
                brokerAddr = entry.getValue();
                slave = entry.getKey() != MixAll.MASTER_ID;
                found = true;
            }
        }

        if (found) {
            return new FindBrokerResult(brokerAddr, slave, findBrokerVersion(brokerName, brokerAddr));
        }

        return null;
    }
```

```html
 *  brokerName：broker名称；brokerId：brokerId；onlyThisBroker：是否必须返回brokerId的broker对应的服务器信息。
 *  brokerAddrTable地址缓存表中根据brokerName获取所有的broker信息。brokerAddrTable的存储格式如：brokerName：\{brokerId:brokerAddress\}。
 *  根据brokerId从broker主从缓存表中获取指定broker名称，如果根据brokerId未找到相关条目，此时如果onlyThisBroker为false,则随机返回broker中任意一个Broker，否则返回null。
 *  组装FindBrokerResult时，需要设置是否是slave这个属性。如果brokerId=0表示返回的broker是主节点，否则返回的是从节点。
```

上述方法，根据brokerName是如何获取brokerId的呢？

请看MQClientInstance\#recalculatePullFromWhichNode：

```
public long recalculatePullFromWhichNode(final MessageQueue mq) {
        if (this.isConnectBrokerByUser()) {
            return this.defaultBrokerId;
        }

        AtomicLong suggest = this.pullFromWhichNodeTable.get(mq);
        if (suggest != null) {
            return suggest.get();
        }

        return MixAll.MASTER_ID;
    }
```

首先从 `pullFromWhichNodeTable` 缓存表中获取该消息消费队列的 `brokerId`，如果找到，则返回，否则返回 `brokerName` 的主节点。由此可以看出 `pullFromWhichNodeTable` 中存放的是消息队列建议从从哪个Broker服务器拉取消息的缓存表，其存储结构：`MessageQueue`：`AtomicLong`，那该信息从何而来呢？

原来消息消费拉取线程 `PullMessageService` 根据 `PullRequest` 请求从主服务器拉取消息后会返回下一次建议拉取的 `brokerId`，消息消费者线程在收到消息后，会根据主服务器的建议拉取 `brokerId` 来更新 `pullFromWhichNodeTable`，消息消费者线程更新 `pullFromWhichNodeTable` 的代码如下：

PullAPIWrapper\#processPullResult

```
this.updatePullFromWhichNode(mq, pullResultExt.getSuggestWhichBrokerId());
public void updatePullFromWhichNode(final MessageQueue mq, final long brokerId) {
        AtomicLong suggest = this.pullFromWhichNodeTable.get(mq);
        if (null == suggest) {
            this.pullFromWhichNodeTable.put(mq, new AtomicLong(brokerId));
        } else {
            suggest.set(brokerId);
        }
    }
```

那服务端是如何计算下一次拉取建议从哪台 `Broker` 服务器拉取消息呢?

请看：`DefaultMessageStore\#getMessage`

```
long diff = maxOffsetPy - maxPhyOffsetPulling;
long memory = (long) (StoreUtil.TOTAL_PHYSICAL_MEMORY_SIZE * (this.messageStoreConfig.getAccessMessageInMemoryMaxRatio() / 100.0));
getResult.setSuggestPullingFromSlave(diff > memory);
```

```html
 *  maxOffsetPy：代表当前主服务器消息存储文件最大偏移量，maxPhyOffsetPulling：此次拉取消息最大偏移量。
 *  diff：对于PullMessageService线程来说，当前未被拉取到消息消费端的消息长度。
 *  TOTAL\_PHYSICAL\_MEMORY\_SIZE：RocketMQ所在服务器总内存大小；accessMessageInMemoryMaxRatio：表示RocketMQ所能使用的最大内存比例，超过该内存，消息将被置换出内存；memory表示RocketMQ消息常驻内存的大小，超过该大小，RocketMQ会将旧的消息置换会磁盘。
 *  如果diff大于memory,表示当前需要拉取的消息已经超出了常驻内存的大小，表示主服务器繁忙，此时才建议从从服务器拉取。
```

PullMessageProcessor\#processRequest

```
if (getMessageResult.isSuggestPullingFromSlave()) {
     responseHeader.setSuggestWhichBrokerId(subscriptionGroupConfig.getWhichBrokerWhenConsumeSlowly());
} else {
     responseHeader.setSuggestWhichBrokerId(MixAll.MASTER_ID);
}
```

当 `GetResult` 的 `suggestPullingFromSlave` 为真是，将会直接返回消息消费组的配置信息 `whichBrokerWhenConsumeSlowly`，默认为1，可以通过客户端命令 `updateSubGroup` 配置当主服务器繁忙时，建议从哪个从服务器读取消息。

> 注意：RocketMQ 读写分离不按套路出牌，并不是主服务器只负责消息发送，消息从服务器主要负责消息拉取，而是只有当主服务器消息拉取出现堆积时才将拉取任务转向从服务器。

--------------------

备注：本文是《RocketMQ技术内幕》的前期素材，建议关注笔者的书籍：《RocketMQ技术内幕》。

[RocketMQ]: https://mp.weixin.qq.com/s/QnFDFBOoI6pDZf8L2KpBDg
[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/19/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/19/img_0914_01_2.png



写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")