---
title: 前端之瀑布流布局（多种实现方案）
category: FrontEnd
---

## {{ page.title }}

> 本文字数稍长，思路清晰，耐心读下来绝对让你掌握瀑布流布局，大家加油！

瀑布流布局一般是下面这个样子

PC：

![PC端](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/pc.webp)

移动端：

![image.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/mobile.webp)


> Tips：本文会介绍三种实现方案，推荐大家仔细看看第一种

### 方案一： js实现，css略微辅助

#### 目标要求：

1. 实现瀑布流
2. PC和移动端都要适配
3. 图片不可变形，宽度固定
4. 瀑布流的列数不固定，随屏幕宽度的变化而变化
5. 若屏幕宽度有富余，则图片瀑布流区域居中展示。

#### 思路分析

1. 实现瀑布流思路如下：
    （1）根据图片宽度，计算当前屏幕一共有几列。
    举例：当前屏幕宽度是820px，每一个包裹着图片的盒子宽度是200px，所以一共可以排820/200 = 4 列（向下取整）
    （2）先按顺序排第一列，记录下每一列当前占据的高度值。
    举例：假设当前屏幕宽度为820px,每一个包裹着图片的盒子宽度是200px,可以排4列，现在有10个图片高度分别是```[120,55,800,380,77,20,130,600,453,780]``` （单位```px```），先排前4个图片，排完之后这4列高度依次是```[120,55,800,380]```，把这4列当前高度值存起来。
    （3）第一列排完之后开始排后面的元素，要找出所有列数中高度最小的那一个，排在它的下面，高度值累加，以此类推。

    举例：接（2）中的例子，现在这4列高度依次是```[120,55,800,380]```，可以看到第2列高度最小，所以第5个图片排在第2列，紧挨着第2列的第1个图片下。然后第2列的高度变成了 55+77 = 132 ，当前这4列的高度变成了```[120,132,800,380]```。剩下的图片也按照这个逻辑去排就ok了。

2. PC和移动端适配在html中配置如下：

    ```html
    <meta name='viewport' content='width=device-width,initial-scale=1.0,user-scalable=no' />
    ```

3. 图片不变形，宽度固定
    ```html```中的 ```img```不是```块级元素```，它属于```行内替换元素```，虽然```width```、```height```等属性仍然有效，但非块状元素在用css或js修饰时会遇到很多不可预测的问题。基于此，我习惯给每一个```img```用一个```div```包裹住，以后直接操作外层```div```，如下

    ```html
    <div style="width:200px;height:auto">
      <img src="xxxxx" alt='xxxxx' style="width:100%;height:auto"/>
    </div>
    ```

4. 第4个要求其实是要监听浏览器窗口宽度变化，然后重新布局。
    所以给窗口对象添加一个resize 监听事件就ok了

    ```javascript
    window.addEventListener('resize',()=>{
        // 重新布局
    })
    ```
5. 图片区域居中显示
    如果针对单个图片去计算位置以使整个区域居中显示，这种方法计算量大，得不偿失。所以将所有图片包在一个div里，使最外层div居中就ok了。

#### 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>瀑布流布局</title>
    <meta name='viewport' content='width=device-width,initial-scale=1.0,user-scalable=no' />
    <style>
        html{
            font-size: 12px;
        }
        *{
            margin: 0;
            padding: 0;
        }
    </style>
