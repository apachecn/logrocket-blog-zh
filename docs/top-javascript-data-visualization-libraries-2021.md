# 2021 年顶级 JavaScript 数据可视化库

> 原文：<https://blog.logrocket.com/top-javascript-data-visualization-libraries-2021/>

## 介绍

简而言之，数据可视化是指我们如何用图表、图形等视觉效果来表示我们的数据。它以图形格式表示数据，通常是图表或图形的数据点和线条之间的映射或关系。通常，它需要为我们的数据设计正确的可视化界面。

可视化的形式通常取决于所选择的图形元素或类型与所涉及的数据点之间的关系。最终目标是通过视觉或图形方式清晰有效地传达来自数据源的信息。

Data viz，正如它有时被称为的那样，特别有助于我们讲述关于我们的数据的故事，并且在这个过程中，理解一大组数据点的表示。从本质上讲，以一种美观且易于使用的方式来表示数据是数据的主要目标，即。

当涉及到非常大和复杂的数据集时，data viz 库的功能对于有效的洞察也很重要。在本教程中，我们将会看到一些 JavaScript 的数据可视化库，以及它们之间的比较。

## 探索 JavaScript 中的数据 viz 库

如今，我们构建的几乎每个应用程序都需要或利用数据来增强提供给用户的功能。现在有大量的 JavaScript 库可以用于数据可视化。因此，找到正确的方法对于简化我们的开发过程至关重要。

在本帖中，我们将探索 JavaScript 中一些常见的数据可视化库:

*   高图表
*   吐司 UI 图表
*   D3.js
*   雷查尔兹
*   Chart.js

我们将比较它们的优缺点、主要特性和功能、理念等等。最终，我们的目标是理解为什么我们会根据这些可视化库的可用特性和我们的特定用例来选择其中一个。

现在让我们在下面依次强调这些库:

### 高图表

Highcharts 是一个完全基于原生浏览器技术的数据可视化库，这意味着它不需要像 Flash 这样的客户端插件。使用 Highcharts 的主要优势之一是它可以在所有现代浏览器中很好地工作，包括移动设备和版本 6 的 Internet Explorer。

Highcharts 可以在任何支持 HTML 的服务器上运行。我们甚至可以从文件系统本地运行 Highcharts，因为所有的渲染都是在浏览器中本地完成的。请注意，标准浏览器使用 SVG 进行图形渲染。

> **注**:就老浏览器(IE 6–8)支持而言，需要一些包含常用数组函数的 polyfills。此外，由于旧的 IE 版本不支持 SVG 渲染，VML 渲染器也需要高图表工作。

