作者：唯有坚持不懈 | 出处：[https://blog.csdn.net/prestigeding/article/details/78888290](https://blog.csdn.net/prestigeding/article/details/78888290)


RocketMQ事务消息阅读目录指引：

[RocketMQ源码分析之从官方示例窥探RocketMQ事务消息实现基本思想][RocketMQ_RocketMQ]  
[RocketMQ源码分析之RocketMQ事务消息实现原理上篇][RocketMQ_RocketMQ 1]  
[RocketMQ源码分析之RocketMQ事务消息实现原理中篇----事务消息状态回查][RocketMQ_RocketMQ_----]  
[RocketMQ源码分析之事务消息实现原理下篇-消息服务器Broker提交回滚事务实现原理][RocketMQ_-_Broker]  
[RocketMQ事务消息实战][RocketMQ]


   我们以一个订单流转流程来举例，例如订单子系统创建订单，需要将订单数据下发到其他子系统（与第三方系统对接）这个场景，我们通常会将两个系统进行解耦，不直接使用服务调用的方式进行交互。其业务实现步骤通常为：  
   1、A系统创建订单并入库。  
   2、发送消息到MQ。  
   3、MQ消费者消费消息，发送远程RPC服务调用，完成订单数据的同步。 
   
   1、方案一  
![img\_0914\_01\_1.png][img_0914_01_1.png]  

   方案弊端：  
   1、如果消息发送成功，在提交事务的时候JVM突然挂掉，事务没有成功提交，导致两个系统之间数据不一致。  
   2、由于消息是在事务提交之前提交，发送的消息内容是订单实体的内容，会造成在消费端进行消费时如果需要去验证订单是否存在时可能出现订单不存在。  
   3、消息发送可以考虑异步发送。  
   
   方案二：  
   由于存在上述问题，在MQ不支持事务消息的前提条件下，可以采用下面的方式进行优化。  
![img\_0914\_01\_2.png][img_0914_01_2.png]  
   然后在控制器层，使用异步发送，将消息发送，并在消息发送成功后，更新待发送状态为已发送。  
   
   然后通过定时任务，扫描待发送，结合创建时间的记录（小于当前时间5分钟的消息待发送记录），进行消息发送。  
   
   方案弊端：  
   1、消息有可能重复发送，但在消费端可以通过唯一业务编号来进行去重设计。  
   2、实现过于复杂，为了避免 极端情况下的消息丢失，需要使用定时任务。
   
   方案三：基于RocketMQ4.3版本事务消息  
![img\_0914\_01\_3.png][img_0914_01_3.png]  
   额外需要实现事务会查监听器：`TransactionListener`，其实例代码：

```
import org.apache.rocketmq.client.producer.LocalTransactionState;
import org.apache.rocketmq.client.producer.TransactionListener;
import org.apache.rocketmq.common.message.Message;
import org.apache.rocketmq.common.message.MessageExt;

import java.util.concurrent.ConcurrentHashMap;

@SuppressWarnings("unused")
public class OrderTransactionListenerImpl implements TransactionListener {
	
	private ConcurrentHashMap<String, Integer> countHashMap = new ConcurrentHashMap<>();
	
	private final static int MAX_COUNT = 5;


	@Override
    public LocalTransactionState executeLocalTransaction(Message msg, Object arg) {
        // 
    	String bizUniNo = msg.getUserProperty("bizUniNo"); // 从消息中获取业务唯一ID。
    	// 将bizUniNo入库，表名：t_message_transaction,表结构  bizUniNo(主键),业务类型。
        return LocalTransactionState.UNKNOW;
    }

    @Override
    public LocalTransactionState checkLocalTransaction(MessageExt msg) {
        Integer status = 0;
        // 从数据库查查询t_message_transaction表，如果该表中存在记录，则提交，
        String bizUniNo = msg.getUserProperty("bizUniNo"); // 从消息中获取业务唯一ID。
        // 然后t_message_transaction 表，是否存在bizUniNo，如果存在，则返回COMMIT_MESSAGE，
        // 不存在，则记录查询次数，未超过次数，返回UNKNOW，超过次数，返回ROLLBACK_MESSAGE
        
        if(query(bizUniNo) > 0 ) {
        	return LocalTransactionState.COMMIT_MESSAGE;
        }
        
        return rollBackOrUnown(bizUniNo);
    }
    
    public int query(String bizUniNo) {
    	return 1; //select count(1) from t_message_transaction a where a.biz_uni_no=#{bizUniNo}
    }
    
    public LocalTransactionState rollBackOrUnown(String bizUniNo) {
    	Integer num = countHashMap.get(bizUniNo);
    	
    	if(num != null &&  ++num > MAX_COUNT) {
    		countHashMap.remove(bizUniNo);
    		return LocalTransactionState.ROLLBACK_MESSAGE;
    	}
    	
    	if(num == null) {
    		num = new Integer(1);
    	}
    	
    	countHashMap.put(bizUniNo, num);
    	return LocalTransactionState.UNKNOW;
    	
    }
  
} 
```

   TransactionListener 实现要点：  
   `executeLocalTransaction`：  
   该方法，主要是设置本地事务状态，该方法与业务方代码在一个事务中，例如`OrderServer\#createMap`中，只要本地事务提交成功，该方法也会提交成功。  
   
   故在这里，主要是`t\_message\_transaction`添加一条记录，在事务会查时，如果存在记录，就认为是该消息需要提交。  
   `checkLocalTransaction`：  
   该方法主要是告知RocketMQ消息是否需要提交还是回滚，如果本地事务表（`t\_message\_transaction`）存在记录，则认为提交，如果不存在，可以设置会查次数，如果指定次数内还是未查到消息，则回滚，否则返回未知，rocketmq会按一定的频率回查事务，当然回查次数也有限制，默认为5次，可配置。

[RocketMQ_RocketMQ]: https://blog.csdn.net/prestigeding/article/details/81259646
[RocketMQ_RocketMQ 1]: https://blog.csdn.net/prestigeding/article/details/81263833
[RocketMQ_RocketMQ_----]: https://blog.csdn.net/prestigeding/article/details/81275892
[RocketMQ_-_Broker]: https://blog.csdn.net/prestigeding/article/details/81277067
[RocketMQ]: https://blog.csdn.net/prestigeding/article/details/81318980https://blog.csdn.net/prestigeding/article/details/81318980
[img_0914_01_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/24/img_0914_01_1.png
[img_0914_01_2.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/24/img_0914_01_2.png
[img_0914_01_3.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/24/img_0914_01_3.png
[img_0914_01_4.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/24/img_0914_01_4.png
[img_0914_01_5.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/09/0914/01/24/img_0914_01_5.png


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")