</head>
<body>
    <div class="wrap">
        <div id='forcenter'></div>
    </div>
    <div class="wrap">
        <div id='ant'></div>
    </div>
    <script src="./waterfall.js"></script>
    <script>
        let mwf = new WaterFall({
            id: 'forcenter',
            imgUrls: [
                'https://icweiliimg6.pstatp.com/weili/l/195564498297815041.webp',
                'https://weiliicimg9.pstatp.com/weili/l/461972764095807490.webp',
                'https://icweiliimg9.pstatp.com/weili/l/447251256884854785.webp',
                'https://icweiliimg6.pstatp.com/weili/l/165755629970653210.webp',
                'https://weiliicimg6.pstatp.com/weili/l/218593064967209016.webp',
                'https://icweiliimg6.pstatp.com/weili/l/320841925450530819.webp',
                'https://icweiliimg1.pstatp.com/weili/l/165755655736786957.webp',
                'https://icweiliimg1.pstatp.com/weili/l/452540045247774720.webp',
                'https://weiliicimg9.pstatp.com/weili/l/223888416044482620.webp',
                'https://icweiliimg9.pstatp.com/weili/l/292051943627161624.webp',
                'https://icweiliimg1.pstatp.com/weili/l/235158650747486283.webp',
                'https://weiliicimg1.pstatp.com/weili/l/165755681505542155.webp',
                'https://icweiliimg1.pstatp.com/weili/l/202821919289376781.webp',
                'https://weiliicimg9.pstatp.com/weili/l/611415730252415145.webp'
            ]
        });

        let twf = new WaterFall({
            id: 'ant',
            imgUrls: [
                'https://icweiliimg6.pstatp.com/weili/l/195564498297815041.webp',
                'https://weiliicimg9.pstatp.com/weili/l/461972764095807490.webp',
                'https://icweiliimg9.pstatp.com/weili/l/447251256884854785.webp',
                'https://icweiliimg6.pstatp.com/weili/l/165755629970653210.webp',
                'https://weiliicimg6.pstatp.com/weili/l/218593064967209016.webp',
                'https://icweiliimg6.pstatp.com/weili/l/320841925450530819.webp',
                'https://icweiliimg1.pstatp.com/weili/l/165755655736786957.webp',
                'https://icweiliimg1.pstatp.com/weili/l/452540045247774720.webp',
                'https://weiliicimg9.pstatp.com/weili/l/223888416044482620.webp',
                'https://icweiliimg9.pstatp.com/weili/l/292051943627161624.webp',
                'https://icweiliimg1.pstatp.com/weili/l/235158650747486283.webp',
                'https://weiliicimg1.pstatp.com/weili/l/165755681505542155.webp',
                'https://icweiliimg1.pstatp.com/weili/l/202821919289376781.webp',
                'https://weiliicimg9.pstatp.com/weili/l/611415730252415145.webp'
            ]
        });

        window.onload = ()=>{
            mwf.WFRender();
            twf.WFRender();
        }

        window.onresize = ()=>{
            mwf.resizeWF();
            twf.resizeWF();
        }
    </script>
</body>
</html>
```

```javascript
// waterfall.js

function WaterFall(obj){
    this.id = obj.id;
    this.imgUrls = obj.imgUrls;
    this.container = document.getElementById(this.id);
    this.container.className = 'forcenter';
}

WaterFall.prototype = {
    WFRender: function(){
        let flag = 0;   // 标志位，用于检测每一张图片都加载完毕

    this.imgUrls.map((item,i)=>{
      let div = document.createElement('div');
      div.className = 'item';

      let img = new Image();
      img.src = item;
      img.onload = () => {
        flag++;
        div.appendChild(img);
        this.container.appendChild(div);

                // 全部图片加载完毕后再开始布局，否则获取不到元素的最终高度。
        if(flag===this.imgUrls.length){
          this.resizeWF();
        }
      }
        });

    let styleE = document.createElement('style');
    styleE.innerHTML = `div.item{
        position: absolute;
        transition: all .5s;  /* 使过渡平滑*/
        width : 200px;
        height: auto;
        padding:5px;
        box-sizing: border-box;  /* 非常重要，可将不必要的计算略去 */
      }
      div.item img{
        width: 100%;
        height: auto;
      }
      .forcenter{
        position: relative;
        margin: auto;
      }`;
     this.container.appendChild(styleE);
    },
    resizeWF: function(){
        let winW = window.innerWidth;
    let itemNum = Math.floor(winW/200); // 当每一个项的宽度都是固定的时候，需要计算出浏览器一行可以排列几个。
    this.container.style.width = (itemNum*200)+'px'; // 用于居中的包裹盒子的宽度

    let saveColumnHeight = [];  // 定义一个数组，用于存储 每一列所有元素的高度 之和
    let items = document.querySelectorAll('.item');

    for(let i=0; i< items.length; i++){
        if(saveColumnHeight.length<itemNum){
            saveColumnHeight[i] = items[i].offsetHeight;// 当布局的元素还没占满一行时，继续向数组中添加第一行第i列的高度
            setDiv(items[i],200*i,0);    // 放置div
        }else{  // 当已经占满一行时，就找出每一列的最小高度，然后当前的这个div放在高度最小的那一列
            let pos = getMinH(saveColumnHeight);    //去找高度最小的那一列
            saveColumnHeight[pos.column] += items[i].offsetHeight;
            setDiv(items[i],pos.left,pos.top);    // 放置div
        }
    }
    }
}

