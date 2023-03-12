# React apps 中使用的顶级 JS 动画库比较

> 原文：<https://blog.logrocket.com/comparing-the-top-js-animation-libraries-for-use-in-react-apps-2/>

你可能已经注意到动画在网络上非常流行。针对运动爱好者的教程、案例研究和图书馆并不缺乏。这是开发的一个方面，可以激发、增强、愉悦、激励和打动用户。

在本文中，我选择了五个 JavaScript 动画库，并从商业和开发的角度剖析了它们的性能。

而不是问“我用什么来构建 X？”我探讨了以下问题:

*   这个项目维护的怎么样？
*   一个团队容易接吗？
*   语法是什么样的？

检查有助于为项目和业务目标提供解决方案的其他方面，例如:

*   成本有关联吗？
*   是开源的吗？
*   仅限会员？
*   有附加产品吗？
*   社区是什么样的？

这些细节有助于企业和团队做出明智的决策，有望满足所有人的需求。

## 什么构成了一个动画库？

动画库是创建动画的工具集，并明确允许自定义支持和创建。

这些库还必须提供动画对象的方法，允许控制对象运动的工具，以及对初学者的方向控制。它还可以迎合特定能力的口味，如反应式动画。

由于我对每个图书馆的评分是主观的，你的观点可能与我不同。如果一个库为你、你的项目和你的商业目标服务，那才是真正重要的。让我们开始吧。

## 绿色岩石

文档: **B+** ，特性: **A** ，语法: **B+** ，社区&支持: **A+** ，稳定性: **A**

![](img/ac014d1bf18ca7f4099481b1a78de0be.png)

GreenSock (GSAP)是网络动画师的首选 JavaScript 库之一。您可以创建各种令人惊叹的效果，包括那些需要 SVG 支持的效果。

