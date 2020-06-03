---
title: ansible setup
date: 2020-03-21 20:44:15
tags: kubernetes
---

## 安装可以下载centos版本的rpm
https://cbs.centos.org/koji/buildinfo?buildID=27713
至于依赖的其他package，可以yum来安装

## ssh免登录
http://getansible.com/begin/an_zhuang_ansile
```
# 生成ssh key
ssh-keygen

# 拷贝ssh key到远程主机，ssh的时候就不需要输入密码了
ssh-copy-id remoteuser@remoteserver

# ssh的时候不会提示是否保存key
$ssh-keyscan remote_servers >> ~/.ssh/known_hosts
```
配置root->root， ccms->ccms免登录

## ad-hoc commands
https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html

下面的命令都可以直接在root执行或者ccms执行
ansible all -m copy -a "src=/tmp/xu dest=/tmp/xu2"
ansible all -m ping
ansible all -a "/sbin/reboot"

如果在root用户下面执行下面的会报错，需要实现root到对方ccms的免登录
这个只要执行上面的第二步ssh-copy-id ccms@remoteserver就可以了，因为1，3步在同用户名之间的免登录已经执行过了
ansible all -m ping -u ccms

## sudo issue (TODO)
在对方机器执行sudo需要把用户ccms加入到wheel组里面
```
usermod -aG wheel user
```
sudo是ccms的密码，但是需要加入到wheel组里面
```
ansible all -m copy -a "src=/tmp/xu dest=/etc/xu3" -u ccms  --ask-become-pass
```

但是下面的重启会报错
```
ansible all -a "/sbin/reboot" -u ccms --ask-become-pass 会报错
```

## playbook
```
---
-
  hosts: all
  name: "install httpd"
  tasks:
    -
      name: "install httpd*"
      yum: "name=httpd state=latest"
```
上面是一个可以work的例子，安装一个httpd，最顶部的三个dash代表文档的开始，其他的dash可以单独一行也可以不单独一行


## ansible tower
https://computingforgeeks.com/install-and-configure-ansible-tower-on-centos-rhel/
 /bin/ansible-tower-service {start|stop|restart|status}

## yaml
关于yaml中dash的解释，https://www.reddit.com/r/ansible/comments/5jhff3/when_to_use_dash_in_yaml/
```
tasks:
    - name: date/time stamp
      command: /usr/bin/date
      register: timestamp_start
    - debug: var=timestamp_start
    - name: another task
```
dash表示一个list的开始，如果转换成json你可以看的很清楚有没有dash的差异
https://onlineyamltools.com/convert-yaml-to-json
