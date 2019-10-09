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
3. 4. 图片充满整个屏幕，需要多重考虑：
（1）如果此时浏览器可视窗口的宽高 和图片宽高 比例是下面这种

![bgimg1.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/bg1.webp)

为满足充满屏幕且不变形，图片应该宽度100% 高度自适应
	```background-size:100% auto;```

（2）如果此时浏览器可视窗口的宽高 和图片宽高 比例是另一种情况

![bgimg2.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/bg2.webp)

为满足充满屏幕且不变形，图片应该高度100% 宽度自适应
```background-size:auto 100%;```

5. 套一层蒙版，在里面直接写就行，记得加一个背景色和透明度，宽高和父元素相同完全覆盖就ok了。

### 代码：

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>背景图片问题</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }
        html,body,.bgimg,.bgshadow{
            height: 100%;
        }

        .bgimg{
            background: url('https://images.unsplash.com/photo-1544247341-a0ab7d0955c5?ixlib=rb-1.2.1&q=99&fm=jpg&crop=entropy&cs=tinysrgb&w=2048&fit=max&ixid=eyJhcHBfaWQiOjcwOTV9') no-repeat center;
        }
        .bgshadow{
            display: flex;
            align-items: center;
            justify-content: center;
            color: #fff;
            font-size: 10em;
            background: rgba(68,68,68,.4);
        }
    </style>
</head>
<body>
    <div class="bgimg" id="bgimg">
        <div class="bgshadow">Hello ~ ~ ~</div>
    </div>
    <script>
        var bgimg = new Image();
        bgimg.src = 'https://images.unsplash.com/photo-1544247341-a0ab7d0955c5?ixlib=rb-1.2.1&q=99&fm=jpg&crop=entropy&cs=tinysrgb&w=2048&fit=max&ixid=eyJhcHBfaWQiOjcwOTV9';

        var imgInfo = {
            w: bgimg.naturalWidth,
            h: bgimg.naturalHeight
        }

        var bgimgDOM = document.getElementById('bgimg');

        window.onload = function(){
            window.addEventListener('resize',resizeListener())
            resizeListener();
        }

        function resizeListener(){
            var winfo = {
                w: window.innerWidth,
                h: window.innerHeight
            }

            if(winfo.w/winfo.h >= imgInfo.w/imgInfo.h){
                bgimg.style={backgroundSize: 'auto 100%'}
            }else{
                bgimg.style={backgroundSize: '100% auto'}
            }
        }

    </script>
</body>
</html>
```

我自己试验了一下，完全ok