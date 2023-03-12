# BigInt 使用指南

> 原文：<https://blog.logrocket.com/why-and-how-to-use-bigint/>

过去，开发人员为使用极大数字的程序而苦恼，因为 JavaScript `Number`原语对它能正确表示的最小值和最大值有限制。

因此，这导致了许多不稳定的解决方法，例如将大值转换为字符串，或者将工作外包给第三方供应商，这导致了错误和/或大的构建规模。

但是随着 ECMAScript 规范中引入了`BigInt`原语，开发人员不再需要依赖脆弱的工作区或第三方库。相反，`BigInt`允许他们安全地处理超出`Number`原语限制的数字。

在本文中，我们将了解是什么促使将`BigInt`原语添加到 ECMAScript 规范中，以及`BigInt`如何解决这个问题，最后，我们将了解如何开始使用`BigInt`。

## 为什么要用`BigInt`？

因为在 JavaScript 中使用`Number`和数据类型有很多限制。

在 JS 中，`Number`数据类型使用 IEEE 754 定义的格式将 JavaScript 中的所有数字表示为双精度浮点数，这意味着 JavaScript 中的数字表示为双精度浮点数，简称 double。

按照惯例，因为`Number`原语将所有数字表示为双精度数，所以它们总是被分配 64 位内存。有了它，从-1.7 *10^308 到 1.7* 10^308 的数字都可以用变量来表示和存储。

不幸的是，我们无法可靠地处理这个范围内的所有数字，因为其中大多数都是*不安全的整数*——引用多个真实数字的数字表示。

发生这种情况是因为即使一个特定的真实世界的数字不能根据 IEEE 754 格式精确地表示，它也将使用一种标准的“舍入模式”进行舍入，以便强制该数字遵守该格式。

结果呢？计算机会对某些数字进行舍入，使它们等于其他不需要舍入的数字，以确保它们符合格式。

本质上，这些不安全的整数没有自己的私有表示；相反，它们错误地共享了不需要进行舍入以符合格式的其他真实世界数字的表示。

这里有一个例子:

```
// JS defines the maximum safe interger as a constant Number.MAX_SAFE_INTEGR
const safeInt = Number.MAX_SAFE_INTEGER // -> 9_007_199_254_740_991

// If we add one we get
safeInt + 1 // -> 9_007_199_254_740_992 ✅

// If we add 2...
safeInt + 2 // -> 9_007_199_254_740_992 🤦🏾‍♂️

// Therefore 9_007_199_254_740_992 or (2^53) is deemed unsafe because two real world numbers 9_007_199_254_740_992 and 9_007_199_254_740_993 are represented through it. That is why

safeInt + 1 === safeInt + 2 // -> true

```

那么，这意味着什么呢？使用大于或小于`Number.MAX_SAFE_INTEGR`或`Number.MIN_SAFE_INTEGER`的数字肯定会导致错误。

我们中的许多人可能不需要担心这一点，因为我们使用的数值范围完全在`Number.MAX_SAFE_INTEGR`和`Number.MIN_SAFE_INTEGR`的范围内。

尽管如此，一些开发人员不得不在这些界限之外工作，例如那些从事金融工作的人，或者发现自己不断地执行难以置信的大量计算的人。

好在有解决办法:`BigInt`。

## 什么是`BigInt`？

`BigInt`是 JavaScript 中相对较新的数值原语/整数类型。它的创建是为了解决人们在使用`Number`原语和安全整数限制时遇到的限制。

`BigInt`表示任意精度的数字，这意味着它使用所需的空间来存储和表示大数，而不是像`Number` integer 类型那样使用固定数量的内存来强行表示它们。

你可以把`BigInt`和`Number`想象成静态和动态数组。`BigInt`如果需要的话，当表示一个大的数字时，会使用更多的空间，比如一个动态数组。但是`Number`只会利用最初分配给它的固定内存来表示数字，就像静态数组一样。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使我们能够处理大量数字，而不必担心潜在的精度损失(数字)或妨碍准确性和产生错误的奇怪表示问题。

## 开始使用`BigInt`

