---
title: io读写
date: 2019-07-26 20:45:59
tags: hardware
---

## 随机-顺序读写

这个问题要分情况讨论：在机械硬盘上写还是在固态硬盘上写。尽管结论都是顺序写比随机写快，但是原因却是不一样的。
首先说机械硬盘，我先介绍一下它的存储原理。机械硬盘的结构你可以想象成一个唱片机，它有一个旋转的盘片和一个能沿半径方向移动的磁头。处理读取和写入请求时，首先可以根据请求的开始地址算出要处理的数据在磁盘上的位置，之后要进行以下几步工作：
1、磁头沿半径方向移动，直至移动到数据所在的柱面（相同半径的磁道组成的环面）
2、盘片高速旋转，使磁头到达数据的起始位置
3、磁头沿磁道从磁盘读取或写入数据
当一次读取的数据量很少的时候，1、2步骤带来的开销是无法忽略的，这使得随机写相对于顺序写会有巨大的性能劣势。因为在顺序写的时候，1、2步骤只需要执行一次，剩下的全是数据传输所需要的固有开销；而每次随机写的时候，前两个步骤都需要执行，带来了极大的额外开销。
比如kafka的那么多个消息如果每次都随机写的话要经历很多次的寻道，但是顺序写就简单多了，比如累计到1M的消息可以一次性顺序append到commit log后面去
你平时使用java访问文件既可以是顺序的也可以是随机的，取决于你使用的Java API。比如RandomAccessFile和FileInputStream/FileOutputStream这样的stream类就是两种不同的方式。
具体到Kafka而言，它使用了基于日志结构(log-structured)的数据格式，即每个分区日志只能在尾部追加写入(append)，而不允许随机"跳到"某个位置开始写入，故此实现了顺序写入。 kafka没有必要把N个消息随即写入到文件的各个position
其次说固态硬盘。理论上来说，它不应该存在明显的随机写与顺序写的速度差异，因为它就是一块支持随机寻址的存储芯片，没有寻道和旋转盘片的开销，但是随机写实际上还是比顺序写要慢。这是由于其存储介质闪存的一些特性导致的，简单来说：
1、闪存不支持in-place update：你更新一个数据，不可以直接在原有数据上改，而要写到新的空白的地方，并把原有数据标记为失效。
2、标记失效的数据不是浪费空间么？可以将其清除。但是闪存上清除操作的最小单位是一个大块，大约128K-256K的大小。一次清除会影响到还未标记失效的有用的数据，要先把它们移走。
这种感觉就如同你在网格纸上写一篇文章，一格一格往下写，只能写在空白的格子里；但是你若要清除之前写的内容，只能整行擦除。非常难受而且浪费空间对吧？所以固态硬盘里实现了垃圾回收算法，用来更好地利用存储空间，同时减少数据迁移，保护闪存寿命。
那么随机写显然比顺序写带来更大的碎片化，从而带来更多的垃圾回收开销、数据迁移开销，自然就比顺序写要慢了。