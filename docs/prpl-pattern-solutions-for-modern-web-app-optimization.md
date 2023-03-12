# PRPL 模式:现代 web 应用优化的解决方案

> 原文：<https://blog.logrocket.com/prpl-pattern-solutions-for-modern-web-app-optimization/>

## 什么是 PRPL 模式？

PRPL 是一种模式，用于构建具有出色用户体验的可伸缩、快速的现代 web 应用程序。

PRPL 是以下单词的首字母缩写:

*   **推送**(或**预载**)最重要的资源
*   **尽快渲染**初始路线
*   **预缓存**剩余资产
*   **惰性负载**其他路线和非关键资产

PRPL 架构是由谷歌 Chrome 团队构思的，旨在提高网络速度。

这个 PRPL 是多年来开发的一个个人优化技巧，旨在促进更快的网络体验。这是随着服务工作者、后台同步、缓存 API、优先级提示和预取的出现而出现的。

具体来说，当手机离线或处于数据保护模式时，PRPL 适用于网络较弱的手机。

在本帖中，我们将看看 PRPL 的每个单元。

### p–推动(或预载)

这告诉浏览器预先获取资源并将其存储在浏览器中。因此，当我们需要资源时，可以从浏览器中快速检索资源，而无需通过网络获取资源。这是通过使用`rel="preload"`完成的。

> Preload 是一个声明性的获取请求，告诉浏览器尽快请求资源。通过在你的 HTML 文档头添加一个带有`rel="preload"`的`<link>`标签来预加载关键资源。

为了预加载一个资源，我们使用 link 标签并为其添加`rel='preload'`属性。

```
<link rel="preload" as="style" href="style.css">
```

这将把资源`style.css`作为样式表预加载。`as='style'`告诉浏览器要预加载的资源是一个样式表，应该这样加载。

可以预加载的资源包括:

*   网页
*   JS 文件
*   CSS 文件
*   媒体文件(音频、图像、视频、文档和 Web 字体)
*   DNS 查找

```
<link rel="preload" href="/your/webpage/link">
```

```
<link rel="preload" href="/your/js/file/link">
```

```
<link rel="preload" href="/your/css/file/link">
```

```
<link rel="preload" href="/your/audio/file/link">
```

```
<link rel="preload" href="/your/audio/file/link">
```

```
<link rel="preload" href="/your/video/file/link">
```

```
<link rel="preload" href="/your/image/file/link">
```

```
<link rel="preload" href="/your/document/file/link">
```

```
<link rel="preload" href="/your/webfont/file/link">
```

预装和预取基本相同。

预取包括一个过程，通过这个过程，浏览器获取一个`<link>`标签的资源，并将其存储在其本地缓存中。当用户最终通过`<link>`标签请求页面时，浏览器为用户提供缓存的页面。这加快了网页的加载和渲染速度。

为了预取资源，我们使用了`rel="prefetch"`属性。

```
<link rel="prefetch">
```

这将预取那里的任何资源，就像预加载一样。

### r–初始渲染

这是一条规则，规定 web 应用程序的初始路由应该尽可能快地呈现，并且初始路由不应该延迟加载。

我们称之为最初的心满意足的绘画。无论应用程序做什么，它必须在浏览器上快速生成第一个画图。为了优化第一个内容丰富的绘画，我们必须消除渲染阻塞资源，优化 CSS 交付，并使用 SSR。

这主要适用于 JS 框架，因为它们在浏览器/客户端呈现其有效负载，所以如果应用程序的有效负载很大，您会看到它必须在呈现其内容之前加载 JS/CSS 资产。

因此，您的 web 应用程序上的 SSR 将有助于在服务器中呈现 web 应用程序，并在其余的 JS/CSS 到达之前生成第一幅画。
例如，在 React 中，我们可以使用`ReactDOMServer`构建一个服务器端渲染的应用。

让我们看一个例子:

```
import React from "react" import ReactDOM from "react-dom" import App from "App"

ReactDOM.hydrate(<App />, window.root)
```

我们使用`ReactDOM.hydrate`而不是 render，因为我们想让 React DOM renderer 知道我们正在服务器端渲染后重新水合应用程序。这意味着 React DOM 呈现器将从服务器获得呈现。它会先显示这个。然后，应用程序组件将从浏览器中进行反应。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，我们的 Express 服务器应该是这样的:

```
import fs from "fs" import React from "react" import App from "./App" import ReactDOMServer from "react-dom/server"

...

app.get("/", (req, res, next) => { // This would render the <App /> and return it as string. const app = ReactDOMServer.renderToString(<App />)

fs.readFile("./build/index.html", (err, data) => {
    // We read the index.html file, replace the `div#root` with the rendered App component and send it to the browser.
    return res.send(data.replace("<div id='root'></div>", "<div id='root'>" + app + "</div>"))
})
})

