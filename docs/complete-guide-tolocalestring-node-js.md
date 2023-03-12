# Node.js 中的 toLocaleString 完全指南

> 原文：<https://blog.logrocket.com/complete-guide-tolocalestring-node-js/>

## 介绍

世界上使用的不同语言非常多样化。语言不仅在词汇上不同，在句子和单词结构上也不同。这些变化引发了 web 开发人员以对语言敏感的格式呈现信息的需求。

`toLocaleString`方法是一种方便的功能，用于对日期、数字、时间、货币和数据结构(如 JavaScript 中的数组和类型化数组)进行语言敏感的格式化。`toLocaleString`方法使用环境的默认语言环境进行格式化。但是，您可以使用它来格式化不同于默认语言的语言。

这样做是必要的，不仅是因为上面强调的不同语言之间的差异，而且是因为同一种语言中存在的差异。同一种语言有几种方言和地区差异是很常见的，比如英语，世界各地说的都略有不同。

本文将向您介绍`toLocaleString`方法，并解释如何在 Node 中使用它。

### 内容

## `toLocaleString`方法介绍

正如在简介中已经提到的，`toLocaleString`用于将日期、数字、时间、货币以及一些数据类型和数据结构转换成对语言敏感的字符串表示。

不管对象是什么，都可以使用`toLocaleString`方法。它将`locales`和`options`对象作为参数。两个参数都是可选的。如果不传递它们，运行时将使用缺省值:

```
toLocaleString(locales, options)

```

如果您想要一个不同于默认语言环境的语言环境，`locales`参数应该是一个语言标签或者是一个语言标签数组。一个语言标签，通常被称为 BCP 47 语言标签，通常是由一个连字符分隔的一个或多个子标签的序列。语言标签中唯一需要的子标签是主要语言子标签。

但是，有些语言具有附加属性，您可以使用这些属性来缩小由“主要语言”子标签标识的语言范围。一个典型的例子是英语，它因地区而异。英语的主要语言子标签是`en`。

由于地区差异，您可以使用 region 子标签将其缩小到英语的特定变体。下表显示了一些英语语言标签以及相应的主要和地区子标签。

| 语言标签 | 主要语言子标签 | 区域子标签 | 地区 |
| --- | --- | --- | --- |
| en-GB | （构成动词）表示“使处于…状态” | 千兆字节 | 联合王国 |
| en-US | （构成动词）表示“使处于…状态” | 美国 | 美国人 |
| 恩奥(奥省) | 在中 | 你好 | 澳大利亚 |

