---
title: upgrade python3 
date: 2019-05-26 22:51:52
tags: python
---

https://www.rosehosting.com/blog/how-to-install-python-3-6-4-on-centos-7/

yum install -y https://repo.ius.io/ius-release-el7.rpm
yum update
yum install -y python36u python36u-libs python36u-devel python36u-pip
python3 --versoin

rm /usr/bin/python  （delete this soft link)
ln -s /usr/bin/python3 /usr/bin/python
python --version


升级了python后最好用pip3，这样才可以安装package到/usr/lib/python3.6/site-packages

下面是升级后遇到的一些问题的解决办法
https://www.cnblogs.com/linkxu1989/p/6955137.html
升级了后下面两个文件的头部要修改成/usr/bin/python2.7
/usr/lib/python2.7/site-packages/urlgrabber/grabber.py
/usr/libexec/urlgrabber-ext-down
/usr/bin/yum