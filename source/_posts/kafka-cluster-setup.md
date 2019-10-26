---
title: kafka cluster setup
date: 2019-08-03 14:40:56
tags:
---
bin/kafka-server-start.sh -daemon config/server.properties
bin/kafka-server-stop.sh

broker.id不要写错

而且启动日志开头打印了[2019-08-03 04:36:30,484] INFO Client environment:host.name=218.93.250.18 (org.apache.zookeeper.ZooKeeper)很奇怪，这个是
 try {
            put(l, "host.name",
                InetAddress.getLocalHost().getCanonicalHostName());
        } catch (UnknownHostException e) {
            put(l, "host.name", "<NA>");
        }
只有第一个机器正常
[2019-08-03 04:35:41,120] INFO Client environment:host.name=k8s-master (org.apache.zookeeper.ZooKeeper)


所以listeners=PLAINTEXT://192.168.77.132:9092 写ip才可以，hostname就不行

producer发送完成需要close

__consumer__offsets何时创建?用户启动了一个消费者组(下称consumer group)进行消费或调用kafka-consumer-groups --describe等
https://www.cnblogs.com/huxi2b/p/8316289.html


https://kafka.apache.org/23/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html


 bin/kafka-topics.sh --create --bootstrap-server k8s-master:9092 --replication-factor 2 --partitions 6 --topic my-replicated-topic2
 这个可以在任意一台集群机器执行，执行后可以看到每台机器都有4个 my-replicated-topic2-x的文件夹，有两个是replication，
 每个replication都有一个leader