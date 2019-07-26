---
title: mysql8 centos7 setup
date: 2019-03-09 22:51:52
tags: mysql
---
![mysql 8](/images/mysql-8.png)

## Install MySQL YUM repository and mysql
* yum localinstall https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm (这个仅仅是安装/etc/yum.repos.d)
* yum install mysql-community-server


## Start MySQL server and autostart MySQL on boot
* systemctl start mysqld.service ## use restart after update
* systemctl enable mysqld.service

## Get Your Generated Random root Password
```bash
grep 'A temporary password is generated for root@localhost' /var/log/mysqld.log |tail -1
```
Example Output:
`2015-11-20T21:11:44.229891Z 1 [Note] A temporary password is generated for root@localhost: -et)QoL4MLid`
And root password is: -et)QoL4MLid

## Connect to MySQL database (localhost) with password
```
mysql -u root -p
```
会提示修改密码
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Python3&&890';
```
但是至此是不能从远程访问此mysqld

## enable root remote access
```bash
use mysql;
UPDATE user SET host = '%' WHERE user = 'root';
flush privileges;
```

## create user and enable remote access
```bash
use mysql;
CREATE USER 'xuhang'@'%' IDENTIFIED BY 'Python3&&890';  -- 创建用户
GRANT ALL ON *.* TO 'xuhang'@'%' WITH GRANT OPTION;  -- 授权
flush privileges;
```

## backup data
```bash
// backup all db
mysqldump --all-databases -uroot -p> /tmp/dump.sql

// backup specific db
mysqldump --databases test1 -uroot -p> /tmp/dump.sql
```

## recover data
```bash
mysql -uroot -p
source /tmp/dump.sql
```
## reference
* https://dev.mysql.com/doc/refman/8.0/en/using-mysqldump.html