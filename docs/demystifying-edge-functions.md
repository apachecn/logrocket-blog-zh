# 揭开 edge 函数的神秘面纱

> 原文：<https://blog.logrocket.com/demystifying-edge-functions/>

边缘计算是一种分布式架构，其中计算发生在收集和分析数据的物理位置附近。它包括将一些存储和计算资源移出中央数据中心，并靠近生成数据的源。通过这样做，更少的流程在云中运行，而是转移到本地，如用户的计算机、物联网设备或边缘服务器上。

将计算带到网络边缘可以最大限度地减少客户端和服务器之间的长距离通信，从而减少延迟和带宽使用。

在这方面，边缘计算不同于传统的基于云的架构或内部架构，在传统架构中，托管在公共云的大型中央数据中心或内部基础架构中的工作负载随后通过网络进行访问，并且相对远离用户。

在这篇文章中，我们将学习边缘函数，它们是什么，它们的好处，以及它们的用例。我们还将讨论边缘函数对计算的未来意味着什么。

### 内容

## 边缘计算的使用案例

边缘计算可以整合到各种应用、产品和服务中。一些可能性包括:

### 交通管理

通过利用边缘计算，城市可以更有效地管理交通。它消除了将大量流量数据传输到中央云的需要，从而降低了带宽和延迟成本。

### 卫生保健

通过处理来自医疗传感器和可穿戴设备的实时数据，AI/ML 系统有助于各种疾病的早期检测，如糖尿病和皮肤癌。

### 内容交付

通过在边缘缓存内容(例如，音乐、视频流、网页),可以显著改善内容交付，并减少延迟。

### 智能家居

边缘计算缩短了回程和往返时间，敏感信息可以在边缘处理。例如，亚马逊(Amazon)的 Alexa 等基于语音的助理设备的响应时间将大幅缩短。

### 云游戏

云游戏公司可以在尽可能靠近游戏玩家的地方构建边缘服务器，以减少延迟，并提供完全响应的沉浸式游戏体验。

### 电视会议

交互式实时视频占用相当多的带宽，因此将后端进程移近视频源可以减少滞后和延迟。

## 边缘计算的优势

### 表演

边缘架构通过缩短最终用户和服务器之间的地理距离，最大限度地减少了网络相关的性能问题。事实上，通过边缘计算，只有需要发送到中央数据中心或云的数据才需要返回。使用这种方法，可以在有效利用现有网络的同时提供丰富的应用体验。

### 边缘安全性

借助边缘计算，任何通过网络传输回云或数据中心的数据都可以通过加密得到保护。可以针对黑客和其他恶意活动加强边缘部署。

### 更少的带宽限制

当互联网连接可用时，边缘计算在本地保存数据并将其传输到中央服务器。边缘计算在连接不可靠或带宽有限的环境中非常有用，例如石油钻井平台、偏远农场、海上船只、雨林和沙漠。此外，通过在本地处理数据，要发送的数据量可以大大减少，需要的带宽或连接也少得多。

### 法规遵循

收集、处理和存储数据的组织必须遵守数据驻留和本地化法律。对于位于远程位置的组织，边缘计算有助于满足这些要求。

通过将处理和存储放在边缘位置，组织可以实现对不同应用程序和数据托管位置的精确控制。因此，他们可以更好地遵守特定国家或地区的监管规则。例如，组织可以使用边缘服务器将位于欧洲的客户的数据保存在欧盟，并根据欧盟 GDPR 法规进行管理，而位于不受这些法律约束的其他管辖区的客户的数据则以不同的方式进行处理。

## 什么是边缘函数？

边缘功能在处理请求之前在边缘运行 JavaScript 代码，简化后端开发人员和前端开发人员之间的连续交付，以缩短上市时间。

Edge 函数使开发团队能够更有效地交付多种功能，如 A/B 测试、自定义身份验证、动态路由、路径处理、地理定位、个性化等等。

edge 功能的目标是通过允许您简化实验、自定义身份验证、个性化 web 内容和增强安全性，使开发人员的体验更加轻松。

