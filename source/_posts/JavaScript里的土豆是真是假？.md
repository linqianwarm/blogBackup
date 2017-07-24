---
title: JavaScript里的土豆是真是假？
date: 2017-07-12 19:18:41
tags: JavaScript
---

本翻译同时发布在平台<a href="http://www.zcfy.cc/article/truth-equality-and-javascript-8211-javascript-javascript-8230-3148.html" target="_blank">众成翻译</a>
附上<a href="https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108" target="_blank">原文链接</a>
PS: 本文的一些点经常容易忘记，收藏一下随时来看看哦

<hr>

你没有必要因为自己是一个JavaScript初学者而被以下这些迷惑...

```
if ([0]) {
    console.log([0] == true); //false
    console.log(!![0]); //true
}

```

或者这样…

```
if ("potato") {
    console.log("potato" == false); //false
    console.log("potato" == true); //false
}

```
<!-- more -->
好消息是，所有浏览器都得遵循一个标准。 有些作者会告诉你害怕胁迫和写码反对它。 我希望说服你，强制规则的特征我们可以利用（或至少理解），而不是去逃避...

x是true吗？x等于y？真假问题和等于运算，属于JavaScript的三个主要领域的核心问题：条件语句与运算符（if，三目运算，&&，||等），等于运算符（==）和严格相等运算符（===）。 让我们看看在每种情况下会发生什么...

### 条件语句Conditionals

在JavaScript中，所有条件语句和运算符都遵循相同的强制模式。 我们将以`if`语句为例。

