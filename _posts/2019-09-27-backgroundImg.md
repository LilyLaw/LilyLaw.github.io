---
title: html css 中非常经典的背景图片充满屏幕且不变形问题
---

## {{ page.title }}

### 要求：
1. 图片尺寸任意，图片宽高比任意（意思就是随便拿张图片都得ok），渲染后不可变形
2. 图片中心与可视区中心重合
3. 图片充满整个屏幕
4. 自适应屏幕宽高变化
5. 给图片上一层蒙版，便于在其上渲染文字

### 思路分析：
1. 宽高比任意，且不变形，可以推测到背景图的宽和高必定是一方某一长度设置，一方是auto，这样才能保证不变形，所以代码中关于```background-size```属性需要这样设置。
2. 图片中心与可视区中心重合，所以 ```background-position: center```
3. 图片充满整个屏幕，需要多重考虑：
    （1）如果此时浏览器可视窗口的宽高 和图片宽高 比例是下面这种

    ![bgimg1.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/bg1.webp)

    为满足充满屏幕且不变形，图片应该宽度100% 高度自适应
    	```background-size:100% auto;```

    （2）如果此时浏览器可视窗口的宽高 和图片宽高 比例是另一种情况

    ![bgimg2.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/bg2.webp)

    为满足充满屏幕且不变形，图片应该高度100% 宽度自适应
    ```background-size:auto 100%;```
4. 监听浏览器窗口宽度变化，然后重新布局。
    所以给窗口对象添加一个resize 监听事件就ok了

    ```javascript
    window.addEventListener('resize',resizeListener())
    ```

5. 套一层蒙版，在里面直接写就行，记得加一个背景色和透明度，宽高和父元素相同完全覆盖就ok了。

### 代码：

``` html
<!-- test.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>背景图片问题</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        html,body{
            height: 100%;
        }
    </style>
</head>
<body>
    <div id="bgimg"></div>

    <script src="./bgimg.js"></script>
    <script>
        let MybgImg = new MiddleBg({
            id: 'bgimg',
            imgsrc: 'https://weiliicimg6.pstatp.com/weili/l/627990026226630794.webp'
        })

        MybgImg.bgRender();
    </script>
</body>

</html>
```

```javascript
// bgimg.js

function MiddleBg(obj){
    this.id = obj.id;
    this.imgsrc = obj.imgsrc;
    this.bgimgDOM = document.getElementById(this.id);
    this.bgimg = new Image();
    this.bgimg.src = this.imgsrc;
}

MiddleBg.prototype = {
    init: function(){
        let innbgshadow = document.createElement('div');
        innbgshadow.className = 'bgshadow';
        innbgshadow.innerHTML = 'Hello ~ ~ ~'
        this.bgimgDOM.appendChild(innbgshadow);

        this.bgimgDOM.className = 'bgimg';
        let styleEle = document.createElement('style');

        // es6 模板字符串，不了解的请自行查阅
        styleEle.innerHTML = `.${this.bgimgDOM.className},.${this.bgimgDOM.className} .bgshadow {
          height: 100%;
        }
        .${this.bgimgDOM.className}{
            background: url('${this.imgsrc}') no-repeat center;
        }
        .${this.bgimgDOM.className} .bgshadow {
          display: flex;
          align-items: center;
          justify-content: center;
          color: #fff;
          font-size: 5em;
          background: rgba(68, 68, 68, .4);
        }`;
        this.bgimgDOM.appendChild(styleEle);
    },
    resizeListener: function(){
        if (window.innerWidth / window.innerHeight >= this.bgimg.naturalWidth / this.bgimg.naturalHeight) {
            this.bgimgDOM.style.backgroundSize = '100% auto';
        } else {
            this.bgimgDOM.style.backgroundSize = 'auto 100%';
        }
    },
    bgRender: function(){
        // onload 确保图片资源已加载以获取图片原始大小，再进行箭头函数中的操作
        window.onload = ()=>{ // es6 箭头函数，不了解的请自行查阅
            this.init();
            this.resizeListener();
        }
        window.onresize = ()=>{ // 添加监听事件，在此处使用箭头函数，防止this指向window
            this.resizeListener();
        }
    }
}
```

我自己试验了一下，完全ok