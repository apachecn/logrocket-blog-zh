# 如何实际使用性能 API 测量性能

> 原文：<https://blog.logrocket.com/how-to-practically-use-performance-api-to-measure-performance/>

从历史上看，我们在性能监控客户端的性能指标方面的信息非常有限。我们还遇到了 API 浏览器的限制，这阻碍了我们准确地测量性能。

幸运的是，由于新的面向性能的 API，这种情况正在开始改变。现在，浏览器的[性能 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API) 提供了精确测量网页性能的工具。

在我们深入研究这些性能 API 是什么之前，让我们看看为什么应该使用它们的一些令人信服的理由。

### 使用性能 API 的好处

*   这些 API 增强了在开发工具中使用性能分析的体验
*   Chrome 开发工具和其他类似 Lighthouse 的工具只在开发阶段有用。但是有了性能 API，我们可以在生产中获得真实用户测量(RUM)。
*   我们可以获得非常精确的时间戳，这使得对这些性能指标的分析非常准确。

现在我们来谈谈这些 API 是什么。

“性能 API 是高分辨率时间 API 的一部分，但由[性能时间线 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_Timeline) 、[导航计时 API](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_timing_API) 、[用户计时 API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API) 和[资源计时 API](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API) 增强。”–MDN

每当阅读有关 Performance API 的内容时，您会遇到一系列令人困惑的术语，如高分辨率时间、性能时间线 API 等，这使得很难理解它到底是什么以及如何利用它来测量 web 性能。

让我们分解这些术语，以便更好地理解。

### 高分辨率时间

高分辨率时间精确到几分之一毫秒。

相比之下，基于`Date`的时间只精确到毫秒。这种精度使其成为精确测量时间的理想选择。

由用户代理(UA)测量的高分辨率时间不随系统时间的任何变化而变化，因为它取自 UA 创建的全局时钟。

在 Performance API 中测量的每个度量都是高分辨率时间。这就是为什么你总是听到性能 API 是高分辨率时间 API 的一部分。

### 绩效时间线 API

