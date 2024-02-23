---
title: Python基础知识
tags: 
- Python
categories: Python
---

Python是一种解释型、面向对象、动态数据类型的高级程序设计语言。
计算机工作的CPU只认识机器指令，尽管不同的编程语言差异极大，最后都得“翻译”成CPU可以执行的机器指令。而不同的编程语言，干同一个活，编写的代码量，差距也很大。
比如，完成同一个任务，C语言要写1000行代码，Java只需要写100行，而Python可能只要20行。
所以Python是一种相当高级的语言。

<!--more-->

### 1. 直接运行py文件
在Windows系统上是不行的，但是在Mac和Linux上是可以的，方法是

(1)在.py文件的第一行加上一个特殊的注释：

`#!/usr/bin/env python3`

(2)然后，通过命令给.py文件以执行权限：

`$ chmod a+x *.py`

之后在终端直接`./*.py`即可运行

### 2. 类型
布尔类型为True 或 False；
空值为None，不能理解为0，因为0是有意义的，None是一个特殊的空值

### 3. 运算
/ 除法，计算结果是浮点数，即使两个整数恰好整除，结果也是浮点数：

```
>>> 9 / 3
3.0
```

// 地板除，结果永远是整数，只取结果的整数部分

```
>>> 10 // 3
3
```

### 4. 编码
Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符

```
>>> ord('A')
65
>>> chr(66)
'B'
```

由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上:

`# -*- coding: utf-8 -*-`

这行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

### 5. 格式化字符串
(1)使用%

```
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

%d	整数

%f	浮点数

%s	字符串

%x	十六进制整数

有些时候，字符串里面的%是一个普通字符怎么办？这个时候就需要转义，用%%来表示一个%：

```
>>> 'growth rate: %d %%' % 7
'growth rate: 7 %'
```

（2）format()

另一种格式化字符串的方法是使用字符串的format()方法，它会用传入的参数依次替换字符串内的占位符{0}、{1}……，不过这种方式写起来比%要麻烦得多：

```
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```

### 6. list-数组（有序，可变）
```
>>> classmates = ['Michael', 'Bob', 'Tracy']
// （1）添加元素
>>> classmates.append('Adam')
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam']
// （2）插入元素
>>> classmates.insert(1, 'Jack')
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']
// （3）删除元素
pop()删除末尾；pop(i)删除指定索引元素
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
>>> classmates.pop(1)
'Jack'
>>> classmates
['Michael', 'Bob', 'Tracy']
```

### 7. tuple元组（有序，不可变）
另一种有序列表叫元组：tuple。tuple和list非常类似，但是tuple一旦初始化就不能修改，元组使用“()”,而不是“[]”

```
>>> classmates = ('Michael', 'Bob', 'Tracy')
```

*如果元组只有一个数

```
>>> t = (1) // 结果是1这个数字，而不是元组
>>> t = (1,) // 代表一个数的元组
```

### 8. dict-字典
（1）判断key是否存在
判断key是否存在，使用in

`'key' in dict` 返回True 或 False

或者使用get()

`dict.get('key')` 如果不存在，返回None

（2）删除key，使用pop

```
>>> dict.pop('key')
75
```

### 9.set-集合（无序，元素不重复）
（1）

```
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```

通过 add(key) 方法可以添加元素

通过 remove(key) 方法删除元素

（2）set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：

```
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```

### 10. 函数
（1）定义函数 def

```
def my_abs(x):
	if x >= 0:
		return x
	else:
		return -x
```

（2）空函数

pass可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个pass，让代码能运行起来

```
def nop():
	pass
```
pass还可以用在其他语句里，比如：

```
if age >= 10:
	pass
```

（3）参数检查

对参数类型做检查，只允许整数和浮点数类型的参数。数据类型检查可以用内置函数isinstance()实现：

```
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```

（4）默认参数

```
def enroll(name, gender, age=4, city='BeiJing'):
	...

>>> enroll('guoxb', 'F')
>>> enroll('guoxb', 'F', city='HeBei')
```

* 定义默认参数要牢记：默认参数必须指向不变对象！

```
def add_end(L=[]):
    L.append('END')
    return L

>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

以上代码明显是有问题的，要修改上面的例子，我们可以用None这个不变对象来实现：

```
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

（5）可变参数

* 定义

```
# 请计算a2 + b2 + c2 + …
def calc(numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum 
# 但是调用的时候，需要先组装出一个list或tuple：
>>> calc( [1, 2, 3] )
14
>>> calc( (1, 3, 5, 7) )
84

# 如果利用可变参数，调用函数的方式可以简化成这样：
>>> calc(1, 2, 3)
14
>>> calc(1, 3, 5, 7)
84

# 函数的参数改为可变参数：
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
    
# 如果已经有一个list或者tuple，要调用一个可变参数怎么办？可以这样做：
# Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去：
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

（6）关键字参数

* 定义

```
# 函数person除了必选参数name和age外，还接受关键字参数kw
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw
    
>>> person('Michael', 30)
name: Michael age: 30 other: {}

# 可以传入任意个数的关键字参数：
>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}

# 关键字参数有什么用？它可以扩展函数的功能。比如，在person函数里，我们保证能接收到name和age这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。

# 和可变参数类似，也可以先组装出一个dict，然后，把该dict转换为关键字参数传进去：

>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}

# **extra表示把extra这个dict的所有key-value用关键字参数传入到函数的**kw参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra。
```

（7）命名关键字参数

* 定义

```
# 和关键字参数**kw不同，命名关键字参数需要一个特殊分隔符*，*后面的参数被视为命名关键字参数。
def person(name, age, *, city, job):
    print(name, age, city, job)
    
# 调用方式如下：
>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer

# 如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了：
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

（8）参数组合

* 定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。
* 参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

```
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

（9）小结

* `*args是可变参数，args接收的是一个tuple；`
* `**kw是关键字参数，kw接收的是一个dict。`
* `可变参数既可以直接传入：func(1, 2, 3)，又可以先组装list或tuple，再通过*args传入：func(*(1, 2, 3))；`
* `关键字参数既可以直接传入：func(a=1, b=2)，又可以先组装dict，再通过**kw传入：func(**{'a': 1, 'b': 2})。`

