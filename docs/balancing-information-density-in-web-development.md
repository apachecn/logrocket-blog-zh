# 平衡 web 开发中的信息密度

> 原文：<https://blog.logrocket.com/balancing-information-density-in-web-development/>

极简设计等同于可用设计的神话在[得到了很好的证明](https://uxmyths.com/post/115783813605/myth-34-simplicity-minimalism)。在设计者试图创建降低复杂性的界面时，他们经常基于简单性的想法而不是内容的细微差别和观众的需求来过度简化内容和交互。

美学上的简洁被视为优秀设计的事实标准，即使密集、信息丰富的界面——尽管它们看起来很复杂——在许多情况下可能更清晰、更有用。

在本文中，我们将讨论什么是信息密度，以及设计人员和开发人员在设计和构建 web 和移动应用程序时应该如何考虑它。

## 什么是信息密度？

信息密度指的是界面中信息内容的数量——用[爱德华·塔夫特的话](https://infovis-wiki.net/wiki/Data-Ink_Ratio)来说就是“数据墨水”——并通过信息与可用空间的比例来衡量。最终，网络和移动应用应该为给定的界面找到正确的信息密度，以平衡上下文和设计，而不是只追求表面的简单。

在数字屏幕上，这可以包括静态或交互式内容:文本、缩微副本、标签、链接、按钮、控件、图表——任何有指导意义或有用的东西，而不是纯粹的美学。数据丰富的高密度显示器具有丰富的内容，而数据薄的低密度显示器相对较少。

![Density Interfaces Comparison](img/0cabc4bee2996aad2ac8d930a0da9f79.png)

Examples of low-, medium-, and high-density interfaces: Google’s search page, Google’s search results page, and the New York Times landing page.

对于适当的信息密度水平，没有一个通用的规则。一个界面的成功很大程度上取决于设计者在多大程度上为给定的上下文、内容、受众和屏幕尺寸找到了合适的密度，而后者使这项工作变得复杂。

例如，在桌面上实现理想水平密度的布局在移动屏幕上可能会变得过于密集，而在桌面上移动布局可能会感觉过于简单。可用性和理解性问题可能出现在这个范围的任何一端。

设计师的任务不是任意简化看似“繁忙”的设计，而是巧妙地管理复杂性。这里有一些原则和技巧，可以帮助产品团队在使用丰富的、响应性强的界面时找到理想的信息密度。

## 管理应用程序设计的信息密度

### 1.通过添加细节创建上下文

界面设计的重点是创造环境，帮助人们理解手头的任务和内容。而像[迪特·拉姆斯](http://cameronkippdesign.com/blog/2019/1/25/dieter-rams-principle-3)(“我的意图是省略每一个不需要的元素”)和[约翰·梅达](http://lawsofsimplicity.com/)(“实现简单的最简单方法是通过深思熟虑的简化。当有疑问时，就删除”)已经使设计师习惯于删除多余的设计元素，强调减法可能会以有效的上下文创建为代价，这需要添加细节。

拉姆斯和前田分别对“让产品变得可理解”和“小心你从设计中移除的东西”的告诫则不太受重视。

当设计低保真度的界面时，我发现逐步加入高保真内容很有帮助——例如，包括缩微副本、上下文帮助文本、代表性正文副本和关键数据等。

从一开始就考虑代表性的内容，要求设计师在处理交互的核心元素时，对视觉层次做出决定。

考虑用户可能会问这个界面什么问题。我可以加入哪些相关信息来帮助他们回答这些问题，而不脱离他们的背景？我该如何调整交互，使其对初次用户更容易理解，对重复用户更高效？

![High Fidelity Content With Low Fidelity Design](img/cf6ddb245af316fae879f7534fcd4779.png)

An example of how high-fidelity content can be used in low-fidelity design work.

就像填一本涂色书的轮廓一样，我把线框作为最终设计的概念边界；当我以更高的保真度工作时，我会引入美学上的改进，并融入细节，因为我在设计上花了更多的时间，并通过了可用性测试。

### 2.保持各种屏幕尺寸的密度一致

移动优先的设计运动在一定程度上是对处理不当的信息密度的回应——全桌面网站被硬塞进移动视窗宽度，导致内容小得令人难以阅读，以及笨拙的平移和缩放。

然而，随着设计师学会优先考虑小屏幕的基本元素并在流体网格系统中工作，为移动观看优化的布局有可能在桌面和显示器宽度上变得空白。

![Interface with Better Density on Mobile](img/fb462904650cea61181e84fa391bcbeb.png)

An example of an interface that achieves better density on mobile viewport widths than desktop widths.

除了为响应式内容行为创建模式(例如，定义堆叠规则)，考虑如何在各种屏幕尺寸范围内保持相似的信息密度水平也很有帮助——因此，在 web 开发中创建响应式设计也很有帮助。

移动界面不需要稀疏就可以使用或吸引人。一个很好的例子可以在我的同事 Kevin 的文章中看到。他认为，标准的内容堆叠惯例会导致较小设备上不必要的长页面。他的探索展示了如何在营销页面上增加密度以达到良好的效果。

![Exploring Density Mobile Widths](img/4de139b671fe5f22fc1542342ca9b4c4.png)

Explorations for improving density for a marketing page at mobile widths.

有些界面太复杂，不能有效地缩小规模，需要对布局、导航结构和交互进行大量的修改。

这也是为什么像 Airtable 这样具有高度交互性的信息丰富的产品会创建一个完全独立的移动应用程序来显示用户可以在桌面上访问的相同数据。

在下面的截图中，浏览器视窗(1280px × 694px)比移动视窗(375px × 667px)大 3.5 倍，显示了 63 个表格单元格，而移动视窗只有 12 个单元格。

如果移动视图能够显示 6 个以上的单元格，它将或多或少地与桌面视图的比例密度相匹配。考虑到所有因素，Airtable 可以有效地处理每个视口密度。

![Airtable density](img/2c816d1f3a1bd8dffd09a54abf2ae16b.png)

An example of how Airtable preserves density across mobile and desktop viewport widths.

### 3.使用最小文本和触摸目标尺寸作为边界

虽然内容(而不是美学)的考虑应该促使设计师在更高的密度水平上工作，但可访问性标准可以作为防止界面变得过于密集的指南。

尽管 web 的最小字体大小[还没有定义(可读性取决于字母表、对比度和用户的视觉)，但人们普遍认为 12px 左右是界面文本的最小字体。](https://css-tricks.com/accessible-font-sizing-explained/)

另一个有用的参考是移动触摸目标尺寸，谷歌的材料设计规范定义为至少 48px [平方](https://material.io/develop/web/supporting/touch-target)。(需要注意的是，链接元素的可视表示可以小于 48px 正方形，但是有效的可点击区域应该至少是这个尺寸)。

材料设计[密度指南](https://material.io/design/layout/applying-density.html)给出了引入更高密度时保持可达性的有益示例。

![Material Design Mobile Touch Targets](img/ffbbec65a6a74bd30f31aa407f4e9313.png)

Material Design’s recommendations for mobile touch targets.

### 4.进行理解设计测试

增加密度的目的是帮助人们理解内容并与之互动，而验证这一点的最佳方式是通过可用性和理解测试——尤其是在移动设备上。

除了标准的任务可用性度量之外，产品团队在测试密集界面时可能会留意一些事情:

*   用户在信息的摘要视图和详细视图之间切换的难易程度如何？当他们从一个层次进入另一个层次时，他们是否保持了对上下文的感觉？尝试用不同的方法来表现抽象层次(如杰夫·丹斯的“T2”、“垂直”和“分层”概念)，找到最有效的方法来激发探索和理解。
*   用户滚动到他们期望的地方了吗？Mike Bostock [列出了一些滚动交互式、数据密集型图形和视觉叙事的规则，指出只要访问者注意到视窗之外的附加内容(并且滚动交互行为符合用户的预期)，滚动(而不是点击或跳转)就是一种轻松的行为。](https://bost.ocks.org/mike/scroll/)
*   显示的信息有助于用户的认知努力吗？在较小的屏幕上呈现复杂信息的一个挑战是用户浏览内容时的记忆负担。正如 Raluca Badiu [所说的那样](https://www.nngroup.com/articles/scaling-user-interfaces/)，“人与设备之间的通信通道的容量自然会受到用户工作记忆的限制。如果网站或应用程序要求用户学习太多新信息……用户通常会陷入僵局，他的工作记忆将不再有这些信息，他将需要去搜索。”较小的设备屏幕固有地提供了较窄的内容视图，要求用户将顺序查看的信息拼凑在一起，否则他们可能会在桌面上的单个视图中看到这些信息。在窄屏幕上，什么类型的上下文帮助或响应性布局变化可能有助于这种意义的形成？
*   用户在使用前后如何感知界面的密度？对密度的感知[是主观的](https://www.nngroup.com/articles/china-website-complexity/)，因文化、技术成熟度、认知能力和年龄而异。询问人们对界面密度的第一印象，以及在完成一些任务后，可以消除对产品过于复杂的担忧。如果人们一开始觉得这个应用程序很复杂，在熟悉它之后，很可能需要额外的修改。

## 结论

对高密度接口的厌恶很大程度上是对设计糟糕的高密度接口的厌恶。杰西·詹姆斯·加勒特(Jesse James Garrett)在他的经典之作《用户体验的要素》(The Elements of User Experience)中指出，“当人们评论一个设计‘繁忙’或‘混乱’时，他们实际上是对这个设计不能让他们流畅地浏览页面的事实做出了反应。”设计师未能建立清晰的信息和视觉层次。

问题不在于内容本身，而在于它是如何显示的。通过将复杂性重新定义为一个设计问题，而不是一个内容问题，更多经过深思熟虑显示的信息会带来更明智、更清晰的交互。

![Information Dense Interfaces](img/916875890614c7253c162c48ea9ede2c.png)

Examples of information-dense interfaces used by wide audiences every day.

随着设计师认识到 Airtable、Spotify 和谷歌地图等日常信息密集型应用的可用性，他们应该寻求最佳密度水平而不是最低水平，寻求复杂内容的精心展示所带来的优雅。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)