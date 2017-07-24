---
title: 编写模块化的CSS（第二部分）—命名空间 | Zell Liew
date: 2017-06-15 19:12:33
tags: CSS
---

本翻译同时发布在平台<a href="http://www.zcfy.cc/article/writing-modular-css-part-2-namespaces-zell-liew-3112.html" target="_blank">众成翻译</a>
附上<a href="https://zellwk.com/blog/css-architecture-2/" target="_blank">原文链接</a>
PS: 本文还有1和3，建议宝宝们看这个之前先看<a href="http://www.zcfy.cc/article/writing-modular-css-part-1-bem-zell-liew-3042.html" target="_blank">1</a>；
还有<a href="https://zellwk.com/blog/css-architecture-3/" target="_blank">链接3</a>

<hr>

上周，我分享了如何使用BEM创建一个合理的CSS架构。 虽然BEM很棒，但它只是解决方案的一部分。 还有另一部分我还没有提到 - 命名空间。

在今天的这篇文章中，我想与大家分享一下为什么只用BEM还是不够的，以及如何使用命名空间来弥补一些不足。

## 为什么BEM不能满足我们

我[上周](https://zellwk.com/blog/css-architecture-1)给大家展示的例子很简单。 我只向大家展示了如何处理单个块中不同的修饰符和子代（或孙子代）元素。 但是如果有多个区块咱怎么办呐？

事情有点复杂。 我们使用一个网站范围的导航来说明两个块之间的关系。

```
<nav class="main-nav">
  <a href="#">Home</a>
  <button class="button">Menu</button>
</nav>

```

好啦。 现在有两个区块。 一个叫`.main-nav`，另一个叫`.button`。 `.button`存在于`.main-nav`内。

然后现在你想把这个button的颜色从blue变成green。同时你也想给`.button`加些左边距来和`home`链接分开。

那么问题来了，你应该怎样书写CSS代码呢？这下面有几种可能的答案：
<!-- more -->

1.给`.main-nav .button`添加`margin`和`background-color`。

2.给`button--modifier`添加`margin`和`background-color`。

3.给`.main-nav .button`添加`margin`，给`button--modifier`添加`background-color`。

4.给`.main-nav a`添加`margin`，给`.main-nav .button`添加`background-color`。

5.给`.main-nav a`添加`margin`，给`button--modifier`添加`background-color`。

哪一种方式最能引起你的情感共鸣呢？ 你又如何能确保您的项目中的每个开发人员都以同样的方式来接受呢？

即使您的所有开发人员都拷贝了这个方案（因此也是以同样的方式），您如何知道您是否没有引入副作用（破坏了网站的另一部分）？😱😱😱

老实说，很难保证！如果我们只有BEM，有太多可能的因素导致。

这就是之所以引入命名空间。它可以帮助你创建一个结构来控制CSS属性的写入。 如果您遵循惯例，您将能够无惧副作用地编写CSS。

这里是一个示例。

假设我把上面的代码转换成一个带有命名空间的代码。HTML将完全相同（只加了少数的class前缀）。 在这个例子中要特别注意`.o`和`.c`前缀：

```
<nav class="c-main-nav">
  <a href="#">Home</a>
  <button class="o-button">Menu</button>
</nav>

```

`.o-`和`.c-`是什么意思呢？从这个代码来看，我知道如果我想，我可以改变`.o-button`的颜色，但我不应该添加任何边距到`.o-button`。

啥！？ 那么我必须来解释这些命名空间，gogogo~  :)

## 我使用的命名空间

以下是我使用的命名空间列表：

1.  `.l-`: 布局(layouts)

2.  `.o-`: 对象(objects)

3.  `.c-`: 组件(components)

4.  `.js`: js的钩子(JavaScript hooks)

5.  `.is-`|`.has-`: 状态类(state classes)

6.  `.t1`|`.s1`: 排版大小(typography sizes)

7.  `.u-`: 实用类(utility classes)

我们来看看具体每个命名空间是什么，以及咱应该怎么用。

