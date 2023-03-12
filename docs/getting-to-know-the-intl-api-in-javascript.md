# 在 JavaScript - LogRocket 博客中了解 Intl API

> 原文：<https://blog.logrocket.com/getting-to-know-the-intl-api-in-javascript/>

随着应用程序开始被越来越多的人采用，跨不同时区和地区个性化用户体验变得越来越重要。

在过去，实现这一点的综合解决方案是通过几个库，如 [momentjs](https://momentjs.com/) 、 [luxon](https://moment.github.io/luxon/index.html) 、 [date-fns](https://date-fns.org/) 等。

Javascript `Intl` API 最近增加了一些功能，作为定制用户体验的一个选项值得一提。

`Intl` API 也有一个构造函数，它添加了一些特殊的格式。以前，当连接一个字符串数组时，这需要用一个`utils`函数来完成。

根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) 的说法，`Intl`对象是 ECMAScript 国际化 API 的名称空间，并提供语言敏感的字符串比较、数字格式以及日期和时间格式。

对象提供了对几个构造函数以及国际化构造函数和其他语言敏感函数的访问。

理论已经足够了——让我们看一些实际工作的例子:

```
const date = new Date();
const locale = "en-US"
const engUsFormat = new Intl.DateTimeFormat(locale).format(date);
console.log(engUsFormat); // 4/23/2020
```

只需几行代码，我们就有了一个本地化的日期。

所有这些都可以在一行代码中完成，但是为了强调和便于理解，我把它分解了。

我们有一个名为`engUsFormat`的变量，我们可以在我们的应用程序中重用它，而无需重复。

`Intl.DateTimeFormat`构造函数收到的一个非常重要的参数是 locale 参数。

理想情况下，我们希望根据我们的应用程序被访问的位置动态地获取这些信息:

```
// this result will vary depending on your location and user setting preference.
const locale = navigator.language
console.log(locale); // "en-US"
```

### 焦点所在的区域

**NB** :你不需要对 locale 选项有更深入的理解就能理解本文的其余部分或者使用`Intl`对象。

然而，如果你想知道这是如何工作的，请随意阅读。

区域设置是代表一组用户首选项的字符串，包括但不限于以下内容:

*   日期和时间(即，我们应该使用阿拉伯日历还是中国日历显示日期)
*   数字和货币(即，我们应该使用罗马数字还是数字、英镑还是美元)
*   时区、语言和国家
*   测量单位(即千克或磅等)。)

区域设置参数必须是 [BCP 47](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#locales_argument) 语言标签中的字符串。它由连字符分隔，带有一些可选和必需部分，例如:

```
"en-US" // only the en is compulsory the US is additional information that helps customization
"ja-JP-u-ca-japanese" // only the ja is compulsory.
```

到目前为止，我们已经看到了`Intl`全局对象的一瞥。但是，有几个添加到命名空间的构造函数值得一提:

*   `DateTimeFormat`
*   `NumberFormat`
*   `Collator`
*   `ListFormat`
*   `PluralRules`
*   `RelativeTimeFormat`

我们将详细探讨上面的一些内容，并在我们的应用程序中查看这些内容的可能用例。

`intl`还有选项参数，这给了我们很大的灵活性:

```
const options = {
year: "2-digit",
month: "short",
day: "2-digit",
hour: "numeric",
minute: "numeric",
weekday: "long",
hour12: true,
};

console.log(new Intl.DateTimeFormat("en-US", options).format(new Date()));
"Saturday, Apr 25, 20, 5:44 PM"
```

位于 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat) 的本指南提供了可能选项的完整列表。

### `NumberFormat`

我们可以使用此构造函数通过以可读格式显示数字来格式化数字，并通过提供选项参数来格式化货币:

```
console.log(new Intl.NumberFormat("en-Us").format(1234567890));
"1,234,567,890"
console.log(new Intl.NumberFormat("de-DE").format(1234567890));
"1.234.567.890"
```

对于格式化货币:

```
//for the American Dollar
new Intl.NumberFormat("en-Us", {
style: "currency",
currency: "USD",
minimumFractionDigits: 2
}).format(7654);
"$7,654.00"

//for the British Pounds
new Intl.NumberFormat("en-Us", {
style: "currency",
currency: "GBP",
minimumFractionDigits: 2
}).format(7654);
"£7,654.00"
```

### `RelativeTimeFormat`

此构造函数用于将日期和时间转换为用户友好的可读格式。

这是以前`momentjs`独有的非常好的功能之一。

不是所有的浏览器都支持这个功能。

```
const relativeTimeFormat = new Intl.RelativeTimeFormat("en-US");
relativeTimeFormat.format(10, 'seconds');
"in 10 seconds"
relativeTimeFormat.format(-10, 'seconds');
"10 seconds ago"
relativeTimeFormat.format(-5, 'month');
"5 months ago"
relativeTimeFormat.format(3, 'year');
"in 3 years"
```

### `ListFormat`

此构造函数用于用合取或析取连接字符串数组，以形成有意义的短语。当没有提供类型时，它默认为一个连接词。

```
const listFormat = new Intl.ListFormat("en-US");
listFormat.format(['Dafe', 'Daneil', "Gbolahan", "Kelani", "David"]);
"Dafe, Daneil, Gbolahan, Kelani, and David"

const listFormatOr = new Intl.ListFormat("en-US", {type: 'disjunction'});
listFormatOr.format(["Beans", "Rice", "Plantian"])
"Beans, Rice, or Plantian"
```

## 结论

很多时候，如果我们利用内置的`Intl`全局对象，当根据用户偏好格式化日期和时区时，不需要使用外部库。

它为我们的应用程序节省了一些额外的 JavaScript 字节来解析和减少大小和加载时间。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.