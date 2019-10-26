---
title: kakfa索引
date: 2019-07-26 22:34:31
tags: kafka
---

Index file stores the offsets and physical position of the message in the log file.

If you need to read the message at offset 1, you first search for it in the index file and figure out that the message is in position 79. Then you directly go to position 79 in the log file and start reading. This makes it quite effective as you can use binary search to quickly get to the correct offset in the already sorted index file.

kakfa内部用的是稀疏索引，一般来说每个block 512Kb，这样通过offset定位到512KB的数据，然后在这批数据中进行下一步查询，如果是 稠密索引就更简单了，直接定位到目标的record（因为segment中的消息是顺序写入的，稀疏索引定位到某个offset对应的512kB的block后是可以找到其中真正所需的消息的)

![index](/images/kakfa-index.jpeg)

稀疏索引中，左边的offset对应右边的消息，一个索引记录对应3个消息，真是的case可能是512Kb，比如offset为5的消息怎么定位呢？先找到索引记录3，然后找到右边具体的block，就是3到5，这个block肯定包含offset为5的记录，然后在这个有限数据中通过二分查找找到offset为5的记录效率会很快

https://medium.com/r/?url=https%3A%2F%2Fwww2.cs.sfu.ca%2FCourseCentral%2F354%2Fzaiane%2Fmaterial%2Fnotes%2FChapter11%2Fnode5.html
https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f
https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f
https://stackoverflow.com/questions/19394669/why-do-index-files-exist-in-the-kafka-log-directory
https://tech.meituan.com/2015/01/13/kafka-fs-design-theory.html
https://blog.csdn.net/idber/article/details/8096941