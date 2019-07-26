---
title: kakfa索引
date: 2019-07-26 22:34:31
tags: kafka
---

1.稠密索引
定义：它是由键值和指针(指向记录本身地址)组成的一系列存储块，该存储块的键值与文件的逻辑顺序一致。
特性：每个存储块的每一个键对应的指针都指向每个数据块每一条记录，当要查找指定键 K 时，采用二分查找即可找到键K对应的记录，复杂度为 log2n。

2.稀疏索引
定义：它是由键值和指针(指向记录本身地址)组成的一系列存储块，该存储块的键值与文件的逻辑顺序单调性一致。
特性：每个存储块的每一个键对应的指针都指向每个数据块的第一条记录，当要查找指定建 K 时，先采用二分查找找到 <=K 的键 S，如果 S=K，则命中记录，如果 S<k，则顺序查找 =K 的键，复杂度大于 log2n，小于 n。

比较：
稀疏索引占用的索引存储空间比较小，但是查找时间较长；
稠密索引查找时间较短，索引存储空间较大。

kafka用的是稀疏索引

https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f
https://stackoverflow.com/questions/19394669/why-do-index-files-exist-in-the-kafka-log-directory
https://tech.meituan.com/2015/01/13/kafka-fs-design-theory.html
https://blog.csdn.net/idber/article/details/8096941