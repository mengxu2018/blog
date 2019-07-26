---
title: kakfa自我随记
date: 2019-07-26 21:24:32
tags: kafka
---

kafka利用page cache提升性能，但是page cache有风险，如果机器crash了，数据会丢失，但是kakfa利用replica保证了可用性
