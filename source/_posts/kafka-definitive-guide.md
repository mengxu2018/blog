---
title: kafka definitive guide
date: 2019-07-20 16:58:31
tags: kafka
---

## kafka配置 log.dirs：

kafka存放数据的路径。这个路径并不是唯一的，可以是多个，路径之间只需要使用逗号分隔即可；每当创建新partition时，都会选择在包含最少partitions的路径下进行。
Note that the broker will place a new partition in the path that has the least number of partitions currently stored in it, not the least amount of disk space
选择最少分区的目录，而不是最少磁盘空间的目录

## Memory

The normal mode of operation for a Kafka consumer is reading from the end of the partitions, where the consumer is caught up and lagging behind the producers very little, if at all. In this situation, the messages the consumer is reading are optimally stored in the system’s page cache, resulting in faster reads than if the broker has to reread the messages from disk. Therefore, having more memory available to the system  for page cache will improve the performance of consumer clients.  Kafka itself does not need much heap memory configured for the Java Virtual Machine (JVM). Even a broker that is handling X messages per second and a data rate of X megabits per second can run with a 5 GB heap. The rest of the system memory will be used by the page cache and will benefit Kafka by allowing the system to cache log segments in use. This is the main reason it is not recommended to have Kafka collocated on a system with any other significant application, as they will have to share the use of the page cache. This will decrease the consumer performance for Kafka.

https://en.wikipedia.org/wiki/Page_cache

## page 33

Linux swap space, vm.swappiness 用法, Virtual_memory
虚拟内存是计算机系统内存管理的一种技术。
Virtual memory is a combination of RAM and disk space that running processes can use.
它使得应用程序认为它拥有连续的可用的内存（一个连续完整的地址空间），而实际上，它通常是被分隔成多个物理内存碎片，还有部分暂时存储在外部磁盘存储器上，在需要时进行数据交换。与没有使用虚拟内存技术的系统相比，使用这种技术的系统使得大型程序的编写变得更容易，对真正的物理内存（例如RAM）的使用也更有效率。

It is an important kernel parameter for Kafka because the more memory allocated to the swap space, the less memory can be allocated to the page cache. Cloudera recommends to set vm.swappiness value to 1.

所以kafka可以禁止使用swap到磁盘

## page 60

If a key exists and the default partitioner is used, Kafka will hash the key (using its own hash algorithm, so hash values will not change when Java is upgraded), and use the result to map the message to a specific partition. Since it is important that a key is always mapped to the same partition, we use all the partitions in the topic to calculate the mapping — not just the available partitions. This means that if a specific partition is unavailable when you write data to it, you might get an error. This is fairly rare, as you will see in Chapter 6 when we discuss Kafka’s replication and availability. The mapping of keys to partitions is consistent only as long as the number of partitions in a topic does not change. So as long as the number of partitions is constant,you can be sure that, for example, records regarding user 045189 will always get written to partition 34. This allows all kinds of optimization when reading data from partitions.However, the moment you add new partitions to the topic, this is no longer guaranteed — the old records will stay in partition 34 while new records will get written to a different partition. When partitioning keys is important, the easiest solution is to create topics with sufficient partitions (Chapter 2 includes suggestions for howto determine a good number of partitions) and never add partitions.

如果机器数量是10个，topicA的分区数量是5，某个key来计算hash的时候应该是根据分区来算的，和机器没关系

上面可以看到计算key分布在哪个分区的时候用的是所有的node，而不是available的node，因为计算hash的时候如果你的node节点挂了就不计算在内的话就会出现新的key到不一样的分区去了，但是前提是topic的分区数量没有改变,某个topic的分区是编号的，每个分区都有复制，所以即便某个机器挂了，依然可以用复制的分区来读写，复制的分区的编号和之前的leader应该是一样的

org.apache.kafka.clients.producer.internals.DefaultPartitioner#partition

Partitions in a Kafka topic are numbered from 0...N.

Kafka Default Partiotioner is using modulo for the same purpose

下面还有解释
Keys are used when messages are to be written to partitions in a more controlled manner. The simplest such scheme is to generate a consistent hash of the key, and then select the partition number for that message by taking the result of the hash modulo, the total number of partitions in the topic. This assures that messages with the same key are always written to the same partition. Keys are discussed in more detail in Chapter 3.

kafka并没有需要用一致性hash，默认是mod，分区数量需要保持不变，这样才可以保证每个key永远都落到同一个分区上面

## page 76

![kakfa-re-process-message](/images/kakfa-re-process-message.PNG)

If the committed offset is smaller than the offset of the last message the client processed, the messages between the last processed offset and the committed offset will be processed twice.

