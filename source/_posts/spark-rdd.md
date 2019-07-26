---
title: spark rdd
date: 2019-07-03 21:22:40
tags: spark
---
RDD这种抽象是整个Spark的核心，具有如下特定：
immutable: 不可变（只读）
resilient: 弹性（容错）
distributed:分布式（数据存储在整个集群的各个机器上)
In-Memory:数据位于内存中(Spark提供了其他的持久化选项）
Lazy evaluated:有Action触发时才会执行真正的操作
Cacheable:内存不足以容纳数据时，可以缓存到磁盘
Typed:每个RDD都有类型，例如RDD[String],RDD[(int,String)]
patitioned:RDD的数据划分为多个Partition，分布在集群中，每个JVM（Executor）一个Partition。


https://www.zhihu.com/question/23079001
这个文章讲了为什么多个rdd的DAG没有耗光内存,
比如rdd是不可变的，多个rdd的内存加起来不是很大了吗

iterator rdd
https://www.jianshu.com/p/c05d33b4d70d

rdd的不可变
https://www.zybuluo.com/BrandonLin/note/448121
一个rdd计算出另外一个rdd，之前的rdd就被丢弃了，不可能dag里面的每个rdd都占据着内存
而且各个rdd的内存也有共同的部分，只是在api层面不可变
如果用户没有要求Spark cache该RDD的结果，那么这个过程占用的内存是很小的，一个元素处理完毕后就落地或扔掉了（概念上如此，实现上有buffer），并不会长久地占用内存。

rdd源码分析
https://www.jianshu.com/p/f5b50e233acc
https://mr-dai.github.io/spark_core_source_1/
https://stackoverflow.com/questions/30446706/implementing-custom-spark-rdd-in-java
```
val textFile = sc.textFile("hdfs://…")
val counts = textFile.flatMap(line => line.split(" "))
 .filter(_.length >= 2)
 .map(word => (word, 1))
 .reduceByKey(_ + _)
counts.saveAsTextFile("hdfs://…")
```
上面这个例子textFile会生成一个haddoprdd，然后就是MapPartitionsRDD,
textFile是一个HadoopRDD经过map后的MapPartitionsRDD，
经过flatMap后仍然是一个MapPartitionsRDD，
经过filter方法之后生成了一个新的MapPartitionsRDD，
经过map函数之后，继续是一个MapPartitionsRDD，
最后经过reduceByKey变成了ShuffleRDD。
rdd中主要就是compute方法还有iterator概念
def compute(split: Partition, context: TaskContext): Iterator[T]

RDD 抽象类要求其所有子类都必须实现 compute 方法，该方法接受的参数之一是一个Partition 对象，目的是计算该分区中的数据。
最后actioins执行的时候会一层一层向parent来执行迭代器里面的操作
再两个例子
```
val lines = spark.textFile("hdfs://<input>")
val errors = lines.filter(_.startsWith("ERROR"))
val messages = errors.map(_.split(" ")(1))
messages.saveAsTextFile("hdfs://<output>")
```
Spark在运行时动态构造了一个复合Iterator。就上述示例来说，构造出来的Iterator的逻辑概念上大致长这样：
所以并不是每个transformation都会生成一份新数据，而是串行化的执行(起码在一个stage里面)
下面是伪代码
```
new Iterator[String] {
 private var head: String = _
 private var headDefined: Boolean = false
def hasNext: Boolean = headDefined || {
 do {
 try head = readOneLineFromHDFS(…) // (1) read from HDFS
 catch {
 case _: EOFException => return false
 }
 } while (!head.startsWith("ERROR")) // (2) filter closure
 true
 }
def next: String = if (hasNext) {
 headDefined = false
 head.split(" ")(1) // (3) map closure
 } else {
 throw new NoSuchElementException("…")
 }
}
```
可不可以认为，想map mapPartition这样的操作实际上在运行的时候后都是逐行处理数据的？
yes
Spark的计算执行可以认为是一个这样的过程：从一个RDD读取数据，做处理，然后依照action的不同把结果发回用户空间。这个过程中可能会有很多中间临时的RDD，假如你对这些RDD设置了cache，那么在它所在的计算环节结束后的中间结果就会被缓存起来，缓存有个缓存管理器，spark里被称作blockmanager。注意哦，这里还有一个误区是，很多初学的同学认为调用了cache或者persist的那一刻就是在缓存了，这是完全不对的，真正的缓存执行指挥在action被触发，job被提交开始计算，在计算的中间过程中才会执行。
我的理解是当action执行结束过才会把中间结果缓存起来，因为spark知道了你要缓存的是哪个rdd，所以他把那个rdd的结果都做上标记，结束后一起缓存起来
数据是作为流一条条从管道的开始一路走到结束。最为直观的好处就是：不需要加载全量数据集，上一次的计算结果可以马上丢弃。全量数据集其实是一个很恐怖的东西，全世界都在避免它，所以某种意义上来看，如果没有Shuffle过程，Spark所需要内存其实非常小，一条数据又能占多大空间。第二，如果不是Pipeline的方式，而是马上触发全量操作，势必需要一个中间容器来保存结果，其实这里就又回到MapReduce的老路，效率很低。


MapPartitionsRDD的compute方法的作用了：在有前置依赖的条件下，在父RDD的Iterable接口上给遍历每个元素的时候再套上一个方法