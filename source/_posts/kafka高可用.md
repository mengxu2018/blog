---
title: kafka高可用
date: 2019-07-20 20:29:34
tags: kafka
---

## 如果保证消息不丢失且不重复消费

https://www.zhihu.com/question/34842764
https://blog.csdn.net/matrix_google/article/details/79888144
https://blog.csdn.net/qingqing7/article/details/80054281
关于消息不丢失其实再kafka权威指南这本书已经讲过，但是不重复消费只是讲解了怎么减少重复消息的数量，

首先说明，Kafka 的设计就是 at-least-once 的， 具体可以看
https://www.infoq.cn/article/kafka-analysis-part-8
http://www.jasongj.com/2015/03/10/KafkaColumn1/#Kafka-delivery-guarantee

这里会涉及到幂等性，这个是数学的概念，用在编程领域，则意为对同一个系统，使用同样的条件，一次请求和重复的多次请求对系统资源的影响是一致的，还是要多学习数学，
https://www.jianshu.com/p/475589f5cd7b

baeldung也有文章介绍这个问题，
In this tutorial, we’ll look at how Kafka ensures exactly-once delivery between producer and consumer applications through the newly introduced Transactional API.
https://www.baeldung.com/kafka-exactly-once