您还可以为支持它们的语言添加变体和脚本子标记。 [IANA 语言子标签注册表](http://www.iana.org/assignments/language-subtag-registry)包含一个子标签列表。如果您传递一个区域设置数组，请从最高优先级到最低优先级排列它们；如果支持，运行库将使用第一个区域设置，然后在列表中下移。

`options`参数是用于定制`toLocaleString`方法行为的对象。它的属性很大程度上取决于要格式化的数据类型；格式化数字的选项不同于日期和时间对象的选项。

## 如何使用数字的`toLocaleString`方法

正如前面几节所指出的，您可以使用`toLocaleString`方法来生成数字的区域识别字符串表示。您可以用它来表示科学和工程符号中的普通数字、追加单位、显示百分比和格式化货币。

如前几节所述，`toLocaleString`有两个可选参数。用它来格式化数字也不例外。

### 如何格式化货币

使用`toLocaleString`方法，您可以使用作为第一个参数传递的语言约定将数字格式化为货币。为此，您需要将第二个参数的`style`属性设置为`currency`。

您还必须将`currency`属性的值设置为 [ISO 4217 货币代码](https://www.six-group.com/en/products-services/financial-information/data-standards.html#scrollTo=currency-codes)之一，否则会出现错误。下面的代码展示了如何使用`toLocaleString`进行货币格式化:

```
console.log(
  (-15000).toLocaleString("en-US", {
    style: "currency",
    currency: "USD",
    currencySign: "accounting",
  })
); // => ($15,000.00)

console.log(
  (15000).toLocaleString("en-US", { style: "currency", currency: "JPY" })
); // => ¥15,000

console.log(
  (15000).toLocaleString("fr-FR", { style: "currency", currency: "JPY" })
); // => 15 000 JPY

console.log(
  (15000).toLocaleString("fr-FR", {
    style: "currency",
    currency: "JPY",
    currencyDisplay: "name",
  })
); // => 15 000 yens japonais

console.log(
  (15000).toLocaleString("en-GB", {
    style: "currency",
    currency: "USD",
    currencyDisplay: "narrowSymbol",
    currencySign: "accounting",
  })
); // => $15,000.00

```

如上面代码中的第一个示例所示，将`currencySign`属性设置为`accounting`将格式化一个负数，并将其用一对括号括起来。`currencySign`属性的默认值为`standard`。

### 如何在科学和工程符号中格式化数字

您还可以使用`toLocaleString`方法用简单的科学和工程符号来表示数字。可以通过将 options 参数的`notation`属性设置为`scientific`、`engineering`或`compact`来实现。默认值为`standard`，用于普通数字格式。

以下是如何在给定的语言环境中用简单的科学、工程和简洁的符号表示数字的示例:

```
console.log(
  Math.LOG10E.toLocaleString("fr-FR", {
    notation: "scientific",
    maximumSignificantDigits: 5,
  })
); // => 4,3429E-1

console.log(
  Math.PI.toLocaleString("en-US", {
    notation: "compact",
    compactDisplay: "short",
  })
); // => 3.1

console.log(
  Math.E.toLocaleString("de-DE", {
    notation: "standard",
    maximumFractionDigits: 5,
  })
); // => 2,71828

console.log(
  (0.0034595).toLocaleString("en-US", {
    notation: "engineering",
    minimumSignificantDigits: 2,
    maximumSignificantDigits: 3,
  })
); // => 3.46E-3

console.log((2000).toLocaleString("en-US", { notation: "scientific" })); // => 2E3

console.log((2000).toLocaleString("en-US", { notation: "standard" })); // => 2,000

```

查看文档以获得科学和工程格式选项的完整列表。

### 如何格式化单位

您可以使用`toLocaleString`方法通过将第二个参数的`style`属性设置为`unit`来追加和格式化单元。这些单元可以是简单的，也可以是复合的。ECMAScript 标准有一个当前支持的简单单位的完整列表，比如英里、小时、秒、比特和字节。

另一方面，您可以通过使用`-per-`分隔符连接两个受支持的简单单元来生成复合单元。例如，`mile-per-hour`复合单元是`mile`和`hour`简单单元的衍生物。

如果您传递一个简单单元或一个由不允许在 ECMAScript 中使用的简单单元组成的复合单元，Node 将抛出一个错误:

```
console.log(
  (80).toLocaleString("en-GB", {
    style: "unit",
    unit: "mile-per-hour",
    unitDisplay: "long",
  })
); // => 80 miles per hour

console.log(
  (80).toLocaleString("en-GB", {
    style: "unit",
    unit: "mile-per-hour",
    unitDisplay: "narrow",
  })
); // => 80mph

console.log(
  (80).toLocaleString("en-GB", {
    style: "unit",
    unit: "mile-per-hour",
    unitDisplay: "short",
  })
); // => 80 mph

console.log(
  (40).toLocaleString("de-DE", {
    style: "unit",
    unit: "kilobyte-per-second",
    unitDisplay: "narrow",
  })
); // => 40 kB/s

console.log(
  (80).toLocaleString("en-US", {
    style: "unit",
    unit: "megabyte",
  })
); // => 80 MB

```

使用 options 对象的`unitDisplay`属性来控制单位的格式。`unitDisplay`属性取值为`long`、`short`和`narrow`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

将数字表示为百分比类似于追加单位。但是，您需要将`style`属性的值设置为`percent`:

```
console.log(
  (0.56).toLocaleString("de-DE", {
    style: "percent",
  })
); // 56 %

console.log(
  (200).toLocaleString("en-US", {
    style: "percent",
  })
); // 20,000%

```

## 如何使用带有日期和时间的`toLocaleString`方法

与数字一样，您也可以使用`toLocaleString`方法进行日期和时间格式化。像往常一样，`locales`参数是 BCP 47 语言标签的一个字符串或者这样的字符串的一个数组。`options`参数是一个可以用来定制`toLocaleString`行为的对象。

它有几个日期和时间格式的属性。我们不会在这里一一介绍。但是，下面是一些您可能会使用的常见属性以及它们在指定语言环境下的预期输出:

```
const date = new Date(2011, 3, 10, 10, 30, 10);

console.log(
  date.toLocaleString("en-US", {
    dateStyle: "long",
    timeStyle: "long",
  })
); // => April 10, 2011 at 10:30:10 AM GMT+3

console.log(
  date.toLocaleString("en-US", {
    dateStyle: "long",
    timeStyle: "long",
    calendar: "ethiopic",
  })
); // => Miazia 2, 2003 ERA1 at 10:30:10 AM GMT+3

console.log(
  date.toLocaleString("en-US", {
    timeZone: "America/Chicago",
    dayPeriod: "short",
    year: "numeric",
    month: "long",
    day: "numeric",
    hour: "2-digit",
    minute: "2-digit",
    second: "2-digit",
    timeZoneName: "long",
  })
); // => April 10, 2011 at 02:30:10 at night Central Daylight Time

```

请注意，可以同时使用的 options 对象的属性是有限制的。例如，您可以一起使用`dateStyle`和`timeStyle`属性，但不能与`hour`、`month`和`weekday`等属性一起使用。您需要查看文档以了解 options 对象的哪些属性不能一起使用。

除了通常的日期和时间格式，您还可以在特定的日历中设置日期格式。下面的代码显示了我在埃塞俄比亚日历中的当地日期。如果你不熟悉，埃塞俄比亚历法大约比广泛使用的公历晚八年。

当我写这篇文章的时候，公历是 2022 年，而埃塞俄比亚历是 2014 年。您可以在文档中查找几种受支持的日历:

```
console.log(
  new Date().toLocaleString("en-US", {
    calendar: "ethiopic",
    dateStyle: 'full'
  })
); // Thursday, Sene 2, 2014 ERA1

```

## 如何对数组使用`toLocaleString`方法

当您对数组使用`toLocaleString`时，您会得到一个表示数组元素的字符串。您可以传入前面章节中描述的`locales`和`options`参数。如果数组的元素是数字，您可以使用数字格式选项或日期和时间格式选项(如果它们是日期对象):

```
const nums = [1200, 3000, 4500];
console.log(
  nums.toLocaleString("de-DE", {
    style: "unit",
    unit: "liter",
    unitDisplay: "narrow",
  })
); // 1.200 l,3.000 l,4.500 l

```

在上面的例子中，我们使用了`toLocaleString`方法来格式化`de-DE`地区的数字数组。

## `Intl`界面介绍

虽然本文的重点是在`toLocaleString`方法上，但是`Intl`是另一个强大的接口，用于数字、日期和时间的语言敏感的字符串表示。它的用法非常类似于`toLocaleString`法。

`Intl`接口有像`Intl.NumberFormat`和`Intl.DateTimeFormat`这样的构造函数，可以用来格式化数字和字符串，而不是使用`toLocaleString`方法。在使用构造函数像`toLocaleString`方法一样进行格式化之前，创建一个构造函数的实例。构造函数将区域设置和选项作为参数，如`toLocaleString`:

```
const numberFormat = new Intl.NumberFormat(locale, options);
const dateTimeFormat = new Intl.DateTimeFormat(locale, options);

```

下面的代码说明了如何使用`Intl.NumberFormat`构造函数格式化数字:

```
console.log(new Intl.NumberFormat('en-GB', { style: 'unit', unit: 'kilobyte-per-second'}).format(20)) // 20 kB/s

```

与`toLocaleString`方法不同，您将 locale 和 options 参数传递给构造函数并调用`format`实例方法。与`toLocaleString`一样，两者都是可选参数。

## 结论

在 JavaScript 中，`toLocaleString`方法是可以用于区分语言的数字、货币、日期和时间格式的功能之一。虽然不太常见，但也可以用它来格式化数组和类型化数组。

它的用法包括将区域设置或区域设置数组作为第一个参数传递，将 options 对象作为第二个参数传递，以定制`toLocaleString`方法的行为。但是，参数是可选的。如果您不传递它们，Node 将使用缺省值。

日期和数字`toLocaleString`方法与`Intl`接口的相应构造函数有很多共同之处。因为它们被用于相同的目的，并接受相同的参数，所以您需要阅读`Intl`接口构造函数的文档，以获得对`toLocaleString`的更多了解。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.