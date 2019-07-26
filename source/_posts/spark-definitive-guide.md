---
title: spark 权威指南 笔记
date: 2019-06-30 22:51:52
tags: spark
---

## page 29
In specifying this action, we started a Spark job that runs our filter transformation (a narrow
transformation), then an aggregation (a wide transformation) that performs the counts on a per
partition basis, and then a collect, which brings our result to a native object in the respective
language. You can see all of this by inspecting the Spark UI, a tool included in Spark with which you
can monitor the Spark jobs running on a cluster.
```
val myRange = spark.range(1000).toDF("number")
val divisBy2 = myRange.where("number % 2 = 0")
divisBy2.count()

```
一开始很疑惑为什么count会是wide transformation还包括collect，可以需要看执行计划还有spark ui去加深理解，
写代码rdd的count和dataset的count去比较
尤其是dataset部分


https://dzone.com/articles/apache-spark-3-reasons-why-you-should-not-use-rdds
这篇文章也说了count在rdd和dataframe不一样

spark dataframe的count方法和rdd的count action不是一个意思，
https://stackoverflow.com/questions/47194160/why-is-dataset-count-causing-a-shuffle-spark-2-2

更新：通过sql的执行计划图
http://k8s-master:18080/history/app-20190628083643-0001/SQL/execution/?id=0
![stage](/images/count_stage.PNG)
可以看到第一个stage两个task，每个task计算出count为250，这样HashAggregate出来的rows就是2，通过shuffle进入到
第二个stage，第二个stage汇总count后得出总的count是500，row是1
总结：这个case从第一个stage到第二个stage分区数目从2到1，但是看执行计划确实也算是shuffle操作，
只要有shuffle操作就会有新的stage
这个case的count确实是action，虽然filter操作不会有shuffle操作，但是由于count底层还是有了shuffle操作，
从图片看filter确实也没有wide transformation（shuffle），只不过filter后才有了聚合操作


## page 299
 ```
 spark.read
    .option("header", "true")
    .csv("/root/online-retail-dataset.csv")
    .repartition(4)
    .selectExpr("instr(Description, 'GLASS') >= 1 as is_glass")
    .groupBy("is_glass")
    .count()
    .collect()
```
这个可以在shell里面执行
```
scala> spark.read.option("header", "true").csv("/root/online-retail-dataset.csv")    .repartition(4).selectExpr("instr(Description,'GLASS') >= 1 as is_glass")    .groupBy("is_glass").count().show()
+--------+------+
|is_glass| count|
+--------+------+
|    null|  1454|
|    true| 12861|
|   false|527594|
+--------+------+
```

运行在集群
 ./bin/spark-submit   --class org.apache.spark.examples.SimpleApp2 --master spark://192.168.77.130:7077 --deploy-mode client --executor-memory 1500M --conf spark.eventLog.enabled=true --conf spark.sql.shuffle.partitions=6 /root/spark/spark-example-scala-1.0.jar

这个分为三个stage
第一个stage读取数据默认按照cpu的核心来分区，比如我两个work nodes，每个都是一个core，就是两个分区
第二个是强制分区为4个，反正还是这两个机器，所以shuffle 的代价不大，都是机器内部的转移数据
第三个是执行groupby之后默认的200个分区，上面参数指定为6，groupby的时候相同key的数据需要在一个task里面，所以需要shuffle，所以可以看到虽然有200个task，但是只有3个task是有数据的，最后collect会把所有的数据汇总到driver

看执行计划
http://k8s-master:18080/history/app-20190628102443-0003/SQL/execution/?id=1
![stage](/images/page-299-stage2.PNG)

这个是第二个stage，为什么会输出12个row？
我们知道groupby导致下一个stage，但是上一个stage不可能把几十万行数据都shuffle出去没必要，
他会在当前的分区执行一次groupby，这就是聚合，这样每个分区都是3行数据，
4个分区就是12行数据，这个12行数据会在最后一个stage里面运行，
我发现最后一个stage就三个task有shuffle read，可能因为把同样的key发送到了同一个task上面了，一共就三个key（group by之后就3个key)

下面是书上说的
Notice that in Figure 18-5 the number of output rows is six. This convienently lines up with
the number of output rows（这个就是3） multiplied by the number of partitions（这个是4) at aggregation time. This is because Spark performs an
aggregation for each partition (in this case a hash-based aggregation) before shuffling the data aroundin preparation for the final stage.

## page 264
Regardless of the number of partitions, that
entire stage is computed in parallel. The final result aggregates those partitions individually, brings
them all to a single partition before finally sending the final result to the driver. We’ll see this
configuration several times over the course of this part of the book.

Tasks
Stages in Spark consist of tasks. `Each task corresponds to a combination of blocks of data and a set
of transformations that will run on a single executor`. If there is one big partition in our dataset, we
will have one task. If there are 1,000 little partitions, we will have 1,000 tasks that can be executed in
parallel. A task is just a unit of computation applied to a unit of data (the partition). Partitioning your
data into a greater number of partitions means that more can be executed in parallel. This is not a
panacea, but it is a simple place to begin with optimization.

## page 133
```
val df = spark.read.format("csv")
.option("header", "true")
.option("inferSchema", "true")
.load("/FileStore/tables/retail-data/all/online_retail_dataset-92e8e.csv")
```
这个dateframe一共541909行数据
```
 df.groupBy("InvoiceNo", "CustomerId").count().count()
``` 
这个返回25900,说明分组之后的行是这么多
df.groupBy("InvoiceNo", "CustomerId").count().show()
这个可以看分组后的数据，这个count返回的是个dataset，所以可以继续执行count这个action
这个job有两个stage，
第一个stage有8个task，分别读取8分之1的数据，然后shuffle write， write的行书就是25907，
第二个stage读取这些数据，上面8个分区的数据都是重复的group后的结果，所以继续汇总下，就是剩下25900行数据

## page 57
Overview of Structured Spark Types
Spark is effectively a programming language of its own. Internally, Spark uses an engine called
Catalyst that maintains its own type information through the planning and processing of work. In
doing so, this opens up a wide variety of execution optimizations that make significant differences.
Spark types map directly to the different language APIs that Spark maintains and there exists a lookup
table for each of these in Scala, Java, Python, SQL, and R. Even if we use Spark’s Structured APIs
from Python or R, the majority of our manipulations will operate strictly on Spark types, not Python
types. For example, the following code does not perform addition in Scala or Python; it actually
performs addition purely in Spark:
下面是spark的dataset类型的编译时候就报错的解释例子
```
case class Person(name : String , age : Int)
val personRDD = sc.makeRDD(Seq(Person("A",10),Person("B",20)))
val personDF = sqlContext.createDataframe(personRDD)
val ds:Dataset[Person] = personDF.as[Person]
ds.filter(p => p.age > 25)
ds.filter(p => p.salary > 25)
 // error : value salary is not a member of person
ds.rdd // returns RDD[Person]
```