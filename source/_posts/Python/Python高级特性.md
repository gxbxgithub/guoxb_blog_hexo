---
title: Python高级特性
tags: 
- Python
categories: Python
---

Python是一种解释型、面向对象、动态数据类型的高级程序设计语言。
计算机工作的CPU只认识机器指令，尽管不同的编程语言差异极大，最后都得“翻译”成CPU可以执行的机器指令。而不同的编程语言，干同一个活，编写的代码量，差距也很大。
比如，完成同一个任务，C语言要写1000行代码，Java只需要写100行，而Python可能只要20行。
所以Python是一种相当高级的语言。

<!--more-->

### 1. 切片

从数组取值是非常常见的操作，如果取一个数组前3个元素，可以通过切片截取元素

```
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
>>> L[0:3] # 0和3都是索引，索引为0可以省略L[:3]
['Michael', 'Sarah', 'Tracy']
# 需要注意的是，Python支持L[-1]获取倒数第一个元素，同样支持倒数切片，记住倒数第一个元素的索引是-1

# 前10个数，每两个取一个：
>>> L[0:10:2]
# 所有数，每5个取一个
>>> L[::5]
# 原样复制一个list
>>> L[:]
# 字符串也可以通过切片截取字符串，相当于substring
```

### 2. 迭代

循环遍历 即为 迭代 (for in)

```
# 迭代字典
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
>>> 	print key
>>> 
>>> for value in d.values():
>>> 	print value
>>> 
>>> for k,v in d.items()
>>> 	print k + v
>>> 
# 字符串也可作为迭代对象
>>> for ch in 'ABC':
>>> 	print(ch)
>>> 
# 只要作为一个迭代对象，for循环就可以正常运行，通过collections模块的Iterable类型判断是否是迭代对象：
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```

### 3. 列表生成式

举个例子，要生成list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]可以用list(range(1, 11))：

```
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

但如果要生成[1x1, 2x2, 3x3, ..., 9x9]怎么做？

```
# 生成式
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# for 循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]

# 还可以使用两层循环，可以生成全排列：
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```