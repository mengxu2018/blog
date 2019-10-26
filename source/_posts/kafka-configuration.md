---
title: kafka configuration
date: 2019-08-03 12:21:50
tags: kafka
---

What is advertised.listeners in Kafka?
https://medium.com/@iamsuraj/what-is-advertised-listeners-in-kafka-72e6fae7d68e
一般来说如果producer和broker都是再内网，则不需要advertised.listeners,如果需要外网访问broker，则需要advertised.listeners设置外网ip
https://www.cnblogs.com/justuntil/p/8037969.html