在继续之前，如果您对命名空间不了解，我强烈建议您查看Harry Robert的[具有命名空间的更透明的ui代码](https://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/)。（有趣的事实：Harry启发我使用命名空间）。

如果你阅读Harry的文章，要注意一下我的命名空间不同于他的。（待会儿我会分享有哪些不同的内容）。

以上，让我们先进入第一个命名空间——布局（layouts）

## “.l-”——布局(layouts)

我很确定你听说过[Nicole Sullivan](https://twitter.com/stubbornella)的[Object Oriented CSS(面向对象的CSS)](https://github.com/stubbornella/oocss/wiki)(OOCSS)。 如果您还没有深入了解，那得知道OOCSS背后的主要思想是表层和结构的分离。 换句话说，影响块或其元素的位置的_属性应该被抽象为一个单独的类用于重复利用_。

在CSS中，定位块的行为也称为布局块。 在一般意义上，定位是**布局**。

也许这只是一个快乐的巧合（也许😉），但[Jonathan Snook](https://twitter.com/snookca)在[SMACSS](https://smacss.com)中为布局规则建议一个`.l-`前缀。这两个范例在布局方面有着相同的原则。 因此，我很高兴地从SMACSS中窃取`.l-`作为布局命名空间。

既然你已经了解了命名空间的起源了，它可能会帮助你了解它的使用方式。 当涉及到布局时，我将布局分为两个不同的类别 —— **全局布局**和**块级布局**。

### 全局布局

全局布局是应用于所有页面的布局。 (😑)。 在我的用例中，它们通常是在任何地方都使用的大型网格容器。 一个例子是`.l-wrap` class：

```
// I like to write in Sass :)
.l-wrap {
  padding-left: 1em;
  padding-right: 1em;

  @media (min-width: 1000px) {
    max-width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
}

```

我将在每个地方都使用`.l-wrap` class，比如在header和footer里来对齐内容：

```
<div class="site-header">
  <div class="l-wrap">
    <!-- stuff -->
  </div>
</div>

<div class="site-footer">
  <div class="l-wrap">
    <!-- stuff -->
  </div>
</div>

```

由于这些class在全局使用，所以我更喜欢把它们写在`_layouts.scss`部分。

###  块级布局

每个块（对象或组件，我们将在后面讨论）可能有自己的布局。 通过个人经验，我发现这些布局通常独立于全局布局。

让我来举个栗子。

当我为[Mastering Responsive Typography](https://mastering-responsive-typography.com)建站后，我添加了一个如下所示的付款表单：

![Payment form on Mastering Responsive Typography](http://p0.qhimg.com/t0170835277656c2d14.png)

响应式排版的付款表单

在上面的设计中，您可以看到该表单包含两行输入元素。 第一行中有两个相等大小的输入框，第二行中有两个不同大小的输入框。

为了区分这三个不同大小的输入框，我选择了布局前缀：

```
<form class="form l-form" action="#">
  <div class="form__row">
    <div class="form__item l-form__item"></div>
    <div class="form__item l-form__item"></div>
  </div>
  <div class="form__row">
    <div class="form__item l-form__item--large"></div>
    <div class="form__item l-form__item--small"></div>
  </div>
  <!-- ... -->
</form>

```

你注意到了我是怎样同时保持BEM的实现还有布局的？ 这种实现对我来说使我更加清楚了。 你瞄一眼就可以看到我的CSS将写些啥。 清晰明了。

```
.l-form {/* container styles */}
.l-form__item {/* half-width styles */}
.l-form__item--large {/* larger-width styles */}
.l-form__item--small {/* smaller-width styles */}

```

因为`.l-form`，`.l-form__item`，`.l-form__item - small`和`.l-form__item - large`与其他块无关，我在`_form.scss`中写这些class来保持上下文。

顺便说一句，有些人不同意我在[前一篇文章](https://zellwk.com/blog/css-architecture-1)里讲到当出现`.block - modifier`时删除`.block`这一观点。 那么，看看在这种情况下插入所有“必需”BEM class的情况下会发生什么，你会注意到“HTML开始膨胀”：

```
<form class="form l-form" action="#">
  <div class="form__row">
    <div class="form__item l-form__item"></div>
    <div class="form__item l-form__item"></div>
  </div>
  <div class="form__row">
    <!-- 这段HTML开始变得太太太太长了 😢 -->
    <div class="form__item l-form__item l-form__item--large"></div>
    <div class="form__item l-form__item l-form__item--small"></div>
  </div>
  <!-- ... -->
</form>

```

最后一点：Harry使用对象命名空间（`.o-`）来表示这样的结构布局。 我只是将它们分组成`.l-`，并使用`.o-`来代替别的东西。

以上，让我们转移到对象（objects）上(我的版本 😜)。

## “.o-”——对象(Objects)

_Objects_(`.o-`)_是website_的最小构建块。可以把它们想成是可以在网站各个地方拼凑的【乐高】块（译者注：‘乐高’玩具，没玩过的可以淘宝搜看看）。 如果您曾听过[Brad Frost](https://twitter.com/brad_frost)的[Atomic Design](http://atomicdesign.bradfrost.com)，也可以将对象视为元素和分子的混合物。

对象物们都有着以下的属性：

1. 对象使用`.o-`前缀

2. 它们的里面不能包含其他对象或组件

3. 它们之于上下文是独立的

4. 某些对象可以在有意义的情况下忽略`.o-`前缀。

### 对象不能包含其他对象或组件

对象可大可小。对象中的HTML元素的数量是不相关的。 解释以下。

举个例子,buttons就是对象。它们是很小的而且可以放到任何地方。这是不言而喻的：

```
`<a href="#" class="o-button">A button</a>`

```

一个较大的对象的例子是我为Mastering Responsive Typography构建的倒计时器：

![Example of a large object](http://p0.qhimg.com/t019d144b9579ce97bd.png)

一个大对象的例子。仍然被认为是一个对象，因为它不包含对象和组件。

倒计时器的HTML结构如下：

```
<div class="o-countdown jsCountdown">
  <div class="o-countdown__inner">
    <span data-token="days">1</span>
    <span>day</span>
  </div>
  <div class="o-countdown__inner">
    <span data-token="hours">21</span>
    <span>hours</span>
  </div>
  <div class="o-countdown__inner">
    <span data-token="minutes">41</span>
    <span>minutes</span>
  </div>
  <div class="o-countdown__inner">
    <span data-token="seconds">50</span>
    <span>seconds</span>
  </div>
</div>

```

注意`.o-countdown`包含三层HTML元素。虽然它很大了，但它仍然是一个对象，因为它不包含任何其他对象或组件。`.o-countdown`中的元素的实际数量是无关紧要的，因为所有内部元素只能在`.o-countdown`里存在。

### 对象独立于上下文

当我说对象是上下文独立的时候，我的意思是他们不知道在哪里会被使用。 你可以选择任何的对象，并把它放在你喜欢的地方，而且并不会破坏你的网站的结构。

这也意味着对象不应该更改外部任何结构。 因此，对象块不能包含任何这些属性/值：

1.  `absolute` 和 `fixed` 定位。

2.  `margin`

3.  `padding` (除非你用了`background-color`。 在这种情况下，它不会中断对象外部的对齐)。

4.  `float`.

5.  等等…

既然你知道对象需要与上下文无关，你马上知道我们站点范围的导航示例中的`.button`不能包含任何边距。

以下是我的样式表中典型的`.o-button`对象的示例：

```
/* 如果您不明白这个棘手的选择器，请返回上一篇文章理解 */
[class*='o-button']:not([class*='o-button__']) {
  display: inline-block;
  padding: 0.75em 1.25em;
  border-radius: 4px;
  background-color: green;
  color: white;
  font-size: inherit;
  line-height: inherit;
  transition: all 0.15s ease-in-out;
}

```

虽然对象不能影响外部结构，但它改变其内部结构是很合理的。 例如，我提到的`.o-countdown`计时器可以具有以下HTML和CSS：

```
<div class="o-countdown l-countdown jsCountdown">
  <div class="o-countdown__inner l-countdown__inner">
    <span data-token="days">3</span>
    <span>days</span>
  </div>
  <div class="o-countdown__inner l-countdown__inner">
    <span data-token="hours">20</span>
    <span>hours</span>
  </div>
  <div class="o-countdown__inner l-countdown__inner">
    <span data-token="minutes">57</span>
    <span>minutes</span>
  </div>
  <div class="o-countdown__inner l-countdown__inner">
    <span data-token="seconds">33</span>
    <span>seconds</span>
  </div>
</div>

```

```
.l-countdown {
  display: flex;
}

.l-countdown__inner {
  /* 大概你想咋整就咋整? */
}

```

你可以自由地设计一个对象，底线是只要它不影响任何外面的东西。（另外，请确保您不要意外添加'padding'使其看起来不规整）。

### 合理情况下，某些对象可以忽略 .o- 前缀

哇，我们是否已经违反了规定？ 哎是呀！😈。

一些对象包含`.o-`前缀（甚至是一个类）本身就没有意义，因为它们被使用得太多了。 举一个这样的例子——输入元素：

```
`<input type="text">`

```

当然，如果你喜欢的话，你可以将一个class标记给input，但是如果你不能访问

```
@mixin input {
  padding: 0.5em 0.75em;
  font-size: inherit;
  line-height: inherit;
  font-family: inherit;
}

input[type="text"],
input[type="email"],
input[type="textarea"] {
  @include input;
}

// ...

```

我觉得另一个对象不应该使用`.o-`前缀的例子是字体。 他们得到特别待遇（我稍后会解释）。 在这一点上，你可以自由地反对。

### 对象使用总结

对象（`.o-`）是一个网站的最小的构建块。
对象物们都有着以下的属性：

1. 对象使用`.o-`前缀

2. 它们的里面不能包含其他对象或组件

3. 它们之于上下文是独立的

4. 某些对象可以在有意义的情况下忽略`.o-`前缀。

接下来我们转移到组件上

## “.c-”——组件(Components)

如果对象是最小的构建块，则组件是您可以在整个站点中使用的更大的构建块。 如果您已阅读《原子设计》，请将组件视为有机体。 （除了这种生物体可以含有其他生物体 😉)。

组件有着以下属性：

1. 组件使用'.c-'前缀

2. 组件可以包含其他对象和组件。

3. 组件是_上下文感知_的

让我们来看看这些属性，我会补充你所需要的例子 😜。

### 组件可以包含其他对象和组件

让我们回到我所说的关于布局的形式。 下是组件的完美示例。

![Payment form on Mastering Responsive Typography](http://p0.qhimg.com/t0170835277656c2d14.png)

响应式排版的付款表单

之前我提到过这段HTML:

```
<form class="form l-form" action="#">
  <div class="form__row">
    <div class="form__item l-form__item"></div>
    <div class="form__item l-form__item"></div>
  </div>
  <div class="form__row">
    <div class="form__item l-form__item--large"></div>
    <div class="form__item l-form__item--small"></div>
  </div>
  <!-- ... -->
</form>

```

我实际上省略了很多代码，使其在布局部分中看起来合理。 如果我们深入挖掘，你会看到有`input`和`.o-button`对象。

```
<form class="c-form l-form" action="#">
  <div class="c-form__row">
    <div class="c-form__item l-form__item">
      <label for="fname">
        <span>First Name</span>
        <input type="text" id="fname" name="fname">
      </label>
    </div>
    <!-- ... the email input item -->
  </div>
  <!-- ... other form_rows -->
  <div class="c-form__row">
    <button class="o-button c-form__button">Buy Mastering Responsive Typography!</button>
  </div>
</form>

```

看看`.c-form`现在是否包含其他对象？ :)

### 组件是上下文感知的（一般而言）

组件是相当大的，所以您需要特别注意将它们放置在不同的地方。 例如，这个`.c-form`组件可以放在_整个宽度栏中_或_侧边栏_中。

以下是放在侧栏上下文中的表单：

![Form component in a sidebar context](http://p0.qhimg.com/t015cd2735dae5d98c0.png)

表单组件放在侧边栏上

马上就可以看到三件事情改变了：

1. 标签被隐藏

2. `input`和`o-button`对象的布局变为百分百宽度

3. 文本的`Font-size`和`line-height`在按钮对象上变小。

此更改表单的HTML可能是：

```
<form class="c-form--sidebar l-form--sidebar" action="#">
  <div class="c-form__row">
    <div class="c-form__item l-form__item">
      <label for="fname">
        <span>First Name</span>
        <input type="text" id="fname" name="fname">
      </label>
    </div>
  </div>
  <!-- ... the email input row -->
  <div class="c-form__row">
    <button class="o-button c-form__button">Buy Mastering Responsive Typography!</button>
  </div>
</form>

```

并且各自的（S）CSS更改是：

```
.l-from--sidebar {
  .l-form__item { /* change to full width style */}
}

.c-form--sidebar {
  label {
    // http://snook.ca/archives/html_and_css/hiding-content-for-accessibility
    @include is-invisible;
  }

  .c__button {
    font-size: 16px;
    line-height: 1.25;
  }
}

```

还有一件事。 注意到了我混合了一个对象和组件类在`.c-form__button`里么？ 这被称为[BEM混合](https://en.bem.info/methodology/key-concepts/#mix)，它允许我使用组件的类来创建一个对象，而不影响原始按钮。

### 组件的总结

组件（`.c-`）是您可以在整个站点中使用的_更大的构建块_。
组件有着以下属性：

1. 组件使用'.c-'前缀

2. 组件可以包含其他对象和组件。

3. 组件是_上下文感知_的

接下来我们来说下一个命名空间。

## “.js”——JavaScript的钩子

_Javascript 钩子_（`.js`）_表示对象/组件是否需要JavaScript_。 举个栗子，我之前提到的倒计时器

```
<div class="o-countdown jsCountdown">
  <!-- ... -->
</div>

```

使用JavaScript命名空间的好处是可以将JS功能与样式分开，这使得它们更易于维护。

例如，您刚刚看到`.jsCountdown`类就可以立即知道，`.o-countdown`需要JavaScript才能正常工作。 如果将来有需要将`o-countdown`更改为`c-countdown`，我也不必担心破坏任何JS功能。

JavaScript钩子很简单，所以让我们继续吧。

## “.is-/.has-” ——状态类

_状态类表示对象/组件的当前状态_。当应用状态类时，您可以立即知道对象/组件是否具有下拉（`.has-dropdown`）或当前处于打开状态（`.is-open`）。 这些可爱的课程来自SMACSS（如果你想知道的话）。

当您在CSS中设计状态类时，建议您尽可能保持样式接近所讨论的对象/组件。 例如：

```
// Sass
.object {
  &.is-animating { /* styles */}
}

```

如果您不用Sass,你可以用这种方式来书写CSS：

```
`.object.is-animating { /* styles */ }`

```

由于Jonathan早已介绍了这点，所以你可能会了解状态类。 所以我不再多说:)

让我们继续。

## “.t”或“.s”——排版类(Typography)

在排版中最好的做法是在网页上只使用少数样式（大小，字体等）。 现在，你可能会在标题`<h1>`-`<h6>`中写出这样的排版风格：

```
h1 { /* styles */ }
h2 { /* styles */ }
h3 { /* styles */ }
h4 { /* styles */ }
h5 { /* styles */ }
h6 { /* styles */ }

```

如果您的网站很简单，那么这是一个很好的开始，并且不需要为多个对象/组件使用相同的标题样式。

但是举个栗子哈，如果你有一个带链接的导航样式和你的h5样式一致怎么办？

你会这样做吗？

```
<!-- 哎呀，千万别这么做！-->
<nav class="c-nav">
  <h5><a href="#">Link</a></h5>
  <h5><a href="#">Link</a></h5>
  <h5><a href="#">Link</a></h5>
</nav>

```
显然咱不能这么干。那么更好的方式就是改变我们的CSS样式。所以或许这么改？

```
nav a {
  font-size: 14px;
  line-height: 1.25;
}

```

虽然改动CSS的版本稍微好一点，但是在排版风格方面，解决问题方式定不会只有一种。你能找出30种不同的组合也只是一个时间的问题。

下面是一个潜在的解决方案。

你可以分别创建`.h1`到`.h6`的样式来应用到你的HTML，而不是利用`-`样式，像这样：

```
<nav class="c-nav">
  <a class="h5" href="#">Link</a>
  <a class="h5" href="#">Link</a>
  <a class="h5" href="#">Link</a>
</nav>

```

我喜欢这种解决方案的简单性，其中有一个排版真理的来源。 您只需访问一个`_typography.scss`文件即可在网站上显示不同排版大小的数量。

现在，虽然`.h1` - `.h6`类的解决方案很棒，但我强烈建议不要用`.h1` - `.h6`为你的类，只是因为它们被隐含地绑在`<h1>`-`<h6>`对象上。

如果你有一个`<h2>`元素，但决定用`.h3`来写样式它会发生什么？ 接管你的代码库的另一个开发人员可能会遇到一个最初的不和他们去_“为什么is_`.h3` _和_`<h2>` _写在一起了？

所以，不是写`.h1`到`.h6`的样式，我给排版类_不同的前缀_，这取决于它们是比我的基本font-size大或更小。 以下是一个例子：

*   `.t1` - 最大的字体大小。

*   `.t2` - 第二大字体大小。

*   `.t3` - 第三大字体大小。

*   `.s1` - 第一字体大小较小的基本字体大小。

*   `.s2` - 第二字体大小较小的基本字体大小。

*     ...

这五个class通常是我每个项目所需的一切（到目前为止）。 这样一个惯例的好处就是能够一目了然地告诉元素的大小。 在下面的例子中，我确定这个链接的尺寸小于我的基本字体大小。

```
`<nav><a class="s1" href="#" >Link</a></nav>`

```

现在，如果您无法控制HTML，但想要控制排版类的大小呢？

对于这种情况，我建议您创建和使用mixins，如下所示：

```
@mixin s1 {
  font-size: 14px;
  line-height: 1.25
}

h1,
nav a {
  @include s1;
}

```

在我们进入下个话题的最后一件事。 要特别注意这一点。

排版类是对象的_子集_。您应该_像排列对象那样将相同的一套规则应用于排版类_。 这意味着你不应该在排版类中添加`margin`或`padding`。而这些`margin`或`padding`应该直接添加到组件。（阅读Harry的[在大型应用上管理排版](https://csswizardry.com/2016/02/managing-typography-on-large-apps/)了解为什么我推荐这个）。

让我们继续。

## “.u-” ——实用类(Utility)

_实用类是用来表现样式的一个非常好的辅助类_。它们做得很好，并且其优先级高超过了其他样式。 因此，它们通常只包含一个属性，并且包含`!important`声明。

例子如下：

```
.u-text-left { text-align: left !important; }
.u-text-center { text-align: center !important; }
.u-text-right { text-align: right !important; }

.u-hide-st-med {
  @media (man-width: 599px) {
    display: none !important;
  }
}

.u-hide-bp-med {
  @media (min-width: 600px) {
    display: none !important;
  }
}

```

我刚才在这里说的几乎是我用于实用类的一切。 我从来没有发现有了这些类还有做不好的事。

唷。闲话不说，咱回到工作/玩耍/学习或任何你正在做的事情，所以让我们来回顾一下。

## 结语

在本文中，我向您展示了如何使用命名空间填补BEM的遗憾。通过包含命名空间，我终于实现了一个好的架构中寻找的所有四个标准：

1. 类必须_尽量少地添加避免HTML膨胀_。

2. 我必须_立即知道组件是否使用JavaScript_。

3. 我必须_立即知道是否可以安全地编辑一个类而不会影响其他任何其他CSS_。

4. 我必须_立即知道每个class是适合于什么_，以防止大脑过载。


总之，我总共使用了七个不同的命名空间。 他们是：

1.  `.l-`: 布局(layouts)

2.  `.o-`: 对象(objects)

3.  `.c-`: 组件(components)

4.  `.js`: js的钩子(JavaScript hooks)

5.  `.is-`|`.has-`: 状态类(state classes)

6.  `.t1`|`.s1`: 排版大小(typography sizes)

7.  `.u-`: 实用类(utility classes)

每个命名空间都有一个功能，可以在整个事物的宏伟计划中进行，进一步加强了样式表中的层次结构。

接下来，我将与大家分享一下如何打破这些我刚才设置的规则（_“嗯，再次?!你真的喜欢打破规则吗？”_😅）以及我如何组织我的CSS文件。

现在，我好奇的听到你的想法。你觉得我使用的命名空间如何？我的“违背专家命名空间”的使用`.o-`和`.c-`对你有帮助/有用吗？还是更让你迷糊呢？我很想听到你在下面的评论中的想法:)

（如果你喜欢这篇文章，[分享出来](http://twitter.com/share?text=Great article by @zellwk: Writing modular CSS (Part 2) — Namespaces.&url=[https://zellwk.com/blog/css-architecture-2/&hashtags=](https://zellwk.com/blog/css-architecture-2/&hashtags=)). 我会很感激你的🤗)
                