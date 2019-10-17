---
title: node使用fs模块写文件
---

## {{page.title}}

写文件基本上有两种类型：直接覆盖和追加

### 直接覆盖

1. 异步：```fs.writeFile()```

	```javascript
	fs.writeFile(path.join(__dirname,'./files/writeFile.txt'),JSON.stringify({ name: 'lily', sex: 'female' }),(err)=>{
		if(err) throw err;
		console.log('写入成功！');
	});
	```

2. 同步：```fs.writeFileSync()```

	```javascript
	try{
		fs.writeFileSync(path.join(__dirname,'./files/writeFileSync.txt'),'同步执行');
		console.log('写入成功！');
	}catch(err){
		throw err;
	}
	```

3. 流：```fs.createWriteStream()```

	```javascript
	let writeStream = fs.createWriteStream(path.join(__dirname,'./files/writeStream.txt'));
	writeStream.write('通过流写入文件',()=>{
		console.log('写入成功');
	});
	```

### 追加

1. 异步：```fs.appendFile()```
2. 同步：```fs.appendFileSync()```