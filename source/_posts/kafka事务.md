---
title: kafka事务
date: 2021-12-04 11:24:58
tags: kafka
---

### kafka事务
kakfa事务可以给kakfa stream用
可以参考page 198， kakfa权威指南第二版
kafka事务是在同一个程序中，producer先开启事务，然后consumer得到message，然后处理message，然后再用这个producer发送message到另外的topic，
然后consumer的offset也是这个producer来手动提交，最后提交事务，
（read-process-write模式：将消息消费和生产封装在一个事务中，形成一个原子操作。在一个流式处理的应用中，常常一个服务需要从上游接收消息，然后经过处理后送达到下游，这就对应着消息的消费和生成）
https://www.jianshu.com/p/64c93065473e




下面的文章讲kafka事务非常好
https://www.confluent.io/blog/transactions-apache-kafka/
https://developer.confluent.io/learn/kafka-transactions-and-guarantees/?_ga=2.178254118.229631040.1652850993-2006537077.1617180632
第二篇文章Using the Transactions API for Multiple Topic Writing，可以通过事务控制发两个topic消息在一个事务里面


https://medium.com/@andy.bryant/processing-guarantees-in-kafka-12dd2e30be0e#:~:text=Consumers%20will%20receive%20and%20process,will%20process%20every%20message%20once.
exactly once特性也是给kafka stream用的
    