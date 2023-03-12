# 使用 SVG 和 Vue.js:完整的指南

> 原文：<https://blog.logrocket.com/using-svg-and-vue-js-a-complete-guide/>

## What is SVG？

可缩放矢量图形(SVG)是前端开发人员最有用和最通用的工具之一。使用 SVG，我们可以得到任意大小的动态图像，其带宽通常只是 JPEG 和 PNG 等传统光栅图像格式的一小部分。

但是 SVG 可能更难使用，尤其是在像 [Vue.js](https://vuejs.org/) 这样的现代 JavaScript 框架中。从如何最好地将 SVG 文件加载到模板中的简单问题，到处理动画和可访问性，有许多陷阱需要避免。

在过去的几年里，我一直在与 Vue 和 SVG 合作，并且我已经开发了(双关语)一些技巧和建议，我想与大家分享。

虽然这篇文章中的例子都是用 Vue 展示的，但总体思路应该适用于任何基于组件的框架。

让我们开始吧！

## 使用 SVG 和 Vue

虽然从技术上讲，完成这项任务有三种以上的方法(HTML 默认有几种，这还是在我们添加 Vue 之前)，但我总是发现自己倾向于使用下面三种方法中的一种来掌握 SVG。下面的三种方法涵盖了绝大多数用例。

### 1.加载标准 HTML SVG

默认情况下，在浏览器中有几种方法可以将 SVG 文件加载到页面，其中任何一种都可以与 Vue 一起使用。说到底，Vue 使用动态 HTML 模板，这意味着在 Vue 中使用 SVG 的最直接的方式就是使用标准 HTML 的方式:

*   通过将`<svg>`放置成直线
*   通过使用一个链接到带有`src`属性的外部 SVG 文件的`<img>`标签

每种方法都有其利弊，但它们是用户最熟悉的两种方法，所以我不会在这里过多讨论。

可以说，如果它在 HTML 中有效，它在 Vue 中也会有效。因此，虽然这种方法并没有带来任何新的或华而不实的东西，但它也很简单。

### 2.使用 vue-svg-loader

让我们进入一些稍微令人兴奋的领域。有很多特定于 Vue 的方法来处理 SVG 文件。我个人最喜欢的是`[vue-svg-loader](https://vue-svg-loader.js.org/)`。

vue-svg-loader 唯一真正的缺点是，要使用它，你需要运行一个 npm 安装并编辑你的 [webpack](https://webpack.js.org/) 配置(见这里的[说明](https://vue-svg-loader.js.org/#installation))。工作量不大，但并不是所有项目都热衷于添加依赖项或额外的配置。根据项目的不同，你甚至可能无法控制这些选项。

但是，如果您有这种自由，vue-svg-loader 允许您导入 svg 文件并像使用 vue 组件一样使用它们，这既简单又方便。安装 vue-svg-loader 后，我们可以这样做:

```
 <template>
    <div id="app">
        <h1>Hello, world!</h1>
        <LogoSVG />
    </div>
</template>

<script> import LogoSVG from './logo.svg';

export default {
    components: { LogoSVG },

} </script>

```

如前所述，这条路线需要一些安装和设置，所以一定要查看 [vue-svg-loader 文档](https://github.com/visualfanatic/vue-svg-loader)，但是一旦运行起来就很容易使用了。

额外的好处是，SVG 文件被直接放在使用它的 DOM 中，因此为了动画和样式，您可以使用 JavaScript 和/或 CSS 来处理它的特定部分。

在这篇 StackOverflow 文章中，也有一些其他特定于 Vue 的选项可以让 SVG 进入你的应用程序[，但根据我的经验，vue-svg-loader 是大多数用例中最好的。](https://stackoverflow.com/questions/44695560/how-can-i-import-a-svg-file-to-a-vue-component)

这就引出了我们的第三个选择。

### 3.SVG 检视元件

这是我个人最喜欢的方法，我们将在本文余下的大部分时间里深入探讨它。这种方法允许您最灵活地使用动画，并直接控制 SVG 元素。它还允许轻松导入—所有这些都不需要额外的依赖或配置更改！

## SVG Vue 组件入门

每当我在 Vue 中使用 SVG 时，我的偏好几乎总是创建一个新的 Vue 组件，专门用于特定 SVG 文件的标记和样式。

然后，只要我想在我的项目中使用这个 SVG，我只需像其他 Vue 组件一样导入和使用它。下面是一个用于演示的基本 SVG 组件示例:

```
<template>
    <svg class="my-svg-component">

    </svg>
</template>

<script> export default {
   name: 'MySVGComponent',
} </script>

<style scoped> .my-svg-component {

} </style>

```

这种方法有几个主要优点:

*   它使 SVG 文件易于在任何地方使用。就像导入和使用任何其他 Vue 组件一样
*   SVG 文件以内联方式呈现在页面上，因此它的各个部分仍可用于样式、动画等。，但是它不会占用主模板文件和组件中的额外空间
*   这种方法包括你使用 Vue 得到的所有好处，意味着你可以使用道具和逻辑。如果您有 SVG 的替代版本(例如，黑暗模式的反转 SVG 或同一图标的多个版本)，您可以轻松利用 Vue magic

坏处呢？设置 Vue SVG 组件需要一些额外的工作。您必须手动移动(并且可能编辑)SVG 文件的实际标记。在 SVG 文件只是一个永远不会改变、不需要逻辑或重用的静态图像的情况下，将 SVG 文件引入 Vue 组件可能不值得。

如果您发现自己希望能够用一些 JavaScript 逻辑更新图像的外观，或者使图像适应当前的应用程序状态，SVG Vue 组件是一个完美的选择！

让我们看一个例子。假设您有一个用户可以点击的“收藏夹”图标按钮。最初，它只是一个轮廓(如左图所示)，但如果用户点按它，星号就会填充以指示用户已将该项目标记为收藏(如右图所示):

![Favorite Icon Starred](img/67197c16c7e843170f5710425bcd34b1.png)

在过去，这意味着您需要两个不同的图像，可以随时交换。但是使用 SVG 和 Vue，您可以轻松地创建一个单独的组件来代替道具，并相应地呈现图标的状态。

让我们看看代码:

```
<template>

    <svg
        :class="{ starred }"
        :title="starred ? 'Starred' : 'Unstarred'"
        role="img"
    >
        <path />
    </svg>
</template>

<script> export default {
    name: 'FavStar',

    props: {
        starred: {
            type: Boolean,
            default: false,
        }
    }
} </script>

<style scoped> path {
    fill: none;
}

.starred path{
    fill: #ffd100;
} </style>

```

附注:显然，一个真正的 SVG 文件需要比上面显示的更多的标记(比如一个`viewBox`属性)，但是为了可读性，我省略了与 Vue 无关的部分。

只有在提供了`starred`属性的情况下，`starred`类才会被添加到 SVG 文件中，从而使组件成为动态的。(`{ starred }`是`{ starred: starred }`的简写，其中第一个是有条件添加的类，第二个是有条件检查的 Vue prop。)

上面的组件现在可以导入并放到代码库中的任何地方。例如，您可以在委托数据和处理点击的`<button>`中使用它:

```
<button @click="starOrUnstar" :aria-pressed="isStarred">
    <FavStar :starred="isStarred" />
</button>

```

我不会进一步充实整个按钮组件，但是希望很容易看到如何在需要的地方导入和使用`FavStar`组件，以及如何用 Vue 数据管理它的外观。

## 在 Vue 中处理 SVG 可访问性

在上面的例子中，您可能会注意到`title`属性也是基于`starred`属性的动态属性。这是因为在这种情况下，以非视觉方式传达图标的状态非常重要。

易访问性(或" [a11y](https://www.a11yproject.com/) ")的一个核心原则是，你不应该需要视觉来判断某物处于什么状态或它意味着什么；页面的每一个有意义的部分都应该是非视觉可感知的。

因为我们的星形图标有语义含义(它告诉用户他们以前是否给某个项目加了星或收藏了某个项目)，所以我们需要确保依靠辅助技术(如屏幕阅读器)的用户能够非视觉地判断图标的状态——按下还是未按下。由于一个简单的三元组，`title`属性很容易处理这个问题。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，因为包装 SVG 的按钮实际上是一个双向切换开关——该按钮或者启动或者取消启动与其相关的项目——我还选择在按钮本身上使用一个动态的`:aria-pressed="isStarred"`。

这样，按钮传达它自己的当前状态，并隐式地将它自己标识为一个开关。它有两种可能的状态:开或关、`true`或`false`。

如果没有这一点，辅助技术用户可能会关注按钮，并听到他们的屏幕阅读器从 SVG 的`title`中宣布“加星”或“未加星”,并对缺乏关于按钮功能的进一步上下文感到困惑。

在做出可访问性决策时，与真实用户一起测试总是很重要的。很容易误用旨在使事物更易访问的标记(尤其是 [ARIA](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA) )并以相反的效果结束。

你甚至可以得到你想要的效果，但是你会发现这根本不是你的用户想要的。因此，请确保您没有对您的可访问性选择如何影响您的用户做出任何假设。而是从源头找出来。

要了解更多信息， [Deque](https://www.deque.com/) 有一个极好的列表，列出了在 SVG 上处理可访问性的[方法，我强烈推荐阅读。描述的主要方法是基本的，但是还有其他方法，包括直接在`<svg>`中添加一个`<text>`元素。](https://www.deque.com/blog/creating-accessible-svgs/)

请记住，许多 SVG 文件将纯粹是装饰性的，它们的描述不会增加任何体验。

例如:想象一个按钮，上面写着“添加另一个项目”，并带有一个“+”SVG 图标作为装饰。

![Add Another Item Icon](img/855c3e042d1907f549c4790352f0b19e.png)

在这种情况下，图标“+”不会添加任何内容，因为文本已经说明了需要说明的所有内容。

每当 SVG 文件不传达任何附加信息时，最好对辅助技术隐藏 SVG 文件。最简单的方法是将`aria-hidden="true"`添加到`<svg>`元素中，尽管您也可以将`role="img"`和`alt=""`一起添加到元素中。

## 在 Vue 中创建动态图标

只要我们在 Vue 组件中工作，我们就可以使用 Vue 所有强大的逻辑和工具。我们不局限于每个组件只有一个 SVG 我们可以使用`v-if`或`v-show`有条件地呈现任意数量的单个元素，实现`v-for`循环，甚至为单个元素添加事件处理程序。

让我们想象一个具有三种状态的标签组件:

1.  正常，无图标
2.  带有勾号图标
3.  带有红色“x”图标

![Three Tags](img/2da855bf51742e98e73043bcc363df5c.png)

在这里，我们甚至不需要 SVG——所有这些都很简单，可以用纯 CSS 来呈现。

然而，可以想象，图标在我们应用程序的其他部分可能会有所帮助，在那里我们可能希望显示类似的成功/错误状态。因此，使图标可重复使用可以帮助我们走下去。

我们可以将复选标记和“x”圆圈重叠在一起，创建一个单独的图标，使它们各自的笔画看起来像这样:

![Check Icon](img/8fe4c7939548f9c3716e30b8071932d4.png)

一旦我们使用单个 SVG，我们可以单独导出它，并创建一个使用`status`道具的 Vue 组件。

基于组件在使用时得到的`status`属性(以及它是否有效)，组件显示组成“x”的勾号`<path>`或`<g>`，并相应地改变图标的背景。

```
<template>
    <svg v-if="iconBackgroundColor" width="100%" height="100%" viewBox="0 0 58 58" style="fill-rule:evenodd;clip-rule:evenodd;stroke-linecap:round;stroke-linejoin:round;stroke-miterlimit:1.5;">
        <circle :style="{ fill: iconBackgroundColor }" cx="28.546" cy="28.546" r="25.546" />
        <path v-if="status === 'success'" d="M16.243,28.579l8.18,8.181l16.427,-16.427" />
        <g v-if="status === 'error'">
            <path d="M20.333,37.747l16.427,-16.427" />
            <path d="M20.333,21.32l16.427,16.427" />
        </g>
    </svg>
</template>

<script> const colors = {
    success: '#ffd100',
    error: '#e4002b',
}

export default {
    name: 'StatusIcon',

    props: {
        status: {
            type: String,
            default: '',
            validator(status) {
              return colors[status] || status === ''
            }
        }
    },

    computed: {
        iconBackgroundColor() {
            return colors[this.status]
        }
    }
} </script>

<style scoped> path,
circle {
    stroke: #ffffff;
    stroke-width: 6px;
    fill: none;
} </style>

```

这种情况下只有两种状态:`success`和`error`。但是很容易想象需要类似的图标，并且这个组件可以利用至少一个或两个以上的图标和颜色。

如果发生这种情况，很简单的方法就是在 SVG 文件中添加另一个`<path>`,在`colors`对象中添加另一种颜色。另一个好处是这将自动更新`status`道具的`validator`。

从这里很容易想象可能包含许多图标或变体的动态图标组件，以及它为代码作者提供的能力和灵活性。

您甚至可以想象一个`<Icon>`组件，它的模板中包含多个不同的 SVG 文件，并根据传入的属性有条件地呈现正确的文件。

## 编辑 SVG 文件并将标记移动到 Vue 组件

您可能对创建矢量感兴趣，也可能不感兴趣，但是您每天使用的 SVG 文件很可能是由设计师创建的，并从 Adobe Illustrator 或 Figma 等设计工具中导出。您可能对实际创建 SVG 文件一无所知，这没关系！

通过像 [SVGO](https://github.com/svg/svgo) 这样的优化引擎运行 SVG 文件通常很有帮助。(如果你更喜欢图形用户界面而不是 CLI，还有杰克·阿奇博尔德(Jake Archibald)的 SVGO 的[图形用户界面版本)。](https://jakearchibald.github.io/svgomg/)

这将在您使用 SVG 文件之前去除不必要的内容，使其尽可能具有高性能。请务必仔细比较优化版本和原始版本，以确保没有删除任何不应该删除的内容。

希望当 SVG 文件被导出时，设计软件已经被适当地配置来优化它，所以这一步是多余的。但是你永远不知道，这通常是开发人员比设计人员更关心的领域。

在任何情况下，要将`.svg`文件放入 Vue 组件，请在您选择的文本编辑器(VS 代码、Sublime 文本等)中打开它。)并将`<svg>`部分从开始标签复制到结束标签。(注意:这可能是大量的文本，在编辑器中切换换行可能会更容易查看！)

接下来，只需将`<svg>`及其内容粘贴到 Vue 组件中。它可能需要一些修改。例如，下面是上面我的星形图标的原始导出 SVG:

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?><!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "<http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd>"><svg width="100%" height="100%" viewBox="0 0 203 194" version="1.1"  xmlns:xlink="<http://www.w3.org/1999/xlink>" xml:space="preserve" xmlns:serif="<http://www.serif.com/>" style="fill-rule:evenodd;clip-rule:evenodd;stroke-linecap:round;stroke-linejoin:round;stroke-miterlimit:1.5;"><path d="M95.188,7.487c1.199,-2.297 3.575,-3.737 6.166,-3.737c2.591,0 4.967,1.44 6.165,3.737c0,0 16.769,32.138 24.044,46.08c2.079,3.986 5.907,6.767 10.34,7.513c15.508,2.61 51.254,8.627 51.254,8.627c2.555,0.43 4.659,2.245 5.46,4.709c0.8,2.464 0.165,5.169 -1.649,7.019c-0,-0 -25.383,25.878 -36.395,37.105c-3.148,3.21 -4.61,7.709 -3.95,12.156c2.31,15.556 7.634,51.412 7.634,51.412c0.38,2.563 -0.695,5.124 -2.791,6.647c-2.097,1.523 -4.866,1.755 -7.185,0.601c-0,-0 -32.456,-16.144 -46.537,-23.147c-4.025,-2.002 -8.756,-2.002 -12.781,-0c-14.08,7.003 -46.536,23.147 -46.536,23.147c-2.32,1.154 -5.089,0.922 -7.185,-0.601c-2.096,-1.523 -3.172,-4.084 -2.792,-6.647c0,-0 5.324,-35.856 7.634,-51.412c0.66,-4.447 -0.802,-8.946 -3.95,-12.156c-11.011,-11.227 -36.394,-37.105 -36.394,-37.105c-1.815,-1.85 -2.45,-4.555 -1.649,-7.019c0.8,-2.464 2.904,-4.279 5.459,-4.709c0,-0 35.747,-6.017 51.254,-8.627c4.434,-0.746 8.261,-3.527 10.341,-7.513c7.275,-13.942 24.043,-46.08 24.043,-46.08Z" style="fill:none;stroke:#ffd100;stroke-width:7.5px;"/></svg>

```

您可能想知道 XML 和 DOCTYPE 标签是怎么回事。

它们是为了兼容其他文档类型，比如 XML。它们在 HTML 中不做任何有用的事情。只要您的 SVG 文件仅用于 HTML，那么开始的`<svg>`标记之前的所有内容都可以安全地删除。这同样适用于`version`、`xmlns`和`xml`属性。

要注意的主要部分是 CSS 样式。

根据 SVG 文件的导出方式，它可能在一个或多个 SVG 元素上有内联的`style`属性，或者它可能有一个带有类的`<style>`标记，然后这些类被添加到 SVG 文件的元素中。

如果是后者，不要太担心，因为基于类的样式很容易根据需要在 CSS 中覆盖。

但是，如果 SVG 文件有内联样式，您将需要明智地修剪 CSS，以便可以更容易地应用您想要的样式。

例如，当使用我的星形图标时，我需要从内嵌样式列表中删除`fill:none;`，这样我就可以只在需要的时候添加填充颜色。(覆盖内联样式的唯一方法是使用`!important`标志，这通常是尽可能避免的。)

如果你愿意，你也可以把所有的内联样式复制出来，放到 Vue 组件的`<style>`块中，但是我更喜欢只在有意义的地方这样做，让其余的留在原处。

例如，如果我打算在任何时候改变这个星形图标的轮廓颜色，我会将`stroke`属性从标记中移出，并移到`<style>`块中。

无论您选择做什么，现在您已经有了一个包含 SVG 的 Vue 组件，并且您已经准备好使用样式、逻辑或您可能想要的任何东西了。

## 在 Vue 中处理 SVG 问题

请注意，在浏览器中使用 SVG 时，并不是所有在设计软件中使用 vector 的功能都可以在浏览器中使用 SVG。

首先，阴影和渐变等效果在浏览器中的工作方式与在设计软件中不同。结果可能是混合的，如果可能的话，最好在 CSS 中重新创建这些效果。

更大的问题是，大多数矢量绘图程序将允许您将笔画对齐到路径的中间、内部或外部，如下图所示:

![Vector Outlines](img/d2b094743ce036c489f63fdc9b301a74.png)

但是，浏览器中的 SVG 文件只能与路径中间对齐，因此没有内部或外部对齐选项。

如果您需要描边的外观与路径的内部或外部对齐，则需要将中间对齐的描边适当偏移描边宽度的一半，或者将描边扩展到它自己的 vector 对象中。

如果您正在制作笔划的动画，这可能是一个挑战，因此在这些情况下，请确保与 SVG 作者密切合作。你当然可以达到你想要的任何效果——只是需要一些创造性的变通方法。

## 结论

在 Vue 中使用 SVG 时，我们研究了三种流行的方法:

*   标准 HTML SVG
*   view SVG 载入器
*   SVG 组件视图

在大多数情况下，Vue SVG 组件是我个人的偏好，但是这里讨论的三种方法各有利弊。

| **方法:** | **最适合:** | **缺点:** |
| --- | --- | --- |
| **标准 HTML SVG** | 快速方便地放置静态 SVG 图像 | 根据不同的方法，要么用 SVG 标记来混淆模板，要么阻止对 SVG 的单个元素进行样式化或定位 |
| vista SVG 加载器 | 使用 SVG 像 Vue 组件，而不需要编辑触摸 SVG 文件 | 需要安装和配置更改 |
| SVG 组件视图 | 最大限度的控制和便利 | 需要手动编辑 SVG 文件，这可能是令人畏惧和/或乏味的 |

希望你喜欢这篇文章，并且至少对 SVG 和 Vue 的强大组合有了一点了解和欣赏！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。