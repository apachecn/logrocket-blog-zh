# 终极网页设计工具:浏览器日志火箭博客

> 原文：<https://blog.logrocket.com/ultimate-web-design-tool/>

前端开发工具激增——出现了令人眼花缭乱、铺天盖地的术语、构建工具、框架、库和预处理程序。对于用代码创建可视化设计，这些工具都是不需要的。

CSS 有自己的变量，不需要 Sass。CSS grid 有一个大规模简化的布局，消除了对外部 CSS 框架的需求。你所需要的只是一个文本编辑器，一个 HTML 文档和一个浏览器。前端架构日益组件化的本质也意味着您通常不会从头开始—您可能已经有了一些构建块来拼凑。如果说在浏览器中设计曾经是费力和受限制的，那么现在已经不是了。

浏览器开发工具传统上用于调试 JavaScript 和检查网络请求。最近，我们看到浏览器增加了图形界面来操作 CSS。大多数浏览器都提供了颜色选择器和吸管工具来选择颜色。在 Chrome 中，这个工具将有助于显示颜色对比度。Chrome 还提供了一个 GUI 来添加或调整`text-shadow`和`box-shadow`。

![](img/5acb68078b701cb383a7f4f69f64210c.png)

To the left, Chrome GUI for colors and shadows, respectively. On the right, the gradient editor in Safari.

毫无疑问，火狐已经取代 Chrome 成为用户界面设计的首选浏览器。它的突出特点是 CSS 网格检查器。

## 布局

CSS grid 的到来让布局变得更加容易。如果你能*看到*网格，那么利用网格就容易多了。您可以通过按 HTML 检查器中的网格按钮或 CSS 检查器的`display`属性中的图标来打开网格检查器。

![](img/61530b38f77de0eb32ada3117997a6f6.png)

或者，dev tools 中的 layout 选项卡将列出页面上所有使用`display: grid`的元素。

网格检查器显示行号并可视化使用`grid-gap`属性创建的装订线。

![](img/1258e95f75a75e0a3aeeb0df2a73cb73.png)

Grid can be used for the layout of an entire page, as well as for smaller interface elements, as seen here on the New York Times homepage.

如果您正在使用命名的网格区域，也可以显示区域的名称。

![](img/02661cd6dcd35c5a8383ccabef41f64c.png)

网格检查器甚至可以可视化倾斜，旋转和缩放的网格。

![](img/fd8c193b784cef111ec8386ec9836d42.png)

The Stripe website makes heavy use of CSS grid.

Firefox 也将为 flexbox 提供类似的工具。

对于位置设置为`relative`、`absolute`或`fixed`的元素，Firefox 提供了通过在周围拖动来[重新定位元素的能力。为了让这个工具工作，您还需要为`top`、`bottom`、`left`和`right`属性中的至少一个设置一个值。](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Reposition_elements_in_the_page)

![](img/b8418c106604e9fe697f232c69c44fef.png)

这些工具都不能取代了解 CSS 的需要，但它们确实让在浏览器中进行设计变得更有吸引力。

## 响应式网页设计

响应式设计对传统的基于矢量的设计工具及其固定宽度的画板提出了最大的挑战。你的设计在超薄 iPhone SE 上看起来怎么样？还是一个笨拙的平板手机？

找到答案的唯一方法是不断调整浏览器的大小。基于流行设备预定义断点是一种反模式。设计三种屏幕尺寸并不合适。 *断点应由内容决定。*

