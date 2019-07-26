---
title: Spark Client和Cluster两种运行模式的工作流程
date: 2019-05-30 22:51:52
tags: spark
---


## 伪分布式搭建
- client mode: In client mode, the driver is launched in the same process as the client that submits the application..也就是说在Client模式下，Driver进程会在当前客户端启动，客户端进程一直存在直到应用程序运行结束。
- cluster模式：In cluster mode, however, the driver is launched from one of the Worker processes inside the cluster, and the client process exits as soon as it fulfills its responsibility of submitting the application without waiting for the application to finish.也就是说，在cluster模式下，Driver进程将会在集群中的一个worker中启动，而且客户端进程在完成自己提交任务的职责后，就可以退出，而不用等到应用程序执行完毕

## reference
https://blog.csdn.net/SummerMangoZz/article/details/72627518