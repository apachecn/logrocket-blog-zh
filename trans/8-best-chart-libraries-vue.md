# Vue - LogRocket 博客的 8 个最佳图表库

> 原文：<https://blog.logrocket.com/8-best-chart-libraries-vue/>

作为孩子，我们通常会产生数学恐惧症，因为我们在学校里学的是复杂的计算和难懂的方程。但是，如果你把数据组织成图表、图形或任何其他视觉表现形式，那么它看起来很美。

不用说，使用图表解析数据要容易得多。当信息包含一些视觉效果时，用户会发现它很容易记住。

如果你想自己开发一个图表库，想象一下开发和维护这个库需要多少时间。因此，正确编写和维护的库是救命稻草。

我们有几个图表库，它们提供了可视化数据的方法，允许您开发自己的组件和维护数据。问题是，您需要选择哪个图表库？让我们看看哪些图表库可用于 Vue。

## Vue.js 的顶级库

有很多好的 Vue 图表库，但是从所有选项中选择一个有点令人生畏。

我根据特性、文档和维护等标准，整理了一份 Vue 生态系统中支持的一些最好的库的列表。事不宜迟，不分先后:

### Chart.js

Chart.js 是最简单、最具扩展性和最强大的 Vue 图表库之一，在 GitHub 上有 [4.6K+ stars。vue-chartjs 基于](https://github.com/apertureless/vue-chartjs) [Chart.js](https://www.chartjs.org/) ，也就是说你需要安装 Chart.js 作为依赖包。

如果你熟悉 [Chart.js](https://blog.logrocket.com/creating-chart-components-in-vue-with-chart-js/) ，那么这对你来说似乎很容易。它只是公开 Chart.js 对象，并创建一个允许在其上编写定制逻辑的包装器，从而为您提供最大的灵活性。

此外，vue-chartjs 基于 vue 的组件原则，这有助于您创建可重用的组件。它有解释清楚的文档，非常容易理解。

![Vue-chartjs](img/dbcb4ef0219e22211a8c1dd2b9cd3fa7.png)

vue-chartjs 自 2016 年以来一直可用，并基于坚实的社区支持。首先，使用 npm、纱线包装管理器或 CDN 链接。

该库提供带有静态或动态数据的漂亮图表。除此之外，你还可以改变组件的道具。一些可用的图表类型有条形图、折线图、饼图、雷达图、极区图、气泡图和散点图。vue-chartjs 提供了灵活性，这意味着您可以创建自己的自定义图表类型或修改现有的图表类型。另一个好处是它与 TypeScript 兼容，mixin 模块是默认安装的。

### ApexCharts:一个简单灵活的库

凭借十六种图表类型， [vue-apexcharts](https://github.com/apexcharts/vue-apexcharts) 在所有其他图表库中脱颖而出。ApexCharts 是一个加载特性的图表库，通过提供 100 多个样本简化了数据可视化的任务。

如果你的系统考虑响应式屏幕尺寸，那么你就不能忽略 ApexCharts 它提供了响应式和交互式设计。说到可视化，性能是不可忽视的。因此， [ApexCharts](https://blog.logrocket.com/charting-with-vue-a-comparison/) 的性能是达标的。

ApexCharts 在 GitHub 上有高达 [16.3K 颗星(Vue](https://github.com/apexcharts/apexcharts.js) [repo](https://github.com/apexcharts/vue-apexcharts) 有 1K+颗星)，有近百名贡献者。它不依赖于其他图表库。初学者也可以通过参考其文档快速学习。

![Vue-apexcharts](img/ee6ff86c0765488a7ae1c2019d8af661.png)

ApexCharts 提供了流畅的动画，这对于用户的注意力来说是相当不错的。此外，你有许多现有的主题托盘，可以在飞行中使用。Vue-ApexCharts 拥有麻省理工学院的许可证，可以在默认条件下自由使用、修改和分发。

### Apache ECharts:一个有 20 个图表选项的库

当你有了使用 [Apache ECharts](https://echarts.apache.org/en/index.html) 的实践经验，并希望将其集成到 Vue 中时，那么 [vue-echarts](https://github.com/ecomfe/vue-echarts) 就是为你准备的。它是用 Apache ECharts 构建的，支持 Vue 版本 2 和 3。它有 20 多个图表选项，可以完全定制。vue-echarts 内置了日历、数据缩放、SVG 渲染、可视化映射等组件。，这对创建漂亮的网页很有用。

它目前在 GitHub 上有 6.8K+的启动，有广泛的社区支持。该库支持 Apache ECharts 中几乎所有可用的方法和事件。此外，它还有一个减少包大小的好选择。你只需要像这样直接导入 EChart 模块:

```
import 'echarts/lib/chart/bar'
import 'echarts/lib/component/tooltip'

```

![Vue-Echarts](img/7fdfceeb19a5e1b5a7a670965485063a.png)

![Dual Numeric Axis](img/8c0902f08cc9d062e2169cceb1206ee8.png)

没有合适的文档可用；你需要参考 GitHub 本身的一切。

用作 CDN 或通过 yarn 或 npm 作为软件包安装，开始使用 vue-echarts。

### D3:构建你想要的图表

D3 是一个非常著名和庞大的 JavaScript 库，用于动态和交互式数据可视化。它遵守网络标准的所有规则。D3 在 GitHub 上有 [200M 的下载量](https://observablehq.com/@mbostock/npm-daily-downloads?name=d3)和 [101K 的星星](https://github.com/d3/d3)，用 D3 可以制作出无限多的图表。

D3 通过外部样式表、定制动画和响应图表支持 SVG。使用 D3 的一个好处是，它只是依赖于 HTML、SVG 和 CSS 在 D3 上工作不需要更多的技术或工具。即使有大量的数据点，它也能快速工作。

根据[文档](https://www.npmjs.com/package/d3/v/5.3.0)，“D3 帮助你使用 SVG、Canvas 和 HTML 将数据变得生动。D3 将强大的可视化和交互技术与数据驱动的 DOM 操作方法相结合，为您提供了现代浏览器的全部功能以及为您的数据设计正确的可视化界面的自由。”

D3 提供了一种单独使用单个模块并使用`Object.assign`将它们组合成一个`d3`对象的方法，这减少了项目构建的规模。需要注意的是让 Vue 玩 DOM 操作；不要用 D3 做。

![Data Driven Documents](img/a19b454b7506c0ab4b89b6ac00c22140.png)

D3 有广泛的社区支持，可以帮助你解决障碍。如果你想创建一个动画，自定义图表，或创建一个新的图表类型，那么有机会有人可以帮助你。您可以在您的 Vue 项目中将它作为 npm 包使用。

### Three.js:适用于 3D 数据可视化

Three.js 是最好的 3D 开源库，GitHub 上有巨大的 [80k 明星和近 16k 贡献者。如果您想使用 3D 渲染，并且正在寻找一个轻量级的跨浏览器支持库，那么 Three.js 是最好的选择之一。](https://github.com/mrdoob/three.js/)

生活很简单，因为我们不需要为每个 3D 渲染使用 Canvas API。WebGL(一个 JavaScript API)是这里的支持系统，Three.js 用它来生成 3D 视觉效果；它不需要任何第三方插件。

Three.js 提供了许多可能的选项来创建 3D 可视化，如动画、材质、缩放、灯光、效果、相机等等。3D 图形需要数学，Three.js 抽象了所有的数学运算，并提供了一个可以使用它们的接口。它有自己的数学库，用于 3D 数学。

添加额外的维度总是有帮助的，对于创建 3D 图表，Three.js 中有许多漂亮的呈现选项。在这里，您可以使用可用的方法从头开始设计和构建图表。

![Three-js](img/ac436e68bc7bb83f39a001f293c98e6c.png)

Three.js 以 npm 包的形式提供，因此很容易实现 Vue 项目。如果你想要 CDN，那也是有的。

### trading-vue-js:金融图表的解决方案

当你的应用程序只需要面向交易的图表时，有一个特殊的要求。为了达到这个目的， [trading-vue-js](https://github.com/tvjsx/trading-vue-js) 图表库应运而生。

这个库提供了可视化交易数据集的图表，比如烛台图表。很容易把你的自定义指标放在蜡烛图上。

trading-vue-js 是开源的；您可以根据需要自定义它，例如更改颜色、添加额外的道具或提高图表性能。根据文档，它相当快…直到有三百万个数据点。

![Trading-vue-js](img/36b131840b5d08d4de6875d7ad2253a4.png)

最重要的是，交易页面需要支持所有屏幕尺寸，trading-vue-js 提供了一个响应选项。另一个主要好处是，它提供了缩放选项，以获得更好的可视化效果。

### Plotly:基于 D3 和 stackgl 构建

Plotly 是一个独立的数据可视化库，构建在 [D3.js](https://d3js.org/) 和 [stackgl](https://github.com/stackgl) 之上。它在 GitHub 上拥有超过 [14.5K 颗星星和良好的](https://github.com/plotly/plotly.js)[文档](https://github.com/plotly/plotly.js#documentation)，这使得开发变得容易。Plotly 通过提供 40 多个图表选项简化了图表生成过程。

根据[官方文档](https://github.com/plotly/plotly.js/)，“Plotly.js 可用于制作数十种图表类型和可视化，包括统计图、3D 图、科学图表、SVG 和平铺图、金融图表等。”

Plotly 支持响应式/流体布局和许多配置选项。通常，它支持所有基本图表、统计图表、科学图表、财务图表和三维图表。如果你想玩动画，那么你也可以在 Plotly 的帮助下制作出精美的动画。

![Plotly](img/2033d4336c53c56be232c76847aef8fa.png)

如果你想得到任何关于 Plotly 的帮助，有一个联系表单可以直接把你的问题带到库维护者那里。还有直接使用的 [vue-plotly npm 包](https://github.com/David-Desmaisons/vue-plotly)，但使用它的一个主要缺点是该包在过去两年中没有更新。如果您想省略打包选项，那么您可以使用脚本标签中 Plotly npm 打包或加载链接。

### Billboard:另一个具有 ES6+语法的 D3 库

和其他很多图表库一样， [Billboard](https://naver.github.io/billboard.js/) 是基于 D3 的。这意味着在脚本中导入 Billboard.js 时需要加载 D3。因为有好的文档，学习曲线非常简单；您可以轻松创建图表。此外，它支持 ES6+语法，这对于现代 JS 框架是很好的。它在 GitHub 上拥有超过 [5.1K 颗星星。](https://github.com/naver/billboard.js)

Billboard 提供了多个选项，这些选项可以组合在一个图表中，使图表更加有用。除此之外，Billboard 的库大小约为 169Kb，与其他图表库相比非常低。

![Billboard-js](img/244c4905e09813af2f73c0c4000f1582.png)

有了 Billboard 提供的各种[例子](https://naver.github.io/billboard.js/demo/)，很容易上手。它提供了两种选择:您可以将它用作 CDN 链接或 npm 包。

## 结论

数据在设计中起着至关重要的作用，因此，它是任何应用程序中的必备组件。网络上有许多库可以达到同样的目的。

在这里，我挑选了最好的，应用最广泛的。这些库在数据集成、维护和设计漂亮的页面方面为 Vue 开发人员提供了好处。除此之外，大多数都很容易集成。

您可以使用现有的库来消除额外的编写工作，并清理混乱的代码库。请记住，在将库集成到您的系统中之前，请通读所有提供的特性和用例。至少在定稿前检查一下最终结果。最后，根据您的需求以及您希望系统具备的特性，选择最适合您项目的方案。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。