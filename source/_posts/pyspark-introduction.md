---
title: pyspark introduction
date: 2019-07-14 13:40:42
tags: spark
---
spark内置提供了机器学习的很多api，有scala，java，python版本的，但是如果需要用到深度学习，需要安装第三方的package，
而且需要python来写，也就是pyspark
这里主要使用的是Deep Learning Pipelines，由spark官方提供
https://github.com/databricks/spark-deep-learning#working-with-images-in-spark
https://docs.databricks.com/applications/deep-learning/single-node-training/deep-learning-pipelines.html

## 安装深度学习的第三方package
$SPARK_HOME/bin/spark-shell --packages databricks:spark-deep-learning:1.5.0-spark2.4-s_2.11

## install python3.6, pip
yum install -y https://centos7.iuscommunity.org/ius-release.rpm
yum install -y python36u python36u-libs python36u-devel python36u-pip

测试
python3.6 -V

升级下pip
pip install --upgrade pip

## python环境变量
conf/spark-env.sh下面添加`export PYSPARK_PYTHON=/usr/bin/python3.6`

## 第一个程序
先安装这个第三方package
pip install numpy

进入shell
./bin/pyspark

```
from pyspark.ml.image import ImageSchema
image_df = ImageSchema.readImages("/data/myimages")
```

from sparkdl.image import imageIO as imageIO
这个module也需要pip来安装

## 总结
为什么要修改spakr默认的python环境变量，我发现默认的是python2.6，这样即便是安装了第三方的package还是会not found

https://github.com/databricks/spark-deep-learning/tree/master/python 里面是python的api
https://github.com/databricks/spark-deep-learning/tree/master/src/main/scala 里面是具体的实现