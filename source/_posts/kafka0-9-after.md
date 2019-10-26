---
title: kafka0.9-after
date: 2019-08-03 14:10:25
tags: kafka
---

## offset存储不一样了
Older versions of Kafka (pre 0.9) store offsets in ZK only, while newer version of Kafka, by default store offsets in an internal Kafka topic called __consumer_offsets (newer version might still commit to ZK though).

The advantage of committing offsets to the broker is, that the consumer does not depend on ZK and thus clients only need to talk to brokers which simplifies the overall architecture. Also, for large deployments with a lot of consumers, ZK can become a bottleneck while Kafka can handle this load easily (committing offsets is the same thing as writing to a topic and Kafka scales very well here -- in fact, by default __consumer_offsets is created with 50 partitions IIRC).


