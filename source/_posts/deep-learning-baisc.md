---
title: deep learning
date: 2019-06-29 22:51:52
tags: AI
---


## 训练 模型

你可以把机器想象成一个小孩子，你带小孩去公园。公园里有很多人在遛狗。简单起见，咱们先考虑二元分类问题。你告诉小孩这个动物是狗，那个也是狗。但突然一只猫跑过来，你告诉他，这个不是狗。久而久之，小孩就会产生认知模式。这个学习过程，就叫“训练”。所形成的认知模式，就是”模型“。训练之后。这时，再跑过来一个动物时，你问小孩，这个是狗吧？他会回答，是/否。这个就叫，预测。一个模型中，有很多参数。有些参数，可以通过训练获得，比如logistic模型中的权重。但有些参数，通过训练无法获得，被称为”超参数“，比如学习率等。这需要靠经验，过着grid search的方法去寻找。上面这个例子，是有人告诉小孩，样本的正确分类，这叫有督管学习。还有无督管学习，比如小孩自发性对动物的相似性进行辨识和分类。


自己的理解：
小孩的`认知模式`就是一个`模型`，你要给他足够的数据连训练出来一个新的认知模式，这个新的`模型`适合你自己的task
训练的是模型里的参数，模型是一个从输入到输出的黑盒子，训练是为了让这个黑盒子更适应您手头的任务(通过大量的数据)。
所以说深度学习要选择模型，这些模型一般人写不了，一般选择开源的模型，你把你自己的大量数据通过他来训练之后变成适合自己的模型
深度学习可以理解成用深度神经网络（DNN，Deep Neural Network）来进行机器学习，他俩的关系可以从这个定义中一目了然地看出来。

所以神经网络了解了解拿来用用就行了，不需要自己去实现，有开源的深度学习框架


## 框架 硬件
As such, your Keras model can be trained on a number of different hardware platforms beyond CPUs:

NVIDIA GPUs
Google TPUs, via the TensorFlow backend and Google Cloud
OpenCL-enabled GPUs, such as those from AMD, via the PlaidML Keras backend

## GUDA
GUDA又是什么呢。CUDA就是通用计算，游戏让GPU算的是一堆像素的颜色，而GPU完全可以算其他任何运算，比如大数据量矩阵乘法等。同样，程序准备好对应的数组，以及让GPU如何算这些数组的描述结构（比如让GPU内部开多少个线程来算，怎么算，之类），这些数据和描述，都要调用CUDA库提供的函数来传递给CUDA，CUDA再调用显卡用户态驱动对CUDA程序进行编译，后者再调用内核态驱动将命令以及编译好的程序数据传送给GPU