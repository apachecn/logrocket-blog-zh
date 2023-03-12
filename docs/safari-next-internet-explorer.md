# Safari 是下一个 Internet Explorer 吗？

> 原文：<https://blog.logrocket.com/safari-next-internet-explorer/>

在 2004 年的巅峰时期，微软的 IE 浏览器占据了浏览器市场 95%的份额。随着 Firefox 和 Chrome 越来越受欢迎，web 开发人员在构建网站时考虑跨浏览器兼容性变得越来越重要。

然而，IE 给 web 开发人员带来了很多痛苦，因为许多错误只适用于 IE，而且在 W3C 对其进行标准化后，IE 有时在实现新功能方面落后于 T1。

IE 如此受欢迎，以至于即使在微软自己用 Edge 取代它之后，它仍然保持着持续的市场份额。2016 年，公司结束了对 11 以下[所有版本的支持。虽然微软将在 Windows 10 的整个生命周期内更新 IE 11，但他们正在积极推动人们摆脱 IE。微软服务](https://venturebeat.com/2016/01/12/microsoft-ends-support-for-ie8-ie9-ie10-and-windows-8/)[将在 2021 年](https://www.theverge.com/2020/8/17/21372487/microsoft-internet-explorer-11-support-end-365-legacy-edge)停止支持，当用户试图在 IE 中打开某些网站时，Windows 将开始[在 Edge](https://www.theverge.com/2020/10/27/21537274/microsoft-internet-explorer-force-open-edge-website) 中自动启动这些网站。

虽然 IE 已经非常接近成为互联网历史的遗迹，但有些人声称 Safari 是新的 IE。让我们考虑一下 IE 和 Safari 之间的异同，看看在 2020 年底之前这种比较有多有效。

## 市场占有率

成为下一个 IE 意味着拥有足够大的市场份额，足以让许多开发者担心。在桌面平台上，Chrome 是明显的领导者，拥有大约 68%的市场份额。Safari 位居第二，约占 10%。这本身就是一个相当大的群体，但开发者必须关注 Safari 的更大原因是因为它在 iOS 和 iPadOS 上的移动版本。

绝大多数 iPhone 用户使用 Safari。但同样重要的是，要知道 Safari 的引擎 WebKit 是 iOS 上唯一允许的浏览器引擎。即使是 Chrome 和 Firefox for iOS 也必须在引擎盖下使用 WebKit，以符合苹果的[应用商店审查指南](https://developer.apple.com/app-store/review/guidelines/#software-requirements):

> 浏览网页的应用程序必须使用合适的 WebKit 框架和 WebKit Javascript。

因此，任何关心 iPhone 和 iPad 用户的 web 开发人员都必须关心 Safari。

尽管如此，总的情况比过去 IE 的情况要好很多。Safari 仍然只占整个浏览器市场的 19%。这与 IE 以前的垄断地位相去甚远。

除非一个网站拥有不成比例的大量 iOS 和 iPadOS 用户群，否则 Safari 的任何问题都不太可能像 IE 全盛时期特有的问题那样严重。“这个 bug 影响了我们 95%的用户”并不像“这个 bug 影响了我们 19%的用户”那样令人担忧

## 操作系统更新

IE 的另一个大问题是它与新的 Windows 版本的关系。每个版本的 Windows 最多只支持[到某个版本的 IE](https://en.wikipedia.org/wiki/Internet_Explorer_version_history#OS_compatibility) 。比如 Windows XP 最高只支持 IE 8，Windows Vista 最高只支持 IE 9。

因此，即使新的 IE 版本修复了问题并实现了功能，仍然存在用户因为没有升级 Windows 而停留在旧版本 IE 上的问题。Safari 的情况类似，因为它的更新也与操作系统更新相关。然而，有几个因素可以缓解这种情况。

首先是操作系统更新现在是免费的。过去，我们必须为新的 Windows 和 OS X 版本付费。既然它们是免费的，让人们升级的摩擦就少多了。

苹果在推送更新方面也很执着，比如通过发出[重复提示来更新](https://www.macworld.com/article/3447396/how-to-stop-getting-a-reminder-to-update-to-catalina-in-macos.html)。新的 iOS 版本往往在发布后不久就达到[的高采用率](https://9to5mac.com/2020/06/19/apple-says-ios-13-is-now-running-on-81-of-all-devices-ipados-adoption-hits-73/)，尤其是与新的 Android 版本的采用率相比。

苹果支持几年前的设备也有所帮助。例如，iOS 14 可以在 iPhone 上一直工作到 iPhone 6S，此时已经五年了。因此，在实践中 Safari 更新与 OS 更新一致并不是一个大问题。

## 功能和缺陷

[我可以使用吗](https://caniuse.com/)提供了主流浏览器最新版本之间支持的功能对比。Safari 还缺少的功能并不多。开发人员过去抱怨缺少对 WebRTC 和 WebP 的 T2 支持，但是 Safari 现在有了。

Safari 仍然有一些坚持者，比如网络蓝牙和网络 USB。但这些都是相对先进的、很好拥有的功能，而不是典型网站会广泛使用的功能。苹果可能不会总是采用 W3C 标准，但影响应该不会像 IE 在更关键的标准上行动迟缓时那么严重。

Mozilla 的 [2020 MDN 浏览器兼容性报告](https://mdn-web-dna.s3-us-west-2.amazonaws.com/MDN-Browser-Compatibility-Report-2020.pdf)让我们对社区的意见有了一些了解。对于“什么浏览器/平台导致的问题最多？”，移动 Safari 和桌面 Safari 紧随 IE 之后。

我们也有理由相信 Safari 比其他现代浏览器更容易出错，因为 Bootstrap 的浏览器漏洞墙。Safari 列出的问题最多，这可能是对整体情况的一个很好的估计，因为 Bootstrap 有这么多通常有用的组件。

相对来说，Safari 确实比其他现代浏览器带来更多的痛苦。但是，只要存在竞争，总会有一种浏览器比其他浏览器更不利于开发。Safari 和其他浏览器之间的差距远没有 IE 那么大，差距也小得多，我认为在这一点上称 Safari 为下一个 IE 是不公平的。

## 扩展ˌ扩张

Safari 也有自己的扩展框架。首先，有一个用于`.safariextz`扩展的专有系统。苹果[在 2018 年结束了对那些支持 Safari 应用扩展的](https://www.howtogeek.com/fyi/macos-mojave-will-break-a-bunch-of-safari-extensions/)的抨击。但从版本 14 开始，Safari 支持 Chrome(及其衍生产品如 Edge 和 Opera)和 Firefox 使用的 [WebExtensions API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions) 。

虽然存在某些不兼容性，但这对扩展开发者来说是一大进步，因为至少核心 API 在所有主流浏览器上都是一样的。开发跨浏览器的扩展将会变得更加容易。

## 结论

从表面上看，Safari 的情况似乎与我们过去使用 IE 的情况相似，但有一些关键的差异，这使得很难证明 Safari 是下一个 IE。即使 Safari 在修复漏洞和实现功能方面比其他浏览器慢，至少我们可以期待这些变化对最终用户的影响比 IE 用户快得多。

处理 Safari 特有问题的痛苦与 web 开发人员在支持 IE 用户时不得不处理的问题不在同一个领域。维护跨浏览器兼容性不可避免地会有一定程度的困难，但现在挑战要容易得多，特别是因为像 Can I use 和 MDN 这样的资源使得不兼容性很容易提前知道。

幸运的是，没有浏览器真正继承 IE 的衣钵。我不责怪任何抱怨 Safari 的开发者，但是现在为 Safari 开发和过去为 IE 开发有着明显的不同。就我个人而言，我对此心存感激。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)