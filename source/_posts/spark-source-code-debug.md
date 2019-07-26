---
title: spark source code debug
date: 2019-07-06 18:04:38
tags: spark
---

直接github下载spark的源码
\spark-2.4.2\core\src\test\scala\org\apache\spark\SortShuffleSuite.scala
```
test("mytest") {
 sc = new SparkContext("local", "test", conf)
 val dfsFilename = "c:/temp/abc.txt"
 val readFileRDD = sc.textFile(dfsFilename)
 val wcounts1 = readFileRDD.flatMap(line => line.split(" "))
 wcounts1.collect().foreach(println)
 }

test("mytest2") {
 sc = new SparkContext("local", "test", conf)
 val dfsFilename = "c:/temp/abc.txt"
 val readFileRDD = sc.textFile(dfsFilename)
 val wcounts1 = readFileRDD.flatMap(line => line.split(" ")).filter(w => (w == "Humpty") || (w == "Dumpty")).map(word => (word, 1)).reduceByKey(_ + _)
 wcounts1.collect.foreach(println)
 }
```
最方便的就是源码中找到一个sparkcontext创建好的，然后加上自己的代码就好了