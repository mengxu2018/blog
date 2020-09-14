---
title: elasticsearch cluster
date: 2020-06-27 22:51:52
tags: search
---

 
https://www.elastic.co/guide/en/elasticsearch/reference/current/file-descriptors.html
https://discuss.elastic.co/t/problems-with-access-to-elasticsearch-form-outside-machine/172450

修改配置elasticsearch.yml
```
network.host: 0.0.0.0
```

文件句柄问题就是修改/etc/security/limits.conf，下面的xuhang就是启动es的用户名
```
xuhang soft nofile 65536
xuhang hard nofile 65536
```

the default discovery settings are unsuitable for production use, 修改elasticsearch.yml
```
discovery.seed_hosts: []
```
 