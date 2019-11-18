---
title: 初识Promise（入门级别）
---

## {{page.title}}

Promise 官方文档： [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

个人不建议直接读官方文档，太专业，太学术，很难啃得动（我当初就看得异常痛苦，简直怀疑自己智商，越到后面越难），大家可以先往下看，有个初步的认识后，再回头细看官方文档，相信会深有体会。

### 一、 为啥会需要Promise

具体的原因网上一大堆（请自行百度，不一一列举了），在此举个 **极为常见** 的 **回调地狱**的例子吧。

假设现在项目中有这样一个需求：
>1. 根据课程名称，得到课程id，假设该接口地址为：```course/getCourse.json```，post请求，传入课程名
>2. 根据 id 获取课程详情，假设该接口地址为：```course/getCourseDetail/{id}.json```,其中```{id}```为要传入的课程id
>3. 根据该课程详情里的任课教师的id，获取该老师的详细信息，假设该接口地址为：```teacher/getDetail/{id}.json```,其中```{id}```为要传入的教师id

先不管Promise，我们用传统的回调会怎么写呢：

以下使用jquery语法模拟以下代码（注意是模拟代码，不能运行，只提供算法思路）：
```javascript
// 1. 获取课程id
$.ajax({
	type:'POST',
	url:'course/getCourse.json',
	data:{
		courseName: '高等数学（一）' // 模拟数据和模拟代码，请勿当真
	}
	success: function(courseId){
		// 2. 获取课程详情
		$.ajax({
			type:'GET',
			url:`course/getCourseDetail/courseId.json`,
			success: function(courseDetail){
				let teacherId = courseDetail.teacherId; // 获取教师id
				// 3. 获取教师详情
				$.ajax({
					type:'GET',
					url:`teacher/getDetail/${teacherId}.json`, // es6 模板字符串语法，不了解请自行百度
					success: function(msg){
						console.log('该教师详细信息是： '+msg);
					},error:function(msg){
						console.log(msg);
					}
				});
			},error:function(msg){
				console.log(msg);
			}
		});
	},
	error:function(msg){
		console.log(msg);
	}
});
```

上例共三次嵌套请求web接口，且业务逻辑和代码较为简单。但看起来比较繁琐，一旦有需求改用，维护难度较大，如果中间再有其他逻辑，需要嵌套进去，那维护就太麻烦了，这个例子就是经典的**回调地狱**

所以我们亟需解决回调地狱的问题，所以Promise的就来了！

### 二、 Promise是个啥？究竟该怎么用？
我用比较通俗易懂的表达方式先向大家详细说说Promise：

> - Promise 是一个代理器，它和原先要执行的操作无关（比如ajax请求，文件读取等异步操作）；
> - 使用时须创建一个实例：```new Promise()```，此时这个实例状态为```pending```。
> - 实例化Promise的同时要向其传递一个参数，这个参数是个函数，作为一个**执行器**，执行里面的操作（比如ajax请求，文件读取等异步操作）。
> - **执行器**里面的操作完成了，改变当前Promise实例的状态为```fulfilled```；操作失败，改变状态为```rejected```。
> - 将当前状态传给then，如果状态是```fulfilled```,执行then里面的第一个函数；如果状态是```rejected```,则执行第二个函数。
> - then里面可return一个新的Promise实例，继续重复上述操作，可进行链式调用。

promise实例一经创建，立即执行**执行器**里面的操作：

```javascript
console.log('开始了');

// 实例化一个对象,生成一个代理器，随后立即执行其中的代码，此时这个代理器的状态是pending
new Promise((resolve,reject)=>{

	// 一个耗时的异步操作，打比方是上例中的第一步：根据课程名查找课程id
	$.ajax({
		type:'POST',
		url:'course/getCourse.json',
		data:{
			courseName: '高等数学（一）' // 模拟数据和模拟代码，请勿当真
		}
		success: function(courseId){
			resolve(courseId);		// 成功后执行resolve(可以传参数)，代理器的状态改为fulfilled
		},
		error:function(msg){
			reject(msg);	// 失败后执行reject(可以传参数)，代理器的状态改为rejected
		}
	});
}) // 当代理器的状态改变后，会立即触发then里面的响应函数
.then((courseId)=>{	// 状态为fulfilled时进入第一个函数参数
	console.log(`课程id是：${courseId}`);
},(rej)=>{ // 状态为rejected时进入第二个函数参数
	console.log('出错了：'+rej);
});
```

为了便于大家理解，我画了一个图:

![promise1.jpg](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/promise1.jpg)

**注意** 我们看到上面只有一次异步操作，代码比使用传统回调更多，问题就出在这里，因为当异步请求次数只有一次，或者多次请求间无相互依赖或顺序要求时，用Promise反而会适得其反，增加代码量和后续维护的难度。 **Promise经典的地方是在处理回调地狱问题上，只有多次相互依赖的异步请求才建议使用Promise**，如果你只有一次请求，就用传统回调就ok啦！上面只是给大家举个简单的例子，告诉大家Promise基本使用方法

我自己的感悟： **优雅的代码是在合适的地方用合适的方法，不去盲目追求所谓的最新最前沿。**

不是名言，但具有名言的特质

### 三、 Promise处理多次关联的异步请求

Promise 用法详细文档，这个可以看看：[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)

下面我们来看看Promise究竟是怎么解决回调地狱问题的

Promise的执行器执行里面的代码，发送请求得到请求结果，随后改变状态并将请求结果传给```then```来处理，如果```then```里面返回一个新的Promise对象，即可链式调用来处理相关一系列的异步请求。

> then() 函数会返回一个全新的 Promise，和原来的不同

这段话有点绕，大家仔细看下面的代码辅助理解：
```javascript
new Promise((resolve,reject)=>{		// 这个函数是个执行器，一经创建立即执行
	$.ajax({
		type:'POST',
		url:'course/getCourse.json',
		data:{
			courseName: '高等数学（一）' // 模拟数据和模拟代码，请勿当真
		}
		success: function(courseId){
			resolve(courseId);
		},
		error: function(errmsg){
			reject(errmsg);
		},
	});
}).then((courseId)=>{
	console.log(`获取到了课程id：${courseId},下面开始查询课程详情：`);

	// 返回一个新的Promise
	return new Promise((resolve,reject)=>{
		$.ajax({
			type:'GET',
			url:`course/getCourseDetail/courseId.json`,
			success: function(courseDetail){
				resolve(courseDetail);
			},
			error: function(error){
				reject(error);
			},
		});
	});
},(errmsg)=>{
	console.log(errmsg);
}).then((courseDetail)=>{
	console.log(`获取到了课程详情：`+courseDetail+`\n下面开始根据任课教师id获取教师详情：`);

	// 返回一个新的Promise
	return new Promise((resolve,reject)=>{
		$.ajax({
			type:'GET',
			url:`teacher/getDetail/${teacherId}.json`, // es6 模板字符串语法，不了解请自行百度
			success: function(teacherDetail){
				resolve(teacherDetail);
			},error:function(msg){
				reject(msg);
			}
		});
	});
},(error)=>{
	console.log(err);
}).then((teacherDetail)=>{
	console.log('教师的详细信息是：'+ teacherDetail);
},(msg)=>{
	console.log(msg);
});
```

