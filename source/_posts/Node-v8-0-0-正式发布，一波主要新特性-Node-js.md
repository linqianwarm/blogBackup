---
title: Node v8.0.0 正式发布，一波主要新特性 | Node.js
date: 2017-06-04 19:02:16
tags: Node
---

本翻译同时发布在平台<a href="http://www.zcfy.cc/article/node-v8-0-0-current-node-js-2986.html" target="_blank">众成翻译</a>
附上<a href="https://nodejs.org/en/blog/release/v8.0.0/" target="_blank">原文链接</a>
<hr>
<div>接下来发布的这个版本的node.js会带来一系列重大的改变和影响,有太多要点以至于我们无法在这样一篇文章中充分覆盖其特性。但这篇文章包含了其最大改变和特性的总结。

</div>

### npm version 5.0.0[](#你好呀-version-5-0-0)

npm的公司近期
[宣布发布了5.0.0版本客户端](http://blog.npmjs.org/post/161081169345/v500)
我们也非常开心能够在node.js 8.0.0版本中用到这个新版本.


### V8 5.8[](#你好呀-v8-5-8)

node.js 8.0.0版本附带了v8引擎的5.8版本的，此版本对JavaScript运行时性能提升了很多，也包括在性能改进和面对开发者人员API的主要提升。对于node.js开发者来说最重大的意义莫过于v8 5.8版本保证了在今后的5.9或者即将来临的6.0版本的ABI兼容性，这也将确保Node.js原生插件系统的稳定性。在Node.js 8的有生之年，我们计划转移到v8的5.9版本或者更有甚者到6.0版本。


V8 5.8引擎同时有助于建立一个新[TurboFan + Ignition](https://v8project.blogspot.com/2017/05/launching-ignition-and-turbofan.html)编译管道的过渡，其承诺给所有node.js应用提供重大的新表现优化。虽然TurboFan and Ignition在V8先前的版本也已经存在，但它还是会在V8 5.9的第一时间默认使用。这种新的编译管道代表了如此重大的一个变化以至于Node.js核心技术委员会（CTC）选择推迟了原计划定于四月的Node.js 8版本的发布，只是为了更好地适应它。

<!-- more -->

### Node.js API (N-API)[](#say-hello-to-the-node-js-api-n-api)


对于使用或者开发原生插件的Node.js开发者来说，这个新的实验性的Node.js API（N-API）对于现有的[Native Abstractions for Node.js (`nan`)](https://www.npmjs.com/package/nan)是一个重大的进步。
它将会允许原生插件在系统上无需重复编译并且可跨多个不同版本的Node.js使用。

通过提供一个新的虚拟机不可知应用程序接口（ABI），原生插件不仅可以在多个版本的V8 javaScript运行，也可以在微软的Chakra-Core运行将成为可能。

[N-API](https://nodejs.org/api/n-api.html)在Node.js 8.0.0中还是实验阶段，所以我们应该期待正式的实施和API的重要改变。我们
[鼓励开发者们尽快开始使用API](https://nodejs.org/en/docs/guides/publishing-napi-modules/)
并且希望能够提供反馈，以确保新的API是满足这个生态圈系统的需求。


### async_hooks[](#say-hello-to-async_hooks)

这个实验性的 `async_hooks` 模块（以前叫`async_wrap`）在8.0.0版本中获得重大升级。该诊断API允许开发人员使用监视Node.js事件循环的操作，通过其完整的生命周期跟踪异步请求和处理。
该新模块[完整的文档](https://github.com/nodejs/node/pull/13287)仍然不完整，用户在使用这个实验性的新模块时应格外小心。



### WHATWG URL parser[](#say-hello-to-the-whatwg-url-parser)

去年，围绕[WHATWG URL Standard](https://url.spec.whatwg.org/)实施的实验性`URL`API已添加到Node.js 7.x中，自此一直处于积极的发展之中。我们很高兴地宣布，从8.0.0开始，新的`URL`实现现在是Node.js中完全支持的非实验API。 下面显示了一个示例用法，更多详细信息可在[官方文档](https://nodejs.org/api/url.html#url_the_whatwg_url_api)中看到。

```
const URL = require('url').URL;

const myUrl = new URL('/a/path', 'https://example.org/');

```

这个新的`URL`实现是最有意义的在于它与现代Web浏览器（如Chrome，Firefox，Edge和Safari）中的`URL`实现和API相匹配，允许使用URL在不同环境之间共享代码。


### Buffer changes缓冲区的变化[](#buffer-changes)

对Node.js中的`Buffer`API进行了一些重要更改。
最重要的是调用不推荐使用的 `Buffer（num）`构造函数（使用或不使用`new`关键字）将返回一个零值初始化的`Buffer`实例。 先前版本的Node.js将返回未初始化的内存，这可能包含潜在的敏感数据。


在Node.js 6.0.0中，一组新的 `Buffer`构造方法作为调用`Buffer（num）`构造函数的替代方法[被引入](https://medium.com/@jasnell/node-js-buffer-api-changes-3c21f1048f97)，以解决一些[安全性](https：//securityecurity .io / advice / 67)和可用性问题。 然而，现有的构造函数在Node.js生态系统中被广泛使用，使我们无法完全弃用或禁用它，而不会导致重大的破坏。

默认情况下没有零值初始化的`Buffer（num）`的新实例将对性能产生重大影响。 如果开发人员希望分配具有未初始化内存的`Buffer`实例，则应转移到新的`Buffer.allocUnsafe（num）`API。 Node.js 8中零值初始化和未初始化的“缓冲区”创建示例如下所示。


```
// 使用零值初始化Buffers
const safeBuffer1 = Buffer.alloc(10);
const safeBuffer2 = new Buffer(10);

// 未初始化Buffer
const unsafeBuffer = Buffer.allocUnsafe(10);

```

请注意，虽然目前没有从Node.js中删除Buffer（num）构造函数的计划，但是已经弃用对其的后续维护。


### Pending Deprecations[](#pending-deprecations)

为了在开发时或CI测试环境中更容易地捕获应用程序中的`Buffer（num）`，我们添加一个新的`--pending-deprecation`命令行标志和匹配的`NODE_PENDING_DEPRECATION = 1`环境变量，这会导致当使用`Buffer（num）`（和其他潜在的待弃用的方法）时Node.js发出`DeprecationWarning`的进程警告。 为了避免类似这种弃用影响到生产应用程序，默认情况下是停用它们的。 下面显示一个允许未决弃用的示例。

```
$ ./node --pending-deprecation
> Buffer(num)
<Buffer 00>
> (node:2896) [DEP0005] DeprecationWarning: The Buffer() and new Buffer() constructors are not recommended for use due to security and usability concerns. Please use the new Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() construction methods instead.

```

### 提升对Promises的支持[](#improved-support-for-promises)

Node.js 8.0.0包含一个新的`util.promisify（）`API，它允许在一个返回`Promise`的函数中包装标准的Node.js回调样式API。 `util.promisify（）`的使用示例如下。

```
const fs = require('fs');
const util = require('util');

const readfile = util.promisify(fs.readFile);

readfile('/some/file')
  .then((data) => { /** ... **/ })
  .catch((err) => { /** ... **/ });

```

### Console 的改变[](#console-changes)

通过Node.js中的`console`模块可以将`console.log()`, `console.error()`和其他方法将应用程序定向输出到`stdout`，`stderr`或者管道。 在以前，尝试将控制台输出写入到基础流时发生的错误从而导致Node.js应用程序崩溃。 从8.0.0开始，这样的错误将被忽略，从而使`console.log（）`和其他API更安全。 这将可能通过传递给`Console`构造函数的`ignoreErrors`配置来维护与错误相关的遗留行为。


### 静态错误码[](#static-error-codes)

我们已经开始为Node.js生成的所有错误分配静态错误码的过程。 然后每个错误都需要一段时间才能被分配一个错误码码，在8.0.0之内已经更新了一些错误。 即使错误类型或消息发生变化，也保证这些错误码不会改变。

错误码以两种方式显示给用户：
Codes are manifest to the user in two ways:

*   Using the `code` property on `Error` object instances

*   Printing the `[ERR_CODE]` in the stack trace of an Error

例如，调用`assert（false）`会生成以下的`AssertionError`：

```
> assert(false)
AssertionError [ERR_ASSERTION]: false == true
    at repl:1:1
    at ContextifyScript.Script.runInThisContext (vm.js:44:33)
    at REPLServer.defaultEval (repl.js:239:29)
    at bound (domain.js:301:14)
    at REPLServer.runBound [as eval] (domain.js:314:12)
    at REPLServer.onLine (repl.js:433:10)
    at emitOne (events.js:120:20)
    at REPLServer.emit (events.js:210:7)
    at REPLServer.Interface._onLine (readline.js:278:10)
    at REPLServer.Interface._line (readline.js:625:8)

```

通过引用Node.js文档，可以快速查询有关静态错误码的信息。 例如，查询有关“ERR_ASSERTION”错误码的信息的URL是
[https://nodejs.org/dist/latest-v7.x/docs/api/errors.html#ERR_ASSERTION](https://nodejs.org/dist/latest-v7.x/docs/api/errors.html#ERR_ASSERTION).


### 重定向过程警告[](#redirecting-process-warnings)

使用`--redirect-warnings = {file}`命令行参数或匹配`NODE_REDIRECT_WARNINGS = {file}`环境变量可以处理诸如deprecations之类的警告，可以将其重定向到一个文件。 默认情况下，不会将警告打印到`stderr`，而是将警告写入指定的文件，从而我们将更清晰地分析应用程序的主要输出。


### Stream API 改进[](#stream-api-improvements)

对于Stream API的用户，已添加了用于销毁和完成Stream实例的新标准机制。 每个`Stream`实例现在将继承一个`destroy()`方法，通过提供`_destroy()`方法的自定义实现，可以定制和扩展它们的实现。


### Debugger 的改变[](#debugger-changes)

Node.js 8正在删除遗留的命令行调试器。 作为命令行替换，node-inspect已经直接集成到Node.js运行时。 此外，以前在Node.js 6中的一项实验功能——V8 Inspector调试器，正在升级为完全支持的功能。


### 实验性的检查器 JavaScript API[](#experimental-inspector-javascript-api)

已经引入了用于Inspector协议的新的实验性JavaScript API，使开发人员能够利用调试协议来检查运行的Node.js进程的新方法。


```
const inspector = require('inspector');

const session = new inspector.Session();
session.connect();

// Listen for inspector events
session.on('inspectorNotification', (message) => { /** ... **/ });

// Send messages to the inspector
session.post(message);

session.disconnect();

```

请注意，检查器API是实验性的，可能会发生大改变。

### 长期支持[](#long-term-support)

Node.js 8是下一个进入长期支持（LTS）的发行版。 这将于2017年10月发生。 一旦Node.js 8转换到LTS，它的代号将变为Carbon。

![Node.js Long Term Support Schedule](http://p0.qhimg.com/t01c58d07f2a8de74b1.png)

请注意，在引用Node.js发行版本时，我们已经在Node.js 8中删除了“v”。 以前的版本通常被称为v0.10，v0.12，v4，v6等。为了避免与JavaScript引擎V8混淆，我们删除了“v”并将其称为Node.js 8。


### 明显变化[](#notable-changes)

*   **异步钩子Async Hooks**

    *   在内核中已实现`async_hooks`模块
        [[`4a7233c178`](https://github.com/nodejs/node/commit/4a7233c178)]
        [#12892](https://github.com/nodejs/node/pull/12892).

*   **缓冲区Buffer**

    *   在使用`new Buffer(num)`或`Buffer(num)`时,带上`--pending-deprecation`将导致Node.js发出一个废弃警告。
        [[`d2d32ea5a2`](https://github.com/nodejs/node/commit/d2d32ea5a2)]
        [#11968](https://github.com/nodejs/node/pull/11968).

    *   `new Buffer(num)` and `Buffer(num)` 会生成零值初始化新的`Buffer`实例
        [[`7eb1b4658e`](https://github.com/nodejs/node/commit/7eb1b4658e)]
        [#12141](https://github.com/nodejs/node/pull/12141).

    *   现在许多`Buffer`方法接受`Uint8Array`作为输入
        [[`beca3244e2`](https://github.com/nodejs/node/commit/beca3244e2)]
        [#10236](https://github.com/nodejs/node/pull/10236).

*   **Child Process**

    *   Argument和kill信号验证得到改善
        [[`97a77288ce`](https://github.com/nodejs/node/commit/97a77288ce)]
        [#12348](https://github.com/nodejs/node/pull/12348),
        [[`d75fdd96aa`](https://github.com/nodejs/node/commit/d75fdd96aa)]
        [#10423](https://github.com/nodejs/node/pull/10423).

    *   Child Process方法接受Uint8Array作为输入
        [[`627ecee9ed`](https://github.com/nodejs/node/commit/627ecee9ed)]
        [#10653](https://github.com/nodejs/node/pull/10653).

*   **Console**

    *   使用`console`方法时，错误事件发送现在受到了抑制。
        [[`f18e08d820`](https://github.com/nodejs/node/commit/f18e08d820)]
        [#9744](https://github.com/nodejs/node/pull/9744).

*   **Dependencies**

    *   npm客户端已经升级到5.0.0
        [[`3c3b36af0f`](https://github.com/nodejs/node/commit/3c3b36af0f)]
        [#12936](https://github.com/nodejs/node/pull/12936).

    *   V8引擎已经升级到5.8版本并具有向前的ABI兼容性一直到6.0版本
        [[`60d1aac8d2`](https://github.com/nodejs/node/commit/60d1aac8d2)]
        [#12784](https://github.com/nodejs/node/pull/12784).

*   **Domains**

    *   原生`Promise`实例现在是`域`感知的
        [[`84dabe8373`](https://github.com/nodejs/node/commit/84dabe8373)]
        [#12489](https://github.com/nodejs/node/pull/12489).

*   **Errors**

    *   我们已经开始为Node.js生成的错误分配静态错误代码。
        这是通过多次提交完成的，目前仍在进行中。

*   **File System**

    *   实用程序类`fs.SyncWriteStream`已被弃用
        [[`7a55e34ef4`](https://github.com/nodejs/node/commit/7a55e34ef4)]
        [#10467](https://github.com/nodejs/node/pull/10467).

    *   已弃用的`fs.read()`字符串接口已被删除
        [[`3c2a9361ff`](https://github.com/nodejs/node/commit/3c2a9361ff)]
        [#9683](https://github.com/nodejs/node/pull/9683).

*   **HTTP**

    *   改进对用户使用代理的支持
        [[`90403dd1d0`](https://github.com/nodejs/node/commit/90403dd1d0)]
        [#11567](https://github.com/nodejs/node/pull/11567).

    *   溢出的Cookie headers会被连接成一个字符
        [[`d3480776c7`](https://github.com/nodejs/node/commit/d3480776c7)]
        [#11259](https://github.com/nodejs/node/pull/11259).

    *   其`httpResponse.writeHeader()`方法已被弃用
        [[`fb71ba4921`](https://github.com/nodejs/node/commit/fb71ba4921)]
        [#11355](https://github.com/nodejs/node/pull/11355).

    *   用于访问HTTP头的新方法已添加到`OutgoingMessage`中。
        [[`3e6f1032a4`](https://github.com/nodejs/node/commit/3e6f1032a4)]
        [#10805](https://github.com/nodejs/node/pull/10805).

*   **Lib**

    *   所有弃用消息已分配静态标识符
        [[`5de3cf099c`](https://github.com/nodejs/node/commit/5de3cf099c)]
        [#10116](https://github.com/nodejs/node/pull/10116).

    *   遗留的`linkedlist`模块已被删除
        [[`84a23391f6`](https://github.com/nodejs/node/commit/84a23391f6)]
        [#12113](https://github.com/nodejs/node/pull/12113).

*   **N-API**

    *   添加了对新的N-API API的实验性支持
        [[`56e881d0b0`](https://github.com/nodejs/node/commit/56e881d0b0)]
        [#11975](https://github.com/nodejs/node/pull/11975).

*   **Process**

    *   可以使用`--redirect-warnings`命令行参数将进程警告输出重定向到文件
        [[`03e89b3ff2`](https://github.com/nodejs/node/commit/03e89b3ff2)]
        [#10116](https://github.com/nodejs/node/pull/10116).

    *   过程警告现在可能包括额外的细节
        [[`dd20e68b0f`](https://github.com/nodejs/node/commit/dd20e68b0f)]
        [#12725](https://github.com/nodejs/node/pull/12725).

*   **REPL**

    *   REPL magic模式已被弃用REPL
        [[`3f27f02da0`](https://github.com/nodejs/node/commit/3f27f02da0)]
        [#11599](https://github.com/nodejs/node/pull/11599).
                