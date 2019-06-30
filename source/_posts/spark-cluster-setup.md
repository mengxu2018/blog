---
title: hadoop spark cluster setup
date: 2019-06-23 22:51:52
---

![spark architecture](/images/spark-cluster.png)
![yarn architecture](/images/yarn.png)

## 伪分布式搭建
主要为了学习spark，用的3.2.1版本，所以简单搭建了伪分布式，官方文档有几个坑,
首先把三台机器的hostname和ip配置下，有些case会hostname访问
```
192.168.77.130  k8s-master
192.168.77.131  node1
192.168.77.132  node2
```
这个一般立即生效的

## 参考官网修改配置文件
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

## 更新启动和停止脚本
`sbin/start-dfs.sh`, `sbin/stop-dfs.sh`分别添加如下
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root

其实应该有更好的地方，比如dfs-env.sh类似的被这两个脚本call的地方添加一次就够了
比如可以在hadoop-3.1.2/etc/hadoop/hadoop-env.sh统一设置一次

## 启动
```
bin/hdfs namenode -format
sbin/start-dfs.sh
```
其实还可以启动./sbin/start-all来同时启动hdfs和yarn，当然这里只是搭建了一个假的分布式
```
[root@k8s-master hadoop-3.1.2]# sbin/start-
start-all.cmd        start-balancer.sh    start-dfs.sh         start-yarn.cmd
start-all.sh         start-dfs.cmd        start-secure-dns.sh  start-yarn.sh
[root@k8s-master hadoop-3.1.2]# sbin/start-all.sh
Starting namenodes on [k8s-master]
Last login: Thu Jun 13 21:16:03 CST 2019 from 192.168.77.3 on pts/0
Starting datanodes
Last login: Thu Jun 13 21:16:14 CST 2019 on pts/0
Starting secondary namenodes [k8s-master]
Last login: Thu Jun 13 21:16:17 CST 2019 on pts/0
Starting resourcemanager
Last login: Thu Jun 13 21:16:25 CST 2019 on pts/0
Starting nodemanagers
Last login: Thu Jun 13 21:16:30 CST 2019 on pts/0

```



## test
hdfs ui: http://k8s-master:9870
yarn ui: http://k8s-master:8088

```
bin/hdfs dfs -mkdir /user
bin/hdfs dfs -mkdir /user/xuhang
bin/hdfs dfs -put etc/hadoop/*.xml /user/xuhang
```

## 部署mapreduce
https://hadoop.apache.org/docs/r3.1.2/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html
可以参考这个创建一个java的mapreduce然后编译打包，跑yarn上面


## start spark cluster
https://spark.apache.org/docs/latest/spark-standalone.html#starting-a-cluster-manually
```
在master机器，k8s-master
./sbin/start-master.sh

在其他机器
./sbin/start-slave.sh spark://k8s-master:7077

```
这里的spark不是运行在yarn上面，只是用了standalone的cluster，待会从hdfs取数据

## spark ui
http://k8s-master:8080/
driver， work nodes

http://k8s-master:4040
monitor ui 在job运行过程可以访问,如果想事后访问请参考history log章节

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
java version
```
package org.apache.spark.examples;/* SimpleApp.java */
import org.apache.spark.api.java.function.FilterFunction;
import org.apache.spark.sql.SparkSession;
import org.apache.spark.sql.Dataset;

public class SimpleApp {
    public static void main(String[] args) {
        if (args.length < 1) {
            System.err.println("Usage: JavaWordCount <file>");
            System.exit(1);
        }

        SparkSession spark = SparkSession.builder().appName("Simple Application").getOrCreate();
        Dataset<String> logData = spark.read().textFile(args[0]).cache();
        logData.show();


        FilterFunction<String> f1 = s -> s.contains("a");
        FilterFunction<String> f2 = s -> s.contains("b");

        Dataset<String> newDs = logData.filter(f1);
        System.out.println("start to print new ds");
        newDs.show();
        System.out.println("stop to print new ds");
        long numAs = newDs.count();
        long numBs = logData.filter(f2).count();


        System.out.println("my Lines with a: " + numAs + ", lines with b: " + numBs);

        spark.stop();
        System.out.println("hello world");
    }
}
```

## saprk history log
```
 mkdir /tmp/spark-events(by default)

 ./bin/spark-submit   --class org.apache.spark.examples.SimpleApp --master spark://192.168.77.130:7077 --deploy-mode client --executor-memory 700M --conf spark.eventLog.enabled=true /root/spark/spark-example-java-1.0.jar  hdfs://k8s-master:9000/user/xuhang/core-site.xml
./sbin/start-history-server.sh
```
`http://k8s-master:18080` by default
ref: https://spark.apache.org/docs/latest/monitoring.html

