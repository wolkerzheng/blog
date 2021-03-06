---
title: python学习笔记
date: 2017-01-10 11:03:22
tags: python
---

整理些在写代码过程中自己没有牢记的函数
<!--more-->
## python基本语法
### enumerate函数
对一个列表既要遍历索引又要遍历元素时使用,下面方法是正常使用时,但有点多余
	```
		for i in range(0,len(list)):
			print i,list[i]
	```
	```
		def enumerate(collection):
			i = 0
			it = iter(collection)
			while 1:
				yield(i,it.next())
				i+=1
	```
enumerate会将数组或列表组成一个索引序列.
	```
		for index,text in enumerate(list):
			print index,text
	```
### map和reduce函数
map()函数接收两个参数,一个是函数,一个是序列,map将传入的函数依次作用到序列的每个元素,并把结果作为新的list返回
	```
		print map(str,[1,2,3])
	```
输出:['1','2','3']
	```
		def f(x):
			rerturn x*x
		map(f,[1,2,3,4])
	```
实现了序列的平方.
map()作为高阶函数,它把规则抽象了

reduce()接收两个参数.把一个函数作用在一个序列[x1,x2, .. ,]上,reduce把结果继续和序列的下一个元素做累积计算:
reduce(f,[x1,x2,x3,x4]) = f(f(f(x1,x2),x3),x4)
对一个序列求和,使用reduce实现:
	```
		def add(x,y):
			return x+y
		reduce(add,[1,2,3,4,5])
	```
结果是15,相当于对序列用了一个sum()函数
eg.str2int函数实现
	```
		def str2int(s):
			def fn(x, y):
				return x * 10 + y
			def char2num(s):
				return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5,'6': 6, '7': 7, '8': 8, '9': 9}[s]
			return reduce(fn, map(char2num, s))
	```
### 判断子串
判断集合s中的每个元素是否在集合t中:
s.issubset(t)	s <= t	test whether every element in s is in t

判断集合t中的每个元素是否在集合s中:
s.issuperset(t)	s >= t	test whether every element in t is in s


### sorted函数
sorted(iterable,key,reverse),sorted 一共有三个参数,
iterable,表示可以迭代的对象,eg字典的value或key值dict.items(),dict.keys()
key,用来选取参与比较的元素
reverse,用来指定排序是倒序还是顺序,reverse=true是倒序,reverse=false(默认)是顺序
	```
		d = {'a':23,'c':20,'ac':12}
		print sorted(d.keys())
	```
按key值排序输出结果:['a','ac','c']
	```
		d = {'a':23,'c':20,'ac':12}
		print sorted(d.items(),key=lambda item:item[1])
	```
按value值排序输出结果:[('ac', 12), ('c', 20), ('a', 23)]

### Python垃圾回收机制：gc模块
在Python中，为了解决内存泄露问题，采用了对象引用计数，并基于引用计数实现自动垃圾回收。
Python文档对自动垃圾回收功能函数 __del__() 函数的描述：
```
Some common situations that may prevent the reference count of an object from going to zero include: circular references between objects (e.g., a doubly-linked list or a tree data structure with parent and child pointers); a reference to the object on the stack frame of a function that caught an exception (the traceback stored in sys.exc_traceback keeps the stack frame alive); or a reference to the object on the stack frame that raised an unhandled exception in interactive mode (the traceback stored in sys.last_traceback keeps the stack frame alive).

```
### encode and decode
常见编码介绍：
GB2312编码：适用于汉字处理、汉字通信等系统之间的信息交换
GBK编码：是汉字编码标准之一，是在 GB2312-80 标准基础上的内码扩展规范，使用了双字节编码
ASCII编码：是对英语字符和二进制之间的关系做的统一规定
Unicode编码：这是一种世界上所有字符的编码。当然了它没有规定的存储方式。
UTF-8编码：是 Unicode Transformation Format - 8 bit 的缩写， UTF-8 是 Unicode 的一种实现方式。它是可变长的编码方式，可以使用 1~4 个字节表示一个字符，可根据不同的符号而变化字节长度。
Python内部的字符串一般都是 Unicode编码。代码中字符串的默认编码与代码文件本身的编码是一致的。所以要做一些编码转换通常是要以Unicode作为中间编码进行转换的，即先将其他编码的字符串解码（decode）成 Unicode，再从 Unicode编码（encode）成另一种编码。
	- decode 的作用是将其他编码的字符串转换成 Unicode 编码，eg str.decode(utf8)，表示将utf8编码的字符串decode成Unicode编码
	- encode 的作用是将Unicode编码转换成其他编码的字符串，eg str.encode(”utf8“)，表示将Unicod编码的字符串encode成utf8编码
	
