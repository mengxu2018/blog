---
title: aliyun yum仓库
date: 2020-03-21 20:44:15
tags: linux
---

mv /etc/yum.repos.d /etc/yum.repos.d-bak
mkdir /etc/yum.repos.d
cd /etc/yum.repos.d
wget http://mirrors.aliyun.com/repo/Centos-7.repo
wget http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all
yum repolist

如果要安装的软件比如docker不在这个源里面，就搜索阿里云yum docker
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo