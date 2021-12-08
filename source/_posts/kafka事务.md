---
title: kafka事务
date: 2021-12-04 11:24:58
tags: kafka
---

### kafka事务
kakfa事务是给kakfa stream用的，如果不是用kafka stream，也可以自己写
可以参考page 198， kakfa权威指南第二版
kafka事务是在同一个程序中，producer先开启事务，然后consumer得到message，然后处理message，然后再用这个producer发送message到另外的topic，
然后consumer的offset也是这个producer来手动提交，最后提交事务，
（read-process-write模式：将消息消费和生产封装在一个事务中，形成一个原子操作。在一个流式处理的应用中，常常一个服务需要从上游接收消息，然后经过处理后送达到下游，这就对应着消息的消费和生成）
https://www.jianshu.com/p/64c93065473e