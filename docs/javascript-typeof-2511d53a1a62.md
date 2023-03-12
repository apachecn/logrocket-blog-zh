# JavaScript typeof:理解 JavaScript 中的类型检查

> 原文：<https://blog.logrocket.com/javascript-typeof-2511d53a1a62/>

每种编程语言的一个非常重要的方面是它的类型系统和数据类型。对于像 Java 这样的严格类型编程语言，变量被定义为特定的类型，将变量限制为只包含该类型的值。

> 然而，JavaScript 是一种动态类型语言，尽管存在一些支持严格类型的扩展，如 [TypeScript](https://www.typescriptlang.org/) 。

使用 JavaScript，有可能一个变量开始时包含一个`string`，而在其生命周期的很长一段时间后，变成对一个`object`的引用。甚至在脚本执行过程中，JavaScript 引擎会隐式强制值的类型。类型检查对于编写可预测的 JavaScript 程序非常重要。

> JavaScript 有一个非常基本的用于类型检查的操作符`[typeof](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)`。

但是，您会注意到使用这个操作符可能会产生误导，这一点我们将在本文中讨论。

## javascript 日期

在看用`typeof`进行类型检查之前，看一下 JavaScript 数据类型是很重要的。虽然本文没有深入讨论 JavaScript 数据类型的细节，但是随着研究的深入，您可以收集到一些信息。

在 ES6 之前，JavaScript 有六种数据类型。在 ES6 规范中，增加了`Symbol`类型。以下是所有类型的列表:

1.  线
2.  数字
3.  布尔型(值`true`和`false`)
4.  null(值`null`)
5.  未定义(值`undefined`)
6.  标志
7.  目标

前六种数据类型被称为**原始类型**。除了前六种数据类型之外，其他数据类型都是一个*对象*，并且可以被称为**引用类型**。一个*对象*类型只是名称和值对形式的属性集合。

注意列表中的`null`和`undefined`是原始的 JavaScript 数据类型，每个数据类型只包含一个值。

你可能会开始想:那么*数组*、*函数*、*正则表达式*等等呢？它们都是特殊种类的物体。

*   一个`**array**`是一种特殊的对象，它是一个有序的编号值集合，具有特殊的语法和特征，这使得对它的处理不同于对常规对象的处理。
*   `**function**`是一种特殊的对象，有一个可执行脚本块与之相关联。脚本块通过调用函数来执行。它还有一个特殊的语法和特征，使它不同于其他常规对象。

JavaScript 有几个对象类构造函数，用于创建其他类型的*对象*，例如:

*   `Date` —用于创建日期对象
*   `RegExp` —用于创建正则表达式
*   `Error` —用于创建 JavaScript 错误

## 使用`typeof`进行类型检查

### 句法

JavaScript 中的`typeof`操作符是一个一元操作符(只接受一个操作数),其计算结果是一个指示其操作数类型的字符串。就像其他一元运算符一样，它放在操作数之前，用空格隔开:

```
typeof 53; *// "number"*
```

然而，有一种替代语法允许您像函数调用一样使用`typeof`,方法是将其操作数放在括号中。这对于对 JavaScript 表达式返回的值进行类型检查非常有用:

```
typeof(typeof 53); *// "string"*
```

### 错误安全

在 ES6 之前，`typeof`操作符总是返回一个字符串，而不管它被用于哪个操作数。

> 对于未声明的标识符，`typeof`将返回`“undefined”`，而不是抛出一个`ReferenceError`。

```
console.log(undeclaredVariable === undefined); // ReferenceError
console.log(typeof undeclaredVariable === 'undefined'); // tru
```

然而，在 ES6 中，使用`let`或`const`关键字声明的块范围变量，如果在初始化之前与`typeof`操作符一起使用，仍然会抛出一个`ReferenceError`。这是因为:

> 块范围的变量保持在[时间死区](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_Dead_Zone)中，直到它们被初始化:

```
// Before block-scoped identifier: typeof => ReferenceError

console.log(typeof tdzVariable === 'undefined'); // ReferenceError

const tdzVariable = 'I am initialized.';
```

### 类型检查

下面的代码片段显示了使用`typeof`操作符对公共值进行的类型检查:

```
console.log(typeof ""); // "string"
console.log(typeof "hello"); // "string"
console.log(typeof String("hello")); // "string"
console.log(typeof new String("hello")); // "object"

console.log(typeof 0); // "number"
console.log(typeof -0); // "number"
console.log(typeof 0xff); // "number"
console.log(typeof -3.142); // "number"
console.log(typeof Infinity); // "number"
console.log(typeof -Infinity); // "number"
console.log(typeof NaN); // "number"
console.log(typeof Number(53)); // "number"
console.log(typeof new Number(53)); // "object"

console.log(typeof true); // "boolean"
console.log(typeof false); // "boolean"
console.log(typeof new Boolean(true)); // "object"

console.log(typeof undefined); // "undefined"

console.log(typeof null); // "object"

console.log(typeof Symbol()); // "symbol"

console.log(typeof []); // "object"
console.log(typeof Array(5)); // "object"

console.log(typeof function() {}); // "function"
console.log(typeof new Function); // "function"

console.log(typeof new Date); // "object"

console.log(typeof /^(.+)$/); // "object"
console.log(typeof new RegExp("^(.+)$")); // "object"

console.log(typeof {}); // "object"
console.log(typeof new Object); // "object"
```

注意，所有的对象类型构造函数，当用关键字`new`实例化时，总是有一个类型`“object”`。唯一的例外是`Function`构造函数。

以下是结果的简单总结:

| 价值 | 类型 of |
| --- | --- |
| `undefined` | `"undefined"` |
| `null` | `"object"` |
| `true`或`false` | `"boolean"` |
| 所有数字或`NaN` | `"number"` |
| 所有字符串 | `"string"` |
| 所有符号 | `"symbol"` |
| 所有功能 | `"function"` |
| 所有阵列 | `"object"` |
| 本地对象 | `"object"` |
| 宿主对象 | *取决于实施* |
| 其他对象 | `"object"` |

## 更好的类型检查

上一节的类型检查结果表明，一些值将需要额外的检查来进一步区分它们。例如:当使用`typeof`操作符完成类型检查时，`null`和`[]`都是`“object”`类型。

对价值的额外检查可以通过利用一些其他特征来完成:

*   使用`instanceof`操作符
*   检查对象的`constructor`属性
*   使用对象的`toString()`方法检查对象类

### 检查是否为空

正如您已经看到的，使用`typeof`操作符检查`“null”`值没有任何好处。检查`“null”`值的最好方法是对该值和`null`关键字进行严格的相等比较，如下面的代码片段所示。

```
function isNull(value) {
  return value === null;
}
```

这里使用严格的相等运算符(`===`)非常重要。下面的代码片段使用`undefined`值说明了这种重要性:

```
console.log(undefined == null); // true
console.log(undefined === null); // false
```

### 正在检查 NaN

`NaN`是算术运算导致未定义的值无法表示时收到的特殊值。比如:`(0 / 0) => NaN`。同样，当试图将一个没有原始数字表示的非数值转换成一个数字时，结果是`NaN`。

> 任何涉及到`NaN`的算术运算将总是计算为`NaN`。

如果你真的想在任何形式的算术运算中使用一个值，那么你要确保这个值不是`NaN`。

使用`typeof`操作符检查`NaN`值返回`“number”`。要检查`NaN`值，您可以使用全局`isNaN()`函数，或者最好使用 ES6:

```
console.log(isNaN(NaN)); // true
console.log(isNaN(null)); // false
console.log(isNaN(undefined)); // true
console.log(isNaN(Infinity)); // false

console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN(null)); // false
console.log(Number.isNaN(undefined)); // false
console.log(Number.isNaN(Infinity)); // false
```

> `NaN`值有一个非常特殊的特征。通过比较，它是唯一不等于任何其他值的 JavaScript 值，包括它本身:

```
var x = NaN;

console.log(x == NaN); // false
console.log(x === NaN); // false
```

您可以按如下方式检查`NaN`:

```
function isNan(value) {
  return value !== value;
}
```

上述函数非常类似于 ES6 中添加的`Number.isNaN()`的实现，因此可以用作非 ES6 环境的 polyfill，如下所示:

```
Number.isNaN = Number.isNaN || (function(value) {
  return value !== value;
})
```

最后，您可以利用 ES6 中添加的`Object.is()`函数来测试值是否为`NaN`。`Object.is()`函数检查两个值是否相同:

```
function isNan(value) {
  return Object.is(value, Number.NaN);
}
```

### 检查数组

使用`typeof`检查数组将返回`“object”`。有几种方法可以更好地检查数组，如下面的代码片段所示:

```
// METHOD 1: constructor property
// Not reliable
function isArray(value) {
  return typeof value == 'object' && value.constructor === Array;
}

// METHOD 2: instanceof
// Not reliable since an object's prototype can be changed
// Unexpected results within frames
function isArray(value) {
  return value instanceof Array;
}

// METHOD 3: Object.prototype.toString()
// Better option and very similar to ES6 Array.isArray()
function isArray(value) {
  return Object.prototype.toString.call(value) === '[object Array]';
}

// METHOD 4: ES6 Array.isArray()
function isArray(value) {
  return Array.isArray(value);
}
```

## 泛型类型检查

正如数组一样，`Object.prototype.toString()`方法对于检查任何 JavaScript 值的对象类型非常有用。当使用`call()`或`apply()`在一个值上调用它时，它以`**[object Type]**`的格式返回对象类型，其中`Type`是对象类型。

考虑下面的代码片段:

```
function type(value) {
  var regex = /^[object (S+?)]$/;
  var matches = Object.prototype.toString.call(value).match(regex) || [];

  return (matches[1] || 'undefined').toLowerCase();
}
```

下面的代码片段显示了使用刚刚创建的`type()`函数进行类型检查的结果:

```
console.log(type('')); // "string"
console.log(type('hello')); // "string"
console.log(type(String('hello'))); // "string"
console.log(type(new String('hello'))); // "string"

console.log(type(0)); // "number"
console.log(type(-0)); // "number"
console.log(type(0xff)); // "number"
console.log(type(-3.142)); // "number"
console.log(type(Infinity)); // "number"
console.log(type(-Infinity)); // "number"
console.log(type(NaN)); // "number"
console.log(type(Number(53))); // "number"
console.log(type(new Number(53))); // "number"

console.log(type(true)); // "boolean"
console.log(type(false)); // "boolean"
console.log(type(new Boolean(true))); // "boolean"

console.log(type(undefined)); // "undefined"

console.log(type(null)); // "null"

console.log(type(Symbol())); // "symbol"
console.log(type(Symbol.species)); // "symbol"

console.log(type([])); // "array"
console.log(type(Array(5))); // "array"

console.log((function() { return type(arguments) })()); // "arguments"

console.log(type(function() {})); // "function"
console.log(type(new Function)); // "function"

console.log(type(class {})); // "function"

console.log(type({})); // "object"
console.log(type(new Object)); // "object"

console.log(type(/^(.+)$/)); // "regexp"
console.log(type(new RegExp("^(.+)$"))); // "regexp"

console.log(type(new Date)); // "date"
console.log(type(new Set)); // "set"
console.log(type(new Map)); // "map"
console.log(type(new WeakSet)); // "weakset"
console.log(type(new WeakMap)); // "weakmap"
```

## 额外的事实:一切都不是物体

很有可能在某个时候，你可能会遇到这样一句话:

> " JavaScript 中的一切都是对象."—(错误)

这可能会非常误导人，事实上，**不是真的**。JavaScript 中的一切都不是对象。原语不是对象。

你可能会开始疑惑——如果原语不是对象，为什么我们可以对它们进行以下操作？

*   `**(“Hello World!”).length**` —获取字符串的`length`属性
*   `**(“Another String”)[8]**` —获取索引处字符串的字符`8`
*   `**(53.12345).toFixed(2)**` —在数字上调用`Number.prototype.toFixed()`方法

我们之所以可以用原语来实现这些，是因为 JavaScript 引擎隐式地为原语创建了一个对应的 ***包装对象*** ，并在其上调用方法或访问属性。

当值返回后，包装对象被丢弃并从内存中移除。对于前面列出的操作，JavaScript 引擎隐式执行以下操作:

```
// wrapper object: new String("Hello World!")
(new String("Hello World!")).toLowerCase();

// wrapper object: new String("Another String")
(new String("Another String"))[8];

// wrapper object: new Number(53.12345)
(new Number(53.12345)).toFixed(2);
```

## 结论

在本文中，您已经了解了 JavaScript 类型系统及其数据类型，以及如何使用`typeof`操作符执行类型检查。

您还看到了使用`typeof`操作符进行类型检查会产生多大的误导。最后，您看到了为某些数据类型实现可预测类型检查的几种方法。

如果你有兴趣获得一些关于 JavaScript `typeof`操作符的附加信息，你可以参考[这篇文章](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)。

快乐编码…

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.