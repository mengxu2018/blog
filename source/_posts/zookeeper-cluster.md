---
title: zookeeper cluster
date: 2019-07-28 16:00:08
tags: zookeeper
---

## 安装第一台机器
三台机器192.168.77.130， 192.168.77.131， 192.168.77.132

三台机器都是如下的配置conf/zoo.cfg
```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/root/zookeeper_/data
dataLogDir=/root/zookeeper_/log
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
autopurge.purgeInterval=1

server.1=192.168.77.130:2888:3888
server.2=192.168.77.131:2888:3888
server.3=192.168.77.132:2888:3888
```

每台机器都执行mkdir -p /root/zookeeper_/{logs,data}

三台机器按顺序执行下面的命令
echo "1" >> /root/zookeeper_/data/myid
echo "2" >> /root/zookeeper_/data/myid
echo "3" >> /root/zookeeper_/data/myid

## 启动
./zkServer.sh start

## 测试
[root@node2 bin]#  ./zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /root/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost.
Mode: follower

bin/zkCli.sh -server 192.168.77.131:2181

## 可用性
A Zookeeper cluster is called an ensemble. Due to the algorithm used, it is recom‐ mended that ensembles contain an odd number of servers (e.g., 3, 5, etc.) as a major‐ ity of ensemble members (a quorum) must be working in order for Zookeeper to respond to requests. This means that in a three-node ensemble, you can run with one node missing. With a five-node ensemble, you can run with two nodes missing.

## 参考
dataLogDir : (No Java system property) This option will direct the machine to write the transaction log to the dataLogDir rather than the dataDir. This allows a dedicated log device to be used, and helps avoid competition between logging and snapshots.

## 查错
注意下载apache-zookeeper-3.5.5-bin.tar.gz而不是apache-zookeeper-3.5.5.tar.gz，否则会类找不到的错误