上次的offset是2，但是上一次pull过来的数据是7-11，说明3-6已经处理过了，正在处理10，11还没有处理，所以如果从2加上1开始处理的话，3-10就会重复处理

![kakfa-lost-message](/images/kakfa-lost-message.PNG)

If the committed offset is larger than the offset of the last message the client actually processed, all messages between the last processed offset and the committed offset will be missed by the consumer group

上次的offset是11，但是上次pull过来的4-11只是处理到了5，也就是说6-11就没有机会处理 了

----------------------
至于为什么offset和之前处理过的消息的posistion不一致，看看有没有其他办法解决

前者是commit的速度赶不上pull的速度，后者是pull的速度赶不上commit的速度

因为提交commit是5秒一次，所以就会存在pull过来的offset大于或者小于上次commit的offset，哪怕减少到1s也不可能完全杜绝

如果设置自动提交offset为false的话可以杜绝消息丢失，但是不能杜绝重复消费，但是可以减少重复消费的数量，比如第一个图，如果是pull消息消费完成在手动提交的话，消息6肯定就是last commited offset，如果消息处理到10的时候挂了，那重复消费就是7-10而不是2-10


## page 97

Another task the leader is responsible for is knowing which of the follower replicas is up-to-date with the leader. Followers attempt to stay up-to-date by replicating all the messages from the leader as the messages arrive, but they can fail to stay in sync for various reasons, such as when network congestion slows down replication or when a broker crashes and all replicas on that broker start falling behind until we start the broker and they can start replicating again.

但是重启之后分区的leader就会发生变化了，因为宕机后会重新选举分区的leader，机器重新启动后replicate还是会继续之前的进行，如果这台机器不能启动了，那重新弄台新的机器with the same broker id，这样新的机器也可以把所有消息复制过来，新的机器不会成为controller，新的机器里面也只是众多分区的follower，不会成为leader

在新建topic的时，kafka集群按照上述方法去创建分配，leader是均匀分布的，但是时间久了，当某台机器宕机后，follower会变成leader，之前的leader在机器restart之后，赶上了进度，加入了ISR列表，此时变成了follower，不在提供读写能力。这个时候就会存在leader分配不均的问题
只有partition均匀分布才可以负载均衡
https://www.jianshu.com/p/c987b5e055b0

## page 71

You can't have multiple consumers that belong to the same group in one thread and you can't have multiple threads safely use the same consumer. `One consumer per thread` is the rule. To run multiple consumers in the same group in one application, you will need to run each in its own thread. It is useful to wrap the consumer logic in its own object and then use Java's ExecutorService to start multiple threads each with its own consumer. The Confluent blog has a tutorial that shows how to do just that.

## page 107

By default, each segment contains either 1 GB of data or a week of data, whichever is smaller.
新的segment的条件是先到1 GB或者7天到了，注意是OR，类似于保养3年或者10万公里

As a Kafka broker is writing to a partition, if the segment limit is reached, we close the file and start a new one.

The segment we are currently writing to is called an active segment. The active segment is never deleted, so if you set log retention to only store a day of data but each segment contains five days of data, you will really keep data for five days because we can't delete the data before the segment is closed. If you choose to store data for a week and roll a new segment every day, you will see that every day we will roll a new segment while deleting the oldest segment - so most of the time the partition will have seven segments.
意思就是虽然设置log retention为一天，但是因为数据到了5天才达到1 GB，也就是到5天才生成新的segment，这样你也要保留数据5天，因为active的segment不能删除

但是kafka有个问题，如果分区过多，那么日志分段也会很多，写的时候由于是批量写，其实就会变成随机写了，随机I/O这个时候对性能影响很大。所以一般来说Kafka不能有太多的partition。针对这一点，RocketMQ把所有的日志都写在一个文件里面，就能变成顺序写，通过一定优化，读也能接近于顺序读。
上面这段话是阿里的博客，好像是错误的， 单个partition是可以实现消息的顺序写入的。哪怕再多的分区，对于单个分区也是一样顺序写，比如一个producer，100万个message，5台机器，100个分区，一个机器20个分区，那么平均一个分区接受1万个消息，怎么会变成随机写呢？一个分区内部也只有一个segment是active啊，没有多个segment的说法

我们建议的做法是，如果是3个broker的集群，有5个消费者，那么建议partition的数量是15，也就是broker和consumer数量的最小公倍数。当然，也可以是一个大于消费者的broker数量的倍数，比如6或者9，还请读者自行根据实际环境裁定。
https://bbs.huaweicloud.com/blogs/bf79ccc8d0fd11e7b8317ca23e93a891