> “先从小屏幕开始，然后扩展到看起来像屎一样。断点时间到了！”*–*[斯蒂芬·海伊](http://bradfrost.com/blog/mobile/bdconf-stephen-hay-presents-responsive-design-workflow/)

您只能通过打开 web 浏览器来微调断点。

![](img/e439fab3425c684382480c1503ef95d5.png)

While it’s not a replacement for testing in real devices with tools like Browserstack, responsive design mode is an easy way to preview designs on many different screen sizes.

## 排印

人们被内容所吸引，而这些内容主要是文本。这导致奥利弗·雷琴斯坦写下了 [*网页设计 95%是排版。火狐即将推出的字体编辑器提供了编辑文本属性的控件。这对可变字体特别有用。到目前为止，设计者被迫依靠文档来找出可变字体必须提供哪些自定义轴。Firefox 的开发工具不仅列出了字体自定义特性，还为每个轴提供了滑块控件。*](http://informationarchitects.jp/the-web-is-all-about-typography-period/)

![](img/d5181c412695799b281131ddfd8f2a80.png)

## 形状

传统上很难摆脱 CSS 以盒子为中心的特性。*一切都是长方形*。随着 CSS 形状的引入，这种情况发生了变化。形状路径编辑器是一个使用`clip-path`和`shape-outside`属性查看和编辑形状的工具。

![](img/50a2b06de4d94711b73bcbeda7504f8f.png)

## 动画

大多数屏幕设计工具——Photoshop、Adobe XD、Sketch 等——缺乏设计动画的能力。像 [Principle](http://principleformac.com/) 这样的专业工具是存在的，但是对于设计者来说，与开发人员一起完成这样的任务是非常有意义的。通过拖动贝塞尔曲线的手柄，可以使用开发工具轻松调整过渡和动画的放松。

![](img/f464db6d50c42cceea9691f456e11c59.png)

不久前，Photoshop 还是网页设计的默认工具。一个臃肿，功能丰富的应用程序，可以做任何事情，从详细的照片合成到视频编辑。从那以后，情况有了明显的改善。像 Sketch、Adobe XD 和 InVision Studio 这样的工具是专门针对网页设计的，但是缺乏网页的背景。当你在与最终产品完全不同的媒介中工作时，很难做出完全明智的设计决策。

> “即使是最‘像素完美’的实体模型，也只是暗示了在一种状态下，在一个屏幕尺寸下，在一个浏览器上，在一个设备上，用一组数据，用户界面可能会是什么样子。”–[Colm Tuite](https://twitter.com/colmtuite?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)，Modulz 的创建者

对于所有关于渐进式网络应用程序和单页应用程序的宣传，互联网的大部分仍然是传统的页面和网站。我们不是都在设计下一个脸书。我们可以哀叹大多数传统工具缺乏设计动画、过渡等的能力。最终，动画仍然是大多数网站的一个小点缀。

好的网页设计很大一部分仍然由好的图形设计组成——配色方案、排版、布局——基于矢量的设计工具仍然是建立这种视觉语言的好地方。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

很少有人*只*在浏览器中设计。一个空白的浏览器屏幕和一个空白的 HTML 文件不一定是创作过程的最佳起点。设计工具可以是*勾画想法*的好地方，而不需要被用来创造僵化的*最终*设计。

> “我们经常看到设计师试图创造出完美的视觉效果，然后把它们扔到墙上”——开发者和设计师也可以结对

设计的实现经常稍微偏离设计文档，导致开发人员和设计人员之间乏味的来回奔波。像 [InVision](https://www.invisionapp.com/feature/inspect) [Inspect、](https://www.goabstract.com/blog/introducing-inspect-where-the-file-is-the-design-spec/) [Avocode](https://avocode.com/) 和 [Zeplin](https://zeplin.io/) 这样的工具从这个交接中去掉了很多痛苦，但是用代码设计完全避免了这个问题。

开发人员将设计决策编入变量中以保持一致性:

```
pill-button {
    border: $border-hairline;
    border-radius: $border-radius;
    font-size: $font-size-small;
    padding: $space-xs;
}
```

不幸的是，设计软件无法利用它们。但是，如果你用代码来设计，*你可以*。

> “决策通常仍然隐藏在开发他们所拥有的基于 web 的产品的开发人员所使用的某个回购文件中。设计师呢？……我们为每个人编写了决策代码，但它们被藏在一个别人找不到的回购地牢里。”–内森·柯蒂斯，[设计系统中的符号](https://medium.com/eightshapes-llc/tokens-in-design-systems-25dd82d58421)

经常是设计师谈论设计系统、模式库和风格指南，然而也经常是设计师自己引入了不一致性。

> “设计的特征元素与以前设计的元素略有不同(或明显不同)。当这种情况发生时，开发人员有一个难题。他们是(A)开发一个像素完美的设计实现，还是(B)使用与他们面前的设计不同的现有模式？如果开发人员构建设计组合，代码库会随着异常和设计不一致而增长。”–乔纳森·斯努克，[设计的法典化](https://snook.ca/archives/design/codification-of-design)

Sketch 中的符号、文本样式和图层样式等功能，以及使用库来共享它们的能力，使得设计人员的设计更加容易。

## 这种方法的一个潜在缺点是

当你用实际的代码进行设计时，在生产中使用这些代码是很诱人的，因为你已经经历了编写它们的麻烦。这可能会导致*死代码*——什么都不做的 CSS 样式。编写好的 CSS 很大程度上是知道将什么抽象到一个类中的问题。如果一个设计者可以提供多种页面设计，那么以系统化的方式来做这件事会更容易，而不是像你所做的那样编写 CSS。

## 设计师该不该学 CSS？

除非你跟上 CSS 的发展，否则你根本不知道你的设计在技术上可行。在你觉得有生产力和创造力的地方设计。但至少要在浏览器中测试、提炼、迭代。这可能不是你的起点，但是尽快进入浏览器。不会编码的话，*协作*。