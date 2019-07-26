---
title: spark stage
date: 2019-06-23 22:51:52
tags: spark
---

## intro

https://mapr.com/blog/how-spark-runs-your-applications/
Shuffling is a process of redistributing data across partitions (aka repartitioning) that may or may not cause moving data across JVM processes or even over the wire (between executors on separate machines).
Shuffling is the process of data transfer between stages.


When you invoke an action on an RDD, a "job" is created. Jobs are work submitted to Spark.
Jobs are divided into "stages" based on the shuffle boundary. This can help you understand.
Each stage is further divided into tasks based on the number of partitions in the RDD. So tasks are the smallest units of work for Spark.
Wide transformations basically result in stage boundaries.
spark的stage是各个shuffle操作的边界，也就是如果没有shuffle发生的话，还是在一个stage里面，4040端口看stage的相关信息加深理解
https://medium.com/@goyalsaurabh66/spark-basics-rdds-stages-tasks-and-dag-8da0f52f0454
the stages are created based on the transformations. The narrow transformations will be grouped (pipe-lined) together into a single stage.

action的操作比如collect一般算在最后一个stage里面