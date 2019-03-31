---
title: redis5 centos7 setup
tags:
---

## Install redis
```bash
wget http://download.redis.io/releases/redis-5.0.3.tar.gz
tar xzf redis-5.0.3.tar.gz
cd redis-5.0.3
make distclean
make
```

## start redis server
```bash
src/redis-server redis.conf
```

## test the client
```bash
$ src/redis-cli
redis> set foo bar  ('auth 123456' command to login if has password)
OK
redis> get foo
"bar"
```

## enable remote access
update redis.conf like below:
protected-mode no
bind 127.0.0.1
requirepass 123456

## spring boot redis
https://github.com/mengxu2018/springboot/tree/master/spring-boot-crud
缓存流程：
失效：应用程序先从cache取数据，没有得到，则从数据库中取数据，成功后，放到缓存中。
命中：应用程序从cache中取数据，取到后返回。
更新：先把数据存到数据库中，成功后，再让缓存失效。（删除数据也让缓存失效)

## reference
https://coolshell.cn/articles/17416.html