GreenSock 的主要方面是开放源代码，允许您自由使用 TweenMax (113 KB minified)、TimelineLite (12.6 KB minified)、TimelineMax (20.4 KB minified)和 TweenLite (27.5 KB minified)，并且无需开销成本即可满足广泛的需求。如果您对文件大小有所顾虑，请务必阅读这篇名为“[千字节难题](https://greensock.com/kilobyte-conundrum)”的文章

还有一系列方便的插件来帮助扩展 GSAP 功能，例如:

*   **绘制 SVG:** 逐步显示/隐藏 SVG 笔画
*   **MorphSVG:** 将任何 SVG 形状变形为任何其他形状
*   **ScrollTo:** 动画显示滚动位置。与[卷轴魔法](http://scrollmagic.io/)一起工作
*   投掷道具:使用物理原理平稳地滑行任何道具到停止
*   **贝塞尔曲线:**沿贝塞尔曲线动画显示属性。

像 Draggable 和 SplitText 这样的插件也可以用来补充您所使用的众多插件。这是格林斯托克产品系列的完整列表，供进一步调查。

参见 [CodePen](https://codepen.io) 上 Chris gan non([@ Chris Gannon](https://codepen.io/chrisgannon))
的笔 [SVG](https://codepen.io/chrisgannon/pen/WvJMXP)中的所有设备。

就无处不在而言，GSAP 在大约 4，000，000 个网站上使用，并且已经存在很长时间了。这意味着…

*   更熟悉，更多工作，更少培训
*   使用次数越多，漏洞出现和修复的机会就越多
*   承诺的长期记录

总的来说，绿石社区相当活跃。有太多的演示、文章和教程可用，社区在 Slack workspaces 中非常活跃，如工作中的[动画](https://damp-lake-50659.herokuapp.com/)、[堆栈溢出](https://stackoverflow.com/questions/tagged/greensock)、[成员论坛](https://greensock.com/forums)和 Twitter。文档写得很好，语法易于理解和表达。如前所述，GSAP 的某些部分是开源的，而插件和实用程序将是[成本](https://greensock.com/club)。

如果您从事 motion 工作，并且希望有一个涵盖许多不同需求的库，提供可靠的社区支持，提供具有易读语法的编写良好的文档，并且始终保持最新；格林斯托克是一个伟大的选择。

### 关于 GSAP 反应式动画的一个注记

用 GSAP 构建动态环境当然可以通过使用 [ticker](https://greensock.com/docs/TweenLite/static.ticker) 或 [ModifiersPlugin](https://greensock.com/modifiersPlugin) 来完成，如果你的项目需要这种能力的话。

以下是一些使用 GSAP 展示动态运动的附加示例:

## Anime.js

文档 **: C，**特性 **: B** ，语法: **B，**社区&支持 **: C，**稳定性: **B**

![](img/4c3bb11f18281cfacbd5890eb977b006.png)

Anime 是一个轻量级(6.2 KB minified)，开源的 JavaScript 动画库。

它可以处理 CSS 属性、单个 CSS 转换、SVG 或任何 DOM 属性以及 JavaScript 对象。它的显著特点是能够链接的关键帧，同步多个实例的时间轴方法，播放控制，单个 CSS 变换控制，多个动画目标的单个值，SVG 路径/线/变形和大量的缓动功能。

它的语法是大家熟悉的，包括当今许多动画库中使用的链接方法。时间轴也很容易实例化，使用对象文字方法控制属性是另一个熟悉的 cowpath。

随着 2016 年 6 月 27 日的[公开发布，这无疑是一个处于起步阶段的项目。然而，根据 GitHub pull 请求，动漫正在积极开发中(这是一件好事)。对于社区支持，我建议在进入堆栈溢出之前使用项目的问题跟踪器，以获得快速支持的最佳机会。](https://github.com/juliangarnier/anime/commit/e27c0a6d1d56291d457f71bd112575dbb7600d74)

见 [CodePen](https://codepen.io) 上 droid pinkman([@ dennisgaebel](https://codepen.io/dennisgaebel))
的笔 [Anime.js](https://codepen.io/dennisgaebel/pen/aqXOYz) 。

说到文档，要做好阅读代码的准备。动漫的文档很难理解，因为它缺乏对演示的解释。我还发现事件控制缺乏您通常在其他库中看到的那种复杂性。

浏览器支持非常好，包括 IE10–11 和 Edge 作为基准测试。但是，对于 iOS 或 Android，没有什么值得注意的。还有一个[压力测试](https://codepen.io/juliangarnier/pen/JKEgpO)也可以用来展示性能。

如果你需要一个文件很小的库，适合简单的时间轴序列，适合 SVG(笔画，变形，变换等)。)，是 100%开源的，并且有熟悉的语法，那么动漫就适合你。

## Mo.js

文档 **: C，**特性 **: B** ，语法: **B** ，社区&支持: **C，**稳定性: **C**

![](img/41642b2141a75f8225fccca00eac408a.png)

Mo.js 是一个开源项目，可以通过 npm 或 CDNJS 安装。它声称快速、可靠、经过单元测试、模块化、健壮，并且有一个简单的 API——所有这些都有助于减少大文件的开销。

我发现这些文件有点混乱。它们并不总是包含对其他演示中使用的方法的解释，所以您必须努力寻找符合您需求的答案。我还努力寻找一个关于简单运动工作的用途(这个项目的一些方面在如何做普通动画任务方面还不清楚)。当然，你可以用 Mo.js 制作一些非常酷的效果，比如这个气泡布局:

参见 [CodePen](https://codepen.io) 上 Lego mushroom([@ sol 0 mka](https://codepen.io/sol0mka))
的笔[●Bubble 布局●](https://codepen.io/sol0mka/pen/yNOage)。

经过对 GitHub repo 的检查，这个项目没有包含太多的贡献者，而且最近也没有发布更新。该项目最近的承诺在 2016 年年中至 2017 年年中之间，但此后一直没有太多行动。

我从可靠的消息来源得知，由于创建者的忙碌生活，这个图书馆已经停滞不前，所以这个图书馆的未来是非常开放的。我建议避开它，因为支持将是暗淡的，社区是小的，图书馆的未来方向是不确定的。

## 促进

文档: **D** ，特性: **C** ，语法: **C** ，社区&支持: **B** ，稳定性: **B**

![](img/36c716be83b990f894e40c20ff0f9272.png)

Popmotion 是一款 11.5 KB 的瑞士军刀，适用于旨在成为乐高积木的动画师，并附带有混合、可拖动、姿势、反应和可旋转的软件包。一个功能性的，*反应性的* JavaScript 动作库，提供了补间的方法，然而，文档可能是一个很难破译的；由于 styler 的解释含糊而简短，因此很难让人理解。

您也应该能够自如地使用 ES6 编写，因为所有的演示和代码示例都是以这种方式编写的。这个库的整体语法确实令人困惑，文档的措辞也很复杂，需要对项目内外有深刻的理解。因为你很可能需要很好地理解这个库是如何编写的，如果你很急的话，可能需要一些时间来学习。

通过 pop motion([@ pop motion](https://codepen.io/popmotion))[code Pen](https://codepen.io)上的
看笔 [叠牌滚动](https://codepen.io/popmotion/pen/LeZvXB)。

见笔 [姿势:在](https://codepen.io/popmotion/pen/paXyRE) [CodePen](https://codepen.io) 上通过 pop motion([@ pop motion](https://codepen.io/popmotion))
进行工具提示翻转。

Popmotion 的创造者[希望 Pose](https://github.com/Popmotion/popmotion/tree/master/packages/popmotion-pose) 的工作将通过提供一个声明性的、特定于 DOM 的层来解决我的主要问题，这将使动画变得超级简单。Popmotion 当然具有基于鼠标位置的反应式动画功能，以及任何不基于持续时间的功能。

参见 [CodePen 上](https://codepen.io) [@popmotion](https://codepen.io/popmotion) )
的 pop motion[Pose SVG 线动画](https://codepen.io/popmotion/pen/dmMoVE)。

大多数开发人员会发现学习曲线要求很高。理论上这听起来不错，但是当你真正开始尝试编写代码时，它会令人惊讶地繁琐，并且需要手工编写许多方面。

如果你需要支持，可以通过 **#popmotion** 频道和 GitHub 的问题跟踪器，在[工作松弛期](https://damp-lake-50659.herokuapp.com/)的动画工作空间中联系。

如果你想要一个专注于反应式动画的库，100%开源，并且愿意预先做一些艰苦的开发工作，那么 Popmotion 就是为你准备的。

## 速度

文档 **: B+** ，特性: **B+** ，语法: **B+** ，社区&支持: **B+** ，稳定性: **B**

![](img/2c409c6a512887833b3f87e031f1ae62.png)

熟悉 jQuery 的人会觉得 Velocity 如鱼得水。Velocity 是一个动画引擎，与 jQuery 的 **$具有相同的 API。animate()** 。不管有没有 jQuery，它都可以工作。它具有彩色动画、变换、循环、缓动、SVG 支持和滚动功能。

如果你决定将 jQuery 和 Velocity 一起使用，只需要替换掉 **$就可以了。**同**$【animate】。速度**。它可以在任何地方工作，包括支持早至 IE8 和 Android 2.3。还有很多放松选项可供选择，包括弹簧物理。

参见 [CodePen](https://codepen.io) 上 Tommie Hansen([@ Tommie Hansen](https://codepen.io/tommiehansen))
的 Pen[velocity . js 序列](https://codepen.io/tommiehansen/pen/netCu)。

目前，根据 GitHub 的承诺，这个项目仍在积极开发中。这是一个已经上市很长时间的库，被广泛使用，并且非常有名。Velocity 旨在通过提供的性能测试，从一开始就让您相信它的真正性能。如果你发现自己被困住了，也有大量的文章，包括视频和教程/书籍。如果你是 ScrollMagic 的用户，你会很高兴听到它与 Velocity 配合得很好。 [SVG 支持](http://velocityjs.org/#svg)也很棒。

如果您需要一个库，其中的文档可读性好、语法友好、支持或不支持 jQuery、有长期的跟踪记录、有丰富的教学材料资源、有良好的社区支持，那么 Velocity 就是您的理想之选。

## 离别的思绪

虽然从长远考虑很重要，但在做决定前征求团队反馈也同样重要。如果你今天选择了一个极简主义的图书馆，因为它在技术上满足了你在那个特定时刻的需要，你可能不会计划 6 个月后当你需要添加一些更雄心勃勃的东西时会发生什么。

如果你使用的库缺乏吸引力或支持，从长远来看可能会适得其反；尤其是当你遇到浏览器漏洞的时候。一个长期的记录有助于企业感到安全，但是一个有很好的文档记录，易于阅读的语法，强有力的支持和良好的记录会让双方都满意。让我在评论中听到你的想法，并祝你快乐！

*特别感谢 Animation At Work Slack 社区为这个话题贡献的时间和观点。*

## 附加库和参考资料

1.  *反应式* —“反应式动画”是一种涉及离散变化的动画，由于事件(*Conal Elliot/Paul Hudak 1997*)或基于用户事件/输入和不同输入/事件值发生的变化。反应式库一般不会提前“思考”。【https://www.youtube.com/watch?v=lTCukb6Zn3g】T4。[http://slides.com/davidkhourshid/flipping#/42](http://slides.com/davidkhourshid/flipping#/42)

插头

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。