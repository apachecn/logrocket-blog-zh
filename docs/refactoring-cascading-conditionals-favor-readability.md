# 重构级联条件有利于可读性

> 原文：<https://blog.logrocket.com/refactoring-cascading-conditionals-favor-readability/>

JavaScript 是一种非常灵活的编程语言，可以用来构建你能想到的任何东西，从网站、web 应用程序和桌面应用程序，到智能设备的用户界面、服务器端应用程序等等。

JavaScript 的灵活性造就了它广泛的特性集——但是，正如我们所知，它也是引发开发者想象力的一些奇怪行为的原因。我们编写的一些代码非常适合以聪明优雅的方式解决这些奇怪的问题；有些不是。

在本文中，我们将重点分析开发人员替换冗长且令人困惑的条件语句的不同方式——特别是级联的`if/else if`和`switch`语句。为什么？因为在 JS 中，我们可以做得比仅仅使用`if`更好。

## 三元、`&&`和`||`运算符

让我们使用`if`引入一个带有条件语句的简单函数，并使用[三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)重构它。

```
if (condition) {
   return functionTrue();
} else {
   return functionFalse();
}

```

我们上面的例子没有问题，但是我们确实不必要地占用了几行代码来重复关键字`return`。三元运算符可以简化:

```
return condition ? functionTrue() : functionFalse();

```

那不是简单多了吗？但是它是如何工作的呢？

三元运算符是唯一一个接受三个操作数的 JavaScript 运算符:一个条件后跟一个问号(`?`)，一个 true 条件表达式后跟一个冒号(`:`)，最后一个 falsy 条件表达式。它看起来是这样的:

```
condition ? expressionIfTrue : expressionIfFalse

```

注意，`true`和`false`表达式都必须提供，三元运算符才能工作。但是如果我们只需要在条件真实的时候做一些事情呢？

JavaScript 通过使用操作符`&&`和`||`提供了简化表达式的替代方法。

让我们看一个不同的例子，我们只需要在条件满足时执行一个语句。

```
if (condition) {
   console.log("it's true!");
}

```

我们可以使用`&&`将这条语句改写成一行代码，如下所示:

```
condition && console.log("it's true!");

```

这样做的主要原因是 JavaScript 从左到右读取条件语句中的操作数，并在使参数无效时退出。因此，在`&&`的例子中，如果第一个语句为 false，那么对下一个语句求值就没有意义，因为整个表达式都是 false。

类似地，`||`操作符将继续计算操作数，直到其中一个为`true`，或者整个表达式计算为`false`。看看下面的例子:

```
trueCondition || console.log("Hello world!"); // does not execute the console.log
falseCondition || console.log("Hello world!"); // executes the console.log

```

## 评估表达式的多个结果

通常，当我们阅读或编写代码时，我们会发现多个嵌套的`if`条件——比如在下面的函数中，它获取一种水果的名称并返回它的颜色。

```
function getColor(fruit) {
   if (fruit.toLowerCase() === 'apple') {
       return 'red';
   } else if (fruit.toLowerCase() === 'banana') {
       return 'yellow';
   } if (fruit.toLowerCase() === 'orange') {
       return 'orange';
   } if (fruit.toLowerCase() === 'blueberry') {
       return 'blue';
   } if (fruit.toLowerCase() === 'lime') {
       return 'green';
   }

   return 'unknown';
}

```

即使代码按预期执行了它的功能，也有一些事情我们可以做得更好。首先，对于每个水果，方法`toLowerCase`被多次调用，这不仅会影响性能，还会降低整个函数的可读性。

下一个优化将是避免重复条件，这减少了我们可能引入错误的实例的数量，例如忘记在我们的一行中包含`toLowerCase`方法。

我们可以通过在函数开始时只调用一次方法并评估每个结果来快速解决这个问题——但是我们可以通过使用`[switch](https://www.w3schools.com/js/js_switch.asp)`语句做得更好。

```
function getColor(fruit) {
   switch(fruit.toLowerCase()) {
       case 'apple':
           return 'red';
       case 'banana':
           return 'yellow';
       case 'orange':
           return 'orange';
       case 'blueberry':
           return 'blue';
       case 'lime':
           return 'green';
       default:
           return 'unknown';
   }
}

```

这样看起来好多了，但还是感觉不对。有很多重复的关键词，读起来很混乱。

下面是一种不同的方法——一种更智能、更优雅的方法，就像我们在本文开头讨论的那样。

```
function getColor(fruit) {
   const fruits = {
       'apple': 'red',
       'banana': 'yellow',
       'orange': 'orange',
       'blueberry': 'blue',
       'lime': 'green',
   };

   return fruits[fruit.toLowerCase()] || 'unknown';
}

```

简直美。很容易识别每种颜色对应的水果，我们不会重复关键字，而且它被清楚地阅读和理解。

这种解决级联`if`语句的方法叫做跳表。它不仅仅适用于简单的文本或常量；让我们看一个更复杂的例子。

## 构建地图对象

跳转表方法对于简单的文本和常量来说很棒，但是在更复杂的情况下，比如当`if`语句有多行带有函数调用的代码时，它如何工作呢？

既然我们已经了解了如何简化语句，那么处理这些更复杂场景的方法就很简单了——这完全是关于我们如何构建地图对象。

让我们用两个数字和一个操作作为参数构建一个`calculate`函数，并返回两个数字的操作结果。

```
function calculate(number1, number2, operation) {
   const operations = {
       '+': (a, b) => a + b,
       '-': (a, b) => a - b,
       '*': (a, b) => a * b,
       '/': (a, b) => a / b,
   }

   return operations[operation]?.(number1, number2) ?? 'invalid operation';
}

```

正如预期的那样，代码看起来非常干净，每个操作都被明确地分配了一个函数来执行必要的计算，以获得我们想要的结果。

看起来有点不同，也许很奇怪的是`return`语句；但是背后的想法很简单，我们来分解一下。

```
operations[operation]?.(number1, number2)

```

表达式的第一部分将简单地从字典中返回给定的操作，如果键存在，则执行该函数。如果键不存在，将返回`undefined`。这最后一部分要感谢[可选链接操作符](https://blog.logrocket.com/optional-chaining-and-nullish-coalescing-in-javascript/)。

第二部分使用[无效合并运算符](https://blog.logrocket.com/6-cutting-edge-javascript-features-you-can-use-today/)，当其左侧操作数为`null`或`undefined`时，返回其右侧操作数，否则返回其左侧操作数。

```
?? 'invalid operation';

```

因此，在我们的例子中，当操作出现在字典中时，它将返回操作的结果，或者它将返回一个`invalid operation`。

## 结论

JavaScript 是一种灵活的语言，它提供了多种解决问题的方法。在本文中，我们学习了传统`if`语句的各种替代方法，可以帮助您编写更清晰、更好的代码。

在你的代码库中有多种选择是很重要的，因为没有一种解决方案适合每种情况。此外，JavaScript 还在不断发展，随着新版本的推出，将会引入或发现新的方法，因此保持联系和阅读最新文章会有所帮助。

感谢阅读！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.