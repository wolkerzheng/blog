---
title: tensorflow学习笔记
date: 2017-02-21 19:51:22
tags: tensorflow_note
---

## 综述

Tensorflow:
 - 使用图(graph)来表示计算
 - 在被称之为会话(session)的上下文(context)中执行图
 - 使用tensor表示数据
 - 通过变量(variable)维护状态
 - 使用feed和fetch可以为任意的操作(arbitrary operation)赋值或者从其中获取数据
<!--more-->
Tensorflow是一个编程系统,使用图来表示计算任务,图中的节点op(operation)的简写.
一个op获得0个或多个tensor.执行计算,产生0个或者多个Tensor.
每个Tensor是一个类型化的多维数组.
一个Tensorflow图描述了计算的过程,为了进行计算,图必须在会话里被启动,会话将图的op分发到诸如CPU或ＧＰＵ之类的设备上，同时提供执行后，将产生的tensor返回，（python）是一个numpy ndarray对象．
计算图:
Tensorflow程序通常被组织成一个构建阶段和一个执行阶段,在构建阶段中,op的执行步骤被描述成一个图.在执行阶段,使用会话执行执行图中的op.eg,通常在构建阶段创建一个图来表示和训练神经网络,然后在执行阶段反复执行图中的训练op.
构建图:
构建图的第一步,是创建源op(source op),源op不需要任何输入,例如常量(constant),源op的输出被传递给其他op做运算.
Python库中,op构造器的返回值代表被构造出来的op输出.这些返回值可以传递给其他op构造器作为输入.
TensorFlow Python库有一个默认图(default graph),op构造器可以为其增加节点.
在一个会话中启动图
构造阶段完成后,才能启动图.启动图的第一步是创建一个Session对象,如果无任何创建参数,会话构造器将启动默认图.
Tensor
TensorFlow 程序使用 tensor 数据结构来代表所有的数据, 计算图中, 操作间传递的数据都tensor. TensorFlow tensor 可以看作是一个 n 维的数组或列表. 一个 tensor 包含一个静态类型 rank, 和 一个 shape.
Fetch
为了取回操作的输出内容, 可以在使用 Session 对象的 run() 调用 执行图时, 传入一些 tensor, 这些 tensor 会取回结果.
Feed
该机制 可以临时替代图中的任意操作中的 tensor 可以对图中任何操作提交补丁, 直接插入一个tensor.

constant

tf.constant(value, dtype=None, shape=None,
name='Const', verify_shape=False)

tf.zeros(shape, dtype=tf.float32, name=None) 类似与numpy.zeros()
creates a tensor of shape and all elements will be zeros (when ran in session)
eg:
	```
		tf.zeros([2, 3], tf.int32) ==> [[0, 0, 0], [0, 0, 0]]
	```

tf.zeros_like(input_tensor, dtype=None, name=None, optimize=True)
creates a tensor of shape and type (unless type is specified) as the input_tensor but all elements are zeros.
eg:
	```
		input_tensor is [0, 1], [2, 3], [4, 5]]
		tf.zeros_like(input_tensor) ==> [[0, 0], [0, 0], [0, 0]]
	```

tf.fill(dims, value, name=None)
creates a tensor filled with a scalar value.
eg:
	```
		tf.fill([2, 3], 8) ==> [[8, 8, 8], [8, 8, 8]]
	```
tf.linspace(start, stop, num, name=None) # slightly different from np.linspace
tf.range(start, limit=None, delta=1, dtype=None, name='range')
eg:
	```
		tf.linspace(10.0, 13.0, 4) ==> [10.0 11.0 12.0 13.0]
		tf.range(3, limit=18, delta=3) ==> [3, 6, 9, 12, 15]
 		limit = 5
		tf.range(limit) ==> [0, 1, 2, 3, 4] Tensor objects are not iterable
		for _ in tf.range(4): # TypeError
	```
TensorFlow takes Python natives types: boolean, numeric (int, float), strings

variables:
	# create variable a with scalar value
	a = tf.Variable(2, name="scalar")
	# create variable b as a vector
	b = tf.Variable([2, 3], name="vector")
	# create variable c as a 2x2 matrix
	c = tf.Variable([[0, 1], [2, 3]], name="matrix")
	# create variable W as 784 x 10 tensor, filled with zeros
	W = tf.Variable(tf.zeros([784,10]))

Each session maintains its own copy of variable
