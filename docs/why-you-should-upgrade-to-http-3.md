# 为什么您应该升级到 HTTP/3 - LogRocket 博客

> 原文：<https://blog.logrocket.com/why-you-should-upgrade-to-http-3/>

当你停下来思考这个问题时，很难不被网络令人敬畏的复杂性和深不可测的广度所震撼。出于同样的原因，我们很容易理所当然地认为是错综复杂的技术让互联网成为可能。

在本指南中，我们将关注互联网的基础构件之一:HTTP。我们将具体讨论它的作用，并解释如果您还没有升级到 HTTP/3，为什么应该升级。

## 什么是 HTTP？

简单地说，HTTP 是一种协议，它使你能够从另一个网络服务器访问信息和文件。

HTTP 代表超文本传输协议，它是一组定义网页如何从服务器传送到浏览器的规则。这是促进加密的相同协议。

根据 Mozilla 的说法，“HTTP 是一种客户端-服务器协议:请求由一个实体，即用户代理(或代表它的代理)发送。大多数情况下，用户代理可能是浏览器，但也可能是任何东西，例如，在网上爬行以填充和维护搜索引擎索引的机器人。

所有这些通信都发生在 [TCP 网络](http://www.tcpipguide.com/free/t_TCPIPOverviewandHistory.htm)上。TCP 是一种处理从一个 IP 地址到另一个 IP 地址的数据传输的协议。它涵盖了服务器和客户端。

## HTTP 的简史

如果你熟悉[开放系统互连(OSI)](https://en.wikipedia.org/wiki/OSI_model) 规范，HTTP 是一个应用层协议，这意味着它可以随着带宽等的增加而升级和改进。这导致了该协议的多种版本。

### HTTP/1

HTTP 的最早版本具有以下特性。

*   浏览器友好协议
*   包含关于请求和响应的丰富元数据的头字段(HTTP 版本号、状态代码、内容类型)
*   响应不限于超文本；`Content-Type`头支持以普通 HTML 以外的格式传输文件(例如，脚本、样式表、媒体等)。)
*   支持`GET`、`HEAD`和`POST`方法
*   响应后连接立即终止

HTTP/1 协议中延迟的主要原因是[行首阻塞](https://en.wikipedia.org/wiki/Head-of-line_blocking)问题。网页需要多种资源:CSS、JavaScript、字体、图片、AJAX/XMR 等。，这需要浏览器向服务器发出多个请求。然而，并不是所有的资源都是需要的，所以所有额外的加载都是浪费时间。

在 HTTP/1.0 中，浏览器必须完成一个请求，包括完全接收响应，然后才能开始下一个请求。这还会导致的加载时间增加，因为所有工作都必须连续完成。

HTTP/1.1 通过引入管道技术解决了这个问题，它使 web 浏览器能够开始新的请求，而不需要等待以前的请求完成。这有助于加快低延迟环境的加载速度。

### HTTP/2

目前的标准是 HTTP 2，它使用 TCP 作为主要的通信协议。

使用 HTTP/2，多个请求和响应可以同时在同一个连接上传输。缺点是所有的请求和响应都可能因为网络拥塞而受到数据包丢失的影响。

TCP 以正确的顺序将整个字节流从一个端点传递到另一个端点。有时，网络或连接问题会导致字节数据包丢失或损坏。TCP 将尝试通过重新发送请求来填充丢失的数据包。这一单个动作会导致过程中不必要的延迟，这自然会影响通信的速度和性能。本质上，这就是排队头阻塞问题。

### HTTP/3

HTTP/3 旨在一劳永逸地消除行首阻塞问题。它没有使用 TCP 作为通信的传输层，而是引入了一个全新的传输层，称为[快速 UDP 互联网连接(QUIC)](https://blog.cloudflare.com/the-road-to-quic/) 。

默认情况下加密的 QUIC 旨在加速和保护 HTTP 流量。它的目标是最终取代 TCP 和 TLS。

[Cloudflare](https://blog.cloudflare.com/the-road-to-quic/) 列出了使用 QUIC 优于传统 TCP 和 UDP 的一些优势，包括:

*   内置安全性
*   无行首阻塞
*   一种新的报头压缩方案
*   偏转反射
*   UDP 性能

将 QUIC 和 HTTP/3 结合起来有望解决行首阻塞问题，并解决与以前的标准相关的许多其他缺点，从而开创 web 性能的新时代。

## 为什么您应该升级到 HTTP/3

从 TCP 转换到 QUIC 的好处包括但不限于:

*   更低的延迟。谷歌的一项研究发现，使用 QUIC [将搜索结果加速了 3.6%](https://medium.com/codavel-blog/quic-vs-tcp-tls-and-why-quic-is-not-the-next-big-thing-d4ef59143efd), YouTube 加载时间加快了 15%
*   网络之间的切换对速度几乎没有影响；有了 QUIC，切换或重新协商过程不再是必要。
*   握手优化。QUIC 支持在一个连接上进行单次握手，而不是两次或多次握手，这增加了通信的连接时间
*   多路复用。QUIC 解决了行首阻塞问题
*   前向纠错
*   改进的拥塞控制。目前还不清楚最终的选择是什么，但似乎是基于 TCP NewReno 的

既然您已经了解了 HTTP 的基础知识、协议版本的历史以及 QUIC 协议提高性能的潜力，那么您应该已经具备了转换到 HTTP/3 的基础知识和动机。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)