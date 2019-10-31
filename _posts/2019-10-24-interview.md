---
title: 面试题
---

## {{page.title}}

## js

### 1. 简述以下代码运行结果

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

#### （6）
```javascript
if(!("a" in window)){
	var a = 1;
}

console.log(a);
```

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
#### （8）
```javascript
var foo = 'hello';
(function(foo){
	console.log(foo);
	var foo = foo || 'world';
	console.log(foo);
})(foo)
console.log(foo);
```
####（9）
```javascript
var a = 9;
function fn(){
	a = 0;
	return function(b){
		return b+a++;
	}
}

var f = fn();
console.log(f(5));
console.log(fn()(5));
console.log(f(5));
console.log(a);
```

**考点:**   js变量提升、作用域、闭包

## css

## node

