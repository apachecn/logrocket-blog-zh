# 如何设计高性能的动画和微交互

> 原文：<https://blog.logrocket.com/how-to-design-highly-performant-animations-and-micro-interactions/>

动画可以帮助创建更受欢迎和更吸引人的用户体验。他们可以[提高用户对你品牌的认知和记忆](https://www.researchgate.net/publication/229351931_The_Effects_of_Animation_and_Format_on_the_Perception_and_Memory_of_Online_Advertising)。然而，添加高质量的动画并不总是一件容易的事情。我们不能随意地到处撒动画！

![Pull to Refresh Animation Example](img/bfbdb3d40370e052a111eaed95529847.png)

Pull to refresh by [Eugene Avdalyan](https://dribbble.com/eavdalyan).

让我们把你的网站想象成一个电梯推销:你有很短的时间来赢得用户，如果有延迟，人们不会等着。你的网站需要快，所以缓慢的动画是不允许的。

今天，我将讨论如何创建高性能的动画。

## 为什么网站速度很重要

谷歌委托的一项新研究调查了移动网站速度对商业的影响。这些发现显示了速度和用户体验(UX)之间的密切联系。

> 速度对用户体验有直接影响。速度在任何数字计划的成功中起着至关重要的作用，在电子商务和其他交易网站上尤为明显。70%的消费者承认页面速度会影响他们从在线零售商处购买的意愿，在美国，延迟是消费者决定放弃移动网站的首要原因，10%的消费者将下载速度慢归咎为不购买的原因。

研究发现，网站速度每提高 100 毫秒，零售消费者的支出就会增加近 10%。此外，奢侈品消费者参与度更高，页面浏览量增加了 8%。

赌注很高！

![Experiences Graphs](img/f122cd666faf10fb071b5a545ad5c5a2.png)

网站速度也是搜索引擎对你的网站进行排名的一个重要因素，尽管搜索引擎背后的公司并不清楚速度到底有多重要。

## 网站易访问性如何影响设计

人们不会以同样的方式访问你的网站。设备各不相同。网速各不相同。浏览习惯不一。同一个网站，根据用户的情况不同，体验也不同。如果你想让每个人都在你的网站上有一个好的体验，你需要为低速连接和老式设备设计。

[网络年鉴](https://almanac.httparchive.org/)进行年度网络状态报告。《2020 年报告》中的[绩效结果](https://almanac.httparchive.org/en/2020/performance)显示，要实现为快速网站设定的目标，还有很多工作要做，但是*有一些*乐观的理由。

重要的是，我们不要忽视或忘记这些事实。

## 使用 RAIL 来确定关键指标

谷歌有一个以用户为中心的性能模型，叫做 [RAIL](https://web.dev/rail/) 。RAIL 代表 web 应用生命周期的四个不同方面:响应、动画、空闲和负载。

RAIL 设定了具体的性能目标，并提供了实现这些目标的指导方针。

性能目标是:

*   **响应:**在 50 毫秒内处理事件
*   动画:在 10 毫秒内制作一帧，并以视觉平滑为目标，因为用户会注意到帧速率的变化
*   **Idle:** 最大化空闲时间，增加页面在 50 毫秒内响应用户输入的几率
*   **加载:**在不到 5 秒的时间内交付内容并开始互动

让我们更详细地看看动画性能目标。从技术上来说，每帧的最大预算是 16ms(每秒 1000ms / 60 帧≈16ms)，但浏览器需要大约 6ms 来渲染每帧，因此每帧 10ms 的准则。为什么每秒 60 帧？

如果我们在每次屏幕刷新时创建一个新的帧，我们将会有平滑的移动。典型的设备刷新率是 60Hz(赫兹)，这意味着屏幕每秒钟刷新 60 次。

但是为了以不同的刷新率以 60fps 运行，我们需要在很短的时间内渲染一帧:

当考虑到器件的差异时，这是一个很难达到的标准。不仅仅是屏幕的刷新率，很明显是设备的速度。在最慢的设备上，60fps 可能是遥不可及的。

首要任务是避免 jank——任何滞后或造成不平稳运动的东西。我们努力的目标是提高效率并保持尽可能高的帧速率。

另一个需要争取的重要指标是一致的帧速率。波动将被用户视为口吃。谷歌最近引入了一个新的指标，称为平均丢帧(ADF)。ADF 是一个[平滑度指标](https://www.chromium.org/developers/design-documents/rendering-benchmarks)，衡量网页的 GPU 和渲染性能。ADF 越低，页面就越平滑。这是我们应该监控的另一个关键指标。

对于用户交互，100 毫秒内的响应时间让人感觉结果立竿见影。这应该是触发动画的目标。为了确保在 100 毫秒内给出可见的响应，[用户输入事件应在 50 毫秒内处理](https://web.dev/rail/#50-ms-or-100-ms)。

![Trigger Animation](img/a37b0314015713096de80a455c5fd7da.png)

如果响应时间超过 100 毫秒，用户会感觉到轻微的延迟。如果是这种情况，您应该向用户提供反馈，比如进度条。

你应该考虑动画如何适应网页的整体性能。设置性能预算对于设计相关的决策非常有帮助。您可以在 Lighthouse 工具中用一个简单的 JSON 文件创建一个[性能预算，其中包含您对](https://www.afasterweb.com/2020/01/28/performance-budgets-with-lighthouse/)[重要页面指标](https://web.dev/vitals/?gclid=Cj0KCQiA2af-BRDzARIsAIVQUOf5Qgx0l_iNBaV_V5uxvOunt8IufbdSJWv0quP6suE8Gd027YbxJUwaAvANEALw_wcB#core-web-vitals)的可接受值。如果您的动画在页面加载时处于非活动状态或不在屏幕上，您的动画对这些指标的影响会小得多。

## 改进渲染的算法

有一个通用的算法，各大浏览器渲染一个帧都遵循这个算法。谷歌称之为 [*像素管道*](https://developers.google.com/web/fundamentals/performance/rendering/#the_pixel_pipeline)；而 Mozilla 称之为 [*渲染瀑布*](https://developer.mozilla.org/en-US/docs/Tools/Performance/Waterfall#Rendering_waterfall) 。

![Rendering Waterfall](img/2d586c6a76ab5a14e2b71a4ede47a10e.png)

算法中的每一步都会触发后面的步骤:

1.  **JavaScript** :当 [DOM](https://developer.mozilla.org/en-US/docs/Glossary/DOM) 或 [CSSOM](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Object_Model) 发生变化时，JavaScript 触发视觉变化
2.  **计算样式**:如果浏览器认为页面元素的计算样式已经改变，它必须重新计算
3.  **布局**:布局根据计算的样式确定每个元素的尺寸和位置。Mozilla 也称之为“回流”
4.  **绘画**:绘画将每个元素的像素填充到图层上。它还没有将这些图层绘制到屏幕上
5.  **构图**:以正确的顺序将绘制的图层绘制到屏幕上

浏览器不必每次都经历每个步骤。如果一个改变只需要执行一个绘制，那么它将跳过布局步骤。

你大概能猜到，如果浏览器渲染一帧的步骤少，性能会更好。如果我们了解什么类型的变化触发了不同的步骤，那么我们就可以在创作动画时做出更好的决定。

Google 创建了一个名为 [CSS Triggers](http://csstriggers.com/) 的小网站，上面列出了 CSS 属性以及它们触发的渲染步骤。现在已经几年没更新了，准确性值得怀疑。可以看一下大概了解一下。

## 设计高性能的动画和微交互

首先，重要的是说我们不想制定规则。规则意味着如果你遵循它们，你每次都会得到正确的结果。不是这样的！当然，你正在做的事情的背景很重要。随着浏览器内部结构的变化，事情也会随着时间而变化。

最好有一套设计启发法来帮助你做出合理的设计决策。你不应该被它们吓倒——它们是帮助你避开陷阱的。您应该使用 DevTools 来测量性能，以验证您正在做的事情。

我并不提倡用 CSS、JavaScript 或使用 JavaScript 库来制作动画。你可以在任何一个方面取得相似的成绩。这句话有很多细微差别和上下文，但这是一个不同的对话，我们不会在这里进入。

让我们来讨论一下我们的设计启发法。

### 1.尝试坚持动画合成属性`transform`和`opacity`

如果我们使用仅影响渲染算法的合成步骤的属性，我们将获得最佳性能。`[transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)`和`[opacity](https://developer.mozilla.org/en-US/docs/Web/CSS/opacity)`属性就属于这一类。

这些属性的渲染可以卸载到 GPU，但是需要注意的是，元素应该在自己的层上。在某些情况下，浏览器会自动将元素提升到一个层，但您也可以自己触发它。

![Star Wars Animation](img/fd5630d6ccd33586dc18b2159098d3ec.png)

例如，[这部星球大战动画](https://codepen.io/robjoeol/pen/KKgKzXp)实际上有 3 层。

![Star Wars Animation Layers](img/7389c54d4b56aa32bbf956bc97ab34af.png)

如何将一个元素提升到一个层？

*   在 CSS 中**:可以设置属性`[will-change](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)`。对于不支持`will-change`的老浏览器，你可以添加`transform: translateZ(0);`到一个元素中**
*   JavaScript 中的**:设置一个 3D 特征的变换，比如`translate3d()`和`matrix3d()`会创建一个图层。您可能会注意到一些 JavaScript 库会在幕后完成这项工作**

您需要有选择地将哪些元素提升到层中。问题是你创建的每一层都需要 GPU 内存和管理。在内存有限的设备上，对性能的影响可能远远超过创建层的好处。MDN 以*非常*谨慎的方式讨论了关于`[will-change](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)`财产的一些问题。

最近，[网页动画 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Using_the_Web_Animations_API) 已经在[所有常青树浏览器](https://caniuse.com/web-animation)中可用。MDN 称“它让浏览器在没有黑客、强制或`[Window.requestAnimationFrame()](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)`的情况下进行自己的内部优化。”

我想这意味着您已经摆脱了将元素提升到层的困境，但是它并没有明确地说明这一事实。在 API 被更广泛地使用之前，可能需要更多的时间才能正确地了解其含义。

### 2.优化油漆

更改除`[transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)`和`[opacity](https://developer.mozilla.org/en-US/docs/Web/CSS/opacity)`之外的任何属性都会触发绘画。绘制通常是渲染中最昂贵的步骤，因此这是一个成熟的优化领域。

您可以做的事情很少:

1.  将移动或定期绘制的元素提升到它们自己的层:这是我们第一次试探的结果。如果一个元素在它自己的层中，它不会触发相邻元素的绘制。但是在这样做时要有选择性，并检查它对性能的影响
2.  **减少绘画区域**:你可以通过确保元素不重叠来减少绘画区域。您可以寻找避免动画显示页面某些部分的方法。有时这是一个挑战，因为浏览器可能会将两个需要绘制的区域结合在一起，并且整个屏幕可能会被重新绘制
3.  **简化油漆复杂度**:油漆某些属性比较贵。例如，任何包含模糊(如`box-shadow`)的事物比属性(如`background-color`)的绘制成本更高。问问你自己，是否有可能使用一套更便宜的风格或替代手段来达到你的最终结果

### 3.优化 JavaScript 执行

JavaScript 运行在浏览器的主线程上。如果您的 JavaScript 运行了很长时间，它将阻塞其他渲染任务，可能会导致丢帧。对于 JavaScript 何时运行以及运行多长时间，您应该有策略。

1.  **使用** `[requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)` **或者** [**网页动画 API**](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Using_the_Web_Animations_API) :你想在浏览器合适的时间做视觉上的改变。正确的时间是在帧的开始。保证 JavaScript 在一帧开始时运行的唯一方法是使用`requestAnimationFrame`或 Web 动画 API。避免对动画使用 setTimeout 或 setInterval。它们是不可靠的，因为它们固有地在帧中的某个点运行，并且可能引入 jank。JavaScript 库在幕后使用 requestAnimationFrame
2.  **降低复杂性**:考虑一种批处理方法，将一个较大的任务分成一些小任务。让每个任务花费的时间不超过几毫秒，并在每一帧的`requestAnimationFrame`处理程序中运行它。您还可以考虑使用 web workers，我们接下来将讨论这一点

### 4.将 web workers 用于非 UI 任务

网络工作者是 JavaScript 版本的线程。它们让我们有机会在后台运行任务。

主线程可以被认为是 UI 线程，因为它应该是它的焦点。对于流畅的动画，当动画运行时，你不能承担额外的工作。如果您需要对动画进行一些数据处理，您应该考虑将这项任务转移到 web worker。

好的用例是数据排序、搜索和模型生成等任务。

```
var dataSortWorker = new Worker("sort-worker.js");
dataSortWorker.postMesssage(dataToSort);

// The main thread is now free to continue working on other things...

dataSortWorker.addEventListener('message', function(evt) {
   var sortedData = evt.data;
   // Update data on screen...
});

```

考虑一个动画，它用一个大数据集对一个条形图进行排序。如果我们将数据排序推给一个 web 工作人员，您可以确保条形图仍然能够响应。

然后，您可以选择以递增的方式动画显示这些更改。这将给用户适当的反馈，以确保他们知道任务的进度。

Samsur 在最近的一篇文章中探索了如何在 WebXR 应用程序中使用 web worker，他将物理计算放入 web worker 中。

网络工作者的工作是有限制的。他们无法访问一些 Web APIs，比如 DOM。所以，它们只适合某些类型的任务。

### 5.去抖或节流你的输入处理器

在动画中使用输入处理程序时需要小心。如果你的动画是在滚动或响应鼠标或触摸事件时触发的，你需要考虑几件事。

一些输入事件，例如滚动，每秒可以触发一百个事件。你的滚动处理器准备好这样的执行速度了吗？

我们不想那么快更新一个动画，这是肯定的！我们可以通过[去抖动或](https://css-tricks.com/debouncing-throttling-explained-examples/)节流来防止这种情况。

去抖动把突然爆发的事件组合成一个，所以我们不会如此频繁地对事件做出反应。

![Debouncing](img/840fd834a85bd644e156410a9cb6783a.png)

[Debouncing Leading codepen](https://codepen.io/dcorb/pen/GZWqNV) by Corbacho.

节流是一种类似的技术，但是它看起来保证了每 X 毫秒执行一次的恒定频率。

有时，您可以通过触发重排来丢弃帧。我们希望避免导致回流喷漆循环的操作。Paul Lewis 在谈到动画中的滚动事件时很好地解释了如何防止这种情况。

### 6.使用翻转技术

[翻转技术](https://css-tricks.com/animating-layouts-with-the-flip-technique/)在执行前预先优化动画。这个想法是颠倒动画的状态。通常，我们制作“正前方”动画，在每一帧上做一些昂贵的计算。FLIP 根据最终状态预先计算变更。第一帧是最终状态的偏移。这样，动画以一种更便宜的方式播放出来。

当用户输入触发动画时，这种技术特别有用。你可以进行相对昂贵的预计算，因为你有一个 100 毫秒的窗口，你可以在用户不注意的情况下完成工作。如果你在那个窗口里面，用户会觉得响应是即时的。

![Flip Technique](img/fdbf6c6b3dba8d2f29ae8f35961889bf.png)

对于动画来说，这是一个不同的心理模型，需要花一些努力去理解。翻转让我想起了电影宗旨，颠倒事件顺序！

你需要看到一个例子才能完全理解。David Khourshid 在他的文章[用翻转技术制作布局动画](https://css-tricks.com/animating-layouts-with-the-flip-technique/)中详细介绍了这个主题。

## 这些试探法不会束缚我的风格吗？

不要！你不应该感到压抑！

你会惊讶地发现你可以用属性`[transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)`和`[opacity](https://developer.mozilla.org/en-US/docs/Web/CSS/opacity)`实现多少。像许多创造性的努力一样，给*施加一些*约束实际上可以简化你的过程，促使你更有创造力。这个代码笔演示了你可以用这两个属性做些什么。

请看 [CodePen](https://codepen.io) 上 Rob ( [@robjoeol](https://codepen.io/robjoeol) )
的 Pen [表演动画 Cheatsheet](https://codepen.io/robjoeol/pen/mdrRpzB) 。

记住，也可以随意使用其他属性，核心信息是:不要先伸手去拿，要明智地使用它们！

## 如何在 DevTools 中测量

我将简单介绍一下在 Chrome DevTools 中最有用的上下文。

要审计性能，您可以在 **Performance 选项卡**上找到您需要的一切。

![Devtools Performance Tab](img/7b4ba1b96a3a3647115be592d2ac5f02.png)

请记住，在查看帧速率时，**只有在物体移动时，您才需要保持 60fps** 。所以，当什么都没发生或发生的很少时，自然会有低谷。

**摘要选项卡**中的放射状饼图将渲染活动组合在一起。这些组对应于像素流水线中的各个步骤，例如*渲染*覆盖*布局*和*风格*。这些颜色和我的图表相配。绝非巧合！😉

[**动画选项卡**](https://developers.google.com/web/tools/chrome-devtools/inspect-styles/animations) 允许您检查动画并控制其回放。您需要进入**设置**来打开该选项卡。

![Devtools Animations Tab](img/07ab7cbb86d31ecdef90c63e3b396c78.png)

**渲染选项卡**对于排除渲染故障非常重要。你需要去**设置**打开这个标签。这可以帮助您诊断绘画、图层和滚动的问题。

![Devtools Rendering Tab](img/4c938211d69246ff139d81d40797ab7f.png)

您可以通过启用**帧渲染统计**来显示帧速率统计的插图。在制作动画时打开会很有帮助。

![Devtools Frame Stats](img/e7dd2fadfd1adc778b10cc6e91c66697.png)

你可以阅读 Chrome DevTools 的文档来了解更多信息。[火狐的 Devtools](https://developer.mozilla.org/en-US/docs/Tools) 也很优秀。

## 结论

即使一部动画很美，但 jank 的存在也会给用户留下不好的印象。创作表演性的动画不应该感觉像一个巨大而可怕的努力。每隔 16.7 毫秒，你需要有意识地让浏览器做些什么。如果我们这样做了，我们就给了浏览器成功制作流畅动画的最佳机会。祝你动画制作愉快！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)