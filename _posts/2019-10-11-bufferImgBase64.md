---
title: 使用Node中的Buffer模块，将图片转换成base64编码
---

## {{ page.title }}

Buffer官方文档如下 http://nodejs.cn/api/buffer.html

node开发过程中，常用的就是将图片转换成base64编码，这时候就用到buffer了：

```javascript
const path = require('path');
const fs = require('fs');

fs.readFile(path.join(__dirname,'./files/1.jpg'),(err,data)=>{
	console.log(data.toString('base64'));	// 指定编码格式
});
```

运行
> node bufferReadFile.js > test.js

将打印出来的结果输入到test.js这个文件里，便于查看。之后我们在test.js里就能看到这个图片的base64编码了。

再设置好其 [DataURI前缀](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/data_URIs) 就能在浏览器里查看这张图片啦！

下面是我在浏览器查看的结果：

![imgBase64.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/imgBase64.png)