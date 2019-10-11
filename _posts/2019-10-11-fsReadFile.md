---
title: 使用fs模块 同步和异步读取文件
---

## {{ page.title }}

node中的```fs```模块，对于所有的文件操作几乎都有同步和异步两种方式,今天我们就通过读文件来感受一下同步和异步的区别

为了使耗时时长的差别更明显，我们最好读取一个体积大一点的文件。

### 同步

```fs.readFileSync```是同步读取文件

代码：
```javascript
const fs = require('fs');
const path = require('path');

console.time('sync');
try{
	let data = fs.readFileSync(path.join(__dirname,'files/26.mp3'),'utf8');
	// console.log(data);
}catch(err){
	throw err;
}
console.timeEnd('sync');
```

结果：

![sync.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/sync.png)

### 异步

```fs.readFile```是异步读取文件

代码：

```javascript
const fs = require('fs');
const path = require('path');

console.time('async');
fs.readFile(path.join(__dirname,'files/26.mp3'),'utf8',(err,data)=>{
	if(err) throw err;
})
console.timeEnd('async');
```

结果：

![async.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/async.png)

### 解析

同步操作，会因读取文件发生阻塞，直到读取完后，才会继续下面的```console.timeEnd('sync')```，所以耗时较长

而异步操作，当执行到读取文件代码段时，会将读文件这类会耗时的阻塞操作放到事件队列里，先执行下面的```console.timeEnd('async')```，当非阻塞的代码段执行完后，再按顺序执行事件队列里的代码，所以我们看到async的时间较短。


**注意** 我们有时候会发现，当多次执行同一代码段时，耗时结果不一样，这是因为不同执行时期当时的主机cpu使用情况不同（翻译成人话就是说 忙不忙），这跟机器有关系。