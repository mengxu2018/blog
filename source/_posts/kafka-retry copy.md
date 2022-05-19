---
title: kafka producer retry
date: 2022-05-18 20:29:34
tags: kafka
---

## retries
In general, whe our goal is to never lose a message, our best approach is to configure the producer to keep trying to send the messages when it encounters a retriable error.And the best approach to retries, as recommended in Chapter 3, is to leave the number of retries at its current default (MAX_INT, or effectively infinite) and use delivery.timout.ms to configure the maximum amount of time we are willing to wait until giving up on sending a message - the producer will retry sending the message as many times as possible within this time interval.

## duplicate
Retrying to send a failed message includes a risk that both messages were successfully written to the broker, leading to duplicates. Retries and careful error handling can guarantee that each message will be stored at least once, but not exactly once. Using enable.idempotence=true will cause the producer to include additional information in its records, which brokers will use to reject duplicate messages caused by retries. In Chapter 8 we discuss in details how and when this works.
这里的enable.idempotence=true说的其实是producer这边的保证，但是在我们consumer这一段，也必须通过业务保证重复消息不受影响，因为重复消息也还有可能其他因素导致，比如offset没有commit成功后面从之前的offset开始消费，等等