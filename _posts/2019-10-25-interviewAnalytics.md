---
title: 面试题解析
---

## {{page.title}}
## js
### 1. 简述以下代码运行结果

考点：变量提升、作用域、闭包

程序执行会进行以下几步： 1. 形参赋值；2. 变量提升（变量或函数）；3. 代码自上而下执行

#### （1）
```javascript
console.log(a);

var a = 12;
function fn(){
	console.log(a);
	var a = 13;
}

fn();
console.log(a);
```
**解析**：```var``` 关键字定义的变量会进行变量提升，所以输出结果是 ```undefined  undefined  12```
#### （2）
```javascript
console.log(a);
a = 12;

function fn(){
	console.log(a);
	a = 13;
}

fn();
console.log(a);
```
**解析**： 报错，变量未定义
#### （3）
```javascript
var foo = 1;

function bar(){
	console.log(foo);
	if(!foo){
		var foo = 10;
	}
	console.log(foo);
}

bar();
```
**解析**： ```if```条件判断不管是否成立，里面的变量都会进行变量提升，运行结果为 ```undefined  10```
#### （4）
```javascript
var n = 0;
function a(){
	var n = 10;
	function b(){
		n++;
		console.log(n);
	}
	b();
	return b;
}

var c = a();
c();
console.log(n);
```
**解析**
```javascript
// 1. 全局作用域下的变量提升： var n; a = bbcc0011(一个假设的内存地址); var c;
// 2. 代码自上而下执行: （1）全局作用域下的n被赋值为0；（2）全局作用域下的c被赋值为函数a的执行结果的返回值
// 3. 执行函数 a()
var n = 0;	// n = 0;
function a(){
	// 4. 私有作用域下变量提升：var n; b = ddee2233(一个假设的内存地址);
	// 5. 函数a() 自上而下执行代码：（1）n = 10；（2）执行函数b()；（3）把b中存储的内存地址return出去。
	var n = 10;	// n = 10 -> n = 11 -> n = 12
	function b(){
		// 6. 私有作用域下形参赋值和变量提升都没有，直接执行代码： n++（上层作用域n++）
		n++;	// n = 11;
		console.log(n);	// 11
	}
	b();
	return b;
}
// 7. c = ddee2233(b中存储的内存地址)
var c = a();
// 8. 执行在内存地址ddee2233中存储代码段，由于此段代码的作用域在函数a中，所以函数a不能销毁，因此n此时调用的仍是函数a私用作用域里面的n，再n++
c();	// 12
console.log(n);	// 0，取全局作用域中的n
```
#### （5）
```javascript
var a = 10, b = 11, c = 12;
function test(a){
	a = 1;
	var b = 2;
	c = 3;
}
test(10);
console.log(a);
console.log(b);
console.log(c);
```
**解析**
```javascript
// 1. 全局作用域下的变量提升：var a; var b; var c; test = ddaa2131(一个假设的内存地址);
// 2. 代码自上而下执行： 全局作用域下 a = 10; b = 11; c = 12; 执行test(10);
var a = 10, b = 11, c = 12;
function test(a){
	// 1. 形参赋值：私有作用域下的形参 a = 10,之后该此函数内的a均是访问该私有作用域下的a
	// 2. 变量提升：私有作用域下 var b;
	// 3. 代码自上而下执行：私有作用域 a = 1;b=2; 上层作用域 c = 3(因为私有作用域里没有c这个变量，所以要循着作用域链往上层找);
	a = 1;
	var b = 2;
	c = 3;
}
test(10);
console.log(a);	// 10
console.log(b); // 11
console.log(c); // 3
```
#### （6）
```javascript
if(!("a" in window)){
	var a = 1;
}

console.log(a);
```
**解析**：不管```if```条件是否成立，里面的变量都会被提升：在全局作用域下```var a```, 若是在浏览器中运行此代码，浏览器有```window```对象，将变量```a```作为```window```对象的一个属性添加上去

#### （7）
```javascript
var a = 4;
function b(x,y,a){
	console.log(a);
	arguments[2] = 10;
	console.log(a);
}

a = b(1,2,3);
console.log(a);
```
**解析**：非严格模式下，形参和```arguments```一一映射