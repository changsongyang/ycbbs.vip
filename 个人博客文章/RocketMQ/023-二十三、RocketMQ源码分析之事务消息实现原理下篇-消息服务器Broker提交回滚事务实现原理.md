作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)

--------------------

RocketMQ事务消息阅读目录指引：  
[RocketMQ源码分析之从官方示例窥探RocketMQ事务消息实现基本思想][RocketMQ_RocketMQ]  
[RocketMQ源码分析之RocketMQ事务消息实现原理上篇][RocketMQ_RocketMQ 1]  
[RocketMQ源码分析之RocketMQ事务消息实现原理中篇----事务消息状态回查][RocketMQ_RocketMQ_----]  
[RocketMQ源码分析之事务消息实现原理下篇-消息服务器Broker提交回滚事务实现原理][RocketMQ_-_Broker]  
[RocketMQ事务消息实战][RocketMQ]

--------------------

   本文将重点分析 `RocketMQ` `Broker` 如何处理事务消息提交、回滚命令，其核心实现就是根据 `commitlogOffset` 找到消息，如果是提交动作，就恢复原消息的主题与队列，再次存入 `commitlog` 文件进而转到消息消费队列，供消费者消费，然后将原预处理消息存入一个新的主题RMQ\_SYS\_TRANS\_OP\_HALF\_TOPIC，代表该消息已被处理；回滚消息与提交事务消息不同的是，提交事务消息会将消息恢复原主题与队列，再次存储在 `commitlog` 文件中。源码入口：  
  `EndTransactionProcessor\#processRequest`

```
OperationResult result = new OperationResult();
if (MessageSysFlag.TRANSACTION_COMMIT_TYPE == requestHeader.getCommitOrRollback()) {        // @1
result = this.brokerController.getTransactionalMessageService().commitMessage(requestHeader);    // @2
      if (result.getResponseCode() == ResponseCode.SUCCESS) {  // @3
      	  RemotingCommand res = checkPrepareMessage(result.getPrepareMessage(), requestHeader);    // @4
          if (res.getCode() == ResponseCode.SUCCESS) {
                MessageExtBrokerInner msgInner = endMessageTransaction(result.getPrepareMessage());     // @5
                msgInner.setSysFlag(MessageSysFlag.resetTransactionValue(msgInner.getSysFlag(), requestHeader.getCommitOrRollback()));
                msgInner.setQueueOffset(requestHeader.getTranStateTableOffset());
                msgInner.setPreparedTransactionOffset(requestHeader.getCommitLogOffset());
                msgInner.setStoreTimestamp(result.getPrepareMessage().getStoreTimestamp());    // @6
                RemotingCommand sendResult = sendFinalMessage(msgInner);                              // @7
                if (sendResult.getCode() == ResponseCode.SUCCESS) {             
                    this.brokerController.getTransactionalMessageService().deletePrepareMessage(result.getPrepareMessage());    // @8
                }
                return sendResult;
           }
          return res;
     }
}
```

   代码@1：如果请求为提交事务，进入事务消息提交处理流程。  
   代码@2：提交消息，别被这名字误导了，该方法主要是根据 `commitLogOffset` 从 `commitlog` 文件中查找消息返回 `OperationResult` 实例。  
![img\_0914\_01\_1.png][img_0914_01_1.png]  
    ● `private MessageExt prepareMessag`e ：消息对象。  
    ● `private int responseCode`：查找结果。  
    ● `private String responseRemark` ：错误提示。  
   代码@3：如果成功查找到消息，则继续处理，否则返回给客户端，消息未找到错误信息。  
   代码@4：验证消息必要字段。  
    ● 验证消息的生产组与请求信息中的生产者组是否一致。  
    ● 验证消息的队列偏移量（`queueOffset`）与请求信息中的偏移量是否一致。  
    ● 验证消息的 `commitLogOffset` 与请求信息中的 `CommitLogOffset` 是否一致。  
	
   代码@5:调用 `endMessageTransaction` 方法，该方法主要的目的就是恢复事务消息的真实的主题、队列，并设置事务ID。  
   
   代码@6：设置消息的相关属性，这一步应该直接在 `endMessageTransaction` 中实现就好，统一恢复原消息的数量，特别关注的是取消了事务相关的系统标记。  
   
   代码@7：发送最终消息，其实现原理非常简单，调用MessageStore将消息存储在commitlog文件中，此时的消息，会被转发到原消息主题对应的消费队列，被消费者消费。  
   
   代码@8：删除预处理消息(`prepare`)，其实是将消息存储在主题为：RMQ\_SYS\_TRANS\_OP\_HALF\_TOPIC的主题中，代表这些消息已经被处理（提交或回滚）。  
   上述就是事务消息提交的流程，事务回滚类似，接下来大概分析一下事务消息回滚的流程。  
   `EndTransactionProcessor\#processRequest`

```
 else if (MessageSysFlag.TRANSACTION_ROLLBACK_TYPE == requestHeader.getCommitOrRollback()) {
       result = this.brokerController.getTransactionalMessageService().rollbackMessage(requestHeader);    // @1
       if (result.getResponseCode() == ResponseCode.SUCCESS) {
            RemotingCommand res = checkPrepareMessage(result.getPrepareMessage(), requestHeader);
            if (res.getCode() == ResponseCode.SUCCESS) {
                this.brokerController.getTransactionalMessageService().deletePrepareMessage(result.getPrepareMessage());   // @2
            }
           return res;
       }
}
```

    代码@1：回滚消息，其实内部就是根据 `commitlogOffset` 查找消息。 
	
    代码@2：将消息存储在RMQ\_SYS\_TRANS\_OP\_HALF\_TOPIC中，代表该消息已被处理，与提交事务消息不同的是，提交事务消息会将消息恢复原主题与队列，再次存储在 `commitlog` 文件中。
	
    事务消息在Broker服务端的提交回滚流程就介绍到这了。其核心实现就是根据 `commitlogOffset` 找到消息，如果是提交动作，就恢复原消息的主题与队列，再次存入 `commitlog` 文件进而转到消息消费队列，供消费者消费，然后将原预处理消息存入一个新的主题RMQ\_SYS\_TRANS\_OP\_HALF\_TOPIC，代表该消息已被处理；回滚消息与提交事务消息不同的是，提交事务消息会将消息恢复原主题与队列，再次存储在 `commitlog` 文件中。


[RocketMQ_RocketMQ]: https://blog.csdn.net/prestigeding/article/details/81259646
[RocketMQ_RocketMQ 1]: https://blog.csdn.net/prestigeding/article/details/81263833
[RocketMQ_RocketMQ_----]: https://blog.csdn.net/prestigeding/article/details/81275892
[RocketMQ_-_Broker]: https://blog.csdn.net/prestigeding/article/details/81277067
[RocketMQ]: https://blog.csdn.net/prestigeding/article/details/81318980https://blog.csdn.net/prestigeding/article/details/81318980
[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/23/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/23/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/23/img_0914_01_3.png



写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")