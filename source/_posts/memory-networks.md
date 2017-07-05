---
title: memory networks
date: 2017-04-19 10:42:58
tags: deep learning
---
http://blog.csdn.net/u011274209/article/details/53384232
### 原理
对于很多神经网络模型，缺乏了一个长时记忆的组件方便读取和写入。作为RNN，LSTM和其变种GRU使用了一定的记忆机制。在Memory Networks的作者看来，这些记忆都太小了，因为把状态（state，也就是cell的输出）及其权重全部都嵌入到一个低维空间，把这些知识压缩成一个稠密的向量，丢失了不少信息。
<!--more-->
对于memory Networks主要是增加了一个memory m模块。m是一个对象的数组（an array of objects，for example an array of vectors or an array of strings）,其实也是加了很多插槽（slot)。记忆一个事实（一般是对话组里的一句话），就是把它插到记忆（数组）里。
 一个记忆网络（memory networks，简称为MemNN），包括了上述的记忆m，还包括以下4个组件I、G、O、R：
 组件   名称                       描述
 I     input              convert the incoming input to the internal feature representation
 G     generalization     updates old memories given the new input.
 O     output             produces a new output in the feature representation space given the new input and the current memory state
 R     response           converts the output into the response format desired

I：用于将输入转化为网络里内在的向量。作者使用了简单的向量空间模型，维度为3*lenW+3（熟悉VSM会问为什么不是lenW，下面会说具体）。
G：更新记忆。在作者的具体实现里，只是简单地插入记忆数组里。作者考虑了几种新的情况，虽然没有实现，包括了记忆的忘记，记忆的重新组织。
O：从记忆里结合输入，把合适的记忆抽取出来，返回一个向量。
R：将该向量转化回所需的格式，比如文字或者answer。对于R来说，最简单的是，直接返回o的第一个支撑记忆，也就是一句话。当然作者，打算弄复杂点，返回一个词汇w。
![](/img/memory_networks_1.png)
所有组件都是神经网络的话，叫做记忆神经网络（MemNNs，多了个s）。
![](/img/memory_networks_2.png)
例子：
![](/img/memory_networks_3.png)
I：I输入的是一句话，简单地将I转换为一个频率的向量空间模型。
G：也是如上，简单地把读到的对话组里的每一句话的向量空间模型，插到记忆的list里，这里默认记忆插槽比对话组句子还多。
O：O干的事，就是输入一个问题x，将最合适的k个支撑记忆（the supporting memories，在下文的数据集里会举出例子），也就是top-k。做法就是把记忆数组遍历，挑出最大的值。最后，O返回一个长度为k的数组。
对于top1有o1=O1(x,m)=argmaxsO(x,mi)
对于top2有o2=O2(x,m)=argmaxsO([x,mo1],mi)
R：为了返回一个词汇w，设置公式r=argmaxw∈WsR([x,mo1,mo2],w)。这里对所有的词汇进行循环遍历，挑出最适合答案（分数最高）的结果。在论文里，词汇被转化为同一空间里的向量空间模型的向量。如“memory”=[0,0,0,0,1,0,0,0]，“network”=[1,0,0,0,0,0,0,0]，则句子“memory network”=[1,0,0,0,1,0,0,0]。
