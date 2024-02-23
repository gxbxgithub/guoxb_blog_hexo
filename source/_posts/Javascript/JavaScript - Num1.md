---
title: JavaScript - Num1
categories: JavaScript
tags:
- JavaScript学习
---

JavaScript 是世界上最流行的编程语言。
因为你在电脑、手机、平板上浏览的所有的网页，以及无数基于HTML5的手机App，交互逻辑都是由JavaScript驱动的。JavaScript 插入 HTML 页面后，可由所有的现代浏览器执行。
<!-- more -->

# 快速入门
JavaScript代码可以直接嵌在网页的任何地方，不过通常我们都把JavaScript代码放到`<head>`中：

    <script>
        alert('hello world');
    </script>
由`<scrip>...</script>`包含的代码就是JavaScript代码，它将直接被浏览器执行。

第二种方法是把JavaScript代码放到一个单独的.js文件，然后在HTML中通过`<script src="..."></script>`引入这个文件：

    <head>
        <script src="abc.js"></script>
    </head>
这样abc.js文件就会被浏览器执行；
把JavaScript代码放入一个单独的.js文件中更利于维护代码，并且多个页面可以各自引用同一份.js文件。
值得注意的是：在同一个页面中可以引入多个.js文件，浏览器按照顺序依次执行（jQuery.js之前不能使用jQuery的语法）。

有些时候你会看到`<script>`标签还设置了一个type属性：`<script type="text/javascript">...</script>`,其实这是没有必要的，因为默认的type就是JavaScript代码。

# 语法
JavaScript的语法和Java语言类似，每个语句以“;”结束，语句块用“{...}”，JavaScript并不强制要求在每个语句的结尾加“;”，浏览器中负责执行JavaScript代码的引擎会自动在每个语句的结尾补上“;”。
请注意，JavaScript严格区分大小写，如果弄错了大小写，程序将报错或者运行不正常。

# 数据类型
### Number
JavaScript不区分整数和浮点数，统一用Number表示，下面是两个比较特别的Number类型：
NaN;（NaN表示Not a Number，当无法计算结果时用NaN表示）
Infinity;（Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity）
2 / 0; // Infinity
0 / 0; // NaN
### 字符串
字符串是以单引号'或双引号"括起来的任意文本；
### 布尔值
布尔值和布尔代数的表示完全一致，一个布尔值只有true、false两种值；
### 比较运算符
JavaScript允许对任意数据类型做比较：
false == 0; // true
false === 0; // false
要特别注意相等运算符：
第一种是==比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；
第二种是===比较，它不会自动转换数据类型，如果数据类型不一致，返回false，如果一致，再比较。
### 数组
数组是一组按顺序排列的集合，集合的每个值称为元素。JavaScript的数组可以包括任意数据类型。例如：

    var arr = [1, 2, 3.14, 'Hello', null, true];
    var arr = new Array(1, 2, 3); // [1, 2, 3]
### 对象
JavaScript的对象是一组由键-值组成的无序集合，例如：

    var person = {
        name: 'Bob',
        age: 20,
        tags: ['js', 'web', 'mobile'],
        city: 'Beijing',
        hasCar: true,
        zipcode: null
    };
JavaScript对象的键都是字符串类型，值可以是任意数据类型。
### 变量
变量在JavaScript中就是用一个变量名表示，变量名是大小写英文、数字、$和_的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如if、while等。申明一个变量用var语句，比如：

    var a; // 申明了变量a，此时a的值为undefined
    var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1
    var s_007 = '007'; // s_007是一个字符串
    var Answer = true; // Answer是一个布尔值true
    var t = null; // t的值是null
变量名也可以用中文，但是，请不要给自己找麻烦！！！
JavaScript在设计之初，为了方便初学者学习，并不强制要求用var申明变量。这个设计错误带来了严重的后果：如果一个变量没有通过var申明就被使用，那么该变量就自动被申明为全局变量：

    i = 10; // i现在是全局变量

# 字符串
JavaScript的字符串就是用''或""括起来的字符表示。
如果'本身也是一个字符，那就可以用""括起来。

### 多行字符串
由于多行字符串用\n写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用\` ... `表示：

    alert(`hello
    world`);

### 模板字符串
要把多个字符串连接起来，可以用+号连接：

    var name = '小明';
    var age = 20;
    var message = '你好, ' + name + ', 你今年' + age + '岁了!';
    alert(message);
    
如果有很多变量需要连接，用+号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量：

    var name = '小明';
    var age = 20;
    var message = `你好, ${name}, 你今年${age}岁了!`;   
    alert(message);
    
### 操作字符串
1.字符串长度

    var s = 'Hello, world!';
    s.length; // 13
    
2.获取字符串某个指定位置的字符

    var s = 'Hello, world!';
    s[0]; // 'H'
    s[6]; // ' '
    s[7]; // 'w'
    s[12]; // '!'
    s[13]; // undefined 超出范围的索引不会报错，但一律返回undefined
需要特别注意的是，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果

3.JavaScript为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串：
#### toUpperCase 把一个字符串全部变为大写

    var s = 'Hello';
    s.toUpperCase(); // 返回'HELLO'
    
#### toLowerCase 把一个字符串全部变为小写

    var s = 'Hello';
    var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
    
#### indexOf 指定字符串出现的位置

    var s = 'hello, world';
    s.indexOf('world'); // 返回7
    s.indexOf('World'); // 没有找到指定的子串，返回-1

#### substring 返回指定索引区间的子串

    var s = 'hello, world'
    s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
    s.substring(7); // 从索引7开始到结束，返回'world'

