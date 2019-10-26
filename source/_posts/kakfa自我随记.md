---
title: kakfa自我随记
date: 2019-07-26 21:24:32
tags: kafka
---

1. kafka利用page cache提升性能，但是page cache有风险，如果机器crash了，数据会丢失，但是kakfa利用replica保证了可用性

2. For a topic with replication factor N, we will tolerate up to N-1 server failures without losing any records committed to the log.
一个topic ，默认分区的replication个数，不能大于集群中broker的个数。
分区数量可以超过机器数量，但是replication数量超过机器数量没意义了，因为这个本来就是用来容错的

3.  consumer-offset这个内置topic的 分区数量和replication数量是server.proxx配置的
其他的topic可以命令行创建的时候设定分区数量和replication数量