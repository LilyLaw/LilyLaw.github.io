---
title: node实现控制台进度条
---

## {{page.title}}

### 目标要求

读取文件的过程中，要求在控制台实时获取读取进度，并将进度以进度条的形式展现出来。

### 算法

1. 读文件操作使用```文件流```来实现
2. 使用第三方模块[```progress```](npmjs.com/package/progress)来实现进度条

### 实现

1. 安装第三方模块[```progress```](npmjs.com/package/progress)

	> yarn add progress

2. 代码：

	```javascript
	const fs = require('fs'),
		path = require('path'),
		ProgressBar = require('progress');

	let fpath = path.join(__dirname,'./files/writeF.mp4'),
		frstream = fs.createReadStream(fpath);	// 建立读取流

	fs.stat(fpath,(err,stats)=>{	// 获取文件基本信息，不是获取文件内容
		if(err) throw err;

		let bar = new ProgressBar(':bar  :percent', {	// 实例化对象
			total: stats.size,	// 将文件总大小设置为
			complete: '-',
			incomplete: ' ',
			width: 25	// 总显示宽度，因为total值太大，全部显示不易观察
		});

		frstream.on('data',(chunk)=>{	// 开始读取数据
			bar.tick(chunk.length);
			if (bar.complete) {
		    console.log('\n----complete---\n');
		  }
		});
	});
	```