要创建一个`BigInt`，只需在任意整数文字的末尾添加`n`。注意，用小数/浮点数这样做会抛出一个`RangeError`:

```
// This is alright
const bigInteger = 1000000000000000000000000n

// This will throw a RangeError
const bigInteger = 1.5n // -> RangeError

// You can also create a BigInt with negative numbers
const negativeBigIntInteger = -1111111n // -> -1111111n

```

或者，您也可以使用全局`BigInt`函数传递一个整数作为参数。

```
// This is also alright
const bigIntefer = BigInt(1000000000000000000000000000000000)

// This will still throw a RangeError
const bigInteger = BigInt(1.5)

```

也可以使用字符串、二进制、十六进制或八进制符号来实例化文字。

```
// Strings
BigInt("1111111111111111111111111111111111111")
// -> 1111111111111111111111111111111111111n

// Binary
BigInt(0b100000000000000000000000000000000000000000000000000000000000000000000001111111)
// -> 151115727451828646838272n

// Hexadecimal
BigInt(0xfffffffffffffffffffffffffffffffffff9fff9fffffffffffffffff)
// -> 95780971304118053647396689196894323976171195136475136n

// Octal
BigInt(0o40000000000000000000000000000000000000000011112444)
// -> 713623846352979940529142984724747568191373312n

```

你不能用严格相等(`===`)来比较一个`BigInt`和一个常规的`Number`，因为`BigInt`本身就是一个原语。

因此，在`BigInt`文字上调用`typeof`将返回`"bigint"`而不是`"number"`，导致它们之间的严格比较返回 false。

```
const a = 111111n
const b = 111111

a === b // -> false

```

然而，如果您使用抽象等式(`==`)，那么比较一个值为`11n`的`BigInt`文本和一个值为`11`的`Number`文本将返回`true`，因为两个文本的值相同。

```
const a = 11n
const b = 11

a == b // -> true

```

所有算术运算(`+`、`-`、`/`、`*`)都可以在`BigInt`文字上执行，一元加号除外。比如你不能像写`+11`一样写`+11n`。

另一方面，你可以用`++`增加`BigInt`的字面量，用`--`减少它们。

而且，带`BigInt`字面值的算术必须在`BigInt`字面值之间。在涉及`BigInt`的算术运算中，`Number`文字不能作为操作数。试图这样做将导致一个`TypeError`。

```
// We can do this
11n + 12n // -> 23n

// But we can't do this
11n + 12 // -> TypeError

```

另外，因为`BigInt`算法返回一个`BigInt`，返回值将总是一个`"bigint"`类型的整数。

```
5n / 3n // -> 1n

19n / 2n // -> 9n

```

`BigInt`大于`0n`的文字都被强制为`true`。而`0n`，则被胁迫为`false`。

```
if (5n) {
        // Code block will run
}

if (0n) {
        // Code block will not run
}

```

同样，`BigInt(true)`会返回`1n`。

```
BigInt(true) === 1n // -> true

```

`BigInt`全局函数包含两个静态方法，这两个方法将限制`BigInt`表示使用一定数量的位，这些位被指定为两个方法的第一个参数。

一旦`BigInt`在指定的空间限制内，它将作为有符号或无符号整数返回，这取决于使用的方法。

第一个方法`BigInt.asIntN(bits, <bigInt-number>)`将`<bigInt-number>`作为有符号整数返回。

第二种方法，`BigInt.asUintN(bits, <bigInt-number>)`将`<bigInt-number>`作为无符号整数返回。

这些方法可能对显式内存管理有用。我们知道，默认情况下，`BigInt`使用尽可能多的位来表示一个数，但是，如果您内存不足，并且知道您的应用程序的数值范围，那么这些方法会很有用。

```
// For representing BigInt numbers as signed integers
BigInt.asIntN(bits, <BigInt>)

// For representing BigInt numbers as unsigned integers
BigInt.asUintN(bits, <BigInt>)

```

## 结论

读完这篇文章后，你有望对什么是`BigInt`、它解决的问题以及如何使用它有更深的理解。

感谢您的阅读！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.