## run on other machine(client mode)
client mode可以在任意局域网机器运行
with client mode, you can run the spark submit on any machine, just make sure in the same local network.
for instance, in centostest node,
mkdir /tmp/spark-events
./bin/spark-submit   --class org.apache.spark.examples.JavaWordCount --master spark://192.168.77.130:7077 --deploy-mode client --executor-memory 700M --conf spark.eventLog.enabled=true /root/spark/spark-example-java-1.0.jar  hdfs://k8s-master:9000/user/xuhang/core-site.xml
http://centostest:18080/  (view history on the machine running the submit binary)

## run cluster mode
 ./bin/spark-submit   --class org.apache.spark.examples.JavaWordCount --master spark://192.168.77.130:7077 --deploy-mode cluster --executor-memory 700M --conf spark.eventLog.enabled=true /root/spark/spark-example-java-1.0.jar  hdfs://k8s-master:9000/user/xuhang/core-site.xml
 上面的命令可能会jar包找不到的错误
注意：standalone 模式的 cluster模式 要把jar 文件传到hdfs上面去，因为driver在集群中的任意一节点执行。


## stage
```
JavaRDD<String> lines = spark.read().textFile(args[0]).javaRDD();
JavaRDD<String> words = lines.flatMap(s -> Arrays.asList(SPACE.split(s)).iterator());
JavaPairRDD<String, Integer> ones = words.mapToPair(s -> new Tuple2<>(s, 1));
JavaPairRDD<String, Integer> counts = ones.reduceByKey((i1, i2) -> i1 + i2);
List<Tuple2<String, Integer>> output = counts.collect();
```
对于这个程序有两个stage，一个到mapToPair结束，第二个到collect结束，点开可以看到DAG
![stage](/images/workcount-rdd-stage.png)

## 指定分区数目
spark.default.parallelism
./bin/spark-submit   --class org.apache.spark.examples.JavaWordCount --master spark://192.168.77.130:7077 --deploy-mode client --driver-memory=2g   --executor-memory 2000M   --conf spark.eventLog.enabled=true --conf spark.default.parallelism=4 /root/spark/spark-example-java-1.0.jar  hdfs://k8s-master:9000/user/xuhang/mylarge4.txt


## Yarn的组件及架构
- ResourceManager：Global（全局）的进程
- NodeManager：运行在每个节点上的进程
- ApplicationMaster：Application-specific（应用级别）的进程
- Scheduler：是ResourceManager的一个组件
- Container：节点上一组CPU和内存资源
Container是Yarn对计算机计算资源的抽象，它其实就是一组CPU和内存资源，所有的应用都会运行在Container中。ApplicationMaster是对运行在Yarn中某个应用的抽象，它其实就是某个类型应用的实例，ApplicationMaster是应用级别的，它的主要功能就是向ResourceManager（全局的）申请计算资源（Containers）并且和NodeManager交互来执行和监控具体的task。Scheduler是ResourceManager专门进行资源管理的一个组件，负责分配NodeManager上的Container资源，NodeManager也会不断发送自己Container使用情况给ResourceManager。
ResourceManager和NodeManager两个进程主要负责系统管理方面的任务。ResourceManager有一个Scheduler，负责各个集群中应用的资源分配。对于每种类型的每个应用，都会对应一个ApplicationMaster实例，ApplicationMaster通过和ResourceManager沟通获得Container资源来运行具体的job，并跟踪这个job的运行状态、监控运行进度。
因为application master需要向ResourceManager（全局的）申请计算资源（Containers），他应该是跑在每个node manager上面
https://mapr.com/docs/60/MapROverview/c_application_master.html
https://blog.csdn.net/suifeng3051/article/details/49486927（这个文章讲的很好)

## 总结一下
关于Hadoop本身，有namenode, datanode, resource manager, node manager四个节点，
关于这个4个节点，可以搭建真正的分布式测试下，同时搞起zookeeper防止单点故障
namenode, datanode是hdfs，resource manager, node manager是yarn

## note
spark-submit --class com.cjh.test.WordCount --conf spark.default.parallelism=12 --conf spark.executor.memory=800m --conf spark.executor.cores=2 --conf spark.cores.max=6 my.jar
如果要多个参数在命令行，需要多个--conf
