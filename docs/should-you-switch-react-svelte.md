# 你应该从反应型转变为苗条型吗？- LogRocket 博客

> 原文：<https://blog.logrocket.com/should-you-switch-react-svelte/>

***编者按**:本文最后一次更新于 2022 年 3 月 16 日，以反映 [React](https://github.com/facebook/react/blob/main/CHANGELOG.md) 和[svelite](https://github.com/sveltejs/svelte/blob/master/CHANGELOG.md)的最新发布。*

React 和 Svelte 是两个前端 JavaScript 框架，为开发人员提供了构建 web 应用程序的高效方法。虽然 React 经常被视为前端开发人员的自动选择( [Stack Overflow 的 2021 年开发人员调查](https://insights.stackoverflow.com/survey/2021#section-most-popular-technologies-web-frameworks)已经将 React 列为 web 框架的第一名)，但 Svelte 有很多优点，不应该被忽视。

在这篇文章中，我们将看看 React 和 Svelte 之间的主要区别，并回顾 Svelte 带来的一些优势，这些优势可能被 React 在前端社区中的压倒性存在所掩盖。

### 内容

## React 的虚拟 DOM vs. Svelte 的编译器

React 使用虚拟 DOM 在运行时解释应用程序代码。它捆绑了特定数量的开销代码，这些代码将在浏览器的 JavaScript 引擎中运行，以监控和更新 DOM。虚拟 DOM 监视任何变化，并计算对真实 DOM 进行这些变化的最佳方法。

Svelte 是一个将应用程序翻译成 JavaScript 代码的编译器。这确保了当您的程序在 DOM 中运行时，不会在浏览器中注入额外的框架代码。

在构建时，Svelte 将组件翻译成高效的命令式代码，以外科手术的方式更新 DOM。Svelte 已经证明了没有虚拟 DOM 也可以获得出色的性能，所以他们决定去掉它。

因为所有细长的代码在到达浏览器之前都被编译成最小的 JavaScript，所以你能够编写高性能的代码。

## 易用性

如果你正在决定是学习 React 还是学习 Svelte，这两个框架的易用性是一个需要考虑的重要因素。

虽然 React 的应用更加广泛，但是当您必须学习像 JSX 和 CSS-in-JS 这样的东西来构建最基本的应用程序时，学习 React 可能会令人望而生畏。

与 React 相比，Svelte 更容易理解和入门，因为 Svelte 的主要部分是普通的 JavaScript、HTML 和 CSS。Svelte 也紧跟 JavaScript 的经典 web 开发模型，并且只引入了一些 HTML 的扩展，这使得它更容易学习。

为了演示，这里有一个细长组件的例子。我们这里只有基本的 HTML、CSS 和 JavaScript:

```
<style>
  h1 {
    color: green;
  }
</style>

<script>
  let name = 'Nefe';
</script>

<h1>Hello, {name}!</h1>

```

## 开发者体验

虽然更少的代码并不总是意味着更好，但是更多的功能性代码(Svelte 的一个优点)意味着用 Svelte 编写代码的时间比用 React 少。让我们来看一个演示。

下面是一个用 Svelte 制作的基本应用程序的示例:

```
<script>
  let a = 1;
  let b = 2;
</script>

<input type="number" bind:value={a}>
<input type="number" bind:value={b}>

<p>{a} + {b} = {a + b}</p>

```

下面是用 React 构建的同一个应用程序的代码片段:

```
import React, { useState } from 'react';

export default () => {
  const [a, setA] = useState(1);
  const [b, setB] = useState(2);

  function handleChangeA(event) {
    setA(+event.target.value);
  }
  function handleChangeB(event) {
    setB(+event.target.value);
  }

  return (
    <div>
      <input type="number" value={a} onChange={handleChangeA}/>
      <input type="number" value={b} onChange={handleChangeB}/>
      <p>{a} + {b} = {a + b}</p>
    </div>
  );
};

```

包括空格在内，Svelte 是 9 行代码，而 React 是 21 行。这是一个显著的差异！苗条应用的美妙之处在于它的功能丝毫不减。

此外，使用 Svelte，您不需要担心忘记导出组件，因为它们是默认导出的，并且可以被其他组件导入。使用 React，您必须手动操作。

Svelte 还带有内置的[效果和动画](https://blog.logrocket.com/essential-transitions-and-animations-in-svelte/)。您不需要像在 React 中那样使用第三方库来创建动画。`svelte/motion`、`svelte/transition`、`svelte/animate`、`svelte/easing`为我们提供了强大的模块，让我们能够创作出开箱即用的精彩动画。令人惊讶的是，这些内置效果不会增加你的应用程序的大小。

有了 Svelte，你就不用担心写独特的类，或者样式从它们的组件中泄露出来；样式在其样式标记中是组件范围的，这允许灵活的样式。

所有这些好处通过使您的工作变得更容易，对开发人员来说意味着更少的头痛。这也意味着你不需要学习如何使用样式库，比如 [Styled-Components](https://blog.logrocket.com/benefits-using-styled-components-react/) 和 Emotion。因此，您可以更快地编写和发布代码，因为技术开销更少。

## 服务器端渲染

服务器端呈现(SSR)是应用程序将服务器上的 HTML 文件转换为完全呈现给浏览器的 HTML 页面的能力。当用户请求页面时，服务器向客户端发送完全呈现的 HTML、CSS 和 JavaScript。

SSR 有几个优点，比如更快的初始页面加载时间和更好的 SEO。此外，SSR 页面对于网速较慢的用户来说是最理想的，因为他们可以在处理 JavaScript 时看到呈现的 HTML。

在这个特殊的比较中，React 可能会以 [Next.js](https://blog.logrocket.com/creating-website-next-js-react/) 胜出，这是创建 SSR 应用程序最流行的框架。

在苗条中，有[苗条套装](https://blog.logrocket.com/react-remix-vs-next-js-vs-sveltekit/)。SvelteKit 是一个完整的 Svelte 框架，用于构建 Svelte 网站。和 Next 一样，SvelteKit 附带了路由、布局、API 端点、静态站点生成、服务器端渲染等功能。

虽然 Next 和 SvelteKit 有相似之处，但也有明显的不同，其中之一是它们处理图像的方式。Next 的`Image`组件让处理图像成为开发者的梦想。我们再也不用担心自己压缩和优化图像；下一个为我们处理它们。

Next 相对于 SvelteKit 的另一个优势是受欢迎程度和社区。Next 有 83.3k GitHub 星，SvelteKit 有 7.5k 星。Next 有一个更加活跃的开发者社区，这意味着更多的模板、资源、课程和文章。

## 表演

当您运行一个 Svelte 应用程序的生产版本时，Svelte 会将您的代码编译成高度优化的普通 JavaScript。这意味着在运行时没有添加框架代码，这提高了应用程序的整体性能，因为浏览器需要做的工作更少了。

Svelte 对 DOM 进行了外科手术式的更新，不需要依赖中间人或任何复杂的协调技术。Svelte 的编译器跟踪变量的变化，并相应地更新 HTML。它会检查您的代码，观察依赖于您的变量的组件，然后随着变量的变化更新这些组件。这样，Svelte 是反应式的，不需要依赖第三方 API。

另一方面，通过调用`this.state`或使用`useState`钩子，React 必须被告知注意修改:

```
<script>
  let count = 0;
  $: doubled = count * 2;

  function handleClick() {
    count += 1;
  }
</script>

<button on:click="{handleClick}">Click me!</button>

<p>{count} doubled is {doubled}</p>

```

这里，我们有一个变量`count`，和另一个`doubled`。`doubled`的价值取决于`count`。如果我们要更新`doubled`，在 React 中，我们必须写`setDoubled(count)`。

然而，JavaScript 标识符`$:`告诉 Svelte`doubled`是一个依赖于`count`的变量，Svelte 知道每当`count`改变时自动更新`doubled`。要更深入地了解反应，请查看奥维·奥凯的文章。

## 开发者受欢迎程度

React 仍然是最流行的 web 框架。然而，这个调查并不局限于前端框架，还包括了像 ASP.NET、Ruby on Rails、Laravel 等框架。

根据 JS 2021 的一项调查，Svelte 在开发人员中的满意度较高。90%的开发者对 Svelte 满意，84%的开发者对 React 满意。在同一调查中，Svelte 被选为最受欢迎的框架。

## 捆绑大小

关于 Svelte 的另一个很酷的事情是，它的包大小比 React 留下的足迹小得多。这在性能方面产生了巨大的差异，尤其是在低功耗设备或 CPU 密集型应用中。当 gzipped 时，Svelte 也具有较小的包大小。

Svelte 的 gzipped 版本为 1.6KB，React gzipped 为 42.2KB(含 ReactDOM)。

## 结论

苗条的生态系统发展迅速。随着 [Sapper](https://blog.logrocket.com/sapper-svelte-tutorial-2021/) 的推出，这是 Svelte 的 Next.js 版本，以及用于构建原生移动应用的 [Svelte Native](https://blog.logrocket.com/svelte-native-vs-react-native/)

很明显，Svelte 年复一年地吸引着开发者的兴趣。随着 Rich Harris 搬到 Vercel，Svelte 现在拥有了 Vercel 生态系统的资金和社区支持，并准备通过这一新的发展实现雄心勃勃的增长。从来没有一个更好的时间来关注苗条身材。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。