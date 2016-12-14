--- 
title: '数据预处理:one-hot encoding'
date: 2016-12-14 18:38:34
tags: read
categories: 杂
---

### One-Hot Encoding
one-hot编码，又称为一位有效编码，对任意给定的状态，状态向量中只有一位为1，其余各位为0。主要是采用N位状态寄存器来对N个状态进行编码，每个状态都由他独立的寄存器位，并且在任意时候只有一位有效。这种状态机的速度与状态的数量无关，只取决于某特定状态的转移数量，速度很快。当状态机的状态增加时，如果使用二编码，那么速度会明显下降，但如果采用独热码，虽然多用了触发器，但由于状态译码简单，节省和简化了组合逻辑电路。
一个四种状态：A，B，C，D
独热编码就为：0001,0010,0100,1000

在实际的机器学习的应用任务中，特征有时候并不总是连续值，有可能是一些分类值，如性别可分为“male”和“female”。在机器学习任务中，对于这样的特征，通常需要对其进行特征数字化，如有如下三个特征属性：
	*  性别：["male"，"female"]
 	*  地区：["Europe"，"US"，"Asia"]
	*  浏览器：["Firefox"，"Chrome"，"Safari"，"Internet Explorer"]
性别的属性是二维的，地区是三维的，浏览器则是四维的，这样，可以采用One-Hot编码的方式对上述的样本“["male"，"US"，"Internet Explorer"]”编码，“male”则对应着[1，0]，同理“US”对应着[0，1，0]，“Internet Explorer”对应着[0,0,0,1]。则完整的特征数字化的结果为：[1,0,0,1,0,0,0,0,1]。这样导致的一个结果就是数据会变得非常的稀疏。

	
	from sklearn import preprocessing
	enc = preprocessing.OneHotEncoder()
	enc.fit([[0,0,3],[1,1,0],[0,2,1],[1,0,2]])
	array = enc.transform([[0,1,3]]).toarray()
	print array 
	
结果：[[ 1.  0.  0.  1.  0.  0.  0.  0.  1.]]
