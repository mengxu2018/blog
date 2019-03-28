---
title: 虚拟机增加空间
---

## add space

我们用VMware创建好虚拟机之后可能会遇到空间不足的情况，这个时候需要扩展磁盘空间然后将新的空间挂载 到指定的目录

![vm addspace](/images/add-space.PNG)

虚拟机设置里面先扩展好空间

`fdisk -l`查看磁盘占用情况
可以看到/dev下面的sda已经到几，比如我的机器已经到2，说明新创建的分区将会是sda3

输入`fdisk /dev/sda`,然后一步一步的回车，最后输入w保持修改
重启`reboot`
这时在`/dev/`目录下，才能看到了新的分区比如`/dev/sda3`
`mkfs.ext2 /dev/sda3`  格式化
在根目录下创建disk3 (`mkdir /disk3`)
`mount /dev/sda3 /disk43/`将分区mount到`/disk3/`上
在vi修改/etc/fstab文件,加入 `/dev/sda3   /disk3 ext2 defaults 0 0` 一行，并保存，实现开机自动mount

至此，新增加的磁盘空间容量，即可在disk3上体现，并且重新开机自动mount该分区，追加磁盘空间的工作完毕。

## reference
`/dev/` is the part in the unix directory tree that contains all "device" files
So `/dev/sda9` means the ninth partition on the first drive.

https://blog.csdn.net/lyd135364/article/details/78623119