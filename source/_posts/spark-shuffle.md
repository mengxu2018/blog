---
title: spark shuffle
date: 2019-06-26 22:51:52
tags: spark
---

the `Spark SQL` module contains the following default configuration: `spark.sql.shuffle.partitions` set to 200.

可以看到下面的执行计划有200个分区
```
scala> flightData2015.sort("count").explain()
== Physical Plan ==
*(2) Sort [count#12 ASC NULLS FIRST], true, 0
+- Exchange rangepartitioning(count#12 ASC NULLS FIRST, 200)
   +- *(1) FileScan csv [DEST_COUNTRY_NAME#10,ORIGIN_COUNTRY_NAME#11,count#12] Batched: false, Format: CSV, Location: InMemoryFileIndex[file:/root/spark/data/testdata/2015-summary.csv], PartitionFilters: [], PushedFilters: [], ReadSchema: struct<DEST_COUNTRY_NAME:string,ORIGIN_COUNTRY_NAME:string,count:int>
```
![shuffle-1](/images/shuffle-1.PNG)


spark sql是高级的api，不是spark.default.parallelism?这个属性有关的rdd的底层api
这种高级api的spark.sql.shuffle.partitions是在shuffle的时候分区

这个好像跟之前的理解不一样，难道没有shuffle的transformation不需要分区？

http://blog.itpub.net/31511218/viewspace-2213494/
上面这个文章讲的不错
narrow transformation的分区跟文件大小有关系，wide transformation会需要设置spark.sql.shuffle.partitions，应该就是这样



What is the difference between spark.sql.shuffle.partitions and spark.default.parallelism?
https://stackoverflow.com/questions/45704156/what-is-the-difference-between-spark-sql-shuffle-partitions-and-spark-default-pa
spark.default.parallelism只有在处理RDD时才会起作用，对Spark SQL的无效。
spark.sql.shuffle.partitions则是对sparks SQL专用的设置


## 更新
Shuffling is a process of redistributing data across partitions (aka repartitioning) that may or may not cause moving data across JVM processes or even over the wire (between executors on separate machines).
所以shuffle 不一定是跨jvm或者node的操作，也可能一个stage，8个machine，8个task都shuffle 了，但是下一个stage只有一个task，在一个node上面运行，这个task的shuffle从当前的机器加上另外7台机器的数据 ，新的task应该还是之前的jvm，不是新启动一个jvm，还是在当前的executor上面