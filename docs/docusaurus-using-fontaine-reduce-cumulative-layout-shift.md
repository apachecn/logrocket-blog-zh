# 用 fontaine 减少 Docusaurus 的累积布局偏移

> 原文：<https://blog.logrocket.com/docusaurus-using-fontaine-reduce-cumulative-layout-shift/>

自定义字体的使用会给你的网站带来累积的布局变化。这篇文章展示了如何使用[枫丹白露](https://github.com/unjs/fontaine)来减少与 [Docusaurus](https://blog.logrocket.com/easy-documentation-with-docusaurus/) 网站的冲突，并将涵盖:

## 什么是累积布局偏移？

累积布局偏移(CLS)是一种衡量网页内容不稳定性的指标。这是一个核心网络指标。

你可能知道它叫“jank”。当一个页面加载并且文本移动时，你看到的是 jank，这是一个很大的烦恼。在主题为的[这篇文章中有关于它的精彩描述；我在这里引用一下:](https://web.dev/cls/)

> 你是否曾经在网上阅读一篇文章时，页面上的内容突然发生了变化？没有警告，文本移动，你已经失去了你的位置。或者更糟:你正要点击一个链接或一个按钮，但是在你手指落地的瞬间——嘣——链接移动了，你最终点击了别的东西！

简而言之，这就是 jank 它让最终用户非常沮丧，也是我们作为开发人员应该尽力避免的问题！

在这篇文章的其余部分，我通常称 CLS 为詹克，因为这是一个更贴切的称呼。

## “jank”长什么样

我的博客使用了一种叫做 [Poppins](https://fonts.google.com/specimen/Poppins) 的自定义字体。虽然很可爱，使用字体介绍 jank 到我的网站。在手机上特别明显。这是 jank 在行动中的 gif:

![My Jank](img/5642cd35346b8f8e2a3b1cabe691c511.png)

当自定义字体到达时，您看到文本是如何移动的了吗？在第一行，我们可以看到:

*   在一行上呈现四个单词的后退字体:*“二头肌:静态网络应用”*

…或者:

*   自定义字体(Poppins)在一行上呈现三个单词:*“二头肌:静态网页”*

它非常引人注目——如此引人注目，你甚至可以用一个数字来表示它。

这个数字就是 CLS 分数，你可以用[灯塔](https://developer.chrome.com/docs/lighthouse/overview/)来确定。CLS 分数是页面上发生的布局变化的总和。分数越高，jank 就越多。对于上一页，累积布局移动被记录为 **0.019** 。

这可不好。

我采取了一些措施来减少遇到的问题，如[字体预加载](../2021-12-29-preload-fonts-with-docusaurus/index.md)，但问题仍然存在——特别是在移动网络上，加载速度下降，自定义字体加载需要更长的时间。

我宁愿放弃进一步改进事情。但是后来…

## 介绍方丹

一天晚上，我漫不经心地浏览 Twitter，突然看到丹尼尔·罗发来的一条推文，称 T2 发布了一款名为 fontaine 的新工具:

![Screenshot Tweet About Fontaine](img/37890a60a4a0313a6988978fbd014621.png)

我很好奇。我想尝试一下。我想看看使用这个工具是否能减少我博客上的恶作剧。

## 对 Docusaurus 使用 fontaine

我把方丹作为一个附属品添加到我的博客中:

```
yarn add -D fontaine

```

然后我打开我的`docusaurus.config.js`文件，写了一个小插件来使用 fontaine:

```
const fontaine = require('fontaine');

// ...

/** @type {import('@docusaurus/types').Config} */
const config = {
  // ...

  plugins: [
    // ...
    function fontainePlugin(_context, _options) {
      return {
        name: 'fontaine-plugin',
        configureWebpack(_config, _isServer) {
          return {
            plugins: [
              fontaine.fontaineTransform.webpack({
                fallbacks: [
                  'system-ui',
                  '-apple-system',
                  'BlinkMacSystemFont',
                  'Segoe UI',
                  'Roboto',
                  'Oxygen',
                  'Ubuntu',
                  'Cantarell',
                  'Open Sans',
                  'Helvetica Neue',
                  'sans-serif',
                ],
                // You may need to resolve assets like `/fonts/Poppins-Bold.ttf` to a particular directory
                resolvePath: (id) => '../fonts/' + id,
              }),
            ],
          };
        },
      };
    },
    // ...
  ],
  // ...
};

```

这最初似乎没有什么不同，所以我把它作为一份正在进行的公关工作，尽我所能写下我的发现。丹尼尔好心来看一下。他发现了两个问题:

*   fontaine 在处理 CSS 变量的方式上有一个错误，为此[他实现了一个修复](https://github.com/unjs/fontaine/commit/a708bb07ccc48f385c67ccc3b1eed280d8ee47fc)
*   Docusaurus 通过 CSS 变量的机制使用自定义字体。这种间接性使 fontaine 感到困惑，因为它无法读取这些变量。为了适应这种情况，我们需要更新我的 CSS 变量，将覆盖字体系列添加到 CSS 变量中:

```
-  --ifm-font-family-base: 'Poppins';
+  --ifm-font-family-base: 'Poppins', 'Poppins override';

```

在幕后，方丹创造了一个“Poppins override”`@font-face`规则。通过手动将这个 override 字体系列添加到我们的 CSS 变量中，我们使我们的站点使用回退的“Poppins override”`@font-face`规则和 finding 生成的正确字体度量。

值得强调的是，对于方丹的典型用户来说，这并不是他们需要做的事情。这只对 Docusaurus 用户有必要，因为他们通过 CSS 变量使用自定义字体。使用 fontaine 对大多数用户来说是非常即插即用的。

丹尼尔很友好地提出了一份包含这两个调整的公关。当我合并该 PR 时，我看到了以下内容:

![My Jank Fixed](img/cb0e708351a5c8cf4fa32f890f78b8c4.png)

看那个！你可以看到字体加载，但没有更多的字从一行跳到另一行。这是一个巨大的进步！

## 结论

如果你想提高你的 CLS 分数，方丹是一个很好的工具。

这篇文章演示了如何在 Docusaurus 中使用它，但是请注意，这是一个普遍有用的工具，您可以在 Vite、Next.js 和其他工具中使用它——它并不特定于 Docusaurus。

在使用 fontaine 之前，我的博客的累积布局偏移记录为 0.019。合并后，相同的分数记录为 0。这是好消息！

我非常感谢丹尼尔帮助我的博客。他超越了一切，所以谢谢你，丹尼尔！

证明了方丹是建立在多么伟大的理念之上；在我写这篇文章的时间里， [`@next/font`](https://nextjs.org/blog/next-13#nextfont) 已经宣布，这是基于类似的想法。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)