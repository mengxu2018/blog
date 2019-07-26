---
title: zero-copy
date: 2019-07-26 21:24:19
tags: OS
---

"Zero-copy" describes computer operations in which the CPU does not perform the task of copying data from one memory area to another. This is frequently used to save CPU cycles and memory bandwidth when transmitting a file over a network.[1]

https://en.wikipedia.org/wiki/Zero-copy

https://blog.csdn.net/u013256816/article/details/52589524

Kafka在 消息消费的时候使用该技术
Consumer从Broker获取文件数据的时候，直接通过下面的方法进行channel到channel的数据传输。
```
java.nio.FileChannel.transferTo(
long position,
long count,
WritableByteChannel target)`
```

也就是说你的数据源是一个Channel,数据接收端也是一个Channel(SocketChannel),则通过该方式进行数据传输，是直接在内核态进行的，避免拷贝数据导致的内核态和用户态的多次切换。

https://cloud.tencent.com/developer/article/1195251

Java input streams can support zero-copy through the java.nio.channels.FileChannel's transferTo() method if the underlying operating system also supports zero copy.[5]

https://www.jianshu.com/p/7e5e558bf93e
http://tech.dianwoda.com/2019/01/02/linux-iohe-zero-copyzong-jie/





