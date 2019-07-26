---
title: spark word count in practice
date: 2019-06-24 22:51:52
tags: spark
---

## cluster ui spark-master-ui.PNG
![spark master ui](/images/spark-master-ui.PNG)

## 测试文件
为了减少内存的损耗，可以
xuhang meng
meng xuhang
xuhang xuhang xuhang
meng
xuhang
meng
meng liudehua
xuhang meng
meng xuhang
这个复制无数遍到200M，这样就是hdfs两个block，一个block=128MB

## 测试代码
以reducebyKey为分界点，前面是一个stage，后面是另外一个stage
```
package org.apache.spark.examples;
import scala.Tuple2;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.sql.SparkSession;
import java.util.Arrays;
import java.util.List;
import java.util.regex.Pattern;

public final class JavaWordCount {
  private static final Pattern SPACE = Pattern.compile(" ");

  public static void main(String[] args) throws Exception {
    if (args.length < 1) {
      System.err.println("Usage: JavaWordCount <file>");
      System.exit(1);
    }

    SparkSession spark = SparkSession
      .builder()
      .appName("JavaWordCount")
      .getOrCreate();
    JavaRDD<String> lines = spark.read().textFile(args[0]).javaRDD();
    lines.repartition(6);
    JavaRDD<String> words = lines.flatMap(s -> Arrays.asList(SPACE.split(s)).iterator());
    JavaPairRDD<String, Integer> ones = words.mapToPair(s -> new Tuple2<>(s, 1));
    JavaPairRDD<String, Integer> counts = ones.reduceByKey((i1, i2) -> i1 + i2);
    List<Tuple2<String, Integer>> output = counts.collect();
    for (Tuple2<?,?> tuple : output) {
      System.out.println(tuple._1() + ": " + tuple._2());
    }
    spark.stop();
  }
}
```

## 在master节点启动driver
最后的job http://192.168.77.130:18080/history/app-20190624085329-0015/jobs/

parallelism=2， 并行设置为2， 就是200M的文件分区为2
每个executor分配2500M内存，内存消耗主要看重复的关键字多不多
两个stage，一个stage两个task，几乎就是一个task=1250M内存，也就是说并不是每个task都有2.5G内存
./bin/spark-submit   --class org.apache.spark.examples.JavaWordCount --master spark://192.168.77.130:7077 --deploy-mode client --driver-memory=2g   --executor-memory 2500M   --conf spark.eventLog.enabled=true --conf spark.default.parallelism=2 /root/spark/spark-example-java-1.0.jar  hdfs://k8s-master:9000/user/xuhang/test.txt
![cluster-2-executor.PNG](/images/cluster-2-executor.PNG)
这个官方图片恰好跟我这个例子一样，两个work node，一个node两个task
![2-stage-4-task.PNG](/images/2-stage-4-task.PNG)

在回顾下stage和task的概念
- stage : stage 是一个 job 的组成单位，就是说，一个 job 会被切分成 1 个或 1 个以上的 stage，然后各个 stage 会按照执行顺序依次执行。
- task : A unit of work within a stage, corresponding to one RDD partition。即 stage 下的一个任务执行单元，一般来说，一个 rdd 有多少个 partition，就会有多少个 task，因为每一个 task 只是处理一个 partition 上的数据

我们这个例子恰好两个分区，也就是2个task， stage也恰好两个
下面是第一个stage的2个task
![stage0-task.PNG](/images/stage0-task.PNG)
下面是第二个stage的2个task
![stage1-task.PNG](/images/stage1-task.PNG)
可以看到shuffle read和shuffle write都存在
shuffle write：发生在shuffle之前，把要shuffle的数据写到磁盘
为什么：为了保证数据的安全性，避免占用大量的内存
shuffle read：发生在shuffle之后，下游RDD读取上游RDD的数据的过程
具体关于shuffle需要单独学习

## 进一步
可以把parallelism=3或者更大的value，这样可以看到更多的task运行，如果设置为3，总共一个stage有3个task运行，
两个stage，总共就会有6个task，不过如果不设置的话会跟cpu核心的数量差不多，或者2到4倍