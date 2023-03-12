# Nuxt.js 中的 CSS 转换:概述

> 原文：<https://blog.logrocket.com/css-transitions-in-nuxt-js-an-overview/>

## 介绍

> 好的动画是看不见的。你不应该注意到你在看动画。–[帕斯夸莱·德席尔瓦](https://www.youtube.com/watch?v=TMe0WnkF1Lc&list=UURx1y52pfeMwbuer9Vh2u-A)

CSS 转换是一种动画，通过在状态变化之间创建平滑的视觉连续性来增强用户体验。好的过渡应该几乎不引人注意，而设计糟糕的过渡会在你的应用程序中产生明显的拖拉感或恶作剧感。

在这篇文章中，我将演示如何在 Nuxt.js 中创建令人惊叹的过渡，从如何使用 Nuxt.js 构建应用程序的基础开始。请注意，您应该对 [Vue.js](https://vuejs.org/) 有所了解，以便能够跟随本教程。

## 什么是 CSS 转场？

当 CSS 属性在一段时间内从一个值变为另一个值时，就会发生 CSS 转换。例如，如果我们想将一个元素的颜色从红色变为白色，我们可以使用一个过渡来从一种颜色变为另一种颜色。

因为过渡应该是流畅的，而不是突兀的，所以它们可以帮助创建更好的用户体验，提供视觉提示，保持上下文和连续性，并提高用户参与度。

在 CSS 中，使用简写的`[transition](https://developer.mozilla.org/en-US/docs/Web/CSS/transition)`属性控制过渡，子属性如 [`transition-duration`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-duration) 、 [`transition-timing-function`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function) 和 [`transition-delay`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-delay) 使我们能够为任何有效元素添加过渡效果。

大多数浏览器都支持[过渡，尽管 Internet Explorer 和 Safari 存在一些小问题。](https://caniuse.com/?search=CSS%20transition)

## Nuxt.js 入门

Nuxt.js 是一个基于 Vue.js、Node.js、webpack 和 Babel 的免费、开源的现代 web 应用框架。js 允许我们创建三种类型的应用程序，每一种都取决于我们构建的目的:

*   静态页面
*   单页应用程序
*   服务器端呈现(SSR)应用程序

### 设置我们的 Nuxt.js 应用程序

我们可以通过使用搭建工具来创建我们的 Nuxt 应用程序，或者从头开始构建。出于我们的目的，我将使用前者。

首先，使用您选择的软件包管理器运行以下命令:

```
npx create-nuxt-app <project-name>
//or
yarn create nuxt-app <project-name>
//or
npm init nuxt-app <project-name>

```

请确保用您的项目/应用程序的名称替换`<project-name>`。

一旦安装完成，我们将面临一系列问题来帮助配置我们的应用程序进行开发，包括名称、Nuxt 选项、UI 框架、TypeScript、linter、测试框架等等。

这些问题的答案大多是个人喜好。本文中我的配置如下:

![Setting Up Nextjs App Article Configuration](img/54999f61087a846286694d6dababb3b7.png)

完成后，我们将在终端中运行以下命令:

```
$ cd <project-name>
$ npm run dev

```

完成以上步骤后，我们的应用程序应该在 [http://localhost:3000](http://localhost:3000/) 上运行。这是我们应该看到的:

![App Running Localhost Display Transition App](img/5eb4a24792edeb434fa6925666034dbd.png)

## 利用 Vue.js `transition`组件

Nuxt.js 利用 Vue `transition` [组件](http://vuejs.org/v2/guide/transitions.html#Transitioning-Single-Elements-Components)来让我们在页面和/或路径之间创建惊人的过渡。

当我们的元素或 Vue 组件进入或退出屏幕时，通过更新 CSS value 属性来创建转换。Vue `transition`组件允许我们捕捉元素插入或移除之前、之中和之后的瞬间。

如下图所示，有三个类用于捕获进入的过渡(`v-enter`、`v-enter-to`和`v-enter-active`)，另外三个类用于捕获离开的过渡(`v-leave`、`v-leave-to`和`v-leave-active`)。

![Vuejs-transition-component-three-classes-diagram](img/83474d7acf9d2a85f4d2d2706dc411bc.png)

> **注意**:这些类是命名空间的，所以`v`将被 CSS 转换类替换。例如，`fade`类看起来像这样:`fade-leave`、`fade-leave-to`和`fade-leave-active`。

## Nuxt.js 中的过渡

Nuxt 中的页面过渡可以是全局的，也可以应用于单个页面。页面转换是基于路径的，当我们使用`nuxt-link`导航到那个页面时，我们可以看到它们在起作用。

### 全局页面转换

顾名思义，这些过渡是从一个文件`nuxt.config.js`添加到 Nuxt 应用程序的所有页面中的。设置全局页面转换分为两步。

首先，我们必须定义转型，并使之全球化。为此，我们必须在 assets 文件夹中创建一个全局 CSS 文件。为了演示，我将使用一个非常简单的渐变过渡。

```
// /assets/css/main.css

.page-enter-active,
.page-leave-active {
  transition: opacity 0.5s;
}
.page-enter,
.page-leave-active {
  opacity: 0;
}

```

注意，在这个例子中，我使用了`page`而不是`fade`。这是因为`page`是默认的全局页面转换名称空间。

如果我们想要覆盖默认的名称空间并使用我们自己的名称空间，我们必须使用`pageTransition`属性来覆盖默认的转换名称，如下所示:

```
// /nuxt.config.js

pageTransition: "fade"

```

完成后，我们现在可以使用下面的代码了:

```
// /assets/css/main.css

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-active {
  opacity: 0;
}

```

在我们定义了过渡之后，我们必须在`nuxt.config.js`文件中包含 CSS 文件，这样应用程序的每个页面都可以使用它。为此，我们将使用以下代码:

```
// /nuxt.config.js

css: [
  '~/assets/css/main.css'
],

```

### 单个页面过渡

对于单个页面转换，我们需要在每个页面中使用转换键来定义转换。为此，我们将转到所需页面并添加以下代码:

```
// /About.vue

export default {
  transition: 'fade'
}

```

就这么简单！我们也可以利用`object`选项来定义我们的转换属性。

### 布局转换

布局过渡本质上是全局的，并且具有默认值`layout`。属性使我们能够基于页面布局应用过渡。它的工作方式与`pageTransition`类似，但是它将动画应用于布局而不是页面。

这里有一个例子:

```
// /nuxt.config.js
layoutTransition: 'fade'
  // or
layoutTransition: {
    name: "fade",
    mode: "out-in"
},

```

***注:*****示例* *转场我有* *分享过* *在本文* *中全部集中在* *不透明度**。不言而喻，*** *我们* *可以执行其他类型的转场* *。**

 *### 过渡值

到目前为止，我们只使用了`fade`过渡，但是我们当然可以在我们的 Nuxt 应用中利用其他类型的过渡值。默认情况下，Nuxt 将在我们的全局 CSS 文件中查找这些类名。

虽然默认情况下`transition`值被设置为`string`，但是也有其他方法来分配 CSS 值，比如`object`和`function`。

例如，让我们将`transition`值设置为`object`，如下块所示:

```
export default {
  // namekey becomes required
  transition: {
    name: 'bounce'
  }
}

```

现在我们能够添加更多的属性，比如`mode`和`appear`:

```
export default {
  // namekey becomes required
  transition: {
    name: "bounce",
    mode: "in-out", // default is out-in
    appear: true, // default is false
  }
}

```

下面是正在发生的事情:`mode`属性`out-in`(这是默认模式)在新元素进入之前等待当前元素退出。对`in-out`来说也是如此，只是反之亦然。

第二个属性`appear`，允许我们在初始渲染时应用过渡；默认情况下，它的值被设置为 false，当我们以对象格式定义我们的转换时，我们可以很容易地定制它。

## 结论

在本文中，我们学习了 CSS 转换以及如何在 Nuxt.js 中利用页面和路径中的转换。为了进一步探索这个主题，这里有一些有用的资源:

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。*