---
title: crf
date: 2016-12-29 19:35:40
tags: 机器学习
---

CRF(conditional random field) 条件随机场是在最大熵模型和隐马尔科夫模型的基础上提出的一种无向图学习模型,是一种用于标注和切分有序数据的条件概率模型.这时一种判别式无向图模型.

产生式模型和判别式模型

产生(生成)式模型(Generative):构建o和s的联合分布p(s,o),如HMM,BNs,MRF
产生式模型:样本→概率密度模型---产生模型→预测
判别式模型(Discriminative):构建o和s的条件分布p(s|o),如SVM,CRM,MEMM
判别式模型:样本→判别函数--预测模型→预测
<!--more-->
生成方法:可还原出联合概率分布P(X,Y), 而判别方法不能。生成方法的收敛速度更快,当样本容量增加的时候,学到的模型可以更快地收敛于真实模型;当存在隐变量时,仍可以使用生成方法,而判别方法则不能用。
判别方法:直接学习到条件概率或决策函数,直接进行预测,往往学习的准确率更高;由于直接学习Y=f(X)或P(Y|X),可对数据进行各种程度上的抽象、定义特征并使用特征,因此可以简化学习过程。

概率图模型:
这是一类用图的形式表示随机变量之间条件依赖关系的概率模型.是概率论与图论的结合.
G = (V,E)
V:顶点/结点,表示随机变量
E:边/弧,表示随机变量间的条件依赖关系

有向图:也称贝叶斯网络(Bayesian Networks)或信念网络(Belief Networks,BN's)

无向图:也称马尔科夫随机场(Markov Random Fiels,MRF's)或马尔科夫网络(Markov Networks)
