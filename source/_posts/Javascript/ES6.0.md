---
title: ECMAScript 6.0
categories: JavaScript
tags:
- ES6
---

ECMAScript 6.0（简称ES6）是JavaScript语言的下一代标准，在2015年6月正式发布，目标是使得JavaScript语言可以实现复杂的大型应用程序，成为企业级开发语言。

先说一下 ECMAScript 和 JavaScript 的关系：JavaScript 是 Netscape 公司创造的；ECMAScript 是 ECMA标准化组织规定的浏览器脚本语言标准。可以说，ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现。
<!-- more -->

### 一、let & const

#### 1. 声明变量

let 和 var 类似，都是用来声明变量，只是 let 所声明的变量，只在 let 命令所在的代码块内有效。

```
{
	let numA = 10;
	var numB = 20;
}
console.log(numA); // numA is not defined.
console.log(numB); // 20
```

补充：

```
for (let i = 0; i < 3; i ++) {
	let i = 'abc';
	console.log(i); // abc
}
// 函数内部变量i 与 循环变量i 不在同一个作用域
```

#### 2. 不存在变量提升

var 会发生“变量提升”，即变量可以在声明之前使用，值为undefined。而let 只能在声明之后使用，否则会报错。 

#### 3. const

const 声明一个只读的常量，一旦声明，值就不能改变