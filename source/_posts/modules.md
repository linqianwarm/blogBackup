---
title: 关于一些模块化的东西
date: 2017-04-05 20:34:48
tags: 模块化
---

整理思路：[附有一些资料&链接的脑图][1]
（ps:一篇写了一半的文章）

<h2 id="1"> 一、CMD规范 </h2>

>   Common Module Define 公共模块声明
>   服务器端模块的规范
>   一个文件就是一个模块

<h3>至少有的特点： </h3>


*   1. 序列单独的模块
*   2. 模块内部定义的变量不会暴露给外面
*   3. 都是懒加载

<h3>模块定义： </h3>

```
define(factory);
```

1.define里接受一个参数，即factory模块
2.这个factory可以是一个函数或者其他的合法值
3.如果factory是一个函数，那么这个函数只要写了参数，其前三个参数必然是“require”、“exports”和“module”以及这个秩序
4.如果factory不是一个函数，那么这个模块输出的则是这个对象。

<!-- more -->
<h3>require function</h3>
require是一个函数

>* 1.其接受一个模块标识
>* 2.它返回的是所引模块输出的API
>* 3.如果所引模块不能正常返回，那么require会返回null

require.async是一个函数

>* 1.其接受一个列表的模块还有一个回调函数
>* 2.回调函数会将引入模块的输出作为参数使用，参数顺序依照引入列表顺序
>* 3.如果请求模块不能正常返回，那么回调也相应为NULL

<h2 id="2"> AMD规范 </h2>

(Async Module Define)异步模块声明

浏览器端

```
define(id?,dependencies?,factory)
```

<h2 id="2"> 前端模块化价值 </h2>

模块化：遵守commonjs规范

先是创建了公共工具，占用全局变量名，使后面自定义其他工具函数产生命名冲突。
解决办法是引入命名空间，问题是为了调用一个方法名字太长

解决了不同文件之间的分工和调用问题

<h2 id="2"> 组件化 </h2>

<span style="color:orange">“资源高内聚”</span>—— 组件资源内部高内聚，组件资源由自身加载控制
<span style="color:orange">“作用域独立”</span>—— 内部结构密封，不与全局或其他组件产生影响 
<span style="color:orange">“自定义标签”</span>—— 定义组件的使用方式
<span style="color:orange">“可相互组合”</span>—— 组件正在强大的地方，组件间组装整合
<span style="color:orange">“接口规范化”</span>—— 组件接口有统一规范，或者是生命周期的管理

componentWillReceiveProps() --- 已插入的组件收到新的props时调用


  [1]: https://www.processon.com/view/586cb8e2e4b067ce8543e0f4

