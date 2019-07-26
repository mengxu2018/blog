---
title: kafka存储
date: 2019-07-20 21:54:26
tags: kafka
---
Kafka很大程度上依赖文件系统来存储和缓存消息。有一普遍的认识：磁盘很慢。这让人们怀疑使用磁盘作为持久化的性能。实际上，磁盘是快还是慢完全取决于我们是如何使用它。

就目前来说，一个7200rpm SATA RAID-5磁盘线性（顺序）写入的性能能达到600MB/sec，而任意位置写（寻址再写）的性能只有100k/sec。这些线性读写是所有使用模式中最可预测的，并且由操作系统进行了大量优化。现在的操作系统提供了预读取和后写入的技术。实际上你会发现，顺序的磁盘读写比任意的内存读写更快。 

发现线性的访问磁盘（即：按顺序的访问磁盘），很多时候比随机的内存访问快得多，而且有利于持久化

**基于jvm内存有以下缺点**
- 对象的内存开销非常高，通常会让存储数据的大小加倍（或更多）
- 随着堆内数据的增加，GC的速度越来越慢，而且可能导致错误

**基于操作系统的文件系统来设计有以下好处：**
- 可以通过os的pagecache来有效利用主内存空间，由于数据紧凑，可以cache大量数据，并且没有gc的压力
- 即使服务重启，缓存中的数据也是热的（不需要预热）。而基于进程的缓存，需要程序进行预热，而且会消耗很长的时间。（10G大概需要10分钟）
- 大大简化了代码。因为在缓存和文件系统之间保持一致性的所有逻辑都在OS中。以上建议和设计使得代码实现起来十分简单，不需要尽力想办法去维护内存中的数据，数据会立即写入磁盘。

总的来说，kafka这种是以页面缓存（pagecache）为中心的设计风格, Kafka不会保持尽可能多的内容在内存空间，而是尽可能把内容直接写入到磁盘。所有的数据都及时的以持久化日志的方式写入到文件系统

**下面再详细说Kafka 如何做到高吞吐、低延迟的呢？**
这里提下 Kafka 写数据的大致方式：先写操作系统的页缓存（Page Cache）,然后由操作系统自行决定何时刷到磁盘。
因此 Kafka 达到高吞吐、低延迟的原因主要有以下4点：
- 页缓存是在内存中分配的，所以消息写入的速度很快。
- Kafka 不必和底层的文件系统进行交互，所有繁琐的 I/O 操作都由操作系统来处理。
- Kafka 采用追加写的方式，避免了磁盘随机写操作。
- 使用以 sendfile 为代表的零拷贝技术提高了读取数据的效率。

使用页缓存而非堆内存还有一个好处，就是当 Kafka broker 的进程崩溃时，堆内存的数据会丢失，但是页缓存的数据依然存在，重启 Kafka broker 后可以继续提供服务。

**Page Cache 带来的好处？**
Linux 总会把系统中还没被应用使用的内存挪来给 Page Cache，在命令行输入free，或者 cat /proc/meminfo ，“Cached”的部分就是 Page Cache。

Page Cache 中每个文件是一棵 Radix 树（又称 PAT 位树, 一种多叉搜索树），节点由 4k 大小的 Page 组成，可以通过文件的偏移量（如 0x1110001）快速定位到某个Page。

当写操作发生时，它只是将数据写入 Page Cache 中，并将该页置上 dirty 标志。

当读操作发生时，它会首先在 Page Cache 中查找，如果有就直接返回，没有的话就会从磁盘读取文件写入 Page Cache 再读取。

可见，只要生产者与消费者的速度相差不大，消费者会直接读取之前生产者写入Page Cache的数据，大家在内存里完成接力，根本没有磁盘访问。

而比起在内存中维护一份消息数据的传统做法，这既不会重复浪费一倍的内存，Page Cache 又不需要 GC （可以放心使用60G内存了），而且即使 Kafka 重启了，Page Cache 还依然在。

**关于page cache，最好参考understand linux kernel这本书，下面节选了**
The page cache is the main disk cache used by the Linux kernel. In most cases, the kernel refers to the page cache when reading from or writing to disk. New pages are added to the page cache to satisfy User Mode processes's read requests. If the page is not already in the cache, a new entry is added to the cache and filled with the data read from the disk. If there is enough free memory, the page is kept in the cache for an indefinite period of time and can then be reused by other processes without accessing the disk.

所以如果要深入的学习分布式系统，linux内核必须要深入学习


## 参考
磁盘阵列 https://zh.wikipedia.org/wiki/RAID
ssd vs raid https://mybroadband.co.za/news/hardware/255699-how-raid-can-give-your-hard-drives-ssd-like-performance.html
pcie https://searchstorage.techtarget.com/definition/PCIe-SSD-PCIe-solid-state-drive
常见问题 https://zhuanlan.zhihu.com/p/65512721
索引文件 https://matt33.com/2016/03/08/kafka-store/

page cache
https://www.cnblogs.com/sxhlinux/p/6376480.html
https://qinglinmao8315.github.io/linux/2018/03/14/linux-page-cache.html

kakfa读写 https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f