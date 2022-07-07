---
title: '''Test_classification'''
date: 2022-02-18 09:48:58
tags:
---

测试领域，冒烟测试（smoke test）、可用性测试（sanity test）和回归测试（regression test）彼此之间很相似，范围也有重叠，所以比较容易混淆：都是在需求变更或问题修改后对系统全面测试之前的一种预测试，都是为了发现是否在界面和代码层面引入了问题。

我们可以用一个和河流相关的类比来更好的理解它们之间的差别，在类比之前，我们先了解下这几个测试的简单定义：
Smoke Testing：测试新特性有关的所有方面 (广度) ，但不深入，用以判断我们是否需要执行进一步的测试。
Sanity Testing：测试新特性的有限正常功能，深入测试。
Regression testing：回归新特性所有相关功能，避免引入代码变更存在问题以及引入新问题，深入全面。

如果我们拿一条河流来比喻，比如1000英尺宽，在水里含有杂质（可以比作软件中的bug），那么这三种类型的测试可以被看作如下：
对于Smoke Testing：为了找到河面所有的杂质，但不包括水面以下的。
对于Sanity Testing：为了找到某个特定范围内所有的杂质（比如200英尺半径内)，这不包含所有表面的杂质，但包含了那个范围内水面下直到水底的杂质。
对于Regression Testing：为了这片水域所有的杂质，表面的以及水面以下的          