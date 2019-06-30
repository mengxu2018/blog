---
title: hostname设置
date: 2019-06-01 22:51:52
tags:
---

hostnamectl set-hostname 'k8smaster'
这样设置后仅仅/etc/hostname里面有了

/etc/hosts里面依然要设置，否则ssh k8smaster依然不work， k8smaster是本机名

如果采用clone vm的方式搭建集群的话，clone后需要重新设置hostname,etc/hosts还有固定ip(/etc/sysconfig/network-script/ifcfg-xxx)

最后的结果是/etc/hostname里面各自的ip，/etc/hosts里面三个机器一样的，包括windows就是4个机器一样的


