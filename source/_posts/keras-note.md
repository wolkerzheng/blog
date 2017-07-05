---
title: keras_note
date: 2017-04-18 19:31:59
tags: deep learning
---
记录一些自己在平时使用Keras时候遇到的一些问题。
<!--more-->
关于定制层和attention层的定制：
http://ben.bolte.cc/blog/2016/language.html
keras blog:
https://blog.keras.io/
### Convolution1D,Convolution2D
Convolution1D一维卷积，主要用于过滤一维输入的相邻元素，官方文档是这样的:
  keras.layers.convolutional.Convolution1D(nb_filter, filter_length, init='glorot_uniform', activation=None, weights=None, border_mode='valid', subsample_length=1, W_regularizer=None, b_regularizer=None, activity_regularizer=None, W_constraint=None, b_constraint=None, bias=True, input_dim=None, input_length=None)

```
    # apply a convolution 1d of length 3 to a sequence with 10 timesteps,
    # with 64 output filters
    model = Sequential()
    model.add(Convolution1D(64, 3, border_mode='same', input_shape=(10, 32)))
    # now model.output_shape == (None, 10, 64)

    # add a new conv1d on top
    model.add(Convolution1D(32, 3, border_mode='same'))
    # now model.output_shape == (None, 10, 32)
```
input_shape=(10, 32)简而言之就是10个32维的向量了，nb_filter : 卷积核的数量，也是输出的维度。filter_length : 每个过滤器的长度。
首先我们先看第一个卷积层，输出shape很容易理解，因为有64个卷积核，所以输出也就是64，接下来我们看下参数：其实可以这么理解，我们把例子中（10，32）的信号进行1D卷积相当于对其进行卷积核为（filter_length, 32）的2D卷积

1.Convolution1D主要用于nlp，Convolution2D主要用于cv。实际上，Convolution1D也可以用于cv，Convolution2D也可以用于nlp，只是那个时候不是标准的卷积方式，而是经过一定变形的卷积。

2.可以看到Convolution1D的卷积只有3这一个参数，Convolution2D却有两个参数3（即长度为3，宽度为3的卷积）。表面上Convolution1D没有给出卷积的大小，Convolution2D给出了。实际上，在Convolution1D中，只要给出了句子中word的长度，宽度就是word中的dimension，宽度自动把整个tensor的宽度包裹住。

3.Convolution1D的input_shape是长度为10，宽度为32的tensor。Convolution2D的input_shape是3个channel，长度为256，宽度为256的tensor。

### Embedding
```
    keras.layers.embeddings.Embedding(input_dim,output_dim, init='uniform', input_length=None, weights=None, W_regularizer=None, W_constraint=None, mask_zero=False)
```
将正整数转换为固定size的denses向量。比如[[4], [20]] -> [[0.25, 0.1], [0.6, -0.2]]
Embedding层只能作为模型的第一层
参数：
    nput_dim：大或等于0的整数，字典长度，即输入数据最大下标+1
    output_dim：大于0的整数，代表全连接嵌入的维度
    embeddings_initializer: 嵌入矩阵的初始化方法，为预定义初始化方法名的字符串，或用于初始化权重的初始化器。参考initializers
    embeddings_regularizer: 嵌入矩阵的正则项，为Regularizer对象
    embeddings_constraint: 嵌入矩阵的约束项，为Constraints对象
    mask_zero：布尔值，确定是否将输入中的‘0’看作是应该被忽略的‘填充’（padding）值，该参数在使用递归层处理变长输入时有用。设置为True的话，模型中后续的层必须都支持masking，否则会抛出异常。如果该值为True，则下标0在字典中不可用，input_dim应设置为|vocabulary| + 2。
    input_length：当输入序列的长度固定时，该值为其长度。如果要在该层后接Flatten层，然后接Dense层，则必须指定该参数，否则Dense层的输出维度无法自动推断。
### Dense
```
keras.layers.core.Dense(output_dim, init='glorot_uniform', activation='linear', weights=None, W_regularizer=None, b_regularizer=None, activity_regularizer=None, W_constraint=None, b_constraint=None, input_dim=None)
```
Dense(就是隐藏层，dense是稠密的意思)
Dense就是常用的全连接层，所实现的运算是output = activation(dot(input, kernel)+bias)。其中activation是逐元素计算的激活函数，kernel是本层的权值矩阵，bias为偏置向量，只有当use_bias=True才会添加。

### TimeDistributed
  早期版本的Keras中首先引入了TimeDistributedDense，以便对将序列应用与隐藏层。属于包装器Wrapper，把一个层应用到输入的每一个时间步上
  例如,考虑一个含有 32 个样本的 batch ,每个样本都是 10 个向量组成的序列,每个向量长为 16 ,则其输入维度为(32,10,16),其不包含batch大小的input_shape为(10,16)
  可以使用包装器TimeDistributed包装Dense,以产生针对各个时间步信号的独立全连接
  ```
    # as the first layer in a model
    model = Sequential()
    model.add(TimeDistributed(Dense(8), input_shape=(10, 16)))
    # now model.output_shape == (None, 10, 8)
    # subsequent layers: no need for input_shape
    model.add(TimeDistributed(Dense(32)))
    # now model.output_shape == (None, 10, 32)
  ```
  程序的输出数据shape为 (32,10,8)
