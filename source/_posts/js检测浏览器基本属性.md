---
title: js检测浏览器基本属性
date: 2016-11-03 18:59:19
tags: 浏览器兼容
---

这是一篇用来讲解如何玩浏览器的各项属于以及这些基本属性助力于我们兼容性的文章

<h2>detector</h2>

    利用这个工具我们大致可以知道当前使用端的浏览器基本信息，和设备基本情况，即浏览器名字版本内核等、设备名字操作系统版本等。
    
    
<h2>modernizr</h2>

想安利一下这个工具，特别好用。它是用来检测一些h5 or CSS3的属性支持情况的，在官网可以自定义选择需要检测的属性，使用方法也很简单，最基础的使用就是，引用文件后，直接在脚本里：

<!-- more -->
    
```
    modernizr.attr
    <!--返回一个true/false-->
```

即可判断浏览器是否支持其属性。同时这个文件引用后在html标签里会生成一系列的class例如“js flash no-canvas”的字段，其表示该环境支持js flash，但不支持canvas，可以利用这个class在写stylesheet的时候给不支持的标签写上对应的样式，例如

```javascript

    .no-canvas{
        display:none;
    }
```


<h2>浏览器缩放判断</h2>

思路：（分三类）

*   1. chrome类，即webkit/blink内核。
*  2. IE类
*  3. 其他类型浏览器

码上看


```javascript
//页面的缩放情况判断
function scaleJudge(){
    var scale = 0,
        Screen = window.screen,
        ua = navigator.userAgent.toLowerCase();
    //chrome、blink 
    if (!!window.devicePixlRadio) {
        scale = window.devicePixlRadio;
    //IE下，IE11木有msie 
    }else if (ua.indexOf('msie')>-1 || ua.indexOf("rv:11")>-1 ) {
        if (Screen.deviceXDPI && Screen.logicalXDPI) {
            scale = Screen.deviceXDPI/Screen.logicalXDPI;
        };
    //其他的一些 
    }else if (!!window.outerWidth && !!window.innerWidth){
        scale = window.outerWidth/window.innerWidth;
    }
    if (scale) {
        scale = Math.round(scale * 100);
    };
    // 某些诡异时的outerWidth和innerWidth不相等(比如火狐)
    if( scale === 99 || scale === 101 ){
        scale = 100;
    }
    
}
```


<h2>判断js可用</h2>

way 1:
    在dom中添加js不可用的提示信息，再在脚本里写因此这一节点。
    js正常加载时,是会顺利隐藏它的。否则会出现不可用的提示信息。
    
弊端：正常加载情况下页面会闪一下。
    
way 2（better）:

在dom中直接添加如下信息：

```html
    <script type="text/javascript">
        haosou.docWrite('<!--');
    </script>
    ……只有js不能用啦，我才会展示出来
    <!-- 不要删我 --!>
```

"haosou.docWrite"是在head区域的一个外链js中，重写的document.write的方法，正常加载时候效果相当于把页面里的快给注释掉了。只有当js不可用时后面的内容才会出现。
这样做的好处是页面在渲染的时候就会控制好显隐，减少回流（不会出现闪一下的情况）。


<h2>判断flash可用</h2>

直接上码

```JavaScript
// 是否支持flash
function flashJudge(){

    var flag = false;
    //ie下
    if(window.ActiveXObject){
        try{
          var swf = new ActiveXObject("ShockwaveFlash.ShockwaveFlash");
          if(swf){
            flag = true;
            var flashVersion = swf.GetVariable("$version").split(' ')[1];
            // console.log(flashVersion);
          }
        }catch(e){
        }
    //非IE
    }else{
        try{
          var swf = navigator.plugins['Shockwave Flash'];
          if(swf){
            flag = true;
            var flashVersion = swf.description.split(' ')[2];
          }
        }catch(e){
        }
    }
    // console.log(flashVersion);
}
```

