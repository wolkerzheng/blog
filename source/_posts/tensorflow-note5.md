---
title: tensorflow note5
date: 2017-03-23 21:33:14
tags: tensorflow_note
---

## How to manage your experiments in TensorFlow

tf.train.Saver()类，tensorflow 随机种子和numpy的随机状态。
<!--more-->
### tf.train.Saver()
方便模型的保存重新加载
```
tf​.train.Saver​.save​ (sess​ ,save_path​ ,global_step​ = ​ None​ , ​ latest_filename​ = ​ None​ ,meta_graph_suffix​ = ​ 'meta'​ , ​ write_meta_graph​ = True​ , ​ write_state​ = ​ True)

```
在每次1000次训练步骤后，保存图的参数：
```
# define model
# create a saver object
saver ​ = ​ tf​ . ​ train​ . ​ Saver​ ()
# launch a session to compute the graph
with​ tf​ . ​ Session​ ()​ ​ as​ sess:
  # actual training loop
  for​ step ​ in​ range​ ( ​ training_steps​ ):
    sess​ . ​ run​ ([​ optimizer​ ])
    if​ (step + 1) ​ % ​ ​ 1000​ ==​ 0:
              saver​ . ​ save​ ( ​ sess​ , ​ ​ 'checkpoint_directory/model    
```
在tensorflow中，你每次保存的点，称为checkpoint。
gloabl_step
加载时后可以使用
```
saver​.restore​(sess​ , ​'checkpoints/skip-gram-10000')
```
tf.summary
### Control randomization
```
  tf​.set_random_seed​(seed)
```
```
import​ tensorflow ​ as​ tf
tf.set_random_seed​ (2)
c ​ = ​ tf​.random_uniform​ ([],​ -10​ , ​ 10)
d ​ = ​ tf​.random_uniform​ ([],​ -10​ , ​ 10)
with​ tf​.Session​()​ ​ as​ sess:
    print​ sess.run​(c)
    print​ sess​.run​(d)
```
### Reading Data in TensorFlow
```
tf.TextLineReader
Outputs​ the lines of a file delimited ​ by​ newlines
E​.g. ​ text files​ , ​ CSV files

tf​.FixedLengthRecordReader
Outputs​ the entire file ​ when​ all files have same ​fixed​ lengths
E​.g. each MNIST file has 28​x28​ pixels​,CIFAR​-10​ 32​ x ​32​ x 3
tf​.WholeFileReader
Outputs​ the entire file content
tf.TFRecordReader
Reads​ samples ​ from​ ​ TensorFlow​'s own binary format(TFRecord)
tf​.ReaderBase
Allows​ you to create your own readers
```
