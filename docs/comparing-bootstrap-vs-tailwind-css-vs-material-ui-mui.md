# Bootstrap vs . tail wind CSS vs . Material UI(MUI)比较

> 原文：<https://blog.logrocket.com/comparing-bootstrap-vs-tailwind-css-vs-material-ui-mui/>

***编者按:**这篇文章于 2022 年 7 月 20 日更新，包含了与 Bootstrap、Tailwind CSS 和 MUI 的最新版本相关的信息，并增加了一个讨论[这些库是否可以相互结合使用的部分](#using-bootstrap-tailwind-css-mui)。*

在不依赖现有库的情况下构建前端应用程序是很棒的。但是学习如何实现库不仅仅节省你的时间——它还帮助你不用额外的努力就能构建干净和对称的布局。所有这些库都是为跨浏览器功能而构建的。

虽然有许多 UI 库，但是每一个之间都有大量的差异，从可访问性到可扩展性，以及其他标准。

本文将指出三个流行的 UI 库之间的主要区别:Bootstrap、Material UI (MUI)和 Tailwind CSS。

我们将涵盖的内容:

## 引导程序

Bootstrap 最初是由 Mark Otto 和 Jacob Thornton 开发的，当时他们都在 Twitter 工作。第一个版本最初发布于 2011 年 8 月 19 日星期五。

自发布以来，该库在开发人员社区中获得了很多关注，在撰写本文时 Github 上有大约 [16 万名明星和 1000 多名活跃贡献者。](https://github.com/twbs/bootstrap)

Bootstrap 最初为其组件使用 CSS 和可选的 jQuery 设计模板。然而，[最新版本 Bootstrap 5](https://github.com/twbs/bootstrap/releases) 放弃了对 jQuery 的支持，转而支持普通(普通)JavaScript。

### 使用引导库

将 Bootstrap 集成到一个新的或现有的项目中非常容易。您可以从包含其 CDN 或下载其源文件并在您的标记页面中链接它们开始。

开发者社区也努力将 Bootstrap 引入几乎所有的前端框架，包括 React、Vue.js 和 Svelte。

#### BootstrapVue

[BootstrapVue 帮助你使用 Vue.js 和 Bootstrap 4 构建应用](https://blog.logrocket.com/getting-started-with-bootstrapvue-2d8bf907ef11/)。它提供了 Bootstrap 4 组件和网格系统的最全面的实现，但是对 Bootstrap 5 的支持目前仍在开发中。

除了核心引导组件之外，BootstrapVue 还包括额外的组件，如框架加载器和镜像延迟加载组件。它还具有对引导图标的现成支持。

#### 反应阱

[Reactstrap 与 BootstrapVue](https://blog.logrocket.com/using-bootstrap-with-react-tutorial-with-examples/#example-using-reactstrap) 类似，但用于 React.js，然而，与 BootstrapVue 不同，这个库不支持通常的开箱即用的引导图标。此外，没有额外的组件。

#### 细长的带子

[Sveltestrap 为一个 Svelte 3 应用提供 Bootstrap 5 组件](https://sveltestrap.js.org/?path=/story/components--get-started)而不需要你使用 Bootstrap 组件类。

您还可以获得一些默认情况下 React 没有附带的自定义组件，比如它的离画布和淡入淡出组件。此外，您还可以访问开箱即用的引导图标。

### 引导中的可访问性

理想情况下，构建 Bootstrap 是为了符合标准的 Web 内容可访问性指南 2.0 (WCAG 2.0)。但是当然，大多数可访问性因素也取决于开发人员的标记结构、附加样式和使用的脚本。

Bootstrap 团队很公平地指出了如何在他们的文档中包含每个交互组件的可访问性角色和属性。这使得开发人员更容易更准确地陈述他们组件的功能。

#### 屏幕阅读器的引导支持

Bootstrap 提供了一个实用程序类(`.sr-only`)来可视地隐藏内容，同时使它们可以被屏幕阅读器等辅助技术访问。

在需要向非视觉用户传达额外信息的情况下，这很方便。

这里有一个例子:

```
<p class="text-danger">
  <span class="sr-only">Danger:</span>
  This action is not reversible
</p>

```

### 扩展引导

在某些情况下，您可能希望更改现有的引导样式，以更好地满足您的需求。例如，您可能希望更改不同组件的默认颜色或修改边框。

有两种主要的方法可以解决这个问题:使用定制 CSS，或者使用 SASS。

#### 用自定义 CSS 文件覆盖引导样式

在扩展 Bootstrap 时，使用定制的 CSS 文件绝对是最简单的解决方法。这种方法要求您创建一个外部`[custom].css`文件，并在主引导 CSS 文件之后引用它。

这里有一个例子:

```
<link rel="stylesheet" type="text/css" href="path/to/bootstrap.min.css">
<link rel="stylesheet" type="text/css" href="path/to/override.css">

```

#### 使用 SASS 自定义引导程序样式

SASS(语法上令人敬畏的样式表)本质上是一种编译成 CSS 的预处理器脚本语言。SASS 使得创建和重用 CSS 变量变得更加容易。此外，它对嵌套样式、混合和模块等其他特性的支持使它在开发人员中很受欢迎。

对于那些不熟悉 SASS 的人来说，这个过程可能显得相当复杂。

Bootstrap 是用 SASS 编写的，然后进一步编译成纯 CSS，所以这使得熟悉 SASS 的开发人员可以很容易地根据自己的喜好修改和微调整个库。

但是，建议您尽可能避免修改 Bootstrap 的核心文件，而是创建一个导入 Bootstrap 的自定义 SASS 文档，以便您可以修改和扩展它。

## React 的材质 UI (MUI)

[MUI 是一个材料设计系统](https://blog.logrocket.com/using-material-ui-in-react-native/),用于实施基于 React 的应用程序，提供大量可立即用于生产的预建组件。

材料设计系统由谷歌创建，旨在帮助开发人员为 Android、iOS、Flutter 和 web 构建高质量的数字体验。在这种情况下，应用程序是按照官方网站上记录的原则、行为和设计指南[构建的。](https://material.io/)

设计指南还包括如何创建组件的说明、要遵循的颜色和主题选项、交互和动画流程的概念、字体和推荐图标，以及许多其他指南。

### 使用材料设计

Material Design 的官方开发者为移动和网络应用提供了一套框架和软件包。事实上，它是诸如 Flutter 和 Kotlin 等框架中的默认设计系统。

然而，开发人员社区也构建了许多开源工具来帮助开发人员将材料设计集成到多个 web 框架中，包括 MUI。

MUI 旨在吸引人，具有各种定制选项，允许您在其组件之上轻松构建自己的定制设计系统。

此外，该库包含各种未包含在官方材料设计组件中的组件，但符合其设计标准。

#### Vuetify:探索另一种用于材料设计的开源工具

Vuetify 是一个基于 Vue.js 的框架，它还包括各种材质设计元素。虽然有多种 Vue.js 材料设计框架，但由于与其他框架相比更容易学习， [Vuetify 在 Vue 开发人员中很受欢迎](https://blog.logrocket.com/vue-component-frameworks-compared-is-vuetify-right-for-you/)。

此外，默认情况下，Vuetify 的设计目的是模块化、响应性和高性能，它有许多主题可供选择。

### 材料设计和库(如 MUI)中的可访问性

Google Material Design 系统已经包含了可访问性指南和最佳实践，围绕它创建的库和框架在默认情况下必然会遵循它们。此外，这些库中的大多数，如 Vuetify 和 MUI，都有一些额外的特性来进一步创建丰富的和可访问的应用程序。

### 用 MUI 扩展材料设计

MUI 库有几种定制和扩展其组件的方法。一种选择是包含库的`sx`属性，并将其值设置为您首选的覆盖 CSS。下面是其滑块组件的示例:

```
<Slider
  defaultValue={30}
  sx={{
    width: 300,
    margin: '10px',
  }}
/>

```

它还包括一个`GlobalStyles`和`ThemeProvider`组件，允许您更改样式并为每个想要的组件定义一个单独的主题。为了了解更多关于 MUI 定制和可扩展性的信息，该团队创建了一个[专用页面，上面有一步一步的说明](https://mui.com/material-ui/customization/how-to-customize/#3-global-theme-overrides)。

### 用虚拟化扩展材料设计

定制 Vuetify 组件非常简单。通过向 Vuetify 构造函数提供一个主题字段，可以在库初始化期间轻松修改组件主题。

下面是一个我们如何覆盖亮暗主题的组件颜色的例子:

```
// src/plugins/vuetify.js

import Vue from 'vue'
import Vuetify from 'vuetify/lib'

const vuetify = new Vuetify({
  theme: {
    themes: {
      light: {
        primary: '#3f51b5',
        secondary: '#b0bec5',
        accent: '#8c9eff',
        error: '#b71c1c',
      },
      // or use pre-defined material colors from the vuetify library.
      dark: {
        primary: colors.blue.lighten3,
        secondary: colors.grey.darken2,
      },
    },
  },
})

```

Vuetify 团队还开发了一个专门的主题生成器,允许您从各种颜色选项中为您的应用程序创建和导出主题。

## 顺风 CSS

[Tailwind CSS 是一个相对较新的前端框架](https://blog.logrocket.com/whats-new-tailwind-css-v3-0/)，最初于 2017 年 11 月 1 日发布，其第一个版本由 Adam Wathan 和 Steve Schoger 创建。该库已经获得了很多关注，甚至被大公司采用，包括 Algolia 和 Mozilla。

Tailwind CSS 不同于前面提到的 CSS 库，它不直接提供 UI 组件。相反，它提供了低级的实用程序类，使您能够创建完全独特的设计。

这个库的工作方式是扫描所有 HTML 文件、JavaScript 组件和其他模板中的类名，然后生成相关的样式并将它们写入一个静态 CSS 文件。

为了理解这意味着什么，下面是一个如何在 Bootstrap vs. MUI vs. Tailwind CSS 中创建一个主按钮(蓝色)的例子。

纯引导示例:

```
<button class=”btn btn-primary”>Awesome Button</button>

```

miui 示例:

```
<Button variant="contained" color="primary">Primary</Button>

```

顺风 CSS 示例:

```
<button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded">
  Awesome Button
</button>

```

如您所见，与其他库不同，Tailwind 允许您使用实用程序类直接定制元素。您可以手动指定颜色变量、悬停时的背景颜色等等。

当您有遵循相同设计模式的通用组件时，上面的例子可能会非常详尽和耗时。这就是为什么存在提取组件类的附加选项。这里有一个例子:

```
<button class="btn btn-blue">
  Custom Button
</button>

<button class="btn btn-blue">
  Another Button
</button>

<style>
  .btn {
    @apply font-bold py-2 px-4 rounded;
  }
  .btn-blue {
    @apply bg-blue-500 text-white;
  }
  .btn-blue:hover {
    @apply bg-blue-600;
  }
</style>

```

但是，建议尽可能避免这样做，因为这违背了库的目的，即避免编写 CSS 代码并再次为这种样式提供类名。

### 使用顺风 CSS

Tailwind CSS 作为一个节点包提供，可以作为一个 [PostCSS](https://blog.logrocket.com/getting-started-with-postcss-in-2019-484262a4d725/) 插件进行扩展。它也通过 CDN 提供服务，尽管 CDN 仅用于开发目的，并不是生产的理想选择。

可以定制节点包，以便与所有 JavaScript 前端框架(React、Vue、Vite 等)、Laravel 甚至 Ruby on Rails 应用程序一起运行。

### 顺风 CSS 中的可访问性

默认情况下，Tailwind CSS 是为高性能和可访问而构建的。像 Bootstrap 一样，Tailwind 包含了额外的实用程序类来提高可访问性——最显著的是屏幕阅读器(`sr-only`和`not-sr-only`)类。例如:

```
><span class="sr-only">Only visible to screen readers only</span>
<span class="not-sr-only">Visible to both users and screen readers</span>

```

此外，使用 Tailwind 的条件实用程序类，您可以更进一步，根据元素状态(焦点、悬停等)为屏幕阅读器隐藏或取消隐藏项目。)和设备分辨率:

```
<span class="sr-only sm:not-sr-only">Sample content</span>
<span class="sr-only focus:not-sr-only">Another sample content</span>

```

## 将 Bootstrap 与 Tailwind 或 MUI 一起使用

虽然不建议这样做，但是可以同时使用一个库和另一个库。

请注意，各种库和框架在幕后都有相同的类名，例如`container`、`px`和`mx`类在 Bootstrap 和 Tailwind 中是如何存在的。这可能会与项目的设计顺序冲突。

下面是一个结合使用 Bootstrap 和 Tailwind CSS 的例子:

```
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/[email protected]/dist/css/bootstrap.min.css">
  <script src="https://cdn.tailwindcss.com"></script>
</head>

<body class="container mt-5">
  <!-- Bootstrap button example -->
  <button class="btn btn-primary">Hello</button>

  <!-- Underlined bold text with tailwind -->
  <h1 class="text-3xl font-bold underline">
    Hello world!
  </h1>
</body>

```

在上面的例子中，如果我们利用两个库中都存在的一个实用程序类，那么 Tailwind 的样式将被大部分应用。这是因为我们在 HTML 的 head 部分的 Bootstrap 之后导入了它，从而覆盖了冲突的样式。

## 结论

本文对流行的 CSS 库和框架提供了深入的指导。我希望这有助于您在开发前端应用程序时选择正确的工具。

如果你有另一个支持某个框架的理由，请在下面的评论中分享。大家说说吧。

感谢阅读！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。