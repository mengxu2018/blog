---
title: hadoop cluster setup
tags:
---


## 伪分布式搭建
主要为了学习spark，用的3.2.1版本，所以简单搭建了伪分布式，官方文档有几个坑

### 参考官网修改配置文件
https://hadoop.apache.org/docs/r3.1.2/hadoop-project-dist/hadoop-common/SingleCluster.html#Pseudo-Distributed_Operation
关于官网说的这两个文件，etc/hadoop/core-site.xml, etc/hadoop/hdfs-site.xml
`core-site.xml`比官网多了hadoop.tmp.dir
```
<configuration>
 <property>
      <name>hadoop.tmp.dir</name>
      <value>/root/hadoop-3.1.2/tmp</value>
   </property>
<property>
        <name>fs.defaultFS</name>
        <value>hdfs://k8s-master:9000</value>
    </property>
</configuration>
```

`hdfs-site.xml`,比官网多了dfs.datanode.data.dir，dfs.datanode.data.dir
```
 <configuration>
   <property>
    <name>dfs.namenode.name.dir</name>
    <value>/root/hadoop-3.1.2/dfs/name</value>
   </property>
   <property>
    <name>dfs.datanode.data.dir</name>
    <value>/root/hadoop-3.1.2/dfs/data</value>
   </property>
   <property>
    <name>dfs.permissions</name>
    <value>false</value>
   </property>
   <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>

```

多这个几个配置目录是在启动出错的时候可以方便手动删除目录里的文件，当然提前要创建好这个三个目录

### 更新启动和停止脚本
`sbin/start-dfs.sh`, `sbin/stop-dfs.sh`分别添加如下
export HDFS_NAMENODE_USER="root"
export HDFS_DATANODE_USER="root"
export HDFS_SECONDARYNAMENODE_USER="root"
export YARN_RESOURCEMANAGER_USER="root"
export YARN_NODEMANAGER_USER="root"

其实应该有更好的地方，比如dfs-env.sh类似的被这两个脚本call的地方添加一次就够了


### 启动
```
bin/hdfs namenode -format
sbin/start-dfs.sh
```
