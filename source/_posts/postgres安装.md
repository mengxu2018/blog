---
title: postgres安装
date: 2019-06-16 22:51:52
---


## 安装
https://www.postgresql.org/download/linux/redhat/
官方提供的了新建repo然后yum的安装方式，非常的方便，可以指定版本`yum install postgresql10`
```
/usr/pgsql-10/bin/postgresql-10-setup initdb
systemctl enable postgresql-10
systemctl start postgresql-10
```



## 配置
注意配置文件在/var/lib/pgsql/10/data不在/usr/pgsql-10
可以通过netstat -nlt查看ip的绑定情况，如果修改了/usr/pgsql-10的listen相关的配置，
会发现5432仍然listen在127.0.0.1,而不是远程任意ip地址可以访问
https://blog.bigbinary.com/2016/01/23/configure-postgresql-to-allow-remote-connection.html这个文章价提到了

pg_hba.conf的配置必须是trust，否则远程客户端比如datagrip还是不能登陆
```
host    all             all              0.0.0.0/0                       trust
host    all             all              ::/0                            trust
```
同时注意postgres安装的时候会默认安装一个linux的用户postgres,
可以su - postgres之后然后psql命令进入shell的sql界面，
然后修改密码ALTER USER postgres WITH PASSWORD 'xuhang'，
最后可以客户端用xuhang这个密码登陆


## 其他ref
https://www.postgresql.org/docs/10/auth-pg-hba-conf.html

## 总结
直接看下面步骤进行安装for quick，进入root用户
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
yum install postgresql10
yum install postgresql10-server
/usr/pgsql-10/bin/postgresql-10-setup initdb
systemctl enable postgresql-10
systemctl start postgresql-10
安装好下面开始配置
su - postgres
psql
ALTER USER postgres WITH PASSWORD 'xuhang'


append
```
host    all             all              0.0.0.0/0                       trust
host    all             all              ::/0                            trust
``
to `/var/lib/pgsql/10/data/pg_hba.conf`

update to `listen_addresses = '*'`
in `postgresql.conf`

systemctl restart postgresql-10
可以客户端登陆了，默认数据库postgres，默认用户名postgres，密码是alter修改过的