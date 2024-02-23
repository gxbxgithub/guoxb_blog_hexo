---
title: JavaScript - Num2
categories: JavaScript
tags:
- JavaScript学习
---

内容概要：数组、对象条件判断以及循环。
<!-- more -->

# 数组
JavaScript的Array可以包含任意数据类型，并通过索引来访问每个元素。
可以直接访问length属性，取得Array的长度
```
var arr = [1, 2, 3.14, 'Hello', null, true];
arr.length; // 6
```
值得注意的是：直接给Array的length赋值会导致Array大小变化：
```
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```
Array可以通过索引取值或赋值，*如果通过索引赋值，索引超过了范围，会同样引起Array大小的变化*：
```
var arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```
### 数组的一些方法
#### 1.indexOf(sub)
通过indexOf()来搜索一个指定的元素的位置
#### 2.slice(index, length)
截取Array的部分元素，然后返回一个新的Array;
如果不给slice()传递任何参数，它就会从头到尾截取所有元素
#### 3.push和pop
push()向Array的末尾添加若干元素，pop()则把Array的最后一个元素删除掉
#### 4.unshift和shift
使用unshift()方法往Array的头部添加若干元素，shift()方法则把Array的第一个元素删掉
#### 5.sort
sort()可以对当前Array进行排序，它会直接修改当前Array的元素位置，直接调用时，按照默认顺序排序
#### 6.reverse
reverse()把整个Array的元素调换位置
#### 7.splice
可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素
```
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook');//返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```
#### 8.concat
concat()方法把当前的Array和另一个Array连接起来，并返回一个新的Array
