# JavaScript 中的可选链接和 nullish 合并

> 原文：<https://blog.logrocket.com/optional-chaining-and-nullish-coalescing-in-javascript/>

可选链接和无效合并是新的 JavaScript 操作符。它们都达到了 [TC39](https://tc39.es/) 过程中的[阶段 3](https://tc39.es/process-document/) ，这意味着它们的规格是完整的。

我期待这些运营商很久了。我相信它们是自[async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)/[wait](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)以来对 JavaScript 工效学最重大的改进。就功能而言，它们没有带来任何新的东西，但它们会让相当多的代码变得更好读和写。

## 可选链接

在 JavaScript 中处理数据经常会遇到不确定某样东西是否存在的情况。例如，想象一下从天气 API 获得一个 JSON 响应。

```
{
  "data": {
    "temperature": {
      "current": 68,
      "high": 79,
      "low": 45
    },
    "averageWindSpeed": 8
  }
}
```

你可以通过物体的每一层来获得高温。

```
const highTemperature = response.data.temperature.current;
```

但是，您可能会请求过去不同日期的天气数据，而该服务在某些日期没有高温，或者在其他日期根本没有任何温度数据。所以`temperature`或`temperature.high`可能是`undefined`。

```
{
  "data": {
    "averageWindSpeed": 12
  }
}
```

在这种情况下，试图获得高温将导致许多开发人员在使用 JavaScript: `TypeError: Cannot read property 'current' of undefined`时非常熟悉的异常。

为了避免异常，您必须为对象的每个级别添加检查。可能 API 文档上说出错的时候，顶级属性会是`error`而不是`data`，所以你甚至不能确定`data`的存在。

```
let highTemperature;
if (response.data && response.data.temperature) {
  highTemperature = response.data.temperature.high;
}
```

这段代码更安全，但也更冗长。我们的数据甚至没有嵌套那么深；更复杂的对象可能要检查更多的级别。

可选的链接提供了一个简洁的选择。它是 JavaScript 版本的[安全导航操作符](https://en.wikipedia.org/wiki/Safe_navigation_operator)，在很多语言中都存在，比如 [Swift](https://docs.swift.org/swift-book/LanguageGuide/OptionalChaining.html) 和 [C#](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-) 。使用可选的链接操作符(`?.`)，我们的代码应该是这样的:

```
const highTemperature = response.data?.temperature?.high;
```

这仍然是安全的，但几乎和原始代码一样简洁。如果`response.data`或`response.data.temperature`是`null`或`undefined`，整个表达式[将短路](https://en.wikipedia.org/wiki/Short-circuit_evaluation)并返回`undefined`，而不是抛出异常。

当通过括号符号访问属性时，可选链接的工作方式是相同的。

```
const property = "temperature";
const highTemperature = response.data?.[property]?.high;
```

它不限于子级别。您也可以在顶级使用它。

```
const highTemperature = response?.data?.temperature?.high;
```

可选链接甚至适用于函数调用。

```
const celsiusTemperature = temperature.toCelsius?.();=
```

如果`temperature`没有`toCelsius`属性，这将导致`undefined`，而不是抛出一个错误。但是，请注意，如果`temperature`碰巧有一个`toCelsius`属性不是函数，这仍然会导致错误:`TypeError: temperature.toCelsius is not a function`。

## 无效合并

除了访问嵌套值，JavaScript 中的另一种常见模式是使用逻辑 OR 运算符(`||`)来合并值，因为它返回第一个[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)操作数，而不是布尔值。

假设你正在建立一个网站，并添加了一些动画。您已决定允许用户自定义动画时长。如果用户没有提供默认值，您希望使用默认值，因此您执行以下操作。

```
const defaultTime = 2;
const animationTime = settings.animationTime || defaultTime;
```

这段代码在一般情况下可能可以工作，但是有一个微妙的错误。布尔型`false`、空字符串(`""`)、`NaN`和数字`0`都是假的。在这个例子中，用户可能根本不想要任何动画。但是如果他或她将时间设置为`0`，该代码将忽略它并错误地使用默认值`2`。

我们可以说得更清楚。

```
const defaultTime = 2;
const animationTime =
  typeof settings.animationTime === "number"
    ? settings.animationTime
    : defaultTime;
```

nullish 合并操作符(`??`)给了我们一个更简洁的方法。

```
const defaultTime = 2;
const animationTime = settings.animationTime ?? defaultTime;
```

Nullish 合并的行为类似于常规合并，但它只拒绝严格为`null`或`undefined`的值，因此如果提供了值`0`，该代码将接受该值。

与常规合并一样，一旦操作数令人满意，无效合并就会短路，因此不会对进一步的表达式求值。如果进一步的表达有副作用，记住这一点很重要。

## 结论和生态系统支持

可选的链接和 nullish 合并使得编写更安全的代码变得更加容易，JavaScript 社区似乎急于采用它们。尽管它们还不是正式的 ECMAScript 规范的一部分，但是工具已经开始增加支持。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.