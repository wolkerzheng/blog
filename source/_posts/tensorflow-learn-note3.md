---
title: tensorflow note3
date: 2017-03-14 17:13:27
tags: tensorflow_note
---
## note3

TensorFlow separates definition of computations from their execution
Phase 1: assemble a graph
Phase 2: use a session to execute operations in the graph.

tf.constant and tf.Variable
Constant values are stored in the graph definition
Sessions allocate memory to store variable values

Linear Regression
<!--more-->
Model relationship between a scalar dependent variable y and independent variables X
Phase 1: Assemble our graph
  Step 1: Read in data
  Step 2: Create placeholders for inputs and labels
    tf.placeholder(dtype, shape=None, name=None)
  Step 3: Create weight and bias
    tf.Variable(initial_value=None,trainable=True,collections=None,name=None, dtype=None, ...)
  Step 4: Build model to predict Y
    Y_predicted = X * w + b
  Step 5: Specify loss function
    tf.square(Y - Y_predicted, name="loss")
  Step 6: Create optimizer
    tf.train.GradientDescentOptimizer(learning_rate=0.001).minimize(loss)
Phase 2: Train our model
  Initialize variables
  Run optimizer op
  (with data fed into placeholders for inputs and labels)
  See your model in TensorBoard
  Step 1: writer =tf.summary.FileWriter('./my_graph/03/linear_reg',sess.graph)
  Step 2: $ tensorboard --logdir='./my_graph'

  Trainable variables
  Session looks at all trainable variables that optimizer depends on and update them
  tf.Variable(initial_value=None,trainable=True,collections=None,validate_shape=True,caching_device=None,name=None,variable_def=None,dtype=None,expected_shape=None,import_scope=None)


## Logistic Regression

model
  logits = X * w + b
  Y_predicted = softmax(logits)
  loss = cross_entropy(Y, Y_predicted)

Process data
  from tensorflow.examples.tutorials.mnist import input_data
  MNIST = input_data.read_data_sets("/data/mnist", one_hot=True)

  Step 2: Create placeholders for inputs and labels
  X = tf.placeholder(tf.float32, [batch_size, 784], name="image")
  Y = tf.placeholder(tf.float32, [batch_size, 10], name="label")
  Step 3: Create weight and bias
  tf.Variable(initial_value=None, trainable=True, collections=None,name=None, dtype=None, ...)
  Step 4: Build model to predict Y
    logits = X * w + b
  Step 5: Specify loss function
    entropy = tf.nn.softmax_cross_entropy_with_logits(logits, Y)
    loss = tf.reduce_mean(entropy)
  Step 6: Create optimizer
    tf.train.GradientDescentOptimizer(learning_rate=0.001).minimize(loss)

Phase 2: Train our model
  Initialize variables
  Run optimizer op
  (with data fed into placeholders for inputs and labels)
