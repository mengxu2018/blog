---
title: filesystem
date: 2019-07-31 22:25:46
tags: OS
---

"On a UNIX/linux system, everything is a file; if something is not a file, it is a process."

主要讲述linux文件系统，分区，分区一个比较重要的作用就是安全，比如tomcat占据一个分区，自己本身的数据（比如视频文件)占据一个分区，自己本身的数据分区满了不会影响tomcat分区，不会影响系统分区

通常可以下面几个分区
a partition for user programs (/usr)
a partition containing the users' personal data (/home)
a partition to store temporary data like print- and mail-queues (/var)
a partition for third party and extra software (/opt)

文件系统是针对操作系统而言的，对磁盘而言，就是物理结构一个个的扇区
So, if you have a storage device and you want to use a different file system on it, just copy the files off it first to back them up. Then, format that drive with a tool like Disk Management in Windows, GParted in Linux, or Disk Utility in Mac OS X and copy the backed up data to new filesystem.

- NTFS: Modern versions of Windows - since Windows XP - use the NTFS file system for their system partition. 
- Ext2/Ext3/Ext4: You'll often see the Ext2, Ext3, and Ext4 file systems on Linux. Ext2 is an older file systems, and it lacks important features like journaling - if the power goes out or a computer crashes while writing to an ext2 drive, data may be lost. Ext3 adds these robustness features at the cost of some speed. Ext4 is more modern and faster - it's the default file system on most Linux distributions now, and is faster. Windows and Mac don't support these file systems - you'll need a third-party tool to access files on such file systems. For this reason, it's often ideal to format your Linux system partitions as ext4 and leave removable devices formatted with FAT32 or NTFS if you need compatibility with other operating systems. Linux can read and write to both FAT32 or NTFS.

下面可以看到一块磁盘可以安装两个windows和linux，分别是ntfs文件系统和ext4，可以windows自带的磁盘管理分出一块空间出来，不需要格式化，然后u盘启动linux安装引导文件，把linux安装到空闲的分区，对linux来说可以自动识别ntfs的文件
https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0
![two-os](/images/two-os.jpeg)
https://www.serverbrain.org/administration-practice-2003/an-overview-of-disk-structure.html


https://medium.com/r/?url=http%3A%2F%2Ftldp.org%2FLDP%2Fintro-linux%2Fhtml%2Fsect_03_01.html
https://medium.com/r/?url=https%3A%2F%2Fwww.cs.uic.edu%2F~jbell%2FCourseNotes%2FOperatingSystems%2F11_FileSystemImplementation.html