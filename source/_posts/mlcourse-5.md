---
title: mlcourse-5
date: 2017-03-20 14:55:51
tags: 机器学习
---
## Artificial Neural Networks

### Perceptron:感知机
![](/img/mlcourse-5_1.png)
<!--more-->
输入为实例的特征向量,输出为实例的类别,取+1和-1;
感知机对应于输入空间中将实例划分为正负两类的分离超平面,属于判别模型
导入基于误分类的损失函数
利用梯度下降法对损失函数进行极小化.
输入空间到输出空间的函数：
f(x) = sign(w*x+b)
模型参数：w，x,内积，权值向量，偏置
符号函数：sign(x) = +1 ,if x>= 0
        sign(x) = -1 ,if x<0

训练规则：
Wi <——  Wi + ΔWi
ΔWi = η(t-o)Xi
t为目标值，o为感知机输出之，η为学习率
训练线性单元的梯度下降算法

```
      GRADIENT-DESCENT(training_examples, η )
      training_examples 中每一训练样例形式为序偶<x,t>,其中 x 式输入向量,t 是目标输出值, η 是学习速率(例如 0.05)
      初始化每个 wi 为某个小的随机值
      遇到终止条件之前,做以下操作:
          初始化每个Δwi 为 0
          对于训练样例 training_examples 中的每个<x,t>,做:
                把实例 x 输入到此单元,计算输出 o
                对于线性单元的每个权 wi,做
                      Δwi←Δwi+ η (t-o)xi
          对于线性单元的每个权 wi,做
                wi←wi+Δwis
```

感知机想要成功的分类，需要要求
训练数据线性可分
学习率足够小
线性单元的训练规则是梯度下降：
    收敛于最小的误差平方
    ...

随机梯度下降：
```
    Incremental (Stochastic) Gradient Descent
    Batch mode Gradient Descent:
    Do until satisied
    1. Compute the gradient ▽E_D [w]
    2. w <——  w - η▽E_D [w]
```
```
      Incremental mode Gradient Descent:
```

### Multilayer Networks of Sigmoid Units：Sigmoid多层神经网络
![](/img/mlcourse-5_2.png)

误差反向传播算法
```
  BACPROPAGATION(training_examples, η ,n in , n out ,n hidden )
          training_examples中每一训练样例形式为序偶<x,t>,其中x式输入向量,t是目标输出值, η 是学习速率(例如 0.05)。n_in 是网络输入的数量,n_hidden
          是隐藏层单元数,n_out 是输出单元数。从单元 i 到单元 j 的输入表示为 xji,单元 i 到单元 j 的权值表示为 wji。
    •创建具有n in 个输入,n hidden 个隐藏单元,n out 个输出单元的网络
    •初始化所有的网络权值为小的随机值(例如-0.05 和 0.05 之间的数)
    •在遇到终止条件前:
        •对于训练样例 training_examples 中的每个 < x , t > :
        把输入沿网络前向传播ss
            1. 把实例x输入网络,并计算网络中每个单元u的输出o_u使误差沿网络反向传播
            2. 对于网络的每个输出单元k,计算它的误差项δ_k
                δ_k ←o_k (1-o_k )(t_k -o_k )
            3. 对于网络的每个隐藏单元h,计算它的误差项δ_h
                δ_h← o_h ( 1 − o_h )*sum{w_kh * δ_k}
            4. 更新每个网络权值 wji
                wji←wji+Δwji
                其中
                Δwji= η * δ_j * x_ji
```
