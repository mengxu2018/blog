---
title: hadoop cluster setup
---


## 伪分布式搭建
主要为了学习spark，用的3.2.1版本，所以简单搭建了伪分布式，官方文档有几个坑,
首先把三台机器的hostname和ip配置下，有些case会hostname访问
```
192.168.77.130  k8s-master
192.168.77.131  node1
192.168.77.132  node2
```
这个一般立即生效的

### 参考官网修改配置文件
https://hadoop.apache.org/docs/r3.1.2/hadoop-project-dist/hadoop-common/SingleCluster.html#Pseudo-Distributed_Operation
关于官网说的这两个文件，etc/hadoop/core-site.xml, etc/hadoop/hdfs-site.xml
`core-site.xml`比官网多了hadoop.tmp.dir, 同时用k8s-master或者ip比较好，localhost会导致远程连接失败
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


## test
`web ui:` http://k8s-master:9870

```
bin/hdfs dfs -mkdir /user
bin/hdfs dfs -mkdir /user/xuhang
bin/hdfs dfs -put etc/hadoop/*.xml /user/xuhang
```

## start spark cluster
https://spark.apache.org/docs/latest/spark-standalone.html#starting-a-cluster-manually
```
在master机器，k8s-master
./sbin/start-master.sh

在其他机器
./sbin/start-slave.sh spark://k8s-master:7077

```

## simple spark code
```
package org.apache.spark.examples

import org.apache.spark.sql.SparkSession

object SimpleApp {
  def main(args: Array[String]) {
    val logFile = "hdfs://k8s-master:9000/user/xuhang/core-site.xml" // Should be some file on your system
    val spark = SparkSession.builder.appName("Simple Application").getOrCreate()
    val logData = spark.read.textFile(logFile)
    val numAs = logData.filter(line => line.contains("a")).count()
    val numBs = logData.filter(line => line.contains("b")).count()
    println(s"Lines with a: $numAs, Lines with b: $numBs")
    spark.stop()
  }
}
```