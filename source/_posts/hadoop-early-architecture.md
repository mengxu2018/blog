---
title: old Hadoop Architecture
date: 2019-06-13 22:51:52
tags:
---

关于yarn之前的hadooop架构请参考下面这个文章
https://www.journaldev.com/8808/hadoop1-architecture-and-how-major-components-works
mapreduce的jobtracker和hdfs的name node是在一起的， mapreduce的tasktracker和hdfs的data node是一起的

yarn架构
https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-yarn/
新旧 Hadoop MapReduce 框架比对
让我们来对新旧 MapReduce 框架做详细的分析和对比，可以看到有以下几点显著变化：

首先客户端不变，其调用 API 及接口大部分保持兼容，这也是为了对开发使用者透明化，使其不必对原有代码做大的改变 ( 详见 2.3 Demo 代码开发及详解)，但是原框架中核心的 JobTracker 和 TaskTracker 不见了，取而代之的是 ResourceManager, ApplicationMaster 与 NodeManager 三个部分。