## panda
numpy的核心是ndarray,pandas也是围绕Series和 DataFrame这两个核心数据结构
numpy中tile()函数功能是重复某个数组.比如np.tile(A,n)功能是将数组A重复n次,构成一个新的数组.
二维数组: b = np.tile(A,(2,1))表示将b变为一个二行一列的数组b

Series是类似于数组的对象,它有一组数据与之相关的标签组成
	```
		import pandas as pd
		object = pd.Series([2,5,8,9])
		print object
	```
结果为:
	0 2
	1 5
	2 8
	3 9
结果包含了一列数据和一列标签
也可以构建标签:
	```
		Obj = pd.Series([2,5,8,9],index=['a','b','c','d']
		print obj
	```
Series中的方法:
isnull或者notnull可以判断数据缺失值情况
等等...

	```
		import numpy as np
		import pandas as pd
		data1 = pd.DataFrame({'level':['a','b','c','d'],
				 'numeber':[1,3,5,7]})

		data2=pd.DataFrame({'level':['a','b','c','e'],
				 'numeber':[2,3,6,10]})
		print data1
	```
结果为:
  	level number
     0	a	1
     1	b	3
     2	c	5
     3	d	7

In [6]: dates = pd.date_range('20130101', periods=6)



In [7]: dates
Out[7]:
DatetimeIndex(['2013-01-01', '2013-01-02', '2013-01-03', '2013-01-04',
               '2013-01-05', '2013-01-06'],
              dtype='datetime64[ns]', freq='D')
pandas中merge和concat方法可以用来实现数据集的合并

1.merge函数通过一个或多个键来将数据集的行连接起来。该函数的主要 应用场景是针对同一个主键存在两张包含不同特征的表，通过该主键的连接，将两张表进行合并。合并之后，两张表的行数没有增加，列数是两张表的列数之和减一。
merge(left,right,how='inner',on=None,left_on=None,right_on=None,
left_index=False,right_index=False,sort=False,suffixes=('_x','_y'),copy=True)

on=None 指定连接的列名，若两列希望连接的列名不一样，可以通过left_on和right_on 来具体指定
how=’inner’,参数指的是左右两个表主键那一列中存在不重合的行时，取结果的方式：inner表示交集，outer 表示并集，left 和right 表示取某一边
	```
		import pandas as pd
		df1 = pd.DataFrame([[1,2,3],[5,6,7],[3,9,0],[8,0,3]],columns=['x1','x2','x3'])
		df2 = pd.DataFrame([[1,2],[4,6],[3,9]],columns=['x1','x4'])
		print df1
		print df2
		df3 = pd.merge(df1,df2,how = 'left',on='x1')
		print df3
	```

设置了两个DataFrame类别的变量df1,df2,(平常我们用的表csv文件，读取之后也是DataFrame 格式)。设置 on=’x1’,即以两个表中的x1为主键进行连接，设置how=’left’ ,即是以两个表中merge函数中左边那个表的行为准，保持左边表行数不变，拿右边的表与之合并。

pandas.read_csv()
pandas.read_table()