...
```

有了上面的代码，我们的 React 应用程序现在支持 SSR 了。它会在渲染整个负载之前渲染第一个绘画。

CSS 交付包括将重要的 CSS 内联到 HTML 中，因此交付速度很快。

所有这些都将帮助您的 web 应用程序快速呈现初始路线，并在其余有效负载到达之前与用户进行交互。它提高了性能和用户体验。

### p–预缓存

这是一种将资产缓存在浏览器中的技术，这样当发出对资产的请求时，就可以从缓存中处理这些请求，而不会中断用户体验。这在手机离线时很有帮助。

因此，用户看到的不是网络故障，而是缓存的资产。当手机上线时，资产会从网络上刷新。

预缓存由服务人员完成，主要用于 PWAs。

服务人员的预缓存有不同的策略:

*   **Stale-while-revalidate** :该策略检查缓存中的响应。如果它是可用的，它被传递，并且缓存被重新验证。如果它不可用，服务工作者从网络获取响应并缓存它。
*   **先缓存** :该策略会先在缓存中寻找响应。如果发现之前缓存了任何响应，它将返回并服务于缓存。如果没有，它将从网络中获取响应，服务它，并缓存它以备下次使用。
*   **网络优先** :该策略将尝试从网络获取响应。如果成功，它将缓存响应并返回响应。如果网络出现故障，它将退回到缓存并在那里提供响应。
*   **仅缓存** :该策略仅从缓存**响应**。它不会退回到网络。
*   **仅网络** :该策略仅使用网络来获取和提供响应。它不会回退到任何缓存。

Google 有一个名为 [Workbox](https://developers.google.com/web/tools/workbox) 的库。它旨在为维护服务工作者的缓存提供工具，也提供了我们刚才看到的缓存策略供选择。

让我们看一个如何使用 Workbox 进行预缓存的例子。

使用 Workbox，我们设置路线和我们想要使用的缓存策略。Workbox 将监听路由请求，并确定如何缓存和响应请求。

```
import { registerRoute } from 'workbox-routing'; import { NetworkFirst } from 'workbox-strategies';

registerRoute(

// We are caching the style resources.
({request}) => request.destination === 'style',

// We are setting the style cahcing to use the
// StaleWhileRevalidate strategy.
// Use cache but update in the background.
new StaleWhileRevalidate()
);
```

上面是一个简单的例子，展示了如何在 Workbox 中缓存我们的样式资源来使用`StaleWhileRevalidate`策略。这将缓存我们所有的样式文件，以便在重载时更快地获取。它们在有网络的时候从后台悄悄更新。

```
registerRoute( ({request}) => request.destination === 'script', new NetworkFirst() );
```

这里，我们所有的 JavaScript 文件都是使用`NetworkFirst`策略缓存的。当通过网络提取失败时，从缓存中提取它们。

### l–延迟加载

这是将 web 应用程序中的路线和资产的加载推迟到其他时间。

应用程序性能最重要的概念是**响应时间**和**资源消耗**。它们不可避免地会发生。任何地方都可能出现问题，在问题发生之前发现并解决问题非常重要。

延迟加载有助于将许多 web 应用程序性能问题的风险降至最低。惰性加载检查我们上面列出的概念:

*   **响应时间**:这是 web 应用程序加载和 UI 界面响应用户所需的时间。延迟加载通过代码分割和加载所需的包来优化响应时间。
*   **资源消耗**:人类是没有耐心的生物。我们不喜欢一个网站的加载时间超过 3 秒。在那段时间里，70%的人会放弃。网络应用不应该花这么长时间来加载。因此，为了减少加载的资源量，延迟加载只加载当时必需的代码包。

惰性加载可以在 Chrome 中本地完成，无需外部库的帮助。换句话说，浏览器本身将支持延迟加载。

这很简单，只需将`loading`属性添加到我们想要延迟加载的资源中。例如，我们有一个图像:

```
<img src="./big-image.jpg" />
```

为了本地延迟加载上面的图像，我们只需添加值为`"lazy"`的`loading`属性:

```
<img src="./big-image.jpg" loading="lazy" />
```

`loading`属性是关键。它告诉浏览器，除非被告知，否则不要急切地加载这个资源。这个属性可以包含在`image`、`iframe`、`video`和`audio`标签中，以便延迟加载它们。

`loading`属性有不同的值可供我们选择:

*   **lazy** :这个值让浏览器推迟资源的加载，直到它进入浏览器的视窗。
*   **auto** :这表示没有资源的延迟加载。
*   **eager** :这将立即加载资源，而不会延迟加载。

惰性加载也可以使用交叉点观察器 API 以编程方式完成。Angular、React 和 Vue 中的库使用交集观察者来延迟加载组件和资源。

### 结论

在这篇文章中，我们介绍了什么是 PRPL。它是由现代 web 标准设定的模式或集合技术，指示如何优化我们的 web 应用程序以提高效率。我们继续研究它的不同技术，从预加载和预取到延迟加载。

我会说标准已经定了。我们只需要跟随他们来制作速度惊人的现代网络应用程序。

未来就在这里。

如果你对此有任何问题，或者我应该添加、更正或删除的任何内容，请随时评论、发电子邮件或给我发短信。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。