[性能时间线 API](https://www.w3.org/TR/performance-timeline-2/) 是性能 API 的扩展。该扩展提供了基于特定过滤标准检索性能指标的接口。

Performance Timeline API 提供了以下三种方法，包含在`performance`接口中:

*   `getEntries()`
*   `getEntriesByName()`
*   `getEntriesByType()`

每个方法都返回一个从 Performance API 的所有其他扩展中收集的性能条目列表。

`[PerformanceObserver](https://www.w3.org/TR/performance-timeline-2/#dom-performanceobserver)`是 API 中包含的另一个接口。它在给定的性能条目列表中观察新的条目，并通知相同的条目。

### 绩效条目

我们用性能 API 度量的东西被称为`entries`。这些是我们可用的性能条目:

*   `mark`
*   `measure`
*   `navigation`
*   `resource`
*   `paint`
*   `frame`

我们将利用这些条目和各自的 API 来测量性能。

### 我们能测量什么？

让我们看一些我们可以用这些 API 做的实际测量。

### 使用导航定时 API 和资源定时 API

这两个 API 之间有很大的重叠，所以我们将一起讨论它们。

两者都用于衡量不同的资源。我们不会深入这个重叠的细节，但是如果你好奇的话，你可以看看这个[处理模型](https://www.w3.org/TR/navigation-timing-2/#processing-model)，它可能会帮助你更好地理解这个重叠。

```
// Get Navigation Timing entries:
const navigationEntries = performance.getEntriesByType("navigation")[0]; // returns an array of a single object by default so we're directly getting that out.

// output:
{
  "name": "https://awebsite.com",
  "entryType": "navigation",
  "startTime": 0,
  "duration": 7816.495000151917,
  "initiatorType": "navigation",
  "nextHopProtocol": "",
  "workerStart": 9.504999965429306,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 39.72000000067055,
  "domainLookupStart": 39.72000000067055,
  "domainLookupEnd": 39.72000000067055,
  "connectStart": 39.72000000067055,
  "connectEnd": 39.72000000067055,
  "secureConnectionStart": 0,
  "requestStart": 39.72000000067055,
  "responseStart": 6608.200000133365,
  "responseEnd": 6640.834999969229,
  "transferSize": 0,
  "encodedBodySize": 0,
  "decodedBodySize": 0,
  "serverTiming": [],
  "unloadEventStart": 0,
  "unloadEventEnd": 0,
  "domInteractive": 6812.060000142083,
  "domContentLoadedEventStart": 6812.115000095218,
  "domContentLoadedEventEnd": 6813.680000137538,
  "domComplete": 7727.995000081137,
  "loadEventStart": 7760.385000146925,
  "loadEventEnd": 7816.495000151917,
  "type": "navigate",
  "redirectCount": 0
}
```

```
// Get Resource Timing entries
const resourceListEntries = performance.getEntriesByType("resource");
```

这将返回一个资源计时对象数组。单个对象看起来会像这样:

```
{
  "name": "https://awebsite.com/images/image.png",
  "entryType": "resource",
  "startTime": 237.85999999381602,
  "duration": 11.274999938905239,
  "initiatorType": "img",
  "nextHopProtocol": "h2",
  "workerStart": 0,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 237.85999999381602,
  "domainLookupStart": 237.85999999381602,
  "domainLookupEnd": 237.85999999381602,
  "connectStart": 237.85999999381602,
  "connectEnd": 237.85999999381602,
  "secureConnectionStart": 0,
  "requestStart": 243.38999995961785,
  "responseStart": 244.40500000491738,
  "responseEnd": 249.13499993272126,
  "transferSize": 0,
  "encodedBodySize": 29009,
  "decodedBodySize": 29009,
  "serverTiming": []
}
```

*   **测量 DNS 时间**:当用户请求一个 URL 时，查询域名系统(DNS)将一个域翻译成一个 IP 地址。

导航和资源计时都暴露了两个与 DNS 相关的指标:

–`domainLookupStart`:标记 DNS 查找开始的时间。
–`domainLookupEnd`:标记 DNS 查找结束的时间。

```
// Measuring DNS lookup time
const dnsTime = navigationEntries.domainLookupEnd - navigationEntries.domainLookupStart;
```

**抓住了**:如果主机没有设置正确的`[Timing-Allow-Origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Timing-Allow-Origin)`响应头，那么`domainLookupStart`和`domainLookupEnd`都可能成为第三方提供的资源的`0`。

*   **测量请求和响应时间**

导航和资源计时都用这些指标描述请求和响应-

*   `fetchStart`标记浏览器开始获取资源的时间。这并不直接标记浏览器何时对资源发出网络请求，而是标记它何时开始检查缓存(如 HTTP 和服务工作者缓存)以查看网络请求是否必要。
*   `requestStart`是浏览器发出网络请求的时间
*   `responseStart`是响应的第一个字节到达的时间
*   `responseEnd`是响应的最后一个字节到达的时间
*   `workerStart`标记何时从服务人员处获取请求。如果没有为当前页面安装服务人员，这将始终是`0`。

```
// Request + Request Time
const totalTime = navigationEntries.responseEnd - navigationEntries.requestStart;
// Response time with cache seek
const fetchTime = navigationEntries.responseEnd - navigationEntries.fetchStart;

// Response time with Service worker
let workerTime = 0;
if (navigationEntries.workerStart > 0) {
workerTime = navigationEntries.responseEnd - navigationEntries.workerStart;
}

// Time To First Byte
const ttfb = navigationEntries.responseStart - navigationEntries.requestStart;

// Redirect Time
const redirectTime = navigationEntries.redirectEnd - navigationEntries.redirectStart;
```

```
const headerSize = navigationEntries.transferSize - navigationEntries.encodedBodySize;
```

`transferSize`是包括 HTTP 头的资源的总大小。
`encodedBodySize`是资源*的压缩大小，不包括* HTTP 头。
`decodedBodySize`是资源解压缩后的大小(同样，不包括 HTTP 头)。

*   **测量资源的加载时间**

```
resourceListEntries.forEach(resource => {
  if (resource.initiatorType == 'img') {
    console.info(`Time taken to load ${resource.name}: `, resource.responseEnd - resource.startTime);
  }
});
```

`initiatorType`属性返回启动性能条目的资源类型。在上面的例子中，我们只关心图像，但是我们也可以检查`script`、`css`、`xmlhttprequest`等。

*   **获取单个资源的指标**

我们可以通过使用`getEntriesByName`来做到这一点，它通过名称获得一个性能条目。这里是该资源的 URL:

```
const impResourceTime = performance.getEntriesByName("https://awebsite.com/imp-resource.png");
```

此外，我们还可以使用文档处理指标，如`domInteractive`、`domContentLoadedEventStart`、`domContentLoadedEventEnd`和`domComplete`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

属性传达了文档的加载时间。

### 使用绘制计时 API

绘画是浏览器的任何活动，包括在浏览器窗口上绘制像素。我们可以用这个 API 来衡量“第一次绘画”和“第一次满意的绘画”。
`first-paint:`浏览器在页面上绘制第一个像素的点
`first-contentful-paint`:绘制第一点内容的点——即在 DOM 中定义的东西。这可以是文本、图像或画布渲染。

```
const paintEntries = performance.getEntriesByType("paint");
```

这将返回由两个对象组成的数组:

```
[
  {
    "name": "first-paint",
    "entryType": "paint",
    "startTime": 17718.514999956824,
    "duration": 0
  },
  {
    "name": "first-contentful-paint",
    "entryType": "paint",
    "startTime": 17718.519999994896,
    "duration": 0
  }
]
```

从这些条目中，我们可以提取出指标:

```
paintEntries.forEach((paintMetric) => {
  console.info(`${paintMetric.name}: ${paintMetric.startTime}`);
});
```

### 使用用户计时

用户计时 API 为我们提供了可以在应用程序的不同位置调用的方法，这让我们可以跟踪时间被用在了哪里。

我们可以测量脚本的性能，特定的 JavaScript 任务需要多长时间，甚至用户与页面交互的延迟。

这个 API 提供的 mark 方法是我们的用户计时分析工具包中的主要工具。

它为我们存储了一个时间戳。`mark()`的超级有用之处在于，我们可以命名时间戳，API 会将名称和时间戳作为一个单元记忆下来。

在应用程序的不同地方调用`mark()`可以让我们计算出在我们的 web 应用程序中达到那个标记所花的时间。

```
performance.mark('starting_calculations')
const multiply = 82 * 21;
performance.mark('ending_calculations')

performance.mark('starting_awesome_script')
function awesomeScript() {
  console.log('doing awesome stuff')
}
performance.mark('ending_awesome_script');
```

一旦我们设置了一组计时标记，我们就想找出这些标记之间经过的时间。

这就是`measure()`方法发挥作用的地方。

`measure()`方法计算标记之间经过的时间，也可以测量我们的标记与 [PerformanceTiming](http://www.w3.org/TR/navigation-timing/#sec-navigation-timing-interface) 界面中任何一个众所周知的事件名称之间的时间，比如`paint`、`navigation`等。

`measure`方法接受 3 个参数:首先是度量本身的名称(可以是任何东西)，然后是开始标记的名称，最后是结束标记的名称。

因此，上面带有`measure`的例子应该是:

```
performance.mark('starting_calculations')
const multiply = 82 * 21;
performance.mark('ending_calculations')
+ performance.measure("multiply_measure", "starting_calculations", "ending_calculations");

performance.mark('starting_awesome_script')
function awesomeScript() {
  console.log('doing awesome stuff')
}
performance.mark('starting_awesome_script');
+ performance.measure("awesome_script", "starting_awesome_script", "starting_awesome_script");
```

为了得到我们所有的`measure`,我们可以使用我们可信赖的`getEntriesByType`:

```
const measures = performance.getEntriesByType('measure');
    measures.forEach(measureItem => {
      console.log(`${measureItem.name}: ${measureItem.duration}`);
    });
```

这个 API 对于缩小我们的 web 应用程序中的性能热点非常有用，可以创建一个清晰的时间消耗图。

* * *

厉害！我们已经收集了各种性能指标。现在，我们可以将所有这些数据发送回我们的监控工具，或者将其存储在某个地方供以后分析。

请记住，这些 API 并不是到处都有。但是，重要的是，像`getEntriesByType`这样的方法不会在找不到任何东西时抛出错误。

因此，我们可以检查`getEntriesByType`是否返回了任何内容，然后进行我们的 PerformanceAPI 测量:

```
if (performance.getEntriesByType("navigation").length > 0) {
  // We have Navigation Timing API
}
```

### 额外收获:使用性能 API 和木偶师

[puppeter](https://pptr.dev)是一个无头节点库，它提供了一个高级 API 来通过 [DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)控制 Chrome 或 Chrome。默认情况下，木偶师无头运行。

大多数你可以在浏览器中手动完成的事情都可以使用木偶师来完成！

下面是一个使用导航计时 API 提取计时指标的示例:

```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://awebsite.com'); // change to your website

  // Executes Navigation API within the page context
  const performanceTiming = JSON.parse(
      await page.evaluate(() => JSON.stringify(window.performance.timing))
  );
  console.log('performanceTiming', performanceTiming)
  await browser.close();
})();
```

这将返回一个定时对象，如前面的导航定时 API 部分所示:

```
{
  "navigationStart": 1570451005291,
  "unloadEventStart": 1570451005728,
  "unloadEventEnd": 1570451006183,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 1570451005302,
  "domainLookupStart": 1570451005302,
  "domainLookupEnd": 1570451005302,
  "connectStart": 1570451005302,
  "connectEnd": 1570451005302,
  "secureConnectionStart": 0,
  "requestStart": 1570451005309,
  "responseStart": 1570451005681,
  "responseEnd": 1570451006173,
  "domLoading": 1570451006246,
  "domInteractive": 1570451010094,
  "domContentLoadedEventStart": 1570451010094,
  "domContentLoadedEventEnd": 1570451010096,
  "domComplete": 1570451012756,
  "loadEventStart": 1570451012756,
  "loadEventEnd": 1570451012801
}
```

你可以在[官方网站](https://pptr.dev/)上了解更多关于木偶师的信息，也可以在[这个回购](https://github.com/ananyaneogi/puppeteer-uses)中查看它的一些用途。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

### 额外资源

1.  [MDN 性能 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API)
2.  [Web 性能计时 API 初级读本](https://w3c.github.io/perf-timing-primer/)
3.  [用木偶师](https://michaljanaszek.com/blog/test-website-performance-with-puppeteer)
    测试网站性能