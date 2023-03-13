# 基于性能的分析的 5 大 Web APIs(以及如何使用它们)

> 原文：<https://blog.logrocket.com/top-5-web-apis-performance-based-analysis/>

## 介绍

有许多工具可以用来分析应用程序的性能。在本文中，我们将探索内置于所有现代浏览器中的性能 API。浏览器对这些 API 的支持非常好，甚至可以追溯到 Internet Explorer 9。

在页面的生存期内，浏览器忙于在后台收集性能计时数据。它知道导航过程的每一步需要多长时间，并跟踪任何外部资源(静态资产、API 请求等)的连接和下载时间。).

我们将从这个特性的大脑——性能接口——开始，看看它是如何计算时间戳和持续时间的。接下来，我们将研究一些不同的条目类型，以及如何创建我们自己的定制计时来分析任意代码的性能。

*向前跳转:*

## `Performance`界面和高分辨率时间

内置的性能监控 API 都是`Performance`接口的一部分，通过`window.performance`对象公开。它捕获许多性能计时，包括:

*   页面加载和导航
*   资源加载
*   用户定义的性能计数器

当试图捕捉精确的性能计时时，使用`Date.now()`创建时间戳并不理想。在记录时间时，系统时钟可能稍慢或稍快。这被称为[时钟漂移](https://en.wikipedia.org/wiki/Clock_drift)，它在某种程度上影响所有的计算机。

这可能会导致计时不准确，尤其是当与网络时间协议(NTP)服务器的同步导致系统时间调整时。这可能会扭曲与任何先前时间戳的比较。

因此，性能计时改为使用`DOMHighResTimeStamp`。这种时间戳的主要区别在于:

*   时间戳以毫秒为单位，但包括精确到微秒级(通常精确到五微秒)的小数部分
*   时间戳不是与之前的系统时间进行比较，而是表示从*时间起点*开始经过的时间，当页面加载时从零开始

## 性能时间线 API

从页面第一次加载开始(更准确地说，是当[浏览器上下文](https://developer.mozilla.org/en-US/docs/Glossary/Browsing_context)被创建时)，`window.performance`对象维护一个性能事件缓冲区。这是一个动态数据结构，其他操作(如异步请求和自定义用户计时)会向该时间线添加新条目。

### 性能条目

时间轴由实现`PerformanceEntry`接口的对象组成。有不同类型的性能条目，它们由来自不同 API 的子类型提供，但是它们都被收集在单个时间线中。

条目中的属性因子类型而异，但通常都有以下共同点:

*   `name`:条目的标识符。用于名称的值取决于条目类型；许多条目类型在这里使用 URL
*   `entryType`:指定性能输入的类型。每个`PerformanceEntry`子类型指定它自己的`entryType`；例如，一个`PerformanceNavigationTiming`条目具有一个`navigation`的`entryType`
*   `startTime`:相对于时间起点的高分辨率时间戳(通常为`0`，表示上下文创建的时刻)
*   `duration`:另一个定义事件持续时间的高分辨率时间戳

### 从时间轴中查找条目

`Performance`对象有三种方法来查找性能条目。每个方法返回一个`PerformanceEntry`对象的数组:

1.  `getEntries`:返回时间线中的所有条目
2.  `getEntriesByName`:返回给定名称的所有条目。也可以给定一个给定的条目类型来过滤结果
3.  `getEntriesByType`:返回给定类型的所有条目

您还可以通过创建一个`[PerformanceObserver](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver)`来监听运行时添加的新条目。这是一个处理新条目的回调函数，并提供条目类型监听的标准。未来任何符合标准的条目都将被传递给回调。

## 资源计时 API

资源定时 API 为浏览器发出的每个网络请求提供了`PerformanceResourceTiming`条目。这包括异步`XMLHttpRequest`或`fetch`请求、JavaScript 和 CSS 文件(以及引用的资源，如图像)和文档本身。

计时主要与网络事件有关，如 DNS 查找、建立连接、跟踪重定向和接收响应。完整列表可在`[PerformanceResourceTiming documentation](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceResourceTiming)`中查看。

这些条目的`name`属性将引用下载的资源的 URL。条目还包括一个表示请求类型的`initiatorType`属性。常见的启动器类型有`css`、`script`、`fetch`、`link`和`other`。

除了计时信息，每个条目还包括响应正文的大小。

下面是一个来自 [LogRocket 网站](https://logrocket.com)的示例条目，加载了从`link`标签引用的 Avenir 重字体文件:

```
{
    "name": "https://logrocket.com/static/Avenir-Heavy-65df024d7123b4108578ddbe666a9cba.ttf",
    "entryType": "resource",
    "startTime": 211.10000002384186,
    "duration": 104.69999998807907,
    "initiatorType": "link",
    "nextHopProtocol": "h2",
    "workerStart": 0,
    "redirectStart": 0,
    "redirectEnd": 0,
    "fetchStart": 211.10000002384186,
    "domainLookupStart": 211.10000002384186,
    "domainLookupEnd": 211.10000002384186,
    "connectStart": 211.10000002384186,
    "connectEnd": 211.10000002384186,
    "secureConnectionStart": 211.10000002384186,
    "requestStart": 218.80000001192093,
    "responseStart": 286.4000000357628,
    "responseEnd": 315.80000001192093,
    "transferSize": 59155,
    "encodedBodySize": 58855,
    "decodedBodySize": 134548,
    "serverTiming": []
}
```

## 导航定时 API

性能缓冲区中的第一个条目是具有`navigation`的`entryType`的`PerformanceNavigationTiming`条目。只有一个`navigation`条目，它的`startTime`将是`0`，因为它代表时间线的开始。它包含导航到当前页面和加载当前页面时发生的各种事件的计时。

`PerformanceNavigationTiming`从`PerformanceResourceTiming`扩展而来，继承了它所有的网络属性。它还包括与加载 DOM 内容和触发`load`事件相关的计时。

这里是一个`PerformanceNavigationTiming`条目，同样使用 LogRocket 网站:

```
{
    "name": "https://logrocket.com/",
    "entryType": "navigation",
    "startTime": 0,
    "duration": 2886.300000011921,
    "initiatorType": "navigation",
    "nextHopProtocol": "h2",
    "workerStart": 0,
    "redirectStart": 0,
    "redirectEnd": 0,
    "fetchStart": 18.100000023841858,
    "domainLookupStart": 20.600000023841858,
    "domainLookupEnd": 58.80000001192093,
    "connectStart": 58.80000001192093,
    "connectEnd": 110.19999998807907,
    "secureConnectionStart": 78.19999998807907,
    "requestStart": 110.30000001192093,
    "responseStart": 183.60000002384186,
    "responseEnd": 209.19999998807907,
    "transferSize": 93203,
    "encodedBodySize": 92903,
    "decodedBodySize": 468095,
    "serverTiming": [],
    "unloadEventStart": 0,
    "unloadEventEnd": 0,
    "domInteractive": 1371.9000000357628,
    "domContentLoadedEventStart": 1384,
    "domContentLoadedEventEnd": 1385,
    "domComplete": 2879.900000035763,
    "loadEventStart": 2880.199999988079,
    "loadEventEnd": 2886.300000011921,
    "type": "navigate",
    "redirectCount": 0,
    "activationStart": 0
}
```

## 使用用户计时 API 创建自己的计时

有时，除了资源和页面加载之外，您可能还需要测量其他操作。也许您想看看一段 JavaScript 执行得有多好，或者测量两个事件之间的时间。[用户计时 API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API) 允许我们创建“标记”(时间点)和“度量”(测量两个标记之间的时间)。

这些函数在性能时间线中创建新的条目:`PerformanceMark`和`PerformanceMeasure`。

### 创建标记

性能标记是应用程序运行时的一个命名时刻。它用于捕获要监控的重要事件的时间戳。通过用名称调用`performance.mark`来创建标记。

假设我们想计算渲染一个 UI 组件需要多长时间。我们可以捕捉两个标记:一个在渲染开始之前，另一个在渲染完成时:

```
performance.mark('render-start');

// Perform the rendering logic
uiComponent.render();

performance.mark('render-end');
```

这将向时间线添加两个性能条目，这两个条目都具有条目类型`mark`。

我们可以通过获取类型为`mark`的所有性能条目来验证这一点:

```
performance.getEntriesByType('mark');
```

返回的条目数组将包括我们的`render-start`和`render-end`标记。每个标记将开始时间指定为高分辨率时间戳。

### 创建度量

为了计算这些测试之间经过的时间，我们可以调用`performance.measure`。该函数有三个参数:

1.  度量的唯一名称
2.  开始标记的名称
3.  结束标记的名称

```
performance.measure('render', 'render-start', 'render-end');
```

`performance.measure`计算两个命名标记之间的时间。时间度量将被捕获到一个新的性能条目中，在性能时间线中的`entryType`为`measure`。

下面的演示展示了实际操作中的标记和测量。

参见 [CodePen](https://codepen.io) 上 Joe Attardi([@ thinksInCode](https://codepen.io/thinksInCode))
的 Pen [绩效评分与衡量](https://codepen.io/thinksInCode/pen/ZERemzE)。

## 用`PerformanceObserver`观察新的性能条目

到目前为止，我们已经看到了如何调用`getEntries`和`getEntriesByType`来按需检索性能条目。使用`PerformanceObserver` API，我们可以监听新的性能条目。我们可以设置标准来定义我们感兴趣的事件类型，并提供在创建匹配条目时调用的函数。

这个回调被传递给`PerformanceObserver`构造函数。当接收到新条目时，这个函数用一个`PerformanceObserverEntryList`调用，它是一个包含所有新创建条目的可迭代数据结构。观察者本身也作为第二个参数传递给回调。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

一旦我们定义了回调并创建了一个`PerformanceObserver`，它将不会生效，直到观察者的`observe`方法被调用。

以下是监听所有`resource`性能条目的示例:

```
const observer = new PerformanceObserver(list => {
  list.getEntries().forEach(entry => {
    console.log(entry);
  });
});

observer.observe({ type: 'resource' });
```

这里有一个监听资源条目和发送 HTTP 请求的演示:

参见 [CodePen](https://codepen.io) 上 Joe Attardi([@ thinksInCode](https://codepen.io/thinksInCode))
的 Pen [performance observer Demo](https://codepen.io/thinksInCode/pen/oNyWZmR)。

## 浏览器对性能计时 API 的支持

性能计时 API 有很好的浏览器支持。根据来自[caniuse.com](https://caniuse.com/mdn-api_performance)的数据，对核心性能 API 的支持可以追溯到几年前，版本有:

*   铬 6
*   边缘 12
*   野生动物园 8
*   火狐 7
*   甚至 Internet Explorer 支持这些 API 回到 IE9！

## 使用性能 API 的利与弊

像任何工具一样，使用这些 API 有优点也有缺点。

使用性能计时 API 来分析应用程序性能的最大优势在于，它们不需要外部库或服务来捕获数据。它们与浏览器很好地集成在一起，特别是为了导航和资源计时的目的。为这些性能条目提供了详细信息。时序精度非常高，不受时钟漂移的影响。

导航计时在上下文创建的瞬间开始收集，因此不会像使用外部工具那样丢失数据。

这些工具的缺点是它们本身通常不足以成为真正的解决方案。尽管它们捕获了许多关于浏览器会话的有用数据，但仍然存在一些缺口，您可能需要外部库或服务来填补。

### 报告数据

我们需要一种方法将数据从用户的电脑中取出，并很可能放入一个分析工具中，以使其发挥作用。这从客户端来说并不是很难解决；我们只需要把现有的数据打包送到某个地方。

T2 信标 API T3 可能是一个很好的解决方案。这是一个轻量级的“单向”HTTP 请求，特别适合发送分析数据。信标和使用`XMLHttpRequest`或`fetch`的传统异步请求有一些不同:

*   **数据保证会被发送**:如果我们发起一个获取请求，而用户在请求发送之前离开了我们的应用，数据就会丢失。一旦启动，即使我们离开或关闭标签，也会发送一个信标
*   **请求是“发射并忘记”**:一旦信标被发送，浏览器不等待响应。请求是单向的，因为任何返回的响应都将被丢弃，并且不能被执行
*   **限于`POST`** **请求**:信标无法通过`PUT`或`PATCH`等其他方式发送请求

请注意，任何版本的 Internet Explorer 都不支持信标。

### 汇总数据

来自性能时间线的数据大部分是原始计时。没有元数据，如操作系统或浏览器版本。这些数据必须单独获得并充分匿名，以便与性能计时一起发送。

### 数据不是持久的

缓冲区中的所有性能条目都是特定于当前页面的。这对于[单页应用](https://blog.logrocket.com/micro-frontend-apps-single-spa/)来说不是问题，但是对于多页应用来说，当导航离开时，所有先前的性能数据都会丢失。

这可以通过在离开页面时发送一个信标，或者使用一些中间的持久存储( [web 存储](https://blog.logrocket.com/web-storage-made-simple-use-local-storage-state/)、IndexedDB 等)很容易地解决。).

### 限制

Performance API 的目的完全是为了捕获性能计时。它不提供任何其他类型的数据。如果您需要监控用户行为或其他指标，您将需要使用其他工具。

## 摘要

性能 API 可能只是整体分析解决方案的一部分，但它们是性能计时信息的宝贵来源，否则很难准确捕获。更好的是，我们可以免费获得它，不需要加载任何额外的 JavaScript 或手动触发任何数据收集！

我们确实需要做一些额外的工作来打包数据并将其发送到集合中，但这是内置于当今浏览器中的一个非常强大的工具。它在移动设备浏览器上也得到很好的支持。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.