---
title: tensorflow note4
date: 2017-03-15 21:07:52
tags: tensorflow_note
---
### How to structure your TensorFlow model
Phase 1: assemble your graph
1. Define placeholders for input and output
2. Define the weights
3. Define the inference model
4. Define loss function
5. Define optimizer
<!--more-->
Phase 2: execute the computation
1. Initialize all model variables for the first time.
2. Feed in the training data. Might involve randomizing the order of data samples.
3. Execute the inference model on the training data, so it calculates for each training input example the output with the current model parameters.
4. Compute the cost
5. Adjust the model parameters to minimize/maximize the cost depending on the model.
![](/img/tensorflow_note3_1.png)

对于如果要建立一个skim-gram模型，根据以上的做法：
Phase 1: Assemble the graph
1. Define placeholders for input and output
Input is the center word and output is the target (context) word. Instead of using one-hot vectors, we input the index of those words directly. For example, if the center word is the 1001th word in the vocabulary, we input the number 1001. Each sample input is a scalar, the placeholder for BATCH_SIZE sample inputs with have shape[BATCH_SIZE].
Similar, the placeholder for BATCH_SIZE sample outputs with have shape [BATCH_SIZE].

  ```
        center_words = tf.placeholder(tf.int32, shape=[BATCH_SIZE])
        target_words = tf.placeholder(tf.int32, shape=[BATCH_SIZE])
  ```
Note that our center_words and target_words being fed in are both scalars -- we feed in their corresponding indices in our vocabulary.

2. Define the weight (in this case, embedding matrix)
Each row corresponds to the representation vector of one word. If one word is represented with a vector of size EMBED_SIZE, then the embedding matrix will have shape [VOCAB_SIZE,EMBED_SIZE]. We initialize the embedding matrix to value from a random distribution. In this case, let’s choose uniform distribution
```
    embed_matrix = tf.Variable(tf.random_uniform([VOCAB_SIZE, EMBED_SIZE], -1.0, 1.0))
```

3. Inference (compute the forward path of the graph)
Our goal is to get the vector representations of words in our dictionary. Remember that the embed_matrix has dimension VOCAB_SIZE x EMBED_SIZE, with each row of the embedding matrix corresponds to the vector representation of the word at that index. So to get the representation of all the center words in the batch, we get the slice of all corresponding rows in the embedding matrix. TensorFlow provides a convenient method to do so called tf.nn.embedding_lookup().
```
  tf.nn.embedding_lookup(params, ids, partition_strategy='mod', name=None,validate_indices=True, max_norm=None)
```

4. Define the loss function
```
  tf.nn.nce_loss(weights, biases, labels, inputs, num_sampled, num_classes, num_true=1,sampled_values=None, remove_accidental_hits=False, partition_strategy='mod',name='nce_loss')
  nce_weight = tf.Variable(tf.truncated_normal([VOCAB_SIZE, EMBED_SIZE],stddev=1.0 / EMBED_SIZE ** 0.5))
  nce_bias = tf.Variable(tf.zeros([VOCAB_SIZE]))


  loss = tf.reduce_mean(tf.nn.nce_loss(weights=nce_weight,
            biases=nce_bias,
            labels=target_words,
            inputs=embed,
            num_sampled=NUM_SAMPLED,
            num_classes=VOCAB_SIZE))
```

5. Define optimizer
We will use the good old gradient descent.
```
  optimizer = tf.train.GradientDescentOptimizer(LEARNING_RATE).minimize(loss)
```

Phase 2: Execute the computation
We will create a session then within the session, use the good old feed_dict to feed inputs and outputs into the placeholders, run the optimizer to minimize the loss, and fe3
  with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    average_loss = 0.0
    for index in xrange(NUM_TRAIN_STEPS):
      batch = batch_gen.next()
      loss_batch, _ = sess.run([loss, optimizer],
                      feed_dict={center_words: batch[0], target_words: batch[1]})
      average_loss += loss_batch
      if (index + 1) % 2000 == 0:
        print('Average loss at step {}: {:5.1f}'.format(index + 1,
            average_loss / (index + 1)))
```