// 工具性函数，不必放入原型链。
function getMinH(arr){
  let flag = {
      left:0,
      top:0,
      column:0
  }

  arr.map((item,i)=>{
      if(flag.top===0){
          flag.top = item;
      }else{
          if(flag.top>item){  // 找出高度最小的那一列
              flag.top = item;
              flag.left = 200*i;
              flag.column = i;
          }
      }
  });

  return flag;
}

function setDiv(item,left,top){
  item.style.left = left+'px';
  item.style.top = top+'px';
}
```
#### Tips

1. 在布局之前，确保所有图片已加载完毕，否则无法获取最终高度导致布局出错。
2. css中有一个属性```box-sizing```，它的作用是将元素的```border```和```padding```都计算在元素尺寸之内（详情请自己查阅），这么设置有一个好处：平常我们布局为了避免图片紧挨着不好看经常加一些```padding```或```margin```，因此计算过程就复杂了。box-sizing属性可以将padding包含进图片尺寸，通过padding使图片避免紧挨着，在计算上只考虑最外层尺寸就ok了，省了很多事。

### 方案二 纯css
#### 目标要求
1. 实现瀑布流样式
2. PC和移动端都要适配
3. 图片不变形
4. 瀑布流列数自适应

#### 思路分析
css中有这么两个属性:

- ```column-count``` 将元素划分的列数，若为auto，则自适应
- ```column-gap``` 元素之间的间隙
- ```column-width``` 每一列的宽度

使用这三个属性基本可以实现啦！

#### 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>瀑布流布局</title>
    <meta name='viewport' content='width=device-width,initial-scale=1.0,user-scalable=no' />
    <style>
        html{
            font-size: 12px;
        }
        *{
            margin: 0;
            padding: 0;
        }
        div.item{
            transition: all .5s;
            /*width : 200px;*/
            height: auto;
            padding:5px;
            box-sizing: border-box;
        }
        .wrap{
            column-width: 200px;
            column-count: auto;
            column-gap: 1rem;
        }
    </style>
</head>
<body>
<div class="wrap" id='wrap'>

</div>
<script>
    var imgUrls = [
        'https://icweiliimg1.pstatp.com/weili/l/452540045247774720.webp',
        'https://weiliicimg9.pstatp.com/weili/l/223888416044482620.webp',
        'https://icweiliimg6.pstatp.com/weili/l/320841925450530819.webp',
        'https://icweiliimg6.pstatp.com/weili/l/195564498297815041.webp',
        'https://weiliicimg9.pstatp.com/weili/l/461972764095807490.webp',
        'https://icweiliimg1.pstatp.com/weili/l/165755655736786957.webp',
        'https://icweiliimg9.pstatp.com/weili/l/447251256884854785.webp',
        'https://icweiliimg6.pstatp.com/weili/l/165755629970653210.webp',
        'https://weiliicimg6.pstatp.com/weili/l/218593064967209016.webp',
        'https://icweiliimg9.pstatp.com/weili/l/292051943627161624.webp',
        'https://icweiliimg1.pstatp.com/weili/l/235158650747486283.webp',
        'https://weiliicimg1.pstatp.com/weili/l/165755681505542155.webp',
        'https://icweiliimg1.pstatp.com/weili/l/202821919289376781.webp',
        'https://weiliicimg9.pstatp.com/weili/l/611415730252415145.webp'
    ];

    window.onload = function(){
        let container = document.getElementById('wrap');

        imgUrls.map((item,i)=>{
            let div = document.createElement('div');
            div.className = 'item';

            let img = document.createElement('img');
            img.src = item;
            img.style.width = '100%';
            img.style.height = 'auto';

            div.appendChild(img);
            container.appendChild(div);
        });
    }

</script>
</body>
</html>
```

**注意**
> 其实这样弄出来的根本不是严格意义上的瀑布流布局，只不过有一个瀑布流布局的样子而已，因为对高度的判断不如js计算来的严格，所以会出现一些不容易被注意到的问题（我也是测试了好久才找到），如下图

![image.png](https://raw.githubusercontent.com/LilyLaw/LilyLaw.github.io/master/img/error.webp)

**这也是我推荐大家用第一种方法的最主要原因。**

### 方案三 flex布局

这种方案做出来的效果有点奇葩，特殊的应用场景可能会用到，最终效果是横向瀑布流
flex布局中有一个属性```flex-flow```,指明在哪个方向上在长度不够的情况想拆行，详情大家自己查阅

#### 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>瀑布流布局</title>
    <meta name='viewport' content='width=device-width,initial-scale=1.0,user-scalable=no' />
    <style>
        html{
            font-size: 12px;
        }
        *{
            margin: 0;
            padding: 0;
        }
        div.item{
            transition: all .5s;
            width : 200px;
            height: auto;
            padding:5px;
            box-sizing: border-box;
        }
        .wrap{
            display: flex;
            flex-flow: column wrap;
            height: 400px;
        }
    </style>
</head>
<body>
<div class="wrap" id='wrap'>

</div>
<script>
    var imgUrls = [
        'https://icweiliimg1.pstatp.com/weili/l/452540045247774720.webp',
        'https://weiliicimg9.pstatp.com/weili/l/223888416044482620.webp',
        'https://icweiliimg6.pstatp.com/weili/l/320841925450530819.webp',
        'https://icweiliimg6.pstatp.com/weili/l/195564498297815041.webp',
        'https://weiliicimg9.pstatp.com/weili/l/461972764095807490.webp',
        'https://icweiliimg1.pstatp.com/weili/l/165755655736786957.webp',
        'https://icweiliimg9.pstatp.com/weili/l/447251256884854785.webp',
        'https://icweiliimg6.pstatp.com/weili/l/165755629970653210.webp',
        'https://weiliicimg6.pstatp.com/weili/l/218593064967209016.webp',
        'https://icweiliimg9.pstatp.com/weili/l/292051943627161624.webp',
        'https://icweiliimg1.pstatp.com/weili/l/235158650747486283.webp',
        'https://weiliicimg1.pstatp.com/weili/l/165755681505542155.webp',
        'https://icweiliimg1.pstatp.com/weili/l/202821919289376781.webp',
        'https://weiliicimg9.pstatp.com/weili/l/611415730252415145.webp'
    ];

    window.onload = function(){
        let container = document.getElementById('wrap');

        imgUrls.map((item,i)=>{
            let div = document.createElement('div');
            div.className = 'item';

            let img = document.createElement('img');
            img.src = item;
            img.style.width = '100%';
            img.style.height = 'auto';

            div.appendChild(img);
            container.appendChild(div);
        });
    }

</script>
</body>
</html>
```

### 总结

第一种方法其实是最合适的，也是对开发人员要求最高的，即使有思路与算法在开发过程中也会踩很多坑，比如（1）图片预加载，（2）querySelector取元素居然获取不到，（3）使用box-sizing简化计算过程降低复杂性，等等。**box-sizing 属性用好了非常高效，推荐大家去仔细看看**

第二种方法其实也可行，但是不推荐
第三种方法就有点扯了，估计不常用到，大家看看就行