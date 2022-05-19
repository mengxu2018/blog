---
title: Spark Client和Cluster两种运行模式的工作流程
date: 2019-05-30 22:51:52
tags: spark
---


## 伪分布式搭建
- client mode: In client mode, the driver is launched in the same process as the client that submits the application..也就是说在Client模式下，Driver进程会在当前客户端启动，客户端进程一直存在直到应用程序运行结束。
- cluster模式：In cluster mode, however, the driver is launched from one of the Worker processes inside the cluster, and the client process exits as soon as it fulfills its responsibility of submitting the application without waiting for the application to finish.也就是说，在cluster模式下，Driver进程将会在集群中的一个worker中启动，而且客户端进程在完成自己提交任务的职责后，就可以退出，而不用等到应用程序执行完毕

这里的driver模式仅仅是用户的程序在哪里跑，但是实际上真的的计算逻辑还是在集群里面

Driver program： The process running the main() function of the application and creating the SparkContext
Deploy mode	：Distinguishes where the driver process runs. In "cluster" mode, the framework launches the driver inside of the cluster. In "client" mode, the submitter launches the driver outside of the cluster.

Driver负责整个应用任务的job的划分和stage的切割以及task的切割和优化，并负责把task分发到worker对应的节点的executor进程中的task线程中执行, 并获取task的执行结果，Driver通过SparkContext对象与spark集群获取联系，得到master主机host，就可以通过rpc向master注册自己。
 

## reference
https://blog.csdn.net/SummerMangoZz/article/details/72627518