---
title: ssh password-less
date: 2019-06-01 22:51:52
tags: security
---

### pre
一台client机器A，一台remote机器B
你要从A机器无密码登陆到B，都是root用户

### A机器
home目录下面执行
```
ssh-keygen -t rsa
```
可以看到在/root/.ssh下面生成三个文件


## B机器
```
cd ~
mkdir /root/.ssh/
touch /root/.ssh/authorized_keys
```
然后把A机器的/root/.ssh/id_rsa.pub的内容copy到B机器的/root/.ssh/authorized_keys里面

修改权限
```
chmod 700 /root/.ssh/
chmod 600 /root/.ssh/authorized_keys
```

## test
从A机器上面执行` ssh root@192.168.77.132`， 192.168.77.132是B机器的ip地址

## ref
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7