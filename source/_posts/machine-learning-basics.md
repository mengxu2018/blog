---
title: machine learning basic
date: 2021-11-06 13:27:07
tags: machine learning
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


### 梯度下降与梯度上升
在机器学习算法中，在最小化损失函数时，可以通过梯度下降思想来求得最小化的损失函数和对应的参数值，反过来，如果要求最大化的损失函数，可以通过梯度上升思想来求取。

### Definitions of Train, Validation, and Test Datasets
https://machinelearningmastery.com/difference-test-validation-datasets/

训练集-----------学生的课本；学生 根据课本里的内容来掌握知识。

验证集-----------作业，通过作业可以知道 不同学生学习情况、进步的速度快慢。

测试集-----------考试，考的题是平常都没有见过，考察学生举一反三的能力。

那么为什么要测试集呢？

a)训练集直接参与了模型调参的过程，显然不能用来反映模型真实的能力（防止课本死记硬背的学生拥有最好的成绩，即防止过拟合)。

b)验证集参与了人工调参(超参数)的过程，也不能用来最终评判一个模型（刷题库的学生不能算是学习好的学生）。

c)所以要通过最终的考试(测试集)来考察一个学(模)生(型)真正的能力（期末考试）。

验证数据应该也是有label的，但是测试数据应该是没有label的，就是用来测试看看结果怎么样

### What is Noise in Machine Learning
https://deepchecks.com/glossary/noise-in-machine-learning/

Humans are prone to making mistakes when collecting data, and data collection instruments may be unreliable, resulting in dataset errors. The errors are referred to as noise. Data noise in machine learning can cause problems since the algorithm interprets the noise as a pattern and can start generalizing from it.


### keras vs tf.keras
https://www.pyimagesearch.com/2019/10/21/keras-vs-tf-keras-whats-the-difference-in-tensorflow-2-0/


### false positive 假阳性
https://www.pico.net/kb/what-is-a-false-positive-rate/
False Positive
A False Positive is the incorrect identification of anomalous data as such, i.e. classifying as “abnormal” data which is in fact normal.
实际上是对的，但是predict出来说有问题，比如我们邮件敏感信息预测，我们告诉用户有敏感信息，但是实际上没有，我们需要降低fp

### 回归评价指标MSE、RMSE、MAE、R-Squared
https://www.jianshu.com/p/9ee85fdad150


### 欠拟合与过拟合
https://zhuanlan.zhihu.com/p/72038532
对于深度学习或机器学习模型而言，我们不仅要求它对训练数据集有很好的拟合（训练误差），同时也希望它可以对未知数据集（测试集）有很好的拟合结果（泛化能力），所产生的测试误差被称为泛化误差。度量泛化能力的好坏，最直观的表现就是模型的过拟合（overfitting）和欠拟合（underfitting）


### hyperparameter vs parameter
https://www.geeksforgeeks.org/difference-between-model-parameters-vs-hyperparameters/
简单说就是parameter就是模型训练的过程中自己自动调节的，比如线性回归的x前面的系数
hyperparameter就是我们给模型设置的参数，比如sklearn那些方法的参数，比如sklearn.cluster.KMeans的n_clusters


### 特征向量
A dataset is usually divided into a number of instances, each with its own set of features. Each example corresponds to a single feature vector, which contains all of the numerical values for that example object.

All of the feature vectors are normally stacked into a design matrix, with each row representing a vector for one example and each column representing all of the examples’ values for that feature.

https://deepchecks.com/glossary/feature-vector/