# 使用资源提示优化性能

> 原文：<https://blog.logrocket.com/using-resource-hints-to-optimize-performance/>

资源提示提供了一种在客户端优化网页性能的方法。顾名思义，它们为浏览器提供了一些提示，告诉用户在与您的站点交互时可能会如何请求字体、图像和脚本等资源。通过预测用户的预期行为，浏览器可以预先预取、预呈现或预加载资源。

## 资源提示的类型

从技术上讲，资源提示是用于外部资源的 [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) HTML 元素的`rel`属性的不同值。用户的浏览器将使用这些值来区分资源链接的优先级，而不是将它们视为同等重要。

根据 W3C 的规定，有四种类型的资源提示:

1.  `dns-prefetch`
2.  `preconnect`
3.  `prefetch`
4.  `prerender`

除了这四个资源提示之外，`rel`属性的第五个值与资源提示非常相似:`preload`，它有自己的 [W3C 规范](https://www.w3.org/TR/preload/)。这是弃用的子资源预取功能的升级版本，在 Chrome 和 Opera 中可用了一段时间。

尽管`preload`不是资源提示(建议),而是浏览器必须遵守的强制性指令，因为它也是`rel`属性的值，我们也将在本文中讨论它。

## 1.`dns-prefetch`

是最轻量级的资源提示，允许您预先请求远程资源的最小部分:它的数字 IP 地址。

`dns-prefetch`资源提示告诉浏览器在用户继续浏览页面时对资源进行 DNS 查找。在 DNS 查找期间，用户的浏览器连接到资源的域名服务器，并将域名(例如，`[http://www.example.com](http://www.example.com)`)翻译成数字 IP 地址。由于浏览器在后台执行这项任务，当用户需要资源时，他们不必等待 DNS 解析。

### 句法

要使用 DNS 预取功能，请将`rel="dns-prefetch"`属性添加到`<link>`标签中。

```
<link rel="dns-prefetch" href="https://example.com">

```

虽然`<link>`标签通常放在文档的`<head>`部分，但是资源提示是[主体确定的](https://html.spec.whatwg.org/multipage/links.html#body-ok)，所以如果你愿意，你也可以在`<body>`部分使用它们。

### 用例

因为`dns-prefetch`只解析域名，而不预连接到远程服务器或预加载资源，所以它需要很少的带宽。但是，它可以显著改善 DNS 延迟，即 DNS 服务器和用户浏览器之间的总请求-响应时间。

由于各种原因，延迟可能很高，例如当 DNS 服务器远离用户的机器时，或者当许多用户试图同时访问服务器时。因此，如果您知道存储资源的域具有高延迟，那么使用 DNS 预取可能是值得的。

只有当资源托管在不同的域上时，您才需要使用`dns-prefetch`,因为您不需要解析自己的域名。

预取以下域名时，通常建议使用`dns-prefetch`:

*   网络字体，如谷歌字体
*   分析脚本
*   来自第三方资源的脚本
*   社交媒体小部件或任何通过`<script>`标签加载第三方内容的小部件
*   CDN 上托管的资源

当同一个域在您的站点上被多次引用时，您还应该考虑 DNS 预取，因为您的用户很可能会从该域请求至少一个资源。

## 2.`preconnect`

`preconnect`是一个资源提示，它预先请求远程资源的较大部分。

除了将域名转换成数字 IP 地址(如 DNS prefetch ),它还预连接到托管资源的服务器。这种早期连接包括 HTTP 连接情况下的 DNS 查找和 TCP 握手，以及安全 HTTPS 连接情况下的 TLS 协商。

### 句法

要使用`preconnect`，您需要将`rel`属性与`preconnect`值一起使用:

```
<link rel="preconnect" href="https://example.com">
<link rel="preconnect" href="https://cdn.example.com" crossorigin>

```

为了处理 CORS 请求，您还可以将 [`crossorigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) 属性添加到`<link>`标记中。如果没有指定的值，`crossorigin`属性将阻止浏览器通过 cookies 与其他来源的服务器交换用户凭证(与`crossorigin=anonymous`相同)。

### 用例

在大多数情况下，您可以使用`preconnect`资源提示来处理与`dns-prefetch`相同的事情。如果您相当确定用户确实会从第三方服务器请求脚本、字体、样式表或其他资源，那么您应该选择它。

由于`preconnect`交换更多的数据，它也需要更多的带宽。因此，你必须更加小心，以避免减慢页面速度，并利用冗余数据浪费用户的带宽。

W3C 规范还为两个特定的用例推荐了预连接:

1.  动态 URL 请求
2.  匿名重定向

您可以使用`preconnect`来请求[动态 URL](https://www.w3.org/TR/resource-hints/#dynamic-request-url-preconnect)，稍后将构建最终的 URL。这样，您可以加快资源获取过程，因为在创建动态 URL 时，浏览器已经连接到第三方服务器。

你也可以使用`preconnect`来[保护和匿名重定向](https://www.w3.org/TR/resource-hints/#anonymizing-redirect-preconnect)。如果您提前预连接到服务器，您可以从推荐链接中删除敏感数据，如用户 ID 或购买详细信息。

当您将用户重定向到第三方网站(如广告商网站)时，这一点非常重要。即使你可能不想删除所有的推荐链接，广告商仍然不应该知道只与你的网站相关而与他们的网站无关的用户数据。

## 3.`prefetch`(链接预取)

`prefetch`资源提示更进一步:除了解析域名和预连接到远程服务器(如果需要的话)，它还预取资源并将其存储在浏览器的缓存中。

然而，预取与`dns-prefetch`和`preconnect`资源提示之间有一个重要的区别。虽然 DNS 预取和预连接为浏览器提供了与将在同一页面中加载的资源相关的提示，但预取侧重于用户在当前页面之后可能访问的下一个页面/选项卡/导航所需的资源。

### 句法

要向资源添加预取提示，您需要使用带有`prefetch`值的`rel`属性:

```
<link rel="prefetch" href="https://example.com/video.mp4" as="video" crossorigin>
<link rel="prefetch" href="next-page.html" as="document">

```

您可以将`prefetch`与两个可选参数一起使用:前面提到的`crossorigin`，它允许您定义如何处理 CORS 请求，以及`as`，它允许您指定预取资源的类型。

[`as`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#attr-as) 属性可以取不同的值，如`audio`、`video`、`image`、`document`、`script`、`font`，可以帮助你建立[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)。

### 用例

`prefetch`资源提示主要推荐给那些拥有高级应用程序知识的交互式应用程序。只有当您能够以很高的概率预测用户下一步会去哪里时，您才应该使用这个资源提示。分页的内容、图片库和步进驱动的流程都是使用预取的合理例子。

该规范还提到，您可以通过基于用户或应用程序生成的事件(如点击)将`prefetch`动态添加到资源中，来实现[反应式预取策略](https://www.w3.org/TR/resource-hints/#reactive-resource-prefetching-prefetch)。当事件触发时，您可以在导航请求/意图发生时立即开始预取资源，以便更快地加载它们。

虽然`dns-prefetch`和`preconnect`仅推荐用于第三方资源，但是`prefetch`不仅可以连接到服务器，还可以下载资源，因此您也可以将它用于存储在您自己域中的资源。

如果您想预取当前页面上的资源而不是下一个页面上的资源，您需要使用 preload 指令而不是 prefetch(稍后将详细介绍)。

## 4.`prerender`

与`prefetch`类似，`prerender`资源提示也关注用户可能访问的下一个页面。然而，`prerender`采取了一个额外的步骤，除了预取(请求和下载)资源之外，还执行资源。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`prerender`为查看下一页做好充分准备，包括预取和执行所有子资源，如图像、视频和脚本。使用`prerender`，您总是获取下一个页面作为 HTML 响应，这意味着一个完整的页面。如果您想获取另一种内容类型的后续资源，比如图像或视频，您应该使用`prefetch`提示。

### 句法

为了预呈现预期用户流中的下一页，使用`<link>`标签的`rel`属性和`prerender`值。

```
<link rel="prerender" href="next-page.html">

```

### 用例

和`prefetch`一样，`prerender`推荐用于大概率可以计算用户流量的交互应用。通过预呈现下一页，您可以在应用程序中实现即时导航体验。

因为`prerender`不仅预取后续资源，还执行它们，所以你必须更加小心。做出错误的预测会浪费大量的带宽。

还要注意，[浏览器目前对`prerender`的](https://caniuse.com/#feat=link-rel-prerender)支持不是很好；火狐和 Safari 根本不支持。从支持的角度来看，如果你想在下一页给出关于资源的提示，使用`prefetch`更有意义，因为它带来了[更好的浏览器支持](https://caniuse.com/#feat=link-rel-prefetch)。

## `preload`(不是资源提示)

如前所述，`preload`不是一个资源提示，而是一个拥有自己的 W3C 规范的指令。`preload`和资源提示的主要区别在于，提示只是低优先级的推荐，所以用户的浏览器只有在有空闲时间的情况下才会实现。另一方面，`preload`具有高优先级——它强制获取当前页面所需的资源。

谈到功能，`preload`类似于`prefetch`资源提示。`preload`也请求并下载资源，但不执行它，而是在当前页面而不是下一个页面。另外，这是浏览器必须做的，而不是可选的`prefetch`。

### 句法

要在同一页面上预加载资源，请将`preload`值添加到`rel`属性中:

```
<link rel="preload" href="style.css" as="style">
<link rel="preload" href="https://example.com/font.woff2" as="font" crossorigin type="font/woff2">

```

与`prefetch`类似，您可以使用`preload`和可选的`as`属性来指定预加载资源的类型。注意，如果从第三方域预加载支持 CORS 的资源，还需要使用`crossorigin`属性。

### 用例

最需要记住的是`preload`指令和浏览器内置的预加载器不是一回事。虽然浏览器的预加载器只获取 HTML 文档中定义的资源，但是`preload`指令也允许您预加载 CSS 和 JavaScript 中指定的资源。

此外，如果用`preload`指令预加载资源，获取过程不会被[渲染阻塞资源](https://blog.logrocket.com/how-browser-rendering-works-behind-the-scenes-6782b0e8fb10/)停止，这种情况发生在浏览器预加载器获取资源时。因此，您可以使用`preload`根据应用程序的特定需求来设置资源优先级。

`preload`的一个常见用例是早期获取关键资源。规范详细说明了当主文档解析器被渲染阻塞脚本停止时，如何[预取重要资源](https://www.w3.org/TR/preload/#early-fetch-of-critical-resources)。这是可能的，因为`preload`只下载资源而不执行它。

总而言之，你需要区别对待`preload`和资源提示。因为这是强制性的，所以你给用户的浏览器的不是建议而是命令。您可以使用它来优化当前页面上的资源交付顺序，从而提高性能。 [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Preloading_content) 发布了一些关于如何正确预加载资源的有用提示(和代码示例)。

## 结论

每个资源提示的权衡本质上是相同的:提高性能还是冒浪费带宽的风险。如果您很好地使用资源提示，您可以显著改善页面加载时间。然而，找出使用哪个资源提示以及何时使用并不总是容易的。

另一方面，如果你对用户可能的行为做出了错误的预测，他们的浏览器就会请求不必要的资源。这将使您的网站资源繁重，增加延迟，并花费用户额外的带宽-如果他们通过移动设备和/或计量数据连接访问您的网站，这是特别糟糕的。

您还需要考虑浏览器支持。目前，`preconnect`的支持度比`dns-prefetch`高，`prefetch`的支持度比`prerender`高。由于强制性的`preload`指令也有一些不完整的支持，你不应该依赖它的功能(意味着你的脚本也应该在不支持它的浏览器上正确加载)；仅在支持它的浏览器上使用它进行性能优化。

最后，如果你使用多个资源提示，并且你的站点的结构/内容经常变化，如果你只是把它们扔进 HTML，维护将会很困难。你可以很容易地在你的站点上预先请求一个你不再使用的资源。在这种情况下，更合理的做法是使用 JavaScript 动态地添加资源提示，这样就可以将所有的资源提示保存在同一个文件中，并且更容易维护它们。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.