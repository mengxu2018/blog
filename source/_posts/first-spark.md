---
title: first spark sample
date: 2019-04-26 22:51:52
tags:
---

## intro
这是个本地运行模式，没有standalone spark cluster or spark cluster on yarn

## code
https://github.com/mengxu2018/bigdata/tree/master/learn-spark/first-spark

## build
`mvn package`

## deploy
```
YOUR_SPARK_HOME/bin/spark-submit \
  --class "SimpleApp" \
  --master local[2] \
  simple-project-1.0.jar
```
输出Lines with a: xxx, Lines with b: xxx
