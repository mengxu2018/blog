---
title: kafka重复消息问题
date: 2021-11-29 12:32:06
tags: kafka
---

### 生产者重复消息
发送消息如果配置了重试机制，比如由于网络波动，生产者未得到broker收到了消息的响应，就会触发重试机制，3秒后再次发送此消息。broker之前已经收到过这个消息，但生产者由于触发了重试机制，就导致了消息的重复发送。那么broker就会在磁盘缓存多条同样的消息，消费端从broker拉取消息时，就会造成重复消费。

注意：kafka新版本已经在broker中保证了接收消息的幂等性（比如2.4版本），只需在生产者加上参数 props.put(“enable.idempotence”, true) 即可，默认是false不开启。
 
### 消费端消息重复消费
对于消费端消息的重复消费问题，如果消费端拉取了一部分数据，消费完毕后，准备执行手动提交（或自动提交）时，消费者挂掉了！此时offset还未提交呢，那么当服务重启时，还是会拉取相同的一批数据重复处理！造成消息重复消费

无论是生产者还是消费者的重复消息，一般都会在消费端卡死，做幂等性处理。
幂等性可以用redis的setnx分布式锁来实现。比如操作订单消息，可以把订单id作为key，在消费消息时，通过setnx命令设置一下，offset提交完成后，在redis中删除订单id的key。setnx命令保证同样的订单消息，只有一个能被消费，可有效保证消费的幂等性！

还有如下的思路：
引入单独去重机制，例如生成消息时，在消息中加入唯一标识符如消息id等。在消费端，我们可以保存最近的1000条消息id到redis或mysql表中，配置max.poll.records的值小于1000。在消费消息时先通过前置表去重后再进行消息的处理。

个人总结：
要谈去重问题，首先要了解enable.auto.commit，可以参考kafka权威指南第二版97页
如果是true，那么每隔5秒，会自动提交上一次poll出来的offset，这个是poll做的，每次poll的时候，会检查时间是不是到了，如果到了，就提交前一次poll出来的offset，因为这个commit offset都是内部poll来做的，所以他是不了解消费消息的具体情况
如果设置FALSE，自己控制提交，可以减少重复的消息，因为不可能拖很久不提交offset，基本一批消息处理完就立马commit了
但是rebalance后，每个consumer都可能会重新分配分区，所以会从最小的offset开始消费，就可能重复消费
避免重复消费需要自己业务上面，比如弄一个message_processed表，每个message弄个unique id，每次在自己做业务db操作的时候，也插入这个table，
这样就可以避免重复消费，具体做法可以收到消息先根据这个message id查询是不是查询的到，如果查询的到，说明已经处理过了，也可以直接依靠数据库的主键唯一报错来实现，
但是要让这两种db在同一个事务里面，所以如果这些操作涉及到其他资源，比如mq，文件系统，那么需要分布式事务来保证，
当然最好的办法还是幂等性的consumer业务，但是有些可能没法幂等性，比如在用户账户里面增加余额，这个多次update操作肯定没办法幂等性

关于自动提交其实比较麻烦，得看具体poll的实现才会更清楚，否则书中97页开始的地方会比较难理解为什么会丢失消息，最简单的就是
直接用false，然后自己控制消息的提交，spring已经默认设置为了false

参考https://betterprogramming.pub/how-to-handle-duplicate-messages-and-message-ordering-in-kafka-82e2fef82025

这个kakfa_duplicated_messages.png可以参考，为什么poll为丢失消息重复消息.




这个事务不是给数据库和mq两个资源的协调用的，这种是分布式事务

所以说消费消息的时候，最好不要同时再发送消息又操作数据库，不要同时操作两个资源

update on 2022.4.22
今天发现filewatch上面有部分重复消息，虽然我们有机制,检查数据库避免重复消费，但是我们需要分析下原因
1. 我们的consumer抛出了异常，可能就没办法commit offset了，这个时候offset会被reset到上一次的offset，然后就会导致一部分的消息被重复消费
2. 出现了rebalance，可能因为长时间心跳检测不到，这个时候也会重新分配了分区，也可能会导致之前的offset没有commit
https://kafka.apache.org/22/javadoc/org/apache/kafka/clients/consumer/ConsumerRebalanceListener.html



### reference
https://blog.51cto.com/u_15281317/3007783#3__51
https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/
https://medium.com/@andy.bryant/processing-guarantees-in-kafka-12dd2e30be0e
https://zhuanlan.zhihu.com/p/112745985
https://segmentfault.com/a/1190000023555975
https://stackoverflow.com/questions/29647656/effective-strategy-to-avoid-duplicate-messages-in-apache-kafka-consumer