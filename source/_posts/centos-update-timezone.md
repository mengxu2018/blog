---
title: update timezone
date: 2019-05-30 22:51:52
tags: linux
---

## 修改时区
```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

hwclock -w  写入硬件，避免重启失效
```