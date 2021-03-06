---
title: IBM 的 POWER 处理器比较 X86 处理器
date: 2019-06-13 22:51:52
tags: linux
---

## 硬件体系

从处理能力来说，单Hz的处理能力x86已经超过了Power系列，这是毋庸置疑的。但是Power有其明显的优点。它采用了标准的SMP结构，也就是说对于内存来说所有CPU访问的速度都是一致的，而x86采用了NUMA结构，这就是说CPU和内存是分区的，每个CPU访问自己的这部分内存特别快，但是如果需要访问其它部分那就要走QPI总线（现在已经在不断改进了），这也客观上造成了随着CPU数量的增多，处理能力的增长Power系列的线性程度远好于x86（这也是为什么很少会用4路以上的x86服务器）。而且作为小型机，封闭系统，其设计更加完整紧凑，综合起来性能强于x86

## 软件体系

硬件体系是自己的，操作系统也是自己的（AIX等），所以整合起来Power系列的整体稳定性要强于x86服务器，而且运维也方便（特别是对于一些外围硬件，如果使用IBM更加容易用），抗压能力也强（小型机90%的CPU占用率，运行几个星期可能都OK，x86几天就估计出问题了）
但是Power系列的小型机的价格太高了，而且已经赶不上技术的变化了，由于Google的崛起，云计算的兴盛，现在的分布式系统的成熟度越来越高，系统已经越来越不依赖几台小型机来提供稳定可靠性，而是通过集群来提供，性能也能够通过分布式的处理来解决。所以x86的使用越来越广泛，而最新的一些低成本但是能够带来高效能的新技术都在x86体系下得到应用（x86市场占有率高，也开放），而Power系列由于其封闭的特性，反而难以得到应用，所以Power系列的小型机优势越来越不明显，已经在逐渐退出历史舞台了。

## 后记
AIX is an open operating system from IBM that is based on a version of UNIX. 
ibm小型机的系统是AIX， 所以相关的软件也要on aix， 比如oracle on aix,
不过ibm小型机也可以安装linux,因为linux也可以运行在power处理器， Power ISA指令集