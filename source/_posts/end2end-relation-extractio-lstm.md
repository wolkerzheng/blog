---
title: End-to-End Relation Extraction using LSTMs on Sequences and Tree Structures
date: 2017-05-20 20:23:20
tags: deeplearning
---

## End-to-End Relation Extraction using LSTMs on Sequences and Tree Structures
文章来自ACL2016

### 摘要
文章提出了一中端到端模型来抽取实体以及它们间的关系。模型通过双向序列（从左到右和从右到左）和双向树结构（自底而上和自上而下）的LSTM-RNN实现对单个模型中的实体和关系的联合建模。
模型首先识别实体，然后使用单个递增解码的NN结构提取检测到的实体之间的关系，并使用实体和关系标签共同更新NN参数。
<!--more-->
### 模型
![](/img/e2ere_1.png)
图一是模型的结构。模型主要由三个层组成：
  - 词嵌入层（embedding layer）
  - 基于LSTM-RNN的词序列层（sequence layer）
  - 基于LSTM-RNN的依存树层（dependency layer）
第一类是嵌入层，用于表示word、part-of-speech tag、dependency type和entity label；
第二类是序列层，用于表示word sequence，这一层是一个双向LSTM-RNN；
第三类是依存层，在依存树上表示两个目标词（实体）之间的关系。

在解码过程中，在序列层上使用贪心的从左到右实体检测方法，并在依赖层上实现关系分类，其中每个基于LSTM-RNN的子树对应于检测到的实体之间的候选关系。

  - 嵌入层（Embedding Layer）
    嵌入层将n_w，n_p，n_d，n_e维的词（words），词性（part-of-speech POS），依存关系（dependency types），实体标签（entity labels）分别映射为v^(w),v^(p),v^(d),v^(e)向量。这些distributed represntations作为模型的训练参数的一部分。

  - 序列层（Sequence Layer）
    用双向LSTM-RNN来表示一个句子中的词序列
    ![](/img/e2ere_2.png)
    第t个词的LSTM单元接受词语和它的词性向量的拼接作为输入向量
    将每个单词两个方向的LSTM单元的隐藏状态向量拼接做为其输出向量，并将其传递给后续的层

    将实体检测视为序列标注任务，常用的编码方案BILOU（Begin，Inside，Last，Outside，Unit）为每个词打标签，其中每个实体标签表示实体类型和单词在实体位置。
    在序列层顶部进行实体检测。采用具有n_he维隐层h^(e)的双层NN和用于实体检测的softmax输出层。
    ![](/img/e2ere_3.png)
    从左到右的贪心法来对词语打实体标签。在解码中，使用单词的已预测标签来预测下一个单词的标签，这样可以考虑到标签间依赖关系

  - 依存层（Dependency Layer）
    依存层表示依存关系树中的两个目标词（对应于关系分类中的关系候选对）之间的关系.
    该层主要关注依存关系树中的目标词对之间的最短路径.
    采用双向树结构LSTM-RNN（即自底而上和自上而下）捕获目标词对边的依存结构来表示关系候选.
    ![](/img/e2ere_4.png)

    将依赖层（对应于关系候选）堆叠在序列层的顶部，以将字序列和依赖关系树结构信息并入到输出中。第t个字的依赖层LSTM单元接收输入x t =[ hst; vt（d）; vt（e）i]，即拼接序列层中相应隐藏状态向量st，依赖关系类型嵌入向量vt（d）（表示与父节点的依赖关系类型），标签嵌入向量vt（e）（对应于已预测的实体标签）。
    关系候选对向量dp=[↑hpa;↓hp1;↓hp2]拼接构成。↑hpa是自底而上的LSTM-RNN结构中的顶部LSTM单元的隐藏状态向量（表示目标字对p的最低共同祖先），↓hp1，↓hp2是表示自上而下的LSTM-RNN中第一和第二目标词的两个LSTM单元的隐藏状态向量.
    关系分类与实体检测类似，采用双层NN，包括nhr维的隐层h(r)和softmax输出层（具有权重矩阵W，偏置向量b）。
    ![](/img/e2ere_5.png)