Highcharts 可以通过 [npm](https://highcharts.com/docs/getting-started/install-from-npm) 和 [Bower 打包获得。](https://highcharts.com/docs/getting-started/install-from-bower)为了使用 Highcharts，我们可以在网页的`<head>`部分包含 JavaScript 文件，如下所示。

```
<script src="https://code.highcharts.com/highcharts.js"></script>

```

谈到基本的定制，我们可以应用一个全局主题。主题是通过`[Highcharts.setOptions](https://api.highcharts.com/class-reference/Highcharts#.setOptions)`方法全局应用的一组选项。还有一个顶级配置选项， [responsive](https://api.highcharts.com/highcharts/responsive) ，它包括多种响应图表类型。

Highcarts 还内置了一些性能指标。例如，使用 ES 模块，我们可以创建自己的[自定义 Highcharts 包文件](https://www.highcharts.com/docs/getting-started/how-to-create-custom-highcharts-files)。自定义文件可以优化浏览器加载速度，例如，由于文件更小，需要请求的文件更少。有关其他性能优化的更多详细信息，请参考[文档](https://www.highcharts.com/docs/getting-started/frequently-asked-questions#how-can-i-get-the-best-performance-out-of-highcharts)。

> **注** : Highcharts 可用于多种[多种图表类型](https://www.highcharts.com/docs/chart-and-series-types/sankey-diagram#!)，具有数据排序、boost 渲染、[等高级特性](https://www.highcharts.com/docs/advanced-chart-features/boost-module#!)。它也可以用于股票，地图和 Gantts。

与包括 Chart.js 在内的许多图表库不同，Highcharts 有一个内置的可访问性模块。要包含可访问性模块，只需在包含任何 Highcharts JS 文件后添加以下文件:

```
<script src="https://code.highcharts.com/modules/accessibility.js"></script>

```

其他辅助功能包括屏幕阅读器、国际化和[等等。另外，Highcharts 还附带了一个导出模块，允许用户下载 PDF、PNG、JPG 或 SVG 格式的图表。导出数据模块甚至允许](https://www.highcharts.com/docs/accessibility/accessibility-module#other-accessible-features)[将图表数据导出为 CSV](https://api.highcharts.com/highcharts/exporting.csv) 、XLS 或 HTML 表格格式。

从版本 2 开始，Highcharts 就以模块化的方式构建，依靠扩展来获得额外的功能。扩展允许我们在库的能力范围内构建或添加更多的特性。更多细节可以在这里找到[。](https://www.highcharts.com/docs/extending-highcharts/extending-highcharts)

### 吐司 UI 图表

Toast UI Chart 是一个漂亮的统计数据可视化库。和 Highcharts 一样，Toast UI Chart 也提供了对传统浏览器的强大支持。因此，它保证了所有浏览器的外观一致。它还绘制速度快，性能好，不需要聚合填充。谈到渲染机制，它使用本地浏览器格式，包括 SVG 和 RVML。

吐司 UI 图直观易用。同样值得注意的是，它与当今最流行的两个开源前端库/框架集成得很好:React 和 Vue.js。因此，当谈到一个简单、最小的 js 数据库时，应该想到 Toast UI Chart。

Toast UI 图表提供了各种选项来轻松地更改图表的细节，以及广泛的自定义主题选项。基本上，我们在这里的意思是，它附带了一堆易于定制的图表类型。例如，标题、轴、图例、工具提示、绘图、系列等图表组件可以通过选项进行完全定制。

为了在我们的项目中使用这个库，我们可以使用像 npm 这样的包管理器，或者直接从源代码中下载。也可以通过 CDN 提供服务。虽然在图表类型或动画方面不如 D3.js 强大，但同样容易上手。为了使用可用的图表类型，我们可以看看[Git](https://github.com/nhn/tui.chart)[H](https://github.com/nhn/tui.chart)[ub repo](https://github.com/nhn/tui.chart)中可用的样板示例。

它还带有一个干净、易用的 API。作为一个额外的优势，它还具有响应性、可缩放性，并包含一个实时更新功能，允许用户在使用`addData` API 和`options.series.shift`选项实时添加新数据时查看和管理这些数据。

最后，它是开源的，因此可以免费使用。总而言之，Toast UI Chart 提供了许多类型的图表来可视化不同种类的数据。

### D3.js

D3.js 是一个非常灵活的 data viz 库，遵循数据驱动的方法来处理 DOM。它有助于使用 HTML、SVG 和 CSS 将数据变得生动。它强调 web 标准，并完全支持所有现代浏览器。

D3 解决了问题的关键，它允许基于数据的文档的有效操作。在性能方面，它非常快，这使得它很容易支持大型数据集和交互和动画的动态行为。

使用 D3.js 的好处包括干净的 API 参考，它包含各种图表类型。这些基本上是由一组设计用来协同工作的模块组成的。请注意，我们可以单独使用这些模块，也可以作为默认构建的一部分一起使用。它还捆绑了不同的社区支持的插件。

D3 在动画、数据分析、分层数据和交互领域也工作得很好——事实上，D3 以动画和交互而闻名。例如，看看一个简单的[动画树状图](https://observablehq.com/@d3/animated-treemap)的例子。

目前在 v4 中，D3.js 还捆绑了大量社区开发的资源，包括教程、视频、课程和书籍。D3 团队还发布了数百个自己的可分叉示例和教程，以促进学习和生产力。我们可以使用 npm 或 Yarn 安装它。

最后，值得一提的是 D3 有超过 30 个模块和 1000 个方法！这就是可观察的 T2 的用武之地。

提供最快的方式开始玩 D3，它允许我们编辑单元格并自动运行它们以获得快速反馈。我们还可以几乎不用代码就添加交互或动画！事实上，Observable 是学习 D3 的理想环境，因为它[用数据流简化了代码](https://medium.com/@mbostock/a-better-way-to-code-2b1d2876a3a0)，[就像电子表格](https://observablehq.com/@observablehq/how-observable-runs)。

### 雷查尔兹

Recharts 是一个用 React 和 D3 构建的重新定义的图表库。这是一个可组合的图表库，允许我们使用解耦的、可重用的 React 组件快速构建图表。它的一个主要优点是它的组件建立在 SVG 元素之上，对 D3 子模块有轻量级的依赖。

Recharts 允许通过调整组件属性和传递自定义组件来非常强大地定制图表。一般来说，我们可以很容易地将 Recharts 集成到我们的 React 应用程序中。要开始使用 Recharts，我们可以从 npm 安装或者直接从源代码构建代码。

Recharts 还为用户提供了多种方式来为他们的图表组件添加强大的交互。例如，我们可以轻松地放入一个具有丰富悬停功能的工具提示组件。它拥有一个简洁易用的 API，支持多种图表类型、组件和形状。[示例](https://recharts.org/en-US/examples)可在文档中找到。

### Chart.js

Chart.js 非常容易使用，也非常容易定制。它也是开源的，有开源开发者维护的大量文档和超过八种图表类型，包括内置的图表类型。

开发人员特性允许以多种不同的方式扩展、定制和增强 Chart.js。该配置用于更改图表的行为方式。有一些属性可以控制样式、字体、图例等。顺便提一下，我们可以执行全局和数据集配置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

> **注**:全局通用选项在`Chart.defaults.global`中定义。每种图表类型的默认值在该特定图表类型的文档中讨论。

Chart.js 图表在`[canvas](https://caniuse.com/#feat=canvas)`元素上渲染，渲染速度相当快。因此，它支持大多数现代浏览器，因为所有现代和主流移动浏览器都支持`canvas`元素。另外，它可以很好地与流行的 JS 库集成。

下面是一个如何通过`canvas`元素呈现图表的例子:

```
<canvas id="myChart" width="400" height="400"></canvas>

```

Chart.js 可以与 ES6 模块、普通 JavaScript 和模块加载器一起使用。对于普通的 JavaScript，我们可以使用常规的脚本标签。我们还可以利用不同的模块加载器，包括 webpack、CommonJS 等等。我们可以通过 npm、Bower、CDN 或 building from source 来安装该库。

Chart.js 的亮点包括它的响应性，这可以通过库提供的几个选项来控制调整大小的行为。这通常是通过检测`canvas`显示尺寸何时改变并相应地更新渲染尺寸来实现的——尽管与`canvas`渲染尺寸相关的一些已知缺点可能会导致不准确的渲染。

Chart.js 还支持许多图表扩展、插件和适配器。插件是自定义或更改图表默认行为的最有效方式。它们在[版本 2.1.0](https://github.com/chartjs/Chart.js/releases/tag/2.1.0) 中引入(仅全局插件)，并在[版本 2.5.0](https://github.com/chartjs/Chart.js/releases/tag/v2.5.0) 中扩展(根据图表插件和选项)。

对于大型数据集或对性能敏感的应用程序，请考虑以下技巧:如果图表的渲染时间很长，最好禁用动画。这样做意味着图表只需要在更新期间呈现一次，而不是多次。这将有助于减少 CPU 使用并提高一般页面性能。

谈到可用的图表类型，Chart.js 的主要缺点是它目前不支持仪表图或热图。因为图表呈现在用户提供的`canvas`元素上，这迫使用户决定如何以一种可访问的方式创建`canvas`元素。

## 为您的项目选择正确的 JavaScript 数据 viz 库

如今，我们构建的几乎每个应用程序都需要或利用数据来增强功能或用户体验。这些可视化可以是显示不断增长的客户群的指标，也可以是显示各种类别的业务费用的图表。

虽然现在有相当多的数据可视化 JavaScript 库可用，但是找到正确的库对于我们的开发过程和用例来说是非常关键的。我们需要确保该解决方案易于定制，并且完美适合我们需要构建的内容。这就引出了从这些不同的库进行选择时要考虑的因素:

1.  支持的图表类型
2.  特征
3.  渲染方法
4.  交互性
5.  易于使用的 API 和总体良好的 DX
6.  社区支持

最后要注意的是，如果数据可视化库具有上述所有特征，那么根据具体的使用情况，通常可以认为它是好的。

## 结论

在这篇文章中，我们从特性和性能方面看了 JavaScript 中的顶级数据可视化库。这些都可以帮助我们快速开始为我们的应用程序添加漂亮和高性能的数据可视化。

我们在这里探索的所有库都很棒，它们之间的差异并不是很大——都有各种各样的支持特性、可靠的 API 接口和良好的社区支持。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。