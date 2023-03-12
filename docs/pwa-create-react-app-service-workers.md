# PWA 与 Create React 应用程序和服务人员- LogRocket 博客

> 原文：<https://blog.logrocket.com/pwa-create-react-app-service-workers/>

***编者按**:本文最后更新于 2022 年 7 月 14 日，以反映 Create React App v5.x.*

在本教程中，我们将介绍您需要了解的关于使用服务人员通过 Create React App 构建渐进式 web 应用程序的内容。我们将解释什么是 pwa，讨论什么是服务工作者，然后演示在 React 应用程序中设置服务工作者需要遵循的步骤。我们开始吧！

## 什么是渐进式 web 应用程序？

渐进式 web 应用程序是一种增强型 web 应用程序，具有与本机或特定于平台的应用程序相同的一些功能。

例如，渐进式网络应用程序可以直接安装在用户的主屏幕上，并可以在独立的窗口中运行。这些应用在恶劣的网络条件下运行快速可靠，甚至可以离线运行。

想想你的典型移动用户在使用你的应用时是如何在不断变化的环境中移动的。例如，他们可能从拥有可靠高速网络的建筑开始。但是，当他们走到街上时，他们可能会失去 wifi，转而使用蜂窝连接。

他们可能会捕捉到强大的 4G 甚至 5G 信号，或者，他们可能会遇到只有 3G 的低服务 dip。他们如何在所有网络条件下保持在你的应用上，即使他们根本没有连接？

无论用户身在何处，渐进式 web 应用程序都能让您联系到他们，并在任何网络环境中为他们提供快速可靠的用户体验。

## 什么是服务人员？

服务工作者是一种特殊类型的 [web 工作者](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)，它拦截来自 web 应用程序的网络请求，并控制如何处理这些请求。特别是，服务人员可以管理资源的缓存和从缓存中检索这些资源，从而支持对 PWAs 至关重要的离线优先模式。

服务人员为运行渐进式 web 应用程序提供了必要的支持。服务工作器采用 JavaScript 文件的形式，在独立于主浏览器线程的非阻塞线程中运行。

Create React App v4.0 及更高版本配备了对来自[工具箱](https://developers.google.com/web/tools/workbox/guides/get-started)的 [`InjectManifest`](https://developer.chrome.com/docs/workbox/reference/workbox-webpack-plugin/) 插件的内置支持，该插件处理服务工作者到应用的预缓存列表中的编译和注入。

## 如何为创建 React 应用程序设置服务人员

### 先决条件

开始之前，您需要以下内容:

*   创建 React 应用程序版本 4.0
*   支持 HTTPS 的生产 web 服务器

请记住，出于安全原因，服务人员和渐进式 web 应用程序功能将仅在 HTTPS 上运行。用户仍然可以通过 HTTP 运行你的应用，但他们将无法访问离线优先模式和服务人员的所有其他强大功能。

### 创建一个项目并添加一个`service-worker.js`文件

为了利用 Create React App 与 Workbox 的`InjectManifest`插件的集成，您只需使用自定义 PWA 模板创建一个新项目:

```
npx create-react-app my-app --template cra-template-pwa

```

该模板会自动向您的项目添加一个`src/service-worker.js`文件。该文件包含一个基本的服务工作器，您可以稍后返回并根据自己的喜好进行定制。

或者，如果你更喜欢用 TypeScript 而不是 JavaScript 编写你的应用程序，下面是你如何创建一个包含 starter `src/service-worker.js`文件的项目:

```
npx create-react-app my-app --template cra-template-pwa-typescript

```

### 登记服务人员

默认情况下，服务人员在您注册之前不会变为活动状态，也无法使用。要了解更多信息，您可以阅读[服务人员生命周期](https://developers.google.com/web/fundamentals/primers/service-workers)的基础知识。

转到项目中的`src/index.js`文件，找到下面几行代码:

```
// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://cra.link/PWA
serviceWorker.unregister();

```

要注册 service worker 以供使用，只需修改最后一行，如下所示:

```
serviceWorkerRegistration.register();

```

`src/service-worker.js`文件现在已经注册好了，可以在为用户提供离线优先体验的过程中发挥重要作用了。

### 定制服务人员

使用`cra-template-pwa`或`cra-template-pwa-typescript`模板添加的 starter `src/service-worker.js`文件附带了支持渐进式 web 应用程序的基本逻辑。如果你想扩展你的应用程序的离线优先功能，你可以很容易地定制`src/service-worker.js`。

为了支持预缓存，确保将`self.__WB_MANIFEST`值分配给一个能够被`InjectManifest`插件识别的变量。插件需要看到这个值，以便为预缓存生成 URL 清单。

如何定制`src/service-worker.js`文件的一些例子包括:

*   使用工具箱中的附加[维修工人包](https://developer.chrome.com/docs/workbox/modules/)
*   添加推送通知
*   微调后台数据同步
*   集中更新共享的地理位置或陀螺仪数据
*   增加了处理来自不同域的流量的能力

随着[服务工作者 API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) 的不断发展，您可以期待能够用您的 PWAs 支持更多的特性和用例。

### 帮助用户浏览您的渐进式 web 应用程序

既然您已经将项目配置为支持服务人员，那么是时候确保您的用户准备好并能够充分利用离线优先模式了。由于渐进式网络应用程序[的行为与标准网络应用程序](https://create-react-app.dev/docs/making-a-progressive-web-app#offline-first-considerations)略有不同，所以教育你的用户是个好主意，这样他们就可以充分利用他们的体验。

您可以通过显示应用内消息来提醒用户遵守特定协议，从而为他们提供帮助。例如，您可以提醒他们使用支持服务人员的浏览器，如 Chrome、Firefox、Opera、Samsung Internet、Safari 或 Edge。请记住，任何浏览器都不支持私人或匿名模式的服务人员。

服务人员最初填充缓存后，您可以向用户显示一条消息，通知他们可以离线使用该应用程序。

默认情况下，服务工作程序在获取内容更新后会保持待机状态。因此，用户只有在关闭所有打开的选项卡并加载新页面后才能看到这些更改，因此您可以提醒用户关闭他们现有的浏览器选项卡。

## 结论

借助 Create React App，将您的标准 web 应用程序转变为 PWA 并不困难。一旦你设置了服务人员来支持离线优先的用户体验，你的应用将能够在移动中接触到更多的用户，无论他们碰巧在哪里。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)