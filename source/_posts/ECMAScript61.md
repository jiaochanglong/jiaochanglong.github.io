title: ECMAScript新特性（一）
date: 2015-12-17 11:07:15
tags:
- 学习笔记
- ECMAScript 6
categories: ECMAScript 6
---

最近正在调研[Angular2.0](https://angular.io/)，发现官网给出的dome竟然有三种语言版本的，Angular 2 for TypeScript、Angular 2 for JavaScript 、Angular 2 for Dart 。简单看下原因好像因该是：
> Dart是Google开发的另一种语言，它有自己的运行时和基础类库，挺强大，但是它的API和正常的JavaScript代码不兼容，想要抹平这种差异需要再做一层封装，无疑这会是性能大打折扣。对于Angular来说，这性能损耗是不可接受的，所有就有了Angular Dart版本，Angular1.X google团队一直在维持这两个版本的Angular，Angular2.0把Angular和Angular Dart统一起来，这样团队就不用维护两套系统了。
看一下Angular 2 for TypeScript 的dome代码，发现ECMAScript 6 新特性是在是太方便了，所以打算先系统学习下ECMAScript 6的新特性，在这里做一下记录总结。


## let和const命令:
* ### let命令
let命令用来声明变量,但是所声明的变量，只在let命令所在的代码块内有效，
```javascript
{
	var a = 1;
	let b = 2;
}
a // 1
b // not defined
```

let其实形成了块级作用于，它还不存在变量提升、不允许重发声明、不允许重复声明
```javascript
var a = 1;
var b = 2;
{
	a // 1
	c // 3 变量提升
	var c = 3;
	d // not defined let不存在变量提升
	let d
	let d //error let不允许重复声明
	b // not defined 因为这个块级作用域用let声明了变量b，let又不存在变量提升
	let b
}
```

<!-- more -->
* ### const命令
const也用来声明变量，一旦声明，变量的值就不能改变。与let相同，const声明也只在块级作用域内有效，且不可重复声明，不存在变量提升。
```javascript
const a = 1;
a = 2;
a // 1
const a = 3;// 不报错但也不生效
a // 1
var b = 2;
let c = 3;
const b; // Error 不可重复声明
const c; // Error 不可重复声明
```
用const声明对象，只是对象在内存中的地址不可改变，并不是对象不可改变。
```javascript
const foo = {};
foo.a = 1;

foo.a // 1
foo = {} //Error

const arr = [];
arr.push(1);
arr.pop();

arr = [] // Error
```
* ### 全局对象的属性
let命令、const命令声明的全局变量，不属于全局对象的属性。
```javascript
var a = 1;
window.a // 1
let b = 2;
const c = 3;
window.b // undefined
window.c // undefined
```

## 变量的解构赋值
* ### 数组的解构赋值
ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。
只要等号两边的模式相同，左边的变量就会被赋予对应的值。
如果解构不成功，变量的值就等于undefined。
```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3
let [,,third] = ["foo", "bar", "baz"];
third // "baz"
let [x, , y] = [1, 2, 3];
x // 1
y // 3
// 以下情况均为undefined
var [foo] = [];
var [foo] = 1;
var [foo] = false;
var [foo] = NaN;
var [bar, foo] = [1];
```
* ### 对象的解构赋值
和数组结构赋值类似，但是因为对象是无序的，所以赋值必须要有相同的属性名才能赋值成功。
```javascript
var { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
var { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined

var obj = {
p: [
"Hello",
{ y: "World" }
]
};
var { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```
* ### 字符串的解构赋值
字符串也可以解构赋值,在解构赋值过程字符串被转换成一个类数组对象，带有length属性。
```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"

let {length : len} = 'hello';
len // 5
```
* ### 结构赋值的用途
刚开始看这个结构赋值觉得没什么用，但其实他非常的方便

#### 1. 交换变量的值
```javascript
[x, y] = [y, x];
```
上面代码交换变量x和y的值，这样的写法不仅简洁，而且易读，语义非常清晰。
#### 2. 从函数返回多个值
函数只能返回一个值，如果要返回多个值，只能将它们放在数组或对象里返回。有了解构赋值，取出这些
值就非常方便。

```javascript
// 返回一个数组
function example() {
	return [1, 2, 3];
}
var [a, b, c] = example();
// 返回一个对象
function example() {
	return {
		foo: 1,
		bar: 2
	};
}
var { foo, bar } = example();
```
#### 3. 函数参数的定义
解构赋值可以方便地将一组参数与变量名对应起来。
```javascript
// 参数是一组有次序的值
function f([x, y, z]) {
	...
}
f([1, 2, 3])
// 参数是一组无次序的值
function f({x, y, z}) {
	...
}
f({x:1, y:2, z:3})
```
#### 4. 提取JSON数据
解构赋值对提取JSON对象中的数据，尤其有用。
```javascript
var jsonData = {
	id: 42,
	status: "OK",
	data: [867, 5309]
}
let { id, status, data: number } = jsonData;
console.log(id, status, number)
// 42, OK, [867, 5309]
```
上面代码可以快速提取JSON数据的值。

## 数值的扩展
* ### Number.isFinite(), Number.isNaN()
这两个方法一个检测时候非无穷 一个检测是否NaN
````javascript
Number.isFinite(15); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite("foo"); // false
Number.isFinite("15"); // false
Number.isFinite(true); // false

Number.isNaN(NaN); // true
Number.isNaN(15); // false
Number.isNaN("15"); // false
Number.isNaN(true); // false
```
* ### Number.parseInt(), Number.parseFloat()
这两个方法不变，只是移植到Number上了

* ### Number.isInteger()
Number.isInteger()这个方法用来判断是否为整数
```javascript
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```
* ### Math对象的扩展
这个添加了好多我不怎么用的方法，就不写了。