构造`if`（_表达式_）_声明_将强制使用抽象方法将_表达式_转换为布尔值，在 [ES5 spec](http://ecma262-5.com/ELS5_HTML.htm）的**ToBoolean ** )中定义了以下算法规则：

   参数类型  |  结果
      :---       |              :---
 Undefined  |  **false**
      Null      |  **false**
   Boolean   | 结果等于输入参数（无转换）
   Number   | 如果参数为+0，-0或NaN，结果为**false**;</br>否则结果是**true**
    String     | 如果参数是空字符串（其长度为零），则结果为false。</br>否则结果为**true**。
    Object    |  **true**

这是JavaScript用于将值分类为_true_（`true`，`“potato”`，`36`，`[1,2,4]`和`{a：16}`）或_false_（`false` ，`0`，`""`，`null`和`undefined`）。

现在我们知道了为什么，在之前介绍的例子中，`if（[0]）`允许进入后面的块：一个数组是一个对象，所有的对象被强制转换为`true`。

以下还有几个例子。 一些结果可能令人惊讶，但他们始终坚持上述规定的简单规则：

```
var trutheyTester = function(expr) {
    return expr ? "truthey" : "falsey"; 
}

trutheyTester({}); //truthey (一个object永远都是true)

trutheyTester(false); //falsey
trutheyTester(new Boolean(false)); //truthey (一个object!)

trutheyTester(""); //falsey
trutheyTester(new String("")); //truthey (一个object!)

trutheyTester(NaN); //falsey
trutheyTester(new Number(NaN)); //truthey (一个object!)

```

### 等于运算(==)

这个两个等号‘==’版本规则是相当宽松的。 即使左右两边是不同的类型，值也可以被认为是相等的，因为在执行比较之前，操作者将强制将一个或两个比较者转换为单一类型（通常为数字）。许多开发人员有点担心这种操作，无疑这至少有一个知名的JavaScript大师，会建议大家避免使用==操作符。

这种避免策略让我烦恼，因为除非你完全知道理解它，你才能掌握这种语言，而恐惧和逃避是知识的敌人。 另外假装忽略‘==’的存在并不会让你感到轻松了，因为在JavaScript强制转换是无处不在的！它在条件表达式（如我们刚刚看到的）中，它在数组索引中，它在连接操作处等等。 更强大的是，当我们能安全地使用它时，可以写出简洁，优雅和可读性高的代码。

无论如何，让我们看看ECMA定义的‘==’如何运作的方式。 它真的不是那么吓人。 只要记住`undefined`和`null`彼此相等（而没有别的），大多数其他类型被强制到一个数字以便于比较：

Type(x) | Type(y) | Result
    ---    |      ---    |
x、y 相同类型 | x、y 相同类型  |  **请参阅严格平等（===）算法**
    null      |  Undefined    |   **true**
Undefined |        null       |   **true**
Number     |      String      | x == toNumber(y)
String        |     Number    | toNumber(x) == y
Boolean     |        (any)     | toNumber(x) == y
(any)         |      Boolean   | x == toNumber(y)
String or Number | Object | x == toPrimitive(y)
Object      | String or Number | toPrimitive(x) == y
其他的类型组合 | 其他的类型组合 | **false**

其中结果是表达式，则重新应用算法，直到结果为布尔值。 toNumber和toPrimitive是根据以下规则转换参数的js内部方法：

#### **ToNumber**

   参数类型    |   结果
          ---          |      ---
    Undefined    |   **NaN**
        Null         |   **+0**
     Boolean      |    如果参数是**true**结果为 **1** .</br>如果参数是false结果为 **+0** .
      Number     |   结果等于输入（无转化）
        String      |    有效计算Number(_string_) </br>“abc” -> NaN</br>“123” -> 123
        Object     |    遵循以下步骤:</br>1\. 让 _primValue_进行 ToPrimitive(_input argument_, hint Number).</br>2\. 返回 ToNumber(_primValue_).

#### **ToPrimitive**

参数类型 | Result
 --- | ---
Object | （在等于运算符强制转换的情况下）如果`valueOf`返回一个原始值，则返回它。如果不是，则进行`toString`，如果返回一个原始值，则返回。 否则会出错
其他 | 结果等于输入参数 (没有转换).

这里有一些例子 —— 我将使用代码逐步演示如何应用各种强制转换规则

**[0] == true;** 

```
//‘等于’检查...
[0] == true; 

//看看是怎样运作的...
//对boolean使用toNumber转换
[0] == 1;
//对object使用toPrimitive转换
//[0].valueOf() 返回并不符合原始类型primitive，继续...
//[0].toString() 返回 "0"
"0" == 1; 
//对左边的string使用toNumber转换
0 == 1; //false!

```

**“potato” == true;** 

```
//校验是否等于...
"potato" == true; 

//运作方式...
//对boolean使用toNumber转换
"potato" == 1;
//对string使用toNumber转换
NaN == 1; //false!

```

**“potato” == false;** 

```
//校验是否等于...
"potato" == false; 

//运作方式...
//对boolean使用toNumber转换
"potato" == 0;
//对string使用toNumber转换
NaN == 0; //false!

```

**object使用valueOf** 

```
//校验是否等于...
crazyNumeric = new Number(1); 
crazyNumeric.toString = function() {return "2"}; 
crazyNumeric == 1;

//运作方式...
//对object使用toPrimitive转换
//valueOf返回了一个primitive，所以我们用它
1 == 1; //true!

```

**object使用toString** 

```
//校验是否等于...
var crazyObj  = {
    toString: function() {return "2"}
}
crazyObj == 1; 

//运作方式...
//对object使用toPrimitive转换
//valueOf返回一个object，所以使用toString
"2" == 1;
//对string使用toNumber转换
2 == 1; //false!

```

### 严格相等运算符 (===)

这个很容易.如果运算符左右的类型不同，结果总是false。 如果它们是同一类型，则应用直观的等式测试：对象标识符必须引用相同的对象，字符串必须包含相同的字符集，其他原语必须共享相同的值。 `NaN`，`null`和`undefined`永远不会===另一个类型。 `NaN`甚至不===本身。

类型(x)     |   值   |     结果
   ---  |  --- |  --- 
类型(x) 与 类型(y)不同 |            |  **false**
Undefined or Null        |            |  **true**
Number              |  x 和 y 值相同 (但是不是`NaN`)     | **true**
String                 |  x 和 y 为相同字符                         | **true**
Boolean              |  x 和 y 都是true或者false               | **true**
Object                |  x 和 y 引用同一个对象                  | **true**
其他情况…         |                      |**false**

一些使用等于判断不佳的例子

```
//没必要
if (typeof myVar === "function");

//更好
if (typeof myVar == "function");

```

..因为 `typeOf` 是返回一个字符串的, 所以这个等于运算符总是在比较两个字符串. 因此 == 是 100% 的全等于。

```
//没必要
var missing =  (myVar === undefined ||  myVar === null);

//更好
var missing = (myVar == null);

```

…null 和 undefined 是始终等于（==）自己或对方的.

注意：由于`undefined`变量可能被重新定义（非常小）的风险，所以等于null是稍微更安全的。

```
//没必要
if (myArray.length === 3) {//..}

//更好
if (myArray.length == 3) {//..}

```

…不用我再解释，大家一定都懂啦 😉
                