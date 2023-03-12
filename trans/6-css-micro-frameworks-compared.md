# 6 CSS 微框架比较- LogRocket 博客

> 原文：<https://blog.logrocket.com/6-css-micro-frameworks-compared/>

对于寻找一个小型灵活的库来帮助我们避免从头开始设计前端项目的开发人员来说，有一个简单的解决方案:CSS 微框架。

微框架是非常轻量级的库(有些不到 10kb)，具有一些准系统组件和实用程序类，没有 JavaScript 依赖性。虽然微框架最终有助于加速前端构建，但预计会有一个初始的学习曲线。

在本文中，我们将讨论 6 个微型 CSS 框架以及它们的用途。我们的对比，不分先后，将包括:

*   超光子
*   顺风 CSS
*   纯的
*   羽毛
*   毫克
*   幽灵

## 超光子

名副其实的，[超光速粒子](https://tachyons.io/)旨在帮助开发者用“尽可能少的 CSS”构建快速响应的界面

Tachyon 的模块化框架允许你挑选功能来减少文件大小。还可以使用实用程序类修改元素样式和行为。比方说，你想创建一个鼠标悬停时变暗的链接文本。为此，您只需将`.dim`类应用于`<a/>`元素。

方便的是，Tachyons 还提供了一个 web 界面，用于生成适合您项目的自定义主题:

![tachyons css micro framework](img/28ed01a53b7279df77a5d148b1ca5994.png)

Tachyons customizer interface

此外，对于那些寻找移动优先的微框架的人来说，Tachyons 有一个 React 本机库，允许您使用其实用程序类来设计移动应用程序。

[**成分**](https://tachyons.io/components/) **:** 快子中的成分充其量是简陋的。有用于表单输入和按钮的组件，但是它们的样式有限；这是对这样一个极简主义图书馆的期望。也就是说，对于电影海报和专辑卡片等高度特定的用例，有几个组件。

**Size**:gzip 压缩后，整个库不到 14kb，而且，因为它是一个模块化框架，可以通过只选择您需要的功能来进一步优化。

**活动**:本库最后一次更新是在 2020 年 4 月。它目前在 Github 上有 10.3k 颗星。

**浏览器** **支持**:Tachyons 文档并没有明确说明浏览器支持，但是[源代码](https://github.com/tachyons-css/tachyons/blob/master/css/tachyons.css)表明除了 Internet Explorer 版本 9 及以下版本之外的所有现代主流浏览器都得到支持。

[**文档**](https://tachyons.io/docs/)

## 顺风 CSS

Tailwind 有一个[类似于超光速粒子](https://blog.logrocket.com/tailwindcss-vs-tachyons/)的实用优先方法，但最终提供了更多的定制，以换取一个稍微大一点的库(gzip 后为 78.0kb)。Tailwind 没有内置的组件类，而是[为所有 CSS 属性](https://tailwindcss.com/)使用实用程序类，包括过渡、显示和渐变。

如果你想知道这是如何工作的，想象一下这个。您可以使用`.text-center`将文本居中，并用`.rounded-xl`将按钮弄圆。由于 Tailwind CSS 中没有预定义的组件类，这为开发人员提供了足够的设计灵活性。Tailwind CSS 还为 Visual Studio 代码提供工具，用于自动完成、语法突出显示和使用[智能感知](https://tailwindcss.com/docs/intellisense)的林挺。

[**组件**](https://tailwindui.com/components?utm_source=tailwindcss&utm_medium=navigation):Tailwind CSS 中的组件只是一个实用程序类的组合。要使用 Tailwind 的官方组件，你需要支付 146 美元，但其他网站如 [tailwindtoolbox](https://www.tailwindtoolbox.com/) 有免费的组件和模板。

尺寸:根据你的使用情况，顺风的尺寸会有很大的不同。如果你使用没有经过 PostCSS 预处理的 CDN 版本，预计它的压缩成本约为 73kb。

**活动**:顺风在 Github 上有 30k+颗星。它经常更新。

**浏览器支持**:编译时自动包含厂商前缀。如果你需要支持 IE，你需要降级它的版本。

[**文档**](https://tailwindcss.com/docs)

## 纯的

纯 CSS 由雅虎的设计团队创建，与 Tachyons 和 Tailwind 不同，它提供了创建组件的类。纯 CSS 采用传统的类似 Bootstrap 的方法来设计样式，并为您提供一些基本的样式组件，如按钮和表单输入(如下例所示)。

![Pure css micro framework](img/3dd2cfb38bf782d8936f2434ce391a95.png)

Button classes in Pure

您可以通过使用 CSS 覆盖基类来自定义这些元素。纯 CSS 中的类组件也是按照可以单独导入的模块进行分组的。

**组件:** Pure 有单独的按钮、表单输入、菜单和表格模块；一般来说，这些组件都非常简单，所以不要指望像日期选择器和开关这样的东西。如果你想要的设计与默认的风格有很大的不同，那么你必须[手动覆盖这些风格](https://blog.logrocket.com/how-to-create-yin-yang-symbol-pure-css/)来达到你想要的最终效果。

使用所有的纯模块将花费你微不足道的 3.8KB 的 gzipped。

**活动** : Pure 在 Github 上有 20k+颗星。其最新版本(v2.0.1)于 2020 年 5 月发布。

**浏览器支持**:纯 CSS 使用 Normalize.css 确保支持 IE 10+这样的老浏览器。

[**文档**](https://purecss.io/start/)

## 羽毛

Plume CSS 为常见的 CSS 样式提供了组件类和实用程序类(例如，您可以使用`.pm-text-center`使文本元素居中)。Plume 是纯元素风格的，并且完全没有 JavaScript。

作为一个自我描述的"[高度主题化的](https://felippe-regazio.github.io/plume-css/)"微框架，Plume 具有内置的主题以及一个主题编辑器用于额外的定制:

![Plume CSS micro framework](img/fc9784372b1d8b66f0b0a7d1ae47cf46.png)

Plume theme editor

**组件** : Plume 有相当多的输入组件，包括日期、下拉菜单、开关、复选框、指示器等等。

**Size** : Plume 重约 5kb gzipped，所有这些都可以通过只挑选需要的模块来优化。

**活动**:这是一个比较新的库，最后一次更新是在 2020 年 4 月。

**浏览器支持:**保证支持 Chrome 80+，Firefox 76+，Opera 68+，Safari 11+。不保证对 IE 的支持。

[**文档**](https://github.com/felippe-regazio/plume-css)

## 毫克

在真正的微框架风格中，[毫克](https://milligram.io/)为样式元素提供了一个非常小的类集。这些类是固执己见的，所以要花一些时间来重写它们。毫克还提供了三个实用程序类:`.clearfix`，。`float-right`、`. float-left`。

组件(Components):毫克有字体、按钮、列表、表单和表格的类。它还有一个基于 CSS flexbox 布局的流体网格系统。由于库的简单性，为了定制组件，必须覆盖毫克类。

尺寸:毫克名不虚传，仅重 2kb。

**活动**:毫克最后一次更新于 2020 年 5 月。它在 GitHub 上有 9k 颗星。

**浏览器支持**:毫不掩饰的说，毫克不是为老浏览器设计的，只保证支持最新的浏览器版本。

[**文档**](https://milligram.io/#getting-started)

## 幽灵

尽管号称轻量级微框架，但实际上， [Spect](https://picturepan2.github.io/spectre/index.html) [re](https://picturepan2.github.io/spectre/index.html) 有一组非常健壮的用于样式元素的类，以及一组同样健壮的用于布局、文本、颜色等的实用类。Spectre 还对中文、日文和韩文提供一流的支持，具有更好的可读性。

要自定义 Spectre CSS，使用变量更新 [Sass](https://sass-lang.com/guide) [预处理器](https://sass-lang.com/guide) [文件](https://sass-lang.com/guide)。

[**组件**](https://picturepan2.github.io/spectre/components.html) : Spectre 的组件先进多样，你会发现像头像、面包屑、分页、模态、popovers 等组件。

令人惊讶的是，即使拥有令人印象深刻的职业集合，Spectre 的重量也只有 10kb。而且，就像我们之前列出的一些微框架一样，如果去掉不必要的样式，这个大小会减小。

**活动** : Spectre 最后一次更新是在 2020 年 6 月。它有 10k+颗星星。

**浏览器支持** : Spectre 支持现代浏览器，同时也使用 [autoprefixer](https://www.npmjs.com/package/autoprefixer) 支持 Internet Explorer 10 这样的老浏览器。

[**文档**](https://picturepan2.github.io/spectre/getting-started.html)

## 结论

微型 CSS 框架为 web 开发项目提供了一个简单的起点。他们的规模较小，学习曲线平坦。与 Bootstrap 等成熟的 CSS 库相比，Tailwind、Milligram 和 Tachyons 等库不那么固执己见，给你一个几乎空白的画布来轻松定制组件以满足你的外观需求。

对于高级定制，我个人推荐顺风。仅对于基本的构建工具，检查超光速粒子。

* * *

如有问题和反馈，请通过 [Twitter](https://twitter.com/ChisimdiriE) 联系我。更多我的文章，请在[媒体](https://sim04ful.medium.com/)上关注我。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。