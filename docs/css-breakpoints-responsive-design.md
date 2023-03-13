# 响应式设计的 CSS 断点

> 原文：<https://blog.logrocket.com/css-breakpoints-responsive-design/>

响应式网页设计是一种使网页在所有屏幕尺寸和分辨率下都能很好地呈现的方法，同时确保可用性高。

在这篇文章中，我们将着眼于响应式设计的发展，从媒体查询到网格系统、容器查询，最后是流体设计。我们将讨论断点在响应式设计中的作用，回顾选择断点的不同方法和一些最佳实践。

*向前跳转:*

## 响应式设计的演变

HTML 基本上是响应性的。如果您仅使用 HTML 创建网页并调整窗口大小，浏览器将自动调整文本以适应视窗。但是，你的内容不会在每个屏幕上都好看！

例如，很长的文本行可能很难在宽显示器上阅读。类似地，如果使用 CSS 通过创建列或添加边距来减少行的长度，当在移动设备上查看时，内容可能看起来被压缩了。你必须根据网页的内容和布局来调整页面的风格。

术语响应式设计是由 Ethan Marcotte 在 2010 年创造的，描述了使用流体网格、流体图像和媒体查询来创建响应式内容。当时推荐的是用 [`float`](https://developer.mozilla.org/en-US/docs/Web/CSS/float) 进行布局，用[媒体查询](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Media_queries)查询浏览器宽度或高度来创建不同断点的布局。

断点是一个点，通常是一个特定的宽度，在这个点上，网页的风格以特定的方式进行调整，以提供最佳的用户体验。通过将流体图像的`max-width`属性设置为`100%`，流体图像被设置为不超过其容器的宽度。普遍的态度是控制给定屏幕尺寸的布局的每个像素。

像[Boot](https://getbootstrap.com/)T2 sT4 trap 这样的框架越来越受欢迎，因为它们为开发人员提供了响应迅速的网格系统。这促使我们构建和思考网页的方式发生了转变。随着设计系统的出现，人们倾向于从组件而不是页面的角度来考虑问题。我们将组件组合起来组成一个页面，并希望它们能够并排存在，而不必编写大量的 CSS 来创建一个和谐的布局。

有了现代的 CSS，为不同的屏幕尺寸调整大小或改变布局只需要更少的干预。布局方法如 [F](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) [lexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) 和 [CSS G](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids) [rid](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids) 具有响应能力，其他现代方法也被开发出来使内容具有响应能力:

2018 年，Jen Simmons 在她的演讲“[中介绍了“内在网页设计”一词，你所知道的关于网页设计的一切都改变了](https://talks.jensimmons.com/videos/h0XWcf)。以下是她内在网页设计的三个原则:

1.  收缩和扩张:我们考虑我们的设计如何适应可用空间的变化
2.  **灵活性**:使用现代 CSS 功能来适应可用空间
3.  **视口**:使用视口的宽度和高度作为响应式设计输入的能力

今天，越来越多的人普遍主张顺应潮流，让内容适应可用的空间。正如安迪·贝尔最近所说的，也许我们的偏好应该是:“成为浏览器的导师，而不是微观管理者。”

随着最近[容器查询的引入，](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)我们正在进入一个响应式设计的新时代。容器查询允许我们查看容器的大小，并根据容器的大小而不是视口或其他设备特征对内容应用样式。我认为这与其说是一场革命，不如说是一次进化，因为现在 CSS 可以更容易地与基于组件的思想保持一致。

有些人认为容器查询与以前有所不同，因为现在的设计可以很大程度上独立于视口大小。然而，容器查询仍然涉及断点。所以，我们仍然需要考虑何时何地改变内容的风格。

虽然我们已经从`float`转移到了布局，但媒体的询问仍然是相关的。他们只是比以前更少被要求。随着容器查询的引入，人们一直在预测媒体查询的终结，但是[容器查询并不能解决一切](https://blog.logrocket.com/choose-between-media-container-queries/)。媒体提问仍然有一席之地。在更加清晰地确定角色之前，还需要一些时间。

在深入断点之前，让我们先了解一下媒体查询。

当您想要根据设备的屏幕分辨率或浏览器[视窗](https://developer.mozilla.org/en-US/docs/Glossary/viewport)的宽度或高度等特定特征来修改站点的布局或外观时，媒体查询非常有用。

媒体查询包括:

1.  可选的[媒体类型](https://developer.mozilla.org/en-US/docs/Web/CSS/@media#media_types)定义了媒体查询适用的广泛设备类别:`all`、`print`或`screen`。此类型是可选的；如果省略，则假设为`all`
2.  任意数量的[媒体特征](https://developer.mozilla.org/en-US/docs/Web/CSS/@media#media_features)表达式描述了[用户代理](https://developer.mozilla.org/en-US/docs/Glossary/User_agent)、输出设备或环境的特定特征。例子有: [`hover`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/hover) ， [`prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) ， [`width`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/width)

CSS 媒体查询的常见语法如下:

```
@media media type and (media feature expression) {
/* CSS rules */
}

```

[逻辑操作符](https://developer.mozilla.org/en-US/docs/Web/CSS/@media#logical_operators)`not``and``only`和`or`可用于编写复杂的媒体查询。

对于响应式设计，`min-width`和`max-width`是最常用的媒体特征。它们使样式能够基于视口的宽度。例如，只有当浏览器的[视窗](https://developer.mozilla.org/en-US/docs/Glossary/Viewport)宽度等于或小于`80em`时，下面的 CSS 代码才会应用样式:

```
@media (max-width: 80em) {
/* CSS rules */
}

```

您还可以在媒体特征表达式中使用 height ( `height`、`min-height`、`max-height`、[、`aspect-ratio`、](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/aspect-ratio)、[、](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/resolution)、[、`orientation`、](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/orientation)来处理视窗的尺寸和屏幕的不同方面。

[媒体查询级别 4 规范](https://www.w3.org/TR/mediaqueries-4/#mq-range-context)包括一些语法改进，以使媒体特征具有不太冗长的“范围”类型，例如`width`。通过这种语法改进，我们之前的`max-width`示例可以写成这样:

```
@media (width <= 80em) {
/* CSS rules *
}

```

在撰写本文时，这个[新的范围语法在 Chrome 和 Firefox 中，](https://caniuse.com/css-media-range-syntax) [将很快推出](https://caniuse.com/css-media-range-syntax)[I](https://caniuse.com/css-media-range-syntax)[n](https://caniuse.com/css-media-range-syntax)[Safari](https://caniuse.com/css-media-range-syntax)。

## 如何选择断点？

断点是一个点，通常是一个特定的宽度，在这个点上，网页的风格以特定的方式进行调整，以提供最佳的用户体验。

选择 CSS 断点有两种主要方法；一个是基于设备，一个是基于内容。让我们来看看。

### 基于设备的断点

您可以针对特定的屏幕尺寸设计不同的设计。一个设计可以跨多种屏幕尺寸工作，但是，当可用空间较少时，内容可能会较窄。

由于可用设备的广度和多样性，根据屏幕尺寸确定断点是一项挑战。这种方法维护起来确实不可行:

![Devices Display Content Differently Based Screen Size](img/5d1002bbb568d636d78e362ac0e50d38.png)

为了简化这种方法，人们倾向于根据尺寸范围对设备进行松散的分组。选择分组和具体的断点实际上取决于您。最常见的方法是根据[外形](https://en.wikipedia.org/wiki/Form_factor_(design))对设备进行分组(例如，移动设备、平板电脑、笔记本电脑等。):

![Device Breakpoints Grouping Devices Form Factor](img/b32299759f9592ce3793c8987dc8b545.png)

Here is some data you could use to arrive at this decision:

1.  【2022 年全球最常见屏幕分辨率统计
2.  来自您网站的数据分析指标
3.  CSS 框架选择的断点(我们将在下一节进一步讨论)

例如，大卫·吉尔伯特森(David Gilbertson)在 2016 年写了一篇文章，标题雄心勃勃:“[做 CSS 断点的 100%正确方法](https://www.freecodecamp.org/news/the-100-correct-way-to-do-css-breakpoints-88d6a5ba1862/)”我总是怀疑这种点击诱饵的说法！然而，大卫的方法是可靠的。他从 StatCounter 获得了 2016 年最常见的 14 种屏幕尺寸，并用四个宽泛的范围对它们进行了分组，从而得出了他的断点集。他避免选择范围上限或下限的屏幕宽度。最终，David 选定了 600 像素、900 像素、1200 像素和 1800 像素。

您会发现大多数方法都以相似的方式到达一组断点。

以下是一组媒体查询的示例，涵盖四大类设备:

```
/* Small devices such as large phones (640px and up) */
@media only screen and (min-width: 40em) {...}

/* Medium devices such as tablets (768px and up) */
@media only screen and (min-width: 48em) {...}

/* Large devices such as laptops (1024px and up) */
@media only screen and (min-width: 64em) {...}

/* Largest devices such as desktops (1280px and up) */
@media only screen and (min-width: 80em) {...}

```

### 基于内容的断点

下一个方法是基于在内容开始以某种方式中断的地方改变设计。如果行的长度变得太长，或者如果一个部分变得太拥挤，这就是你需要考虑改变样式的地方。换句话说，这是您希望使用媒体查询或容器查询来更改设计的地方。

浏览器开发工具中的响应模式(Firefox DevTools 中的[响应设计模式](https://firefox-source-docs.mozilla.org/devtools-user/responsive_design_mode/index.html)和 Chrome DevTools 中的[设备模式](https://developer.chrome.com/docs/devtools/device-mode/))对于计算出断点应该位于何处非常有用。您可以轻松地缩小或放大视口，以查看内容样式可以改进的地方:

![Firefox Responsive DevTools](img/71b348f1d30e5cee997ea16948559d66.png)

在菜单中，您可以从列表中选择设备。在上面的截图中，我已经选择了 Galaxy Note 20。您也可以更改方向(例如，纵向、横向)。

您可以拖动视口的一侧来慢慢增加宽度，并查看内容如何适应不同的视口宽度。在下面的视频中，你可以看到我用 [MDN @media page](https://developer.mozilla.org/en-US/docs/Web/CSS/@media) 做这件事。在 769 像素时，您会注意到布局随着左侧边栏的出现而改变:

![Responsive CSS Design Firefox](img/b14987351ff385edc2468903acbda31b.png)

Jeremy Keith 指出，有些断点是为了进行小的调整，他称之为调整点(tweak points):

> “例如，当我在处理[事件](https://www.readmatter.com/)时，实际上只有一个主要的断点，布局从一列变成了两列。这种断点你可以很容易地从你的内容流中找到；仅仅调整你的浏览器窗口大小通常就足够了。但是在 Matter 样式表中有许多其他媒体查询。这些是为了对页边距、字体大小进行较小的调整……这种调整是在[设备实验室](http://clearleft.com/testlab)对手机和平板电脑进行测试后产生的。
> 
> 称它们为断点感觉有点奇怪，好像没有它们布局会“中断”。那些媒体的询问是为了调整版面。它们不是断点。它们是转折点。"

总的来说，这是一个有机的过程，由你具体制作的东西来引导。与此相邻的是，如果你掌握了现代 CSS，你会发现，如果你根据可用空间按比例构建东西，你需要更少地干预媒体查询。

### 您应该遵循哪种方法？

我不会说这里只有一条路可走。但是，我建议您不要仅仅考虑特定的设备来约束自己。相反，你应该更加注重利用内容的可用空间。

一般来说，随着时间的推移，我们将较少使用媒体查询。媒体查询很可能仍然用于与视口宽度相关的组件，如网站的主导航和页脚。在其他情况下，您可以将内容设计为流动的，或者通过容器查询来适应容器大小。

拥有一组断点是有价值的。无论您是从第一种方法中获取一个集合，还是通过测试接口有机地产生断点，都取决于您。我会说，当你有一组断点时，调试布局问题要比有许多特别的断点更容易。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然而，拥有一组 6 个断点并不意味着*而不是*你应该使用它们来调整布局或样式！尽量减少干预——寻找让内容为你工作的机会！

## 流行的 CSS 框架使用了哪些断点？

根据 CSS 调查的[状态，2022](https://2022.stateofcss.com/)[最受欢迎的 CSS 框架(按使用排序)是:](https://2022.stateofcss.com/en-US/css-frameworks/)

1.  [开机](https://getbootstrap.com/) [s](https://getbootstrap.com/) [陷阱](https://getbootstrap.com/)
2.  [尾巴](https://tailwindcss.com/)[w](https://tailwindcss.com/)[ind](https://tailwindcss.com/)[CSS](https://tailwindcss.com/)
3.  [物化 CSS](https://materializecss.com/)
4.  [基础](https://get.foundation/)
5.  [布尔玛](https://bulma.io/)
6.  [蚂蚁设计](https://ant.design/)
7.  [语义界面](https://semantic-ui.com/)
8.  [PureCSS](https://purecss.io/)
9.  [UIKit](https://getuikit.com/)
10.  [立子](https://tachyons.io/)

让我们看看这些流行的框架是做什么的。

您可以在下面看到引导程序 4 的[默认断点:](https://getbootstrap.com/docs/4.0/layout/overview/#responsive-breakpoints)

| 断点 | 规模 |
| --- | --- |
| X-Small | < 576 像素 |
| 小的 | ≥576 像素 |
| 中等 | ≥768 像素 |
| 大的 | ≥992 像素 |
| 特大号 | ≥1200 像素 |
| 特大号的 | ≥1400 像素 |

Bootstrap 使用 12 列网格架构，这影响了它对断点的选择。Bootstrap 描述了用于选择断点的[方法，如下所示:](https://mdbootstrap.com/docs/standard/layout/breakpoints/)

> “每个断点都被选择来舒适地容纳宽度为 12 的倍数的容器。断点也代表了常见设备大小和视口尺寸的子集——它们并不专门针对每个用例或设备。相反，这些产品系列为几乎任何设备提供了强大而一致的基础。”

Ant Design 也遵循 Bootstrap 4 介质查询规则。

Tailwind 有[五个](https://tailwindcss.com/docs/screens) [默认断点](https://tailwindcss.com/docs/screens)，受常见设备分辨率启发:

| 钥匙 | CSS 媒体查询 | 适用 |
| --- | --- | --- |
| 没有人 | 没有人 | **T2 640 px** |
| `sm` | `@media (min-width: 640px) { ... }` | ≥ **640px** |
| `md` | `@media screen and (min-width: 768px)` | ≥ **768px** |
| `lg` | `@media screen and (min-width: 1024px)` | ≥ **1024px** |
| `xl` | `@media screen and (min-width: 1280px)` | ≥ **1280px** |
| `2xl` | `@media screen and (min-width: 1536px)` | ≥ **1536px** |

PureCSS 有[七个](https://purecss.io/grids/) [默认断点](https://purecss.io/grids/):

| 钥匙 | CSS 媒体查询 | 适用 |
| --- | --- | --- |
| 没有人 | 没有人 | **T2 568 px** |
| `sm` | `@media screen and (min-width: 35.5em)` | ≥ **568px** |
| `md` | `@media screen and (min-width: 48em)` | ≥ **768px** |
| `lg` | `@media screen and (min-width: 64em)` | ≥ **1024px** |
| `xl` | `@media screen and (min-width: 80em)` | ≥ **1280px** |
| `xxl` | `@media screen and (min-width: 120em)` | ≥ **1920px** |
| `xxxl` | `@media screen and (min-width: 160em)` | ≥ **2560px** |
| `x4k` | `@media screen and (min-width: 240em)` | ≥ **3840px** |

PureCSS 倾向于使用默认宽度`em`而不是`px`来支持网页缩放。

以下是一些其他框架断点的总结:

*   [**基础**](https://get.foundation/sites/docs/media-queries.html#default-media-queries) : < 640px，≥640px，≥1200px
*   [](https://bulma.io/documentation/overview/responsiveness/)**:<769 px、≥769px、≥1024px、≥1216px、≥1408px**
***   [**语义 UI**](https://1.semantic-ui.com/collections/grid.html#page-grid-breakpoints):<768 像素，≥768 像素，≥992 像素，≥1400 像素，≥1920 像素*   [**引物**](https://primer.style/css/support/breakpoints) : < 544px，≥544px，≥768px，≥1012px，≥1280px*   [**UIKit**](https://getuikit.com/v2/docs/core.html#breakpoints) : < 479px，≥480px，≥768px，≥960px，≥1200px**

 **## 断点的常见做法

无论您最终选择哪种 CSS 框架，都要记住以下一些重要的最佳实践:

1.  **首先为移动设备设计**:大约 [59%的网络总流量](https://www.statista.com/statistics/277125/share-of-website-traffic-coming-from-mobile-devices/#:~:text=Mobile%20accounts%20for%20approximately%20half,since%20the%20beginning%20of%202017.)来自移动设备，因此支持移动屏幕设计是有意义的。优先考虑移动设备的设计还可以确保尽早解决关键限制。然而，拥有更少的空间更具挑战性；它迫使设计师删除任何不必要的东西。一旦你对手机布局感到满意，你可以添加和调整更大的屏幕
2.  **使用相对单位**:使用相对单位，如`em`，当人们放大网页时，允许媒体查询做出适当的响应。查看 Brad Frost 的这篇文章，了解在媒体提问中使用相关单位的背景
3.  **避免断点将设备推入更小的** **或** **更大的范围**:从 CSS 框架选择的断点中你会注意到一件事，那就是它们对于平板设备的截止值大约是 768px。这是因为老一代 iPad(现在的 iPad mini)的分辨率是 768px 乘以 1024px。如果您的断点范围较宽，请注意这些截止点。通常情况下，你会看到 768px 及以上为中等断点类别。如果您基于内容设置断点，这就不是问题了

## 真的需要断点吗？

出现了一些技术，允许元素在不使用断点的情况下成比例地、流畅地缩放。有时这被称为流体设计。

许多流体设计技术使用 CSS 中可用的数学函数，例如: [`clamp()`](https://developer.mozilla.org/en-US/docs/Web/CSS/clamp) 、 [`min()`](https://developer.mozilla.org/en-US/docs/Web/CSS/min) 和 [`max()`](https://developer.mozilla.org/en-US/docs/Web/CSS/max) ，以及基于视口的动态单元，例如`vh`和`vw`，来创建将缩放元素的表达式。如果你想了解这方面的更多信息，这里有一篇关于[无媒体询问的灵活布局](https://blog.logrocket.com/flexible-layouts-without-media-queries/)的文章。

流体设计的一个系统方法是乌托邦。Utopia 倡导设计师和开发人员在响应式设计中分享一种系统化的方法来实现流动性。您没有为任意数量的断点进行设计，而是设计了一个系统，其中的元素成比例地、流畅地缩放。这可以帮助您:

*   最低限度地优雅地设计和编码
*   简化设计和开发角色之间的协作
*   确保视觉和谐一致

乌托邦就像一个花哨的计算器，会吐出一些 CSS 只需输入一些尺寸和一个首选比例来确定值的范围。

例如，流体空间计算器是这样的:

![Utopia FluidSpace Calculator](img/d6935f6a8345b24b283ceb133d020f41.png)

如果您在 Utopia 的计算器中使用`clamp()`，它将生成以下 CSS 片段:

```
/* @link https://utopia.fyi/space/calculator?c=320,18,1.2,1240,20,1.25,5,2,&s=0.75|0.5,1.5|2|3|4|6,s-l&g=s,l,xl,12 */

:root {
--space-2xs: clamp(0.56rem, calc(0.54rem + 0.11vw), 0.63rem);
--space-xs: clamp(0.88rem, calc(0.85rem + 0.11vw), 0.94rem);
--space-s: clamp(1.13rem, calc(1.08rem + 0.22vw), 1.25rem);
--space-m: clamp(1.69rem, calc(1.62rem + 0.33vw), 1.88rem);
--space-l: clamp(2.25rem, calc(2.16rem + 0.43vw), 2.50rem);
--space-xl: clamp(3.38rem, calc(3.24rem + 0.65vw), 3.75rem);
--space-2xl: clamp(4.50rem, calc(4.33rem + 0.87vw), 5.00rem);
--space-3xl: clamp(6.75rem, calc(6.49rem + 1.30vw), 7.50rem);

/* One-up pairs */
--space-2xs-xs: clamp(0.56rem, calc(0.43rem + 0.65vw), 0.94rem);
--space-xs-s: clamp(0.88rem, calc(0.74rem + 0.65vw), 1.25rem);
--space-s-m: clamp(1.13rem, calc(0.86rem + 1.30vw), 1.88rem);
--space-m-l: clamp(1.69rem, calc(1.40rem + 1.41vw), 2.50rem);
--space-l-xl: clamp(2.25rem, calc(1.73rem + 2.61vw), 3.75rem);
--space-xl-2xl: clamp(3.38rem, calc(2.81rem + 2.83vw), 5.00rem);
--space-2xl-3xl: clamp(4.50rem, calc(3.46rem + 5.22vw), 7.50rem);

/* Custom pairs */
--space-s-l: clamp(1.13rem, calc(0.65rem + 2.39vw), 2.50rem);
}

```

这里不需要媒体查询。您可以在填充和边距中使用这些 CSS 变量，在整个网站的元素之间创建成比例的间距。

你可以通过排版、间距和基于网格的布局来实现流畅。然而，这可能不足以做出一个完全响应的网站。

## 最后的想法

响应式设计很有挑战性，但是越来越容易了。如今，选择断点不再那么令人担忧。越来越多的人认为，我们并没有试图在多种屏幕尺寸上创建一个像素完美的网站。

CSS 已经有了很大的发展，现在可以创建适应可用空间的流畅设计，并且需要更少的干预。然而，理解断点仍然很重要，你有时会需要它们！

现在你可以根据内容和你面前的设计任务来选择断点，而不是遵循一条规定的路径。可以选择根据视口(媒体查询)或根据元素块(容器查询)来实现断点。从长远来看，这将简化创建响应式设计的过程。

在撰写本文时，容器查询仍然是新的。因此，我们需要成为好学生，改变我们的习惯，以适应这种多范式的格局。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。**