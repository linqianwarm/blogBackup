---
title: css中有意思的黑魔法
date: 2016-09-12 20:10:30
tags: [CSS,DarkMagic]
---


* [1.Dirty Tricks From The Dark Corners Of Front-End](#1)

    * [1.1 table hover十字架特效](#1.1)

    * [1.2 图片的适应与破碎](#1.2)

    * [1.3 ":empty"的使用](#1.3)
    
    * [1.4 响应式布局](#1.4)
        * [1.4.1 px、em、rem](#1.4.1)
        * [1.4.2 vh&vw / vmin&vmax](#1.4.2) 
        * [1.4.3 calc()](#1.4.3)
        

* [2.日常遇到的一些点](#2)
    * [2.1 浏览器缩放引发的问题](#2.1)
    
    * [2.2 文字超出点点点效果实现](#2.2)
    
    * [2.3 ie7 relative穿透](#2.3)

    * [2.4 推荐实用小工具](#2.4)
    
<h3 id="1">一、 Dirty Tricks From The Dark Corners Of Front-End(分享后感想) </h3>

原演讲PPT地址[dirty-tricks-from-the-dark-corners-of-front-end][1]
    
    本章只选取了部分作分析分享，有兴趣的童鞋可以自己去看看全部哒。

<!--more-->
    
<h4 id="1.1">1.table hover十字架特效</h4>

![此处输入图片的描述][2]

想象一下，在我们工作中如果遇到需要这种情况，可能第一反应都是：横栏的效果是直接在tr上加特效“tr:hover”就行了，可是竖栏的效果呢？像我这样对css玩得不太溜的话可能第一反应是利用js控制选择元素，然后对应那一列的td都给加上背景。

当然在这里我们不用那种方式实现，采用css的伪元素实现。思路如下：

> ① 在hover的对应td后添加一个伪元素
> ② 将它的宽设置和td一样
> ③ 将这个伪元素的高度可以设大（表格高度不确定的情况），添加背景颜色
> ④ 在table上设置overflow：hidden就OK啦


好啦，示例放上codepen链接：[table hover十字架特效][3]

注：css3的gradient也能实现
相关学习文档： [css3-gradient][4]

---

<h4 id="1.2">2.图片的适应与破碎</h4>

    对于图片显示，存在的问题的也比较多，比如尺寸大小适应问题，或者是图片失效了，哦多kei
> (1)对于尺寸自动适应问题

    需求：我们已经创建和了一个完美的格子（或者正方形圆形之类的），场景就是我们的头像，但是当客户上传的图片尺寸不正确的时候，图片会遭受一定的变形。我们应该想些什么办法来处理这个问题呢？
    
    将图片变成一个正方形，我们可以加上一些基本样式
```css
img{
    width:100px;
    height:100px;
    border:1px solid;
}
```
但是这样做的话，图片的尺寸不合可能导致效果图显示不全或变形，我们能联想到在background背景图可以设置cover什么的各样，那么对于img标签的话，我们可以使用css3的一个新属性 **object-fit** （虽然宝宝并没用过，但是看到之后觉得很不错呀）

> **object-fit:**
> fill    想尽办法让自己将充满整个容器（此值为boject-fit的默认值）
> contain  保持长宽比例填充元素内容容器
> cover 保持长宽比例填充元素内容容器，就是一定要让容器变满
> none 替换元素内容不调整大小以适应内部元素的容器，内容完全忽略设置在元素上的任何高度和权重，并且仍在元素尺寸内显示。
> scalce-down 当内容大小设置了non或contain，将导致具体对象变得更小。

ps:此属性主要适用于替换元素（不限于img），也就是说还有video、object、input、svg、svg:image和svg:video等。
object-fit与object-position一起使用更加666，后者属性决定了这个盒子里替换元素的对齐方式。可以在[《CSS3 Object-fit和Object-position》][5]一文中了解更多。

---  
> (2)图片加载不成功的时候，我们应该都会看到以下图片：

![此处输入图片的描述][6]
    
这样比较影响页面的美观，我们有其他什么方式可以设置这个加载失败的样式么？同样又通过伪元素。那么问题来了：img是 **可替换元素**，是不能应用伪元素呀？
通过黑魔法的学习发现虽然img正常加载图片的时候不能应用伪元素，但是在图片加载失败的情况下，是可以使用的。
    
见小示例： [破碎图片的处理][7]
    
    
---

<h4 id="1.3">3.":empty"的使用</h4>

    大家对alert一定不陌生，尤其是我们想要给它自定义样式，变得特别一点。这个时候的alert一般都不是系统的alert了，而是我们自己写的一个模板，一般通过display：none/block 来控制其显示与否，可是当这个alert模板里面没东西的时候，我们怎么能保证它确实是hidden的呢？
    empty可以帮我们解决这个问题，在节点上加上“:empty”，自定义其属性就可以达到在节点内没内容的时候将此模板隐掉。
```css
.alert{
  background-color: beige;
  border: 2px solid rgb(150,150,150);
  border-radius: 5px;
  padding: 5px 10px;
  display: inline-block;
  width:200px;
  height:200px;
}

.alert:empty {
  display: none
}
```

小示例： [:empty控制alert的小示例][8]

---

<h4 id="1.4">4.响应式布局</h4>

<h5 id="1.4.1"> （1）px、em、rem</h5>

首先我要介绍一下rem这个单位，在此之前，提一下常用的两个单位： PX & EM

    px像素（Pixel）。相对长度单位。像素px是相对于显示器屏幕分辨率而言的。(引自CSS2.0手册)
    em是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。(引自CSS2.0手册)
    
任意浏览器的默认字体高都是16px。所有未经调整的浏览器都符合: 1em=16px。为了简化font-size的换算，我们在css中的body选择器中声明Font-size=62.5%，这就使em值变为 16px*62.5%=10px, 这样 10px=1em, 也就是说我们只需要将原来的px数值除以10，然后换上em作为单位就行了。所以我们在css里***任何关于px***单位的书写都可以换成em了。

    在这里特别注明，因为em会继承父级元素的字体大小，所以要小心字体大小的重复定义，因为这个em会不断计算下去。
    
解决了以上确定，又有一个**REM**的单位粗线了，它也是一个相对单位（root em），相对于根EM的单位，它就使我们在嵌套里设定字体大小时，仍然是相对大小，相对html根元素大小，我们可以只修改根元素的设定，就能成比例地调整整个页面。
此属性支持所有浏览器，除了IE8及更早的。

<h5 id="1.4.2"> （2）vh&vw / vmin&vmax </h5>
    
    这几个都是长度单位，都和视口有关。
    
>*  vh: 视口高度。 1vh = 当前浏览窗口高度/100 
>*  vw: 视口宽度。 1vw = 当前浏览窗口宽度/100
>*  vmin,vmax及取浏览窗口长宽的小值和大值的百分之一
 *  eg1：浏览窗口height=1600px,width=800px,则1vmin=8px 1vmax= 16px
 *  eg2：浏览窗口height=990px,width=1400px,则1vmin=9.9px 1vmax=14px
 
 还有两个单位。 ex and ch?

<h5 id="1.4.3"> （3）calc() </h5>
    说到响应式布局，还有一个很重要的属性，看起来很像函数的css属性--calc()。
    
先回忆一下平时我们写的盒子，为自适应大小，有时会设宽度为100%，但是这个盒子也蛮脆弱的，如果我们需要加个border、margin什么的，就很容易把它给撑破了。[示例][9]

cacl()可以动态计算值，给元素设定长度，让我们来看看它的特点：
>* 1 calc(expression)  //我们可以在其中进行加减乘除各种运算，但是运算符前后须有空格
>* 2 运算时可以多种单位混用，我们前面才讲了的px、em、rem在这里可以一起用在一个运算表达式里。 

至于它的兼容性，当然是很友好的。除了IE8及低版本，Opera mini，其他的均已支持。安卓浏览器4.4以后部分支持，5之后均已支持。

<h3 id="2">二、日常遇到的一些点</h3>

<h4 id="2.1">1.浏览器缩放引发的问题</h4>

    一般在还原设计稿的时候，我们都会按照原稿的像素大小一比一还原，切出来的页码在测试了各个浏览器兼容调整之后似乎也没有什么问题了。但是某一天，近视的倩倩因为某网页字太小，按了command+将浏览器缩放比例调大了，然后也忘了将这个缩放比例调回来，看了会儿东西就开始工作了。然后某瞬间突然发现之前工作代码切的页面出现了怪异情况，有一些样式错乱发生，第一反应是修改的代码在哪写错了导致的（当然本宝宝最开始没有反应过来是因为啥），当我正在困惑为毛div的宽度变了，写的明明是206px，出来的实际宽度却是个205.81px时，把浏览器缩放变成100%，发现问题都没有了。🙄
    在学习这个问题的时候，我要引入几个概念：
    
>（1）  屏幕分辨率
>（2）  设备像素
>（3）  CSS像素

    屏幕分辨率和设备像素是物理概念，而CSS像素是我们编程的时候所用的概念。
    *屏幕分辨率和设备像素*的差异在于设备像素显示密度（density px）,这个密度值为1的时候，这两者互等。
    $$ dp=(dpi/160ppi) $$  dpi标准为160
    
    当浏览器不进行缩放时，$ css像素 = 实际像素 $
    但是当浏览器进行缩放时，由PX写的样式错乱，这里有一些解决方式：
    （1）禁止用户缩放页面，在meta里设置user-scalcable=0。（嗯，感觉酱紫不太好，像我这种近视就想看大字呢？）
    （2）少用px控制，多用响应式布局，给min-width,max-width之类，还有上文提过的，采用em、rem单位，并且用calc()自适应，酱紫就能解决这些问题。

<h4 id="2.2">2.文字超出点点点效果实现</h4>

    三个属性实现：(注意给这个框一个宽度or最大宽度)
    
```
    white-space: nowrap;
    overflow:hidden;
    text-overflow:ellipsis
```

white-space  选项有
normal：默认。空白会被浏览器忽略。
pre：空白会被浏览器保留。其行为方式类似 HTML 中的 pre标签。
nowrap:文本不会换行，文本会在在同一行上继续，直到遇到 br标签为止。
pre-wrap:保留空白符序列，但是正常地进行换行。
pre-line:合并空白符序列，但是保留换行符。
inherit:规定应该从父元素继承 white-space 属性的值。

text-overflow  选项有
text-overflow: clip|ellipsis|string;

<h4 id="2.3">3.ie7 relative穿透</h4>

问题描述：
在IE7浏览器中，发现一个容器里的某下拉框的z-index已经被设置到全文最大了，但仍被接下来的div给遮住了，哦多kei？

产生原因：
在IE浏览器中，定位元素会产生一个新的[stacking context][10],并且从z-index的值为0开始，也就是说后面定位的节点（即给过属性值position：absolute/relative）会盖住前面的。

实例：

```html
/!-- html --/
<div class="box1" style="position:relative">
    <ul style="z-index:99999" class="selectlist">  //'假装炒鸡高了，应该在最外层'
        <li>其实我是模拟一个下拉框里面的一个元素</li>
    </ul>
</div>

<div class="box2" style="position:relative">
    <div style="position:absolute;z-index:100" class="innerbox"></div> //虽然z-index值很小，但是是在最上面
</div>
```
以上代码，会优先解释有定位元素的外层元素，selectlist的z-index是相对于它的父级，同理innerbox也是针对于它的父级，这两个index根本木有比较性。

解决方法：
我们给需要比较的父级加上z-index，若这个值是box1>box2，那么box1的层级始终是在box2上的。

<h4 id="2.4">4.推荐实用小工具</h4>

[雪碧图在线生成][11]


  [1]: https://speakerdeck.com/smashingmag/dirty-tricks-from-the-dark-corners-of-front-end
  [2]: http://p0.qhimg.com/t0196c5fa25b592ffbc.jpg
  [3]: http://codepen.io/gtjlq/pen/dpbXoq
  [4]: http://www.w3cplus.com/content/css3-gradient
  [5]: http://www.w3cplus.com/css3/css3-object-fit-and-object-position-properties.html
  [6]: http://p6.qhimg.com/t011cfadace23d9a6f3.png
  [7]: http://codepen.io/gtjlq/pen/XjAjyv
  [8]: http://codepen.io/gtjlq/pen/mAbAyV
  [9]: http://codepen.io/gtjlq/pen/rrBKva
  [10]: https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context
  [11]: http://spritegen.website-performance.org/

