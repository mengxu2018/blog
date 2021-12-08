---
title: kafka消息发送顺序问题
date: 2021-12-04 11:25:58
tags: kafka
---

### 消息发送顺序
异步生产者，对于一个有着先后顺序的消息A、B，正常情况下应该是A先发送完成后再发送B，但是在异常情况下，在A发送失败的情况下，B发送成功，而A由于重试机制在B发送完成之后重试发送成功了。这时对于本身顺序为AB的消息顺序变成了BA。

如果业务要求消息必须是按顺序发送的，那么可以使用同步的方式，并且只能在一个partation上，结合参数设置retries的值让发送失败时重试，设置max_in_flight_requests_per_connection=1，可以控制生产者在收到服务器晌应之前只能发送1个消息，在消息发送成功后立刻flush，从而控制消息顺序发送。
https://bbs.huaweicloud.com/blogs/detail/259907