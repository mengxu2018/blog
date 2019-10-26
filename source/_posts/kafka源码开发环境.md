---
title: kafka源码开发环境
date: 2019-08-10 21:43:59
tags: kafka
---

## 下载源码

cd kafka_source_dir
gradle

./gradlew jar （如果不能下载wrapper可以直接用local的gradle命令)
./gradlew idea

## 去掉编译warning（build.gradle)
tasks.withType(JavaCompile) {
      options.encoding = 'UTF-8'
增加  options.warnings = false   

## 准备配置文件
把config下面的log4j.properties文件copy到kafka-2.3.0-src\core\src\main\resources\log4j.properties
同时修改config下面的server.properties的log.dirs=c:/tmp/kafka-logs

## 启动windows zookeeper
直接执行zkServer.cmd（提前修改zoo.cfg的路径为windows路径)

## 导入intellij
intellij open然后选择kafka-2.3.0-src\build.gradle

修改classpath
![kakfa-build](/images/kakfa-build.PNG)
注意箭头的地方

运行main方法，注意-Dkafka.logs.dir=c:/tmp/log4j-kafka-logs是针对的log4j.properties,
和log.dirs=c:/tmp/kafka-logs里面的不一样
![kakfa-main](/images/kafka-main.PNG)


## 查错
java.io.IOException: No snapshot found, but there are log entries. Something，清空zoo.cfg所配置的dataDir目录下面的东西