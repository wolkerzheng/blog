---
title: sklearn_note
date: 2017-03-17 15:14:15
tags:
---

## Text feature extraction

### The Bag of Words representation
文本分析是机器学习算法的主要应用领域。 然而，原始数据，符号序列不能直接馈送到算法本身，因为大多数人期望具有固定大小的数字特征向量而不是具有可变长度的原始文本文档。
为了解决这个问题，scikit-learn提供了用于从文本内容中提取数字特征的最常用方法的实用程序，即：
1.tokenizing 标记字符串并为每个可能的标记给出整数ID，例如通过使用空格和标点符号作为记号分隔符。
2.counting 计数每个文档中标记的出现次数
3.normalizing 使用大多数样本/文档中出现的减少的重要性标记进行归一化和加权
<!--more-->
在该方案中，特征和样本定义如下：
    每个独立的标记出现频率（标准化或不标准）被视为特征。
    给定文档的所有标记频率的向量被认为是多元样本。

因此，文档语料库可以由一行一个文档的矩阵和在语料库中出现的每个标记（例如，词）的一列表示。

我们将向量化称为将文本文档的集合转换为数字特征向量的一般过程。 这种具体的策略（标记化，计数和归一化）被称为词袋或“n-gram袋”表示。 文档由单词出现来描述，同时完全忽略文档中的单词的相对位置信息。

### Sparsity

由于大多数文档通常使用语料库中使用的单词的非常小的子集，所以得到的矩阵将具有许多为零（通常大于99％）的特征值。

例如，10,000个短文本文档（例如电子邮件）的集合将使用具有总共100,000个唯一字词的词汇表，而每个文档将单独使用100到1000个唯一字词。
为了能够在内存中存储这样的矩阵，并且加速代数运算矩阵/向量，实现方式通常使用稀疏表示，例如在scipy.sparse包中可用的实现。

### Common Vectorizer usage

CountVectorizer在单个类中实现标记化和发生计数.这个模型有很多参数，但是默认值是相当合理的（有关详细信息，请参阅参考文档）：
```
    from sklearn.feature_extraction.text import CountVectorizer
    vectorizer = CountVectorizer(min_df=1)
    corpus = [
      'This is the first document.',
      'This is the second second document.',
      'And the third one.',
     'Is this the first document?',]
     X = vectorizer.fit_transform(corpus)
     analyze = vectorizer.build_analyzer()
     analyze("This is a text document to analyze.") == (['this', 'is', 'text', 'document', 'to', 'analyze'])  #Ｔrue

     vectorizer.vocabulary_.get('document')#1
     vectorizer.transform(['Something completely new.']).toarray()#array([[0, 0, 0, 0, 0, 0, 0, 0, 0]]...)
     bigram_vectorizer = CountVectorizer(ngram_range=(1, 2),token_pattern=r'\b\w+\b', min_df=1)
    analyze =bigram_vectorizer.build_analyzer()
    analyze('Bi-grams are cool!') == ( ['bi', 'grams', 'are', 'cool', 'bi grams', 'grams are', 'are cool'])      #Ｔrue                        
     X_2 = bigram_vectorizer.fit_transform(corpus).toarray()  
     #array([[0, 0, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 1, 1, 0],
      [0, 0, 1, 0, 0, 1, 1, 0, 0, 2, 1, 1, 1, 0, 1, 0, 0, 0, 1, 1, 0],
       [1, 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 1, 1, 1, 0, 0, 0],
       [0, 0, 1, 1, 1, 1, 0, 1, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 1, 0, 1]]...)
     feature_index = bigram_vectorizer.vocabulary_.get('is this')
     X_2[:, feature_index]      #array([0, 0, 0, 1]...)

```

##  Tf–idf term weighting
在大文本语料库中，一些单词（例如英语中的“the”，“a”，“is”）关于文档内容携带非常少有意义的信息。 如果我们直接将直接计数数据馈送到分类器，那些频繁的术语将影响更稀有但更有用的术语的频率。
为了将计数特征重新加权为适合分类器使用的浮点值，使用tf-idf变换是非常常见的。
Tf表示词语频率，而tf-idf表示词语频率乘以逆文档频率：
tf-idf(t,d) = tf(t,d)\*idf(t)
使用TfidfTransformer的默认设置，
TfidfTransformer(norm='l2', use_idf=True, smooth_idf=True, sublinear_tf=False)
词频，词语在给定文档中出现的次数与idf分量相乘，其被计算为:
idf(t) = log {(1+n_d)/(1+df(d,t))} + 1
其中n_d是文档的总数，并且{df}（d，t）是包含词t的文档的数目。 然后通过欧几里得范数标准化所得的tf-idf载体：
Ｖ_norm = v/{||v||^2} = v / {sqrt{(v_1)^2+(v_2)^2+(v_3)^2+...+(v_n)^2+}}
这最初是为信息检索开发的术语加权方案（作为搜索引擎结果的排名函数），其也已经在文档分类和聚类中很好使用。

以下部分包含说明如何精确计算tf-idfs以及如何在scikit-learn的TfidfTransformer和TfidfVectorizer中计算的tf-idfs与定义idf的标准教科书符号略有不同的更多说明和示例
idf(t) = log{n_d/df(d,t)}+1
```
    from sklearn.feature_extraction.text import TfidfTransformer
    transformer = TfidfTransformer(smooth_idf=False)
    counts = [[3, 0, 1],
...           [2, 0, 0],
...           [3, 0, 0],
...           [4, 0, 0],
...           [3, 2, 0],
...           [3, 0, 2]]
    tfidf = transformer.fit_transform(counts)
    tfidf.toarray()
```

虽然tf-idf标准化通常非常有用，但是可能存在二进制出现标记可能提供更好特征的情况。 这可以通过使用CountVectorizer的二进制参数来实现。 特别地，一些估计器例如伯努利朴素贝叶斯显式地建模离散布尔随机变量。 此外，非常短的文本可能具有噪声tf-idf值，而二进制出现信息更稳定。
像往常一样，调整特征提取参数的最佳方式是使用交叉验证的网格搜索，例如通过用分类器流水线化特征提取器：
