---
title: 我们用过的packageJson
date: 2016-08-27 13:18:57
tags: Node
---
<span id="jump"></span>



参考文章：[npm模块管理器][1]、[package.json文件][2]、[why npm scripts][3]、[node.js命令行程序开发教程][4]

* [1.package.json是？](#1)

    * [1.1概述](#1.1)

    * [1.2创建使用](#1.2)

    * [1.3常用的](#1.3)

* [2.被忽略的一些点](#2)

    * [2.1 构建项目的scripts](#2.1)
    
    * [2.2 bin](#2.2)

* [3.一些其他小常识补充](#3)

    * [3.1 gulp&grunt…](#3.1)
    
    * [3.2 常见盲区](#3.2)


<h2 id="1">1 package.json是？</h2>

>package.json对很多前端童鞋来讲都不陌生，在各种项目构建管理潮流的大军中，这个就是赶潮流的入口文件，然而它是什么呢？接下来我将从我理解的角度来介绍它。

<!--more-->

<h3 id="1.1">1.1 概述</h3>

从某个时间开始nodejs开始🔥，我们便发现每个项目的根目录或者前端开发文件的根目录都离不开package.json这个文件了，因为有它我们可以在项目里npm install。
这个json文件定义了我们这个项目所需要的各种模块以及项目的配置信息。而我以上说的npm install就是按照这个信息来进行的一系列配置安装。

**npm**有两层含义。一层含义是Node的开放式模块登记和管理系统，网址为[npmjs.org][5]。另一层含义是Node默认的模块管理器，是一个命令行下的软件，用来安装和管理Node模块。

**npm**不需要单独安装。在安装Node的时候，会连带一起安装npm。

<h3 id="1.2">1.2 创建使用</h3>
使用npm init按照提示的一步步键入就可以创建一个最基本的package.json文件了。当然我们也可以手动创建文件，写入相应的东西。
![npm install][6]

<h3 id="1.1">1.3 常用的</h3>

> 接下来我们要往我们的项目里添加依赖包了，很明显“devDependencies”和“dependencies”这两个对象里面是存放了一些包名的，这些包就是我们在npm install时下载的资源，它们名字比较像，记住前面有**dev**的是我们在开发过程中需要的包，相当于工具，发布项目的时候是不需要发布它们的，而后者则是项目运行所需要的。包名后面跟着版本号这里就不详细说明了。

```json
{
  "name": "npmPackage",
  "version": "1.0.0",
  "description": "倩倩的npm模块管理器练习",
  "main": "",
  "author": "linqian",
  "license": "",
  "devDependencies": {
    "graceful-fs": "^4.1.2",
    "live-server": "^1.1.0",
    "npm-run-all": "^3.0.0",
    "webpack": "^1.12.0"
  },
  "dependencies": {
    "JSON2": "^0.1.0",
    "augment": "^4.3.0",
    "css-loader": "^0.17.0",
    "ejs-loader": "^0.2.1",
    "es5-shim": "^4.4.1",
    "expose-loader": "^0.7.0",
    "jquery": "^1.11.3",
    "shelljs": "^0.7.3",
    "style-loader": "^0.12.3",
    "underscore": "^1.8.3"
  }
}

```

<h2 id="2">2 那些被我们忽略的</h2>
> 查看我们的项目，发现在package.json里大多只利用了依赖包，而其他很多字段都没有用上，比如"scripts"、"bin"、"config"、"style"等，这里我参照了[package.json文件][7]学习，我觉得并不是只有需要的时候才去学习，而是首先了解这一套，在用到它的时候还会有更多花样和解决思路。


<h3 id="2.1">2.1 构建项目的scripts</h3>

<h4>2.1.1 使用</h4>

scripts指定了运行脚本命令的npm命令行缩写，运行使用npm run 名字，比如在如下的设置里，分别使用npm run test/npm run serve/npm run startRepl来执行相应的指令。
```
"scripts": {
  "test": "node ./src/testEcho.js",
  "serve": "live-server dist/ --port=9090",
  "startRepl": "node repl.js"
}
```

npm run命令会自动在环境变量$PATH添加node_modules/.bin目录，所以scripts字段里面调用命令时不用加上路径，这就避免了全局安装NPM模块。

现在我们在执行了以下安装命令后（或者是安装了devDependencies里的包后）
```
$ npm install eslint --save-dev
```

```
  "devDependencies": {
    "eslint": "^1.10.3"
  },
  "scripts": {
    "lint": "eslint ."
  }
```
则可在scripts属性里的命令中不带路径地引用eslint这个脚本。

---

  scripts属性中的命令可以是:

>* 编写的一行直接使用某npm包进行的操作
>* 由命令编写的一个文件
>* 一连串的操作指令

npm run为每条命令提供了“pre”和“post”两个钩子，还是拿示例说明，我现在如果运行npm run lint，则会在lint命令执行前执行“prelint”,在lint命令执行后执行“postlint”。
```
  "devDependencies": {
    "eslint": "^1.10.3"
  },
  "scripts": {
    "lint": "eslint .",
    "prelint": "hello",
    "postlint": "done"
  }
```

当然除了利用上述钩子之外，我们还可以自己写一些东西将命令们给“钩”起来，这得益于Linux系统的管道命令。

---

<h4> 2.1.2 linux系统的管道命令</h4>

在scripts里是支持书写Linux系统下的各种命令的，

```
"build-js": "browserify browser/main.js | uglifyjs -mc > static/bundle.js",
"uglify": "mkdir -p dist/js && uglifyjs src/js/*.js -m -o dist/js/app.js"
```
> * "<" &emsp;将文件的内容输入到一个命令
> * ">" &emsp;将操作的输出重定向到另一个文件
> * "|" &emsp;&nbsp;将一个命令的输出重定向到另一个命令
> * "&"  &emsp;并行执行几个任务
> * "&&" &nbsp;顺序调用任务(当前面执行成功后，后面的命令才继续)
> * "；"  &emsp;顺序调用任务(不管前面成功没有，后面的命令继续)

>这里有个很好的[scripts示例][8]，参照此我们在scripts里面书写指令就基本能满足我们的日常开发需求（这里我指的是能够替换gulp、grunt、webpack之类的工具）

示例部分代码
```
"scripts": {
    "clean": "rm -f dist/{css/*,js/*,images/*}",
    "autoprefixer": "postcss -u autoprefixer -r dist/css/*",
    "scss": "node-sass --output-style compressed -o dist/css src/scss",
    "lint": "eslint src/js",
    "uglify": "mkdir -p dist/js && uglifyjs src/js/*.js -m -o dist/js/app.js && uglifyjs src/js/*.js -m -c -o dist/js/app.min.js",
    "imagemin": "imagemin src/images/* -o dist/images",
    "icons": "svgo -f src/images/icons && mkdir -p dist/images && svg-sprite-generate -d src/images/icons -o dist/images/icons.svg",
    "serve": "browser-sync start --server --files 'dist/css/*.css, dist/js/*.js, **/*.html, !node_modules/**/*.html'",
    "build:css": "npm run scss && npm run autoprefixer",
    "build:js": "npm run lint && npm run uglify",
    "build:images": "npm run imagemin && npm run icons",
    "build:all": "npm run build:css && npm run build:js && npm run build:images",
    "watch:css": "onchange 'src/scss' -- npm run build:css",
    "watch:js": "onchange 'src/js' -- npm run build:js",
    "watch:images": "onchange 'src/images' -- npm run build:images",
    "watch:all": "npm-run-all -p serve watch:css watch:js watch:images",
    "postinstall": "npm run build:all && npm run watch:all"
  }
```
具体命令行怎么写可以参照npm包官网，有比较清晰完整的说明。比如拿上面的sass举例，找到相应文档
![此处输入图片的描述][9]

> 同时有一点要说的，并不是一定要用scripts替换gulp/webpack之类的，他们也是可以共存的，想象一下，加入你的工程在启动的时候需要执行多个gulp流，实在是不方便又不好再改，可以将几个命令集成到一个scripts指令中，是会带来很大便利的。



---
    
    
<h3 id="2.2">2.2 bin</h3>
bin项用来指定各个内部命令对应的可执行文件的位置。
```
"bin": {
  "someTool": "./bin/someTool.js"
}
```
上面代码指定，someTool 命令对应的可执行文件为 bin 子目录下的 someTool.js。Npm会寻找这个文件，在node_modules/.bin/目录下建立符号链接。在上面的例子中，someTool.js会建立符号链接npm_modules/.bin/someTool。由于node_modules/.bin/目录会在运行时加入系统的PATH变量，因此在运行npm时，就可以不带路径，直接通过命令来调用这些脚本。所以可以像下面这么写。
```
scripts: {  
  "start": "node someTool"
}
```

<h2 id="3">3 一些其他小常识补充</h2>
<h3 id="3.1">3.1 gulp&grunt…</h3>

前端自动化工程化的一发不可收拾，给我们的开发带来巨大益处的同时也是有着诸多不便的。
> * 对插件作者的依赖
> * 令人沮丧的调试
> * 脱节的文档


####问题 1：对插件作者的依赖

首先我们看npm插件的对比

![npm差距对比][10]

从上图可以看到，Gulp 有将近 2,100 个插件；Grunt 有将近 5,400 个；而 npm 则提供了 227,000 多个包，同时还以每天 400 多个的速度在持续增加。

在使用npm scripts时，你无需再搜索Grunt或是Gulp插件；只需从227,000多个npm包中选择就行了。公平地说，如果所需要的 Grunt 或是 Gulp 插件不存在，你当然可以直接使用 npm packages。不过，这样就无法再针对这个特定的任务使用 Gulp 或是 Grunt 了。

####问题 2：令人沮丧的调试

如果集成失败了，那么在 Grunt 和 Gulp 中调试是一件令人沮丧的事情。因为你面对的是一个额外的抽象层，对于任何 Bug 来说都有可能存在更多潜在的原因：

> * 基础工具出问题了么？
> * Grunt/Gulp 插件出问题了么？
> * 配置出问题了么？
> * 使用的版本是不是不兼容？ 

使用 npm scripts 可以消除上面的第 2 点，我发现第 3 点也很少会出现，因为我通常都是直接调用工具的命令行接口。最后，第 4 点也很少出现，因为我通过直接使用 npm 而不是任务运行器的抽象减少了项目中包的数量。

####问题 3：脱节的文档

一般来说，我所需要的核心工具的文档质量总是要比与之相关的 Grunt 和 Gulp 插件的好。比如说，如果使用了 gulp-eslint ，那么我就要在gulp-eslint文档与 ESLint 网站之间来回切换；不得不在插件与插件所抽象的工具之间来回切换上下文。Gulp 与 Grunt 的问题在于：光理解所用的工具是远远不够的。Gulp 与 Grunt 要求你还得理解插件的抽象。

> 更多内容请参照 [why npm scripts][11]
当然npm scripts不是一定要替换掉以上工具的，它可以和gulp共同使用，毕竟我们的最终目标都是更加方便快捷清晰地开发以及管理项目。

<h3 id="3.2">3.2 常见盲区</h3>

① 经常看到一些东西很眼熟，但是不清楚是干啥的，比如process.env之类的，就搜了一下：
>[nodejs的process模块][12]

② 一个小疑惑，大家发现 USER 和 HOME 这类的环境变量用的比较多，我们编写流的时候是不是可以直接利用它们做些手脚呢？
试试在本地输入下面命令：
```
echo ${USER}
echo ${HOME}
```

That's all

谢谢观看

[回到顶部](#jump)


  [1]: http://javascript.ruanyifeng.com/nodejs/npm.html#toc2
  [2]: http://javascript.ruanyifeng.com/nodejs/npm.html#toc4
  [3]: https://css-tricks.com/why-npm-scripts/
  [4]: http://www.ruanyifeng.com/blog/2015/05/command-line-with-node.html
  [5]: https://www.npmjs.com/
  [6]: http://p3.qhimg.com/t012c3a6d28aa579127.jpg
  [7]: http://javascript.ruanyifeng.com/nodejs/npm.html#toc4
  [8]: https://github.com/damonbauer/npm-build-boilerplate
  [9]: http://p7.qhimg.com/t01ce0f9c652f8b14b7.png
  [10]: http://p8.qhimg.com/t014f7229d4be833e70.png
  [11]: https://css-tricks.com/why-npm-scripts/
  [12]: http://www.css88.com/archives/4548