## 支持边缘功能的平台

*   [AWS 【电子邮件保护】](https://aws.amazon.com/lambda/edge/) : AWS 提供全球复制的 Lambda 功能
*   [Cloudflare 工作人员](https://workers.cloudflare.com/) : edge 功能在 Cloudflare 全球 CDN 网络中的 CDN 级别进行部署和复制
*   Vercel edge 函数 : edge 函数现已推出公测版。默认情况下，它们部署在 Vercel 的 edge 网络上，并且具有零冷启动
*   [Netlify Edge](https://www.netlify.com/products/edge/) :由 Deno 支持的完整无服务器运行时环境，使您能够直接从 Netlify 的 Edge 网络运行无服务器 JavaScript/TypeScript 函数
*   [Supabase 功能](https://supabase.com/edge-functions):Supabase 团队[最近在他们的平台上发布了](https://supabase.com/blog/2022/03/31/supabase-edge-functions) edge 功能，这个版本是用 Deno 构建的
*   [Oracle 漫游边缘基础设施](https://www.oracle.com/cloud/roving-edge-infrastructure/) : Oracle 云功能支持多种语言。您可以用任何语言编写代码，并将其作为函数或 API 来部署和执行
*   [阿里巴巴 Link IoT Edge](https://www.alibabacloud.com/product/linkiotedge) :通过扩展云的功能，Link IoT Edge 可以管理数百万个边缘节点，从而为用户提供更接近其位置的服务
*   Red Hat open shift:Red Hat open shift 使用 Kubernetes 让开发人员能够轻松配置、部署、供应、管理、监控和跟踪最复杂的容器化应用程序的指标
*   [Stratus ztC Edge](https://www.stratus.com/solutions/platforms/ztc-edge/):ztC Edge 为团队提供了一个专为边缘环境打造的零接触、安全、高度自动化的边缘计算平台

## 支持边缘功能的框架

一些支持边缘功能的框架有:

*   Next.js，由 Vercel 构建，完全支持 Vercel edge 函数。它还支持[网络边缘功能](https://www.netlify.com/blog/next.js-middleware-on-netlify/)
*   SvelteKit 有一个[网络适配器](https://www.netlify.com/blog/sveltekit-with-netlify-edge-functions/)；随着 SvelteKit 的创造者 Rich Harris 成为 Vercel 团队的一员，SvelteKit 将很快在 Vercel edge 函数上运行
*   Remix 是最近做的一个开源项目，它支持[边缘功能](https://remix.run/blog/remix-and-the-edge)
*   [Deno deploy](https://deno.com/deploy) 支持在边缘即时部署 JavaScript 和 TypeScript、WebAssembly 代码。事实上，Supabase 函数和 Netlify Edge 都是用 Deno 构建的
*   Nuxt.js 在其文档中涵盖了对 Netlify Edge 的支持。

## 结论

Gartner 指出，当今 91%的数据是在集中式数据中心创建和处理的，到 2025 年，75%的数据将被移动到边缘。根据[IDC 2025 年数据时代报告](https://www.seagate.com/files/www-content/our-story/trends/files/idc-seagate-dataage-whitepaper.pdf)，到 2025 年，全球将产生 175 的数据。边缘设备将创建超过 90 的数据。

尽管边缘计算已经存在很多年了，新冠肺炎疫情还是推动了它在多个行业的广泛应用。 [MarketsandMarkets](https://www.marketsandmarkets.com/Market-Reports/edge-computing-market-133384090.html) 预计边缘计算市场将在 2021 年达到 367 亿美元。根据预测，到 2026 年，全球边缘计算市场规模有望达到 873 亿美元。

根据这些统计数据，边缘计算的采用将在未来几年变得更加普遍。

云提供商提供边缘功能和边缘计算服务正变得越来越普遍，因为它们提供了将应用逻辑的一些关键方面放置在更靠近最终用户的位置的能力。通过使用这种方法，最终用户可以获得更有价值和更快的体验，同时也开辟了新的和独特的可能性。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)