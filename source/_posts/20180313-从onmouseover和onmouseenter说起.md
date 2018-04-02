---
title: 从onmouseover和onmouseenter说起
tags:
  - JavaScript
comments: true
date: 2018-03-13 18:26:45
categories: 编程开发
---

初学JS时，`onmouseover` 和 ` onmouseenter ` 触发几乎一样，但在使用 `onmouseover` 和 `onmouseout` 时，发现如果在父级元素上定义事件，鼠标移动到其子元素上也会触发该事件，如下图所示，我在父div上设置`mouseover`，希望实现鼠标移入div时，其子元素p能显示，然后鼠标移走时，p再消失，类似某宝商品简介效果：

![onmouseover使用时的一个Bug](http://olvboulzy.bkt.clouddn.com/20180312-onmouseoverBug.gif?watermark/2/text/aHR0cDovL2Jpbmx2LnRvcA==/font/YXJpYWw=/fontsize/260/fill/I0VGRUZFRg==/dissolve/100/gravity/South/dx/10/dy/10)

可以发现当鼠标从div盒子下方移入时，子盒子p会出现鬼畜现象。。。而使用 `onmouseenter` 和 `mouseleave` 时不会出现此种现象。

另外，还有如下神奇的事件：

![](http://olvboulzy.bkt.clouddn.com/20180312-onmouseenter.gif)



![](http://olvboulzy.bkt.clouddn.com/20180312-onmouseover.gif)

<!--more-->

三个div盒子的层级关系为 红div (box1)> 黄div2(box2) > 绿div3 (box3)，当鼠标移入绿div时，使用 `mouseover `出现的是 box3 box2 box1 ，而使用 `mouseenter` 时出现的是box1 box2 box3。

上图的实现代码如下：

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>Document</title>
    <style type="text/css">
          *{
            margin:0;
            padding: 0;
          }
          #box1{
             position:relative;
             width:600px;
             height: 600px;
             background:red;
          }
          #box2{
             position: absolute;
             width:300px;
             height: 300px;
             background:yellow;
          }
          #box3{
             position:absolute;
             width:150px;
             height: 150px;
             background:green;
          }
    </style>
</head>
<body>
    <div id="box1">
         <div id=box2>
                <div id=box3></div>
         </div>
    </div>
    <script type="text/javascript">
         //获取元素
         var box1 = document.getElementById("box1");
         var box2 = document.getElementById("box2");
         var box3 = document.getElementById("box3");
         box1.onmouseover = function  () {
           console.log("box1");
         }
         box2.onmouseover= function  () {
           console.log("box2");
         }
         box3.onmouseover= function  () {
           console.log("box3");
         }
    </script>
</body>
</html>
```

> 注：jQuery中的`mouseover`和`mouseout`也有类似现象。



究其原因为 **事件冒泡（event bubbling）**。

# 事件流

JavaScript与HTML之间的交互是通过 **事件** 实现的。

> 事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间

而 事件流 就是页面接收事件的顺序。早期 IE 和 Netscape开发团队 提出了两种不同的（几乎相反） 事件流 概念 。IE的事件流是事件冒泡流，而 Netscape 的事件流是事件捕获流。

![DOM事件流](http://olvboulzy.bkt.clouddn.com/20180313-DOM%E4%BA%8B%E4%BB%B6%E6%B5%81.png?watermark/2/text/aHR0cDovL2Jpbmx2LnRvcA==/font/YXJpYWw=/fontsize/260/fill/I0VGRUZFRg==/dissolve/100/gravity/South/dx/10/dy/10)



开始时，`onmouseenter`为IE 独有，其他浏览器并不支持，后来各浏览器才陆续支持该事件。 

> Firefox started supporting `onmouseenter` in version 10 (January 2012) and Chrome started supported it in version 30 (October 2013) 



## 事件冒泡

每个元素都有其对应事件处理函数，未定义时事件处理函数一般为 `null` 。事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。

当某元素达到触发条件时，事件处理函数会从该元素的事件处理函数一直执行到最外层 (根) 的事件处理函数后才算结束（**从内到外**--现象类似冒泡）。

> 引用 MDN 上 关于 `mouseenter` 和 `mouseover` 的说明：
>
> ![MDN上关于mouserenter和mouseover的解释](http://olvboulzy.bkt.clouddn.com/20180312-MDN%E5%85%B3%E4%BA%8Emouseenter%E8%A7%A3%E9%87%8A.png)

值得注意的是，事件都属于异步语句，因此与内外层级定义事件处理函数顺序无关，但同一元素定义多个同一事件的事件处理函数，后者会覆盖前者。



## 事件捕获

事件捕获刚好与事件冒泡思想相反，事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。当某元素达到触发条件时，事件处理函数会从最外层 (根) 的事件处理函数一直执行到该元素的事件处理函数后才算结束（**从外到内**--现象类似捕获）。



## DOM 0级 和 DOM 2级

### DOM 0级

一般以on开头的事件以及jQuery中类似的那些事件都属于DOM 0级的事件，这些事件只有冒泡而无捕获，除`onmouerenter` 和 `onmouseleave` 外。

### DOM 2级

DOM 2级事件的定义一般都使用 `addEventListen(事件类型，callBack,Boolean)` ，其中 `Boolean` 为 `true` 时表捕获阶段，`false`时为冒泡阶段。



# 结论

通过以上知识也就能解释开始案例中的现象：

 `mouseover` 和 `mouseout` 在移动到其子元素上也会触发！

当鼠标首次移入到 `div` 的下方时触发了`div` 的`mouseover` 事件 ，此时`p`标签重叠在 `div` 上，此时 鼠标在 `p`元素上，触发了 `div` 的`mouseover` 事件，当 `p` 元素 离开 ，鼠标回到 `div` 上，再次触发 `div` 的 `mouseover` 事件，如此循环。



至于第二个例子的现象，我个人觉得是 `mouseenter` 遵循的是 **事件捕获**，而  `mouseover ` 遵循的是 **事件冒泡**。



本文完。