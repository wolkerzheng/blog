---
title: lstm for sentiment Analysis
date: 2017-04-17 15:14:01
tags: deep learning
---
## Brief Introduction
RNN机器变形：
https://arxiv.org/pdf/1506.00019.pdf
网址：http://deeplearning.net/tutorial/lstm.html
使用Theano实现的LSTM框架。
数据使用IMDB数据集，数据：http://ai.stanford.edu/~amaas/data/sentiment/
根据训练的模型来预测是positive或negative,本质是二分类任务。
<!--more-->
## 模型
### lstm
在传统的循环神经网络中，在梯度反向传播阶段期间，梯度信号可以最终被乘以与次数的神经元之间的连接的权重矩阵的大量次数（与时间步数一样多） 循环隐藏层。 过渡矩阵中权重的大小可以对学习过程产生很大的影响。
如果该矩阵中的权重小（如果权重矩阵的前导特征值小于1.0）可能导致出现梯度弥散问题：学习非常的慢或者是几乎是停止了学习。
如果该矩阵中的权重很大（如果权重矩阵的前导特征值大于1.0），则可能导致梯度信号如此大以致于学习发散的情况 。 这通常被称为爆炸梯度。

传统RNN问题：梯度弥散和梯度爆炸。

LSTM主要介绍了一个新组成， a memory cell 存储单元:
![](/img/lstm_1.png)

主要由四个因素组成：
  - an input gate （输入门）
    输入门可以允许输入信号改变存储器单元的状态或阻塞它。
  - a neuron with a self-recurrent connection（更新门）
    The self-recurrent connection的权重为1.0，除了任何外部干扰之外，存储器单元的状态可以从一个时间步长到另一个时间步长保持不变.用于调节记忆体本身与环境之间的相互作用。
  - a forget gate (遗忘门)
    忘记门可以调制存储单元的自我恢复连接，允许单元根据需要记住或忘记其以前的状态
  - an output gate （输出门）
    输出门可以允许存储单元的状态对其他神经元有影响或者阻止它。

下面的等式描述了在每个时间步长t中如何更新一层存储器单元。 在这些方程式中：
![](/img/lstm_2.png)

![](/img/lstm_3.png)

## Project_Model
标准LSTM的一种变形。在变型中，单元的输出门的激活不依赖于存储单元的状态C_t。这允许我们更有效地执行部分计算（详见下面的实现说明）。
![](/img/lstm_5.png)
模型训练过程：
模型由一个单层LSTM组成，接下来是均化层和逻辑回归层。
输入序列：X_0,X_1,X_2,...,X_n,
经过LSTM后会出输出：h_0,h_1,h_2,...,h_n
然后将所述表示序列在所有时间步长上进行平均得到h。 最后，h被送到逻辑回归层，其对象是与输入序列相关联的类标号。
![](/img/lstm_4.png)
