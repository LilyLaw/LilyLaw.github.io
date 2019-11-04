---
title: 初识Promise（入门级别）
---

## {{page.title}}

Promise 官方文档： https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise

个人不建议直接读官方文档，太专业，太学术，很难啃得动（我当初就看得异常痛苦，简直怀疑自己智商，越到后面越难），大家可以先往下看，有个初步的认识后，再回头细看官方文档，相信会深有体会。

### 一、 为啥会需要Promise

具体的原因网上一大堆（请自行百度，不一一列举了），我就给大家举一个**极为常见**的**回调地狱**的例子吧：

假设现在项目中有这样一个需求：
> - 根据课程名称，得到课程id，假设该接口地址为：```course/getCourse.json```，post请求，传入课程名
> - 根据 id 获取课程详情，假设该接口地址为：```course/getCourseDetail/{id}.json```,其中```{id}```为要传入的课程id
> - 根据该课程详情里的任课教师的id，获取该老师的详细信息，假设该接口地址为：```teacher/getDetail/{id}.json```,其中```{id}```为要传入的教师id

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
					}
				});
			}
		});
	}
});
```

上例共三次嵌套请求web接口，且业务逻辑和代码较为简单。但看起来比较繁琐，一旦有需求改用，维护难度较大，如果中间再有其他逻辑，需要嵌套进去，那维护就太麻烦了，这个例子就是经典的**回调地狱**

所以我们亟需解决回调地狱的问题，所以Promise的就来了！

### 二、 Promise是个啥？

