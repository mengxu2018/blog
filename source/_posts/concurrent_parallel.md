---
title: 并发并行
date: 2019-06-26 22:51:52
tags:
---
tomcat哪怕在多核cpu中运行，也还是多线程并发，不算并行
tomcat内部使用多线程，但是操作系统会把这些现场分布到多个cpu core里面
所谓的并行，是指一个task分解多多个task，比如java 7的fork/join, 又或者spark的分布式计算，是一定需要多core的cpu或者多个cpu来计算

关于并发并行还需要学习