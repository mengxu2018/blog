---
title: machine learning basic
date: 2021-11-06 13:27:07
tags: java
---

### 方差
统计中的方差（样本方差）是每个样本值与全体样本值的平均数之差的平方值的平均数

### 随机变量
简单地说，随机变量是指随机事件的数量表现。例如一批注入某种毒物的动物，在一定时间内死亡的只数；某地若干名男性健康成人中，每人血红蛋白量的测定值；等等

### sk-learn(fit,predict)
https://towardsdatascience.com/fit-vs-predict-vs-fit-predict-in-python-scikit-learn-f15a34a8d39f

fit() is implemented by every estimator and it accepts an input for the sample data (X) and for supervised models it also accepts an argument for labels (i.e. target data y ). Optionally, it can also accept additional sample properties such as weights etc.
这里fit就是拟合，应该就是一个训练的过程

fit:
This is good terminology to use in machine learning, because supervised machine learning algorithms seek to approximate the unknown underlying mapping function for the output variables given the input variables.
Statistics often describe the goodness of fit which refers to measures used to estimate how well the approximation of the function matches the target function.


predict() will perform a prediction for each test instance and it usually accepts only a single input (X)


### feature vs label
https://stackoverflow.com/questions/40898019/what-is-the-difference-between-a-feature-and-a-label/40899529
Briefly, feature is input; label is output. This applies to both classification and regression problems.

A feature is one column of the data in your input set. For instance, if you're trying to predict the type of pet someone will choose, your input features might include age, home region, family income, etc. The label is the final choice, such as dog, fish, iguana, rock, etc.

Once you've trained your model, you will give it sets of new input containing those features; it will return the predicted "label" (pet type) for that person.

对于预测房间的线性回归算法，feature其实就是楼层啊，户型啊，楼龄啊等等， label就是最后的房价
sklearn的回归算法的拟合函数描述如下：
fit() is implemented by every estimator and it accepts an input for the sample data (X) and for supervised models it also accepts an argument for labels (i.e. target data y )
可以看到target data其实就是label

### 损失函数
损失函数（loss function）是用来估量模型的预测值(我们例子中的output)与真实值（例子中的y_train）的不一致程度，它是一个非负实值函数，损失函数越小，模型的鲁棒性就越好。 我们训练模型的过程，就是通过不断的迭代计算，使用梯度下降的优化算法，使得损失函数越来越小。损失函数越小就表示算法达到意义上的最优。

### Text Vectorization
https://towardsdatascience.com/getting-started-with-text-vectorization-2f2efbec6685
https://monkeylearn.com/blog/what-is-tf-idf/


Text Vectorization is the process of converting text into numerical representation. Here is some popular methods to accomplish text vectorization:
Binary Term Frequency
Bag of Words (BoW) Term Frequency
(L1) Normalized Term Frequency
(L2) Normalized TF-IDF
Word2Vec

Machine learning with natural language is faced with one major hurdle – its algorithms usually deal with numbers, and natural language is, well, text. So we need to transform that text into numbers, otherwise known as text vectorization. It’s a fundamental step in the process of machine learning for analyzing data, and different vectorization algorithms will drastically affect end results, so you need to choose one that will deliver the results you’re hoping for.
