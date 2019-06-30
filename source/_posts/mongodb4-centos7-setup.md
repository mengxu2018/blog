---
title: mongodb centos7 setup
date: 2019-01-17 22:51:52
tags:
---

## Install 
* wget https://repo.mongodb.org/yum/redhat/7/mongodb-org/4.0/x86_64/RPMS/mongodb-org-server-4.0.5-1.el7.x86_64.rpm
* rpm -i mongodb-org-server-4.0.5-1.el7.x86_64.rpm
* rpm -i https://repo.mongodb.org/yum/redhat/7/mongodb-org/4.0/x86_64/RPMS/mongodb-org-shell-4.0.5-1.el7.x86_64.rpm  # client


## Start mongodb server and autostart mongodb on boot
* systemctl start mongod.service
* systemctl enable mongod.service

## enable remote access
`update /etc/mongod.conf to bindIp: 0.0.0.0`