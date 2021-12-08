---
title: Kafka __consumer_offsets
date: 2021-12-06 14:09:24
tags: kafka
---

__consumer__offsets是一个内部topic，对用户而言是透明的，除了它的数据文件以及偶尔在日志中出现这两点之外，用户一般是感觉不到这个topic的

这类消息的key是一个三元组，格式是【groupId + topic + 分区号】，value则是要提交的位移信息

https://www.cnblogs.com/huxi2b/p/8316289.html


假如新跑一个consumer，新指定一个group id，那么这个consumer就属于另外一个consumer group，这个group中的consumer根据上面的三元组的key是没法从
__consumer__offsets找到这个partition的offset的，那么这个时候就要按照 offset reset policy是从earlist还是latest来读取这个partition上面的message 了
