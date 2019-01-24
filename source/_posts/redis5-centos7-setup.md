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
src/redis-server  
```

# test the client
```bash
$ src/redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```