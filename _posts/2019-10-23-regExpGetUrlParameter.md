---
title: 正则表达式获取url参数
category: Frontend
---

## {{page.title}}

### 目标要求

获取url地址中的参数，且以键值对的形式整合到一个对象中。

### 思路及算法

首先可以想到两种方法

1. 使用```split()```拆分字符串，但步骤较多，代码量较大，总而言之就是**low**，不考虑
2. 使用正则表达式，比如现在有这样一个url地址，我们把他赋给一个变量```url```
	```javascript
	let url = 'https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&tn=baidu&wd=jekyll%20%20category&oq=github.io%2520category&rsv_pq=c3fd8dd700010f81&rsv_t=67a96qm4qQ0gLWabFVMDEit1rfe0ea50FxuwkLqX8RD6Z0CM5jk7pjsSMhI&rqlang=cn&rsv_enter=1&rsv_dl=tb&inputT=1291&rsv_sug3=58&rsv_sug2=0&rsv_sug4=1291';
	```

	参数规则是：（1）键和键值都不包含```?```，```=```，```&```这三个字符；（2）键和键值分别在```=```的左右两边出现；（3）参数可能有多对，所以要全局考虑。

	根据以上规则，可以写出正则表达式：
	```javascript
	let reg = /([^?=&]+)=([^?=&]+)/g,
	```

### 代码

```javascript
let url = 'https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&tn=baidu&wd=jekyll%20%20category&oq=github.io%2520category&rsv_pq=c3fd8dd700010f81&rsv_t=67a96qm4qQ0gLWabFVMDEit1rfe0ea50FxuwkLqX8RD6Z0CM5jk7pjsSMhI&rqlang=cn&rsv_enter=1&rsv_dl=tb&inputT=1291&rsv_sug3=58&rsv_sug2=0&rsv_sug4=1291';

// 1. () 正则分组
// 2. ^ 取反
// 3. + 出现一到多次
// 4. g 全局修饰符
let reg = /([^?=&]+)=([^?=&]+)/g,
	obj = {};

url.replace(reg,function(...arg){
	obj[arg[1]] = arg[2];
});

console.log(obj);
```

### 升级

这个功能非常典型，可能以后会经常用到，尤其是整站开发的时候，因此我们可以把它挂到原型链上
```javascript
String.prototype.lllgetUrlParameter = function(){
	let reg = /([^?=&]+)=([^?=&]+)/g,
		obj = {};

	this.replace(reg,function(...arg){
		obj[arg[1]] = arg[2];
	});

	return obj;
}

let url = 'https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&tn=baidu&wd=jekyll%20%20category&oq=github.io%2520category&rsv_pq=c3fd8dd700010f81&rsv_t=67a96qm4qQ0gLWabFVMDEit1rfe0ea50FxuwkLqX8RD6Z0CM5jk7pjsSMhI&rqlang=cn&rsv_enter=1&rsv_dl=tb&inputT=1291&rsv_sug3=58&rsv_sug2=0&rsv_sug4=1291';

let urlP = url.lllgetUrlParameter();
console.log(urlP)
```