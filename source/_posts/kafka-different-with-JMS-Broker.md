---
title: kafka不同于一般的message queue
date: 2019-07-20 20:24:02
tags: kafka
---

## Multiple Consumers
In addition to multiple producers, Kafka is designed for multiple consumers to read any single stream of messages without interfering with each other. This is in contrast to many queuing systems where once a message is consumed by one client, it is not available to any other. Multiple Kafka consumers can choose to operate as part of a group and share a stream, assuring that the entire group processes a given message only once.

https://stackoverflow.com/questions/35561110/can-multiple-kafka-consumers-read-same-message-from-the-partition
kafka可以允许多个consumer group同时消费同一个消息