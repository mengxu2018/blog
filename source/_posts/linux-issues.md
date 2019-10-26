---
title: linux issues
date: 2019-08-10 20:31:52
tags: linux
---

今天遇到了新机器yum一直不成功，需要安装几个工具
sudo yum update
sudo yum install yum-utils
sudo yum groupinstall development

另外/etc/resolv.conf也有问题，可以加个nameserver 9.9.9.9

pip3.6 install torch torchvision