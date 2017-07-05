---
title: ufldl_softmax
date: 2016-12-15 18:43:24
tags: read
categories: dl
---

### softmax
http://ufldl.stanford.edu/wiki/index.php/Softmax%E5%9B%9E%E5%BD%92

softmax回归模型是logistic回归模型在多分类问题上的推广，在多分类问题种，类标签y可以取两个以上的值。softmax回归模型对于诸如mnist手写数字分类10个不同的单个数字问题是很有用的。softmax回归是有监督的。
<!--more-->
在logistic回归中，有m个已标记的训练集{（x(1),y(1)）,...,(x(m),y(m))}，logistic回归是针对二分类问题的，类标记y(i)属于{0,1}。假设函数(hypothesis function）可以表示为：
![](/img/blog_softmax_1.png)
将训练模型参数θ，使其能够最小化代价函数：
![](/img/blog_softmax_2.png)


在多分类中，对于给定的测试输入x,用假设函数针对每一个类别j估算出概率值p(y=j|x).
假设函数要输出一个k维的向量（向量元素的和为1）来表示这k个估计的概率值。假设函数hθ（x）形式如下:
![](/img/blog_softmax_3.png)
θ1,θ2,...θk是模型参数。
