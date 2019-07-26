---
title: win10 disable auto-update
date: 2019-06-12 22:51:52
tag: win10
---

被windows 10 自动更新折磨了很久，今天终于找到了办法
运行regedit打开注册表，找到HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services下面的wuauserv服务，然后把右边的ImagePath改成一个错误的，然后update就彻底歇菜了


 