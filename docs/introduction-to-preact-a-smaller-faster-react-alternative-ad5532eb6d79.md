# Preact 简介:一个更小、反应更快的替代博客

> 原文：<https://blog.logrocket.com/introduction-to-preact-a-smaller-faster-react-alternative-ad5532eb6d79/>

Preact 是一个 JavaScript 库，它将自己描述为一个快速的 3kB 替代方案，用于与相同的 ES6 API 进行交互。作为 react 的经常用户，我一直想尝试 Preact，看看它提供了什么，以及 Preact 和 React 之间的区别。在本文中，我将介绍 Preact、它的关键概念以及 Preact 和 react 之间的区别。

我还将通过构建一个 Preact 应用程序来展示 Preact 如何与外部 API 一起工作，该应用程序显示来自 [Dribbble](http://dribbble.com) 的最新出道镜头。

### Preact 简介

如上所述，Preact 是由[杰森·米勒](https://github.com/developit)和一群[贡献者](https://github.com/developit/preact/graphs/contributors)开发的 react 的 3kb 替代品。Preact 的开发目标是构建一个小型的 JavaScript 框架，同时提供与 React ships 相同的 API 和特性。

总大小为 3kb，这意味着您不必担心您的 JavaScript 库/框架会占用应用程序总 JavaScript 大小的大部分。

Preact 速度很快，不仅仅是因为它的大小。得益于简单且可预测的 diff 实现，它是最快的虚拟 DOM 库之一。

Preact 旨在实现几个[关键目标](https://preactjs.com/about/project-goals):

*   **性能:**渲染迅速&高效
*   **尺寸:**体积小，重量轻
*   **效率:**有效内存使用率
*   **可理解性:**理解代码库不会超过几个小时
*   **兼容性:** Preact 的目标是*在很大程度上兼容*React API。preact-compat 试图实现与 react 尽可能多的兼容性。稍后会有更多关于`preact-compat`的内容。

Preact 在浏览器上运行良好(支持所有浏览器)，尽管它可能需要一些用于 IE7 和 IE8 的 polyfills。Preact 也通过大量的[插件](https://preactjs.com/about/libraries-addons)被社区广泛采用，公司现在开始转向 JavaScript 库。

Preact 现在被 Lyft、Housing.com 和 m.uber.com 这样的公司使用。优步的工程团队最近写了一篇[文章](https://eng.uber.com/m-uber/)，强调了 Preact 如何用于构建移动版的优步，这反过来又导致了更好的性能，这主要是因为它的尺寸非常小。

### Preact 和 react 有什么不同？

这是许多 React 开发人员在考虑 Preact 时会问的问题。React 已经是一个相当不错的 JavaScript 库，重约 45kb。那么你为什么会考虑用 Preact 构建应用呢？

> Preact 本身并不是 react 的重新实现。有区别。这些差异中有许多是微不足道的，或者可以通过使用 [preact-compat](https://github.com/developit/preact-compat) 来完全消除，这是 preact 上的一个薄层，试图实现与 react 的 100%兼容性。—Preactjs.com

**文件大小**

首先，文件大小有一个巨大的鸿沟。Preact 的 3kb 对 react 的 45kb 是显而易见的。Preact 没有试图包含 react 的每一个特性的原因是为了保持小巧和专注**。**

**类与类名**

当使用 React 时，如果你想给一个`div`添加一个类，你必须这样做。

```
# React code
<div className="class-here">Content Here</div>
```

在 Preact 中，你可以对 CSS 类使用`class`。仍然支持`className`，但是 Preact 鼓励你使用`class`。

```
# Preact code
<div class="class-here">Content Here</div>
```

**render()**

在 Preact 中，`this.props`和`this.state`被传给`render()`给你。这是什么意思？看看下面的代码。

正如你在上面看到的，在 Preact 中，我们可以通过移除`this`使代码看起来更干净，如果你想更进一步，你可以使用析构。参见下面的示例。

**移除支柱类型**

不是每个人都使用 PropTypes，所以它们不是 Preact 核心的一部分，因此从 Preact 中移除。如果您仍然想在您的项目中使用 PropTypes，您可以在`preact-compat`的帮助下这样做，或者手动使用它们。

### 使用 preact-compat 实现 preact 和 react 之间的兼容性

preact 有一个名为“ [**preact-compat**](http://npm.im/preact-compat) 的包，作为 Preact 和 react 之间的兼容层。使用`preact-compat`可以很容易地从反应切换到预反应。切换到 Preact 可以像在代码中安装和别名`preact-compat`一样简单。

这使您可以继续编写 React/ReactDOM 代码，而无需对工作流或代码库进行任何更改。为您提供支持 npm 上绝大多数现有 React 模块的优势。那么如何将`preact-compat`添加到现有的 React 代码中呢？

用 npm 安装`preact-compat`。

```
npm i preact-compat
```

下一步是别名 preact-compat。您可以通过将以下 resolve.alias 配置添加到您的`webpack.config.js`中来实现这一点

```
{
  "resolve": {
    "alias": {
      "react": "preact-compat",
      "react-dom": "preact-compat"
    }
  }
}
```

上面的代码块基本上解释为——如果我试图导入一个名为 React 或 ReactDOM 的模块，不要给我这些模块，而是给我右边提供的模块。

### Preact 入门

Preact API 和开发人员的体验在很大程度上类似于 react，但是开发人员应该知道一些重要的区别。

为了演示这些，我构建了一个简单的应用程序，使用 Dribbble API 显示最近的设计。在这里可以看到 live app[，在它的 Github](https://build-yjnahwkwzs.now.sh/) [repo](https://github.com/yomete/preactdribbble) 可以看到 app 的源代码。

在这里，我将指出开发人员在使用 Preact 进行构建时应该注意的一些关键区别。

PreactDribbble

为了构建 Preact 应用程序，我们将使用 [Preact CLI](https://github.com/developit/preact-cli) 。Preact CLI 是 Preact 团队的一个项目，帮助开发人员轻松开始使用 Preact。Preact CLI 可帮助您生成渐进式 Web 应用程序，并附带以下功能:

*   由 [sw-precache](https://github.com/GoogleChrome/sw-precache) 支持的自动生成的[服务工作者](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)用于离线缓存
*   路线的自动代码分割。
*   [PRPL](https://developers.google.com/web/fundamentals/performance/prpl-pattern/) 模式支持高效装载。
*   支持 CSS 模块，LESS，Sass，手写笔；使用 Autoprefixer。
*   自动安装应用程序，调试助手和热模块更换。
*   零配置预渲染/服务器端渲染水合

`preact-cli`工具旨在帮助您在很少或没有构建配置的情况下开始编写 Preact 代码。通过运行下面的命令在您的计算机上安装`preact-cli`。

```
npm install -g preact-cli
```

成功安装 CLI 后，在您的终端中运行以下命令，创建一个新的应用程序。`preactdribbble`是要创建的应用程序的名称，可以称为任何名称，应用程序的名称不必以`preact`为前缀。

```
preact create preactdribbble
```

这将创建一个名为`preactdribble`的新文件夹，其中包含 Preact 应用程序所需的所有文件。导航到`preactdribbble`文件夹，因为我们现在将从那里开始工作。让我们启动应用程序，看看它是什么样子的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
# start a live-reload/HMR dev server:
npm start
```

这将在 http://0 . 0 . 0 . 0:8080 或 http://localhost:8080 上启动一个带有**H**ot**M**module**R**e loading(HMR)的开发服务器。