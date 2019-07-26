---
title: hive 和spark的关系
date: 2019-06-27 22:51:52
tags: spark
---

https://www.quora.com/How-does-Apache-Spark-and-Apache-Hive-work-together/answer/Angela-Zhang-%E5%BC%B5%E5%AE%89%E7%90%AA
这个文章是讲的最好的

hive底层可以替换执行引擎为spark
spark也可以执行sql 基于hive的metastore

Spark SQL - Spark module that makes use of the Hive Metastore. You can use this via HiveContext. This currently (as of 2017) has better support, so we’ll talk more about this in the next section.

Hive on Spark - Hive project that integrates Spark as an additional engine. You can enable this by doing hive.execution.engine=spark. This support was added recently (2015 - 2016).