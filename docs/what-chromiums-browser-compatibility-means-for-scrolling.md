# Chromium 的浏览器兼容性对滚动日志博客意味着什么

> 原文：<https://blog.logrocket.com/what-chromiums-browser-compatibility-means-for-scrolling/>

## 介绍

2019 年， [MDN](https://insights.developer.mozilla.org/) 调查了世界各地的数千名开发人员，以深入了解网络目前令人沮丧和不令人沮丧的地方。

从调查来看，web 开发人员最大的挫折是浏览器兼容性。

浏览器兼容性一直是 web 开发人员和设计人员的一个问题，他们试图构建一个与不同浏览器兼容的 web 应用程序，尤其是 Internet Explorer 11 (IE11)。

在这篇文章中，我们将讨论谷歌 Chrome 如何通过关注浏览器兼容性来解决这个问题，以及这对滚动意味着什么。

## 什么是浏览器兼容性？

浏览器兼容性指的是一个特定的 web 应用程序在不同的浏览器上显示完整功能的能力。

假设您正在开发一个希望兼容多种浏览器的 web 应用程序，您必须为此编写 HTML、CSS 和 JavaScript 代码，或者根据用户访问网站的平台创建不同版本的网站。这是开发者生态系统中一个持续的问题。

根据 MDN 的调查， [Chromium](https://www.chromium.org/) 团队一直在试图解决谷歌 Chrome 的一些兼容性问题。这里只是他们试图提高兼容性的几种方法。

### flex box(flex box)的缩写形式

当涉及到构建您的 web 应用程序时，Flexbox 是一个强大的工具，可以用来设计您的 web 应用程序的结构。它是浏览器兼容性的顶级工具之一。

尽管 Flexbox 功能强大，但 Chrome 84 将引入一些新的改进，帮助解决兼容性问题。Chrome 团队正在考虑用现代 [LayoutNG 引擎重新构建 Chromium Flexbox 的实现。](https://www.chromium.org/blink/layoutng)要开始使用 Flexbox，你可以查看初学者指南[这里](https://blog.logrocket.com/flexing-with-css-flexbox-b7940b329a8a/)。

### CSS 网格

CSS Grid 是另一个很好的浏览器兼容性工具，据 Google Chrome 团队称，Chromium 浏览器支持 CSS Grid。值得注意的是，尽管在撰写本文时 Chromium 仍然不支持[子网格](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Subgrid)，但它目前正在开发中，可能会作为新 LayoutNG 引擎的一部分添加进来。关于 CSS 网格的更多信息，请阅读这篇内容丰富的[文章](https://blog.logrocket.com/css-grid-or-css-frameworks/)。

### 卷动

在调查反馈中，出现了许多与滚动相关的问题，例如:

*   根据视窗大小，在移动设备上滚动时缩小/隐藏 URL 栏的效果
*   难以控制原生滚动，因此开发人员最终改用 JavaScript。这包括过度滚动行为和滚动捕捉
*   行为差异或对滚动相关 API 的支持，如`scrollIntoView`

幸运的是，您可以使用 [CSS 滚动捕捉](https://blog.logrocket.com/how-to-use-css-scroll-snap/)来解决这些问题。让我们来演示一下怎么做。

CSS 滚动捕捉允许您在用户完成滚动后锁定某些元素的视窗。你可以用它来构建一个很棒的交互[，就像这个](https://codepen.io/chriscoyier/full/pMRgwW)。

CSS 滚动捕捉于 2016 年推出，在过去几年中有了显著改善，它支持大多数浏览器及其最新版本。

首先，创建一个 HTML 文件。

```
<div class="container">
  <section class="child"></section>
  <section class="child"></section>
  <section class="child"></section>
  <p>...</p>
</div>

```

现在，添加以下 CSS 属性:

```
.container {
  scroll-snap-type: y mandatory;
}

.child {
  scroll-snap-align: start;
}

```

在这里，`y`值简单滚动容器仅捕捉到其水平轴上的位置，而`mandatory`意味着如果当前没有滚动，该滚动容器的可视视口将停留在捕捉点上。

你可以在这里阅读更多关于 CSS 滚动捕捉和不同属性值[的内容。](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-snap-type)

让我们看看如何通过使用名为 [Body Scroll Lock](https://github.com/willmcpo/body-scroll-lock) 的 NPM 包来正确地实现滚动锁定。

将包导入到项目中。

```
yarn add body-scroll-lock
// or
npm install body-scroll-lock

```

接下来，创建一个`index.js`文件并粘贴以下代码:

```
const bodyScrollLock = require('body-scroll-lock');
const disableBodyScroll = bodyScrollLock.disableBodyScroll;
const enableBodyScroll = bodyScrollLock.enableBodyScroll;

```

然后，查询选择所有元素:

```
const targetElement = document.querySelector('.child');

// Disable Body Scrolling for the element
disableBodyScroll(targetElement);

// Renable the Scrollin with the library
enableBodyScroll(targetElement);

```

这就是你需要做的！

## 结论

在这篇文章中，我们学习了如何使用 CSS 滚动捕捉和主体滚动锁定 NPM 包来处理浏览器兼容的滚动。继续编码！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)