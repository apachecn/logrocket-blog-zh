# 使用 React、Vite 和 Tailwind - LogRocket Blog 设置开发环境

> 原文：<https://blog.logrocket.com/setting-up-dev-environment-react-vite-tailwind/>

Tailwind 和 React 是这一领域的两项领先技术。Tailwind CSS 简化了[原子 CSS](https://acss.io/) 的概念，使开发人员能够通过向他们的标记添加一些类来设计他们的 UI。随着新的 JIT 编译器带来的性能改进，Tailwind CSS 显然已经成为开发人员的最爱。

Vite 还简化了捆绑过程，但采用了不同于传统捆扎机的方法。通过利用本地 JavaScript 模块和 esbuild，Vite 为我们提供了即时开发服务器启动时间和超快速捆绑。

Vite 和 Tailwind CSS 在简单性、性能和开发人员体验方面都很出色。

随着软件变得越来越复杂，以满足最终用户的需求，应用程序的大小线性增长，并导致对更快的开发工具和捆绑器的需求。虽然 [Create React App](https://create-react-app.dev/) 运行良好，但随着 App 规模的增大，其性能却在下降。这就是 Vite 的用武之地。

在本文中，我们将了解 Vite 以及如何使用它来设置 React 和 Tailwind CSS 应用程序。

## 背景资料

历史上，JavaScript 没有一个 API 供开发人员以模块化的方式编写代码。这是因为 JavaScript 最初是为小型浏览器脚本设计的。

这些年来，JavaScript 变得非常流行，并被用于编程的不同方面。然而，它的主要缺点是缺乏模块化。这使得 JavaScript 开发人员想出了创建模块的不同方法，例如:

问题是并非所有的模块规范都适用于浏览器。例如，CJS 只支持服务器端模块声明。此外，构建一个现代 web 应用程序需要使用一些浏览器不支持的库和包，如 React、Vue、TypeScript 等。

然而，这个问题可以通过捆绑的概念来解决。这个过程包括使用捆绑器(直觉，对吗？)将我们应用程序的所有文件和资源合并到一个可在浏览器中运行的包中。这导致了传统捆扎机的发展，如[网袋](https://webpack.js.org/)、[包裹](https://parceljs.org/)和[卷装](https://rollupjs.org/guide/en/)。

这有两个主要问题:开发服务器的缓慢冷启动和缓慢更新。下一代 JavaScript 构建工具，比如 Vite，为我们识别并解决了这些问题。

### Vite 是什么？

Vite 是法语中“快”的意思。它是一个现代的前端 web 开发构建工具，利用 es 模块或 ESM。Vite 拥有一个更精简、更快速的捆绑器,附带一个预配置的开发服务器。

## Vite vs. webpack 和传统捆扎机

Vite 作为一款 ESM 捆扎机，解决了上面提到的传统捆扎机的问题。我们将在下面介绍几个关键的竞争优势。

### 冷启动应用

与 webpack 不同，当我们冷启动应用程序时，Vite 会立即启动开发服务器，如下所示:

![Native ESM Based Dev Server ](img/120dda41e10d92e8ded21f5bf42ec87c.png)

Source: [https://vitejs.dev/assets/esm.3070012d.png](https://vitejs.dev/assets/esm.3070012d.png).

由于以下因素，Vite 可以立即冷启动开发服务器:

*   Vite [使用 esbuild](https://blog.logrocket.com/fast-javascript-bundling-with-esbuild/) 预绑定应用的依赖项，esbuild 由 Golang 构建，比 JavaScript bundlers 快 10-100 倍。这有助于 Vite 的性能
*   Vite 通过使用基于路由的代码拆分来动态地确定需要加载代码的哪一部分。因此，Vite 不必重新捆绑我们的整个应用程序
*   Vite 只转换和服务浏览器请求的源代码。这是可能的，因为 Vite 通过本地 ESM 提供我们的应用代码，使浏览器能够接管开发中的部分捆绑过程

### 建筑过程

与 webpack 等传统捆绑软件相比，Vite 在捆绑过程的实施细节上采取了不同的方法。webpack 等传统捆绑软件会在每次更新时重建整个应用程序。这样做的主要问题是它会变得非常昂贵。

为了解决这个问题，这些捆扎机使用了一种称为热模块更换或 HMR 的技术。HMR 是更新正在运行的应用程序中已更改模块的一种方式，因此您不必更新页面的其余部分。

然而，随着应用程序大小的增长，HMR 的更新速度呈线性下降。

因为 Vite 使用 ESM，所以它通过 ESM 执行 HMR。这使得当一个模块被更新时，Vite 只能`invalidate`更新模块和它最近的 HMR 边界之间的链。因此，无论应用程序的大小如何，Vite 中的 HMR 总是很快。

### 性能优势

Vite 使用浏览器通过利用 HTTP 头来加速整个页面的重新加载。它通过`Cache-Control: max-age=31536000, immutable`处理缓存依赖模块请求，因此它们不会再次命中服务器。

最后，源代码模块请求是通过`304 Not Modified`来决定的。

所有这些都使 Vite 比基于包的构建工具具有显著的性能优势。

### 代码拆分

Vite 和传统捆绑器的另一个主要区别是对代码分割的处理。像 webpack 和 Rollup 这样的传统捆绑器产生公共块——在两个或更多其他块之间共享的代码。这与动态导入结合使用时，可能会导致多次网络往返，如下所示:

![Code Splitting Diagram](img/41813c9e59a7575f44b002affdf92a8e.png)

Source: [https://vitejs.dev/assets/graph.8f2f36b7.png](https://vitejs.dev/assets/graph.8f2f36b7.png).

如图所示，在未优化的场景中，当`async`块`A`被导入时，如果不首先请求和解析`A`，浏览器无法计算出它需要公共块`C`。在它发现它需要公共块`C`后，它就导入它，导致额外的网络往返。

Vite 以不同的方式实现代码分割动态导入调用，增加了一个预加载步骤。这样，当请求块`A`时，块`C`被并行获取。这完全消除了网络往返。

与 Create React App 相比，Vite 在性能方面一枝独秀，原因如上。尽管两者都是 Node.js 应用程序，最终可以实现相同的目标，但 Create React App 的一个缺点是其性能。

虽然 Create React App 只适用于 React，但 Vite 是框架不可知的，支持许多现成的库和框架。

在下一节中，我们将通过用 Vite 搭建一个 React 应用程序来学习如何使用 Vite。

## 使用 React、Vite 和 Tailwind 设置项目

要搭建项目，请从终端运行以下代码:

```
npm create [email protected]

```

选择项目名称并选择模板。按照屏幕上的说明完成设置，如下所示:

![Project Setup](img/3dc4e802fb1c02dd3f9e022b305b061e.png)

或者，我们可以通过使用`CLI`中的`— template`标志来选择一个模板，如下面的代码所示:

```
# npm 6.x
npm create [email protected] my-vue-app --template vue

# npm 7+, extra double-dash is needed:
npm create [email protected] my-vue-app -- --template vue

# npm 6.x
npm create [email protected] my-react-app --template react

# npm 7+, extra double-dash is needed:
npm create [email protected] my-react-app -- --template react

```

接下来，安装应用程序依赖项，并通过运行以下命令启动开发服务器:

```
# Installs dev dependencies
npm install

#Starts dev server
npm run dev

```

我们得到了:

![Vite And React Welcome Screen](img/0deeb29f6126c4886aa43a8ec804087f.png)

现在我们将集成 Tailwind CSS。最无缝的方法是按照下面的步骤使用 postCSS:

首先，通过运行以下命令安装 Tailwind CSS 及其对等依赖项:

```
npm install -D tailwindcss postcss autoprefixer

```

接下来，通过运行以下命令创建您的`tailwind.config.cjs`文件:

```
npx tailwindcss init

```

将 Tailwind 添加到您的 postCSS 配置中。为此，创建一个`postcss.config.cjs`文件并添加以下代码:

```
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  }
}

```

Vite 自动将`postcss.config.cjs`对象中所有有效的 postCSS 配置应用于所有导入的 CSS。

如下所示，通过修改`tailwind.config.cjs`文件来配置模板路径:

```
module.exports = {
  content:["./src/**/*.{js,jsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
}

```

通过用以下代码替换`index.css`文件的代码，将 Tailwind 指令添加到您的 CSS 中:

```
@tailwind base;
@tailwind components;
@tailwind utilities;

```

现在，通过运行`npm run dev`来运行构建过程。可以看到已经应用了顺风 CSS 样式。

为了看到这一点，我们可以将一些 Tailwind CSS 类应用于计数器应用程序。

在`src`目录下，创建一个`components`目录。

在`components`目录中，用以下代码创建一个`Counter.jsx`组件:

```
import React, { useState } from "react";
const Counter = () => {
    const [count, setCount] = useState(0)
    return (
        <div className="flex h-screen">
            <div className="m-auto">
                <div className="text-6xl text-red-600">{count}</div>
                <button className="px-6 py-2 rounded bg-green-800 hover:bg-green-600 text-white" type="button" onClick={() => setCount((count) => count + 1)}>
                    count+
                </button>
            </div>
        </div>
    )
}
export default Counter;

```

现在用以下代码替换`App.jsx`组件:

```
import Counter from './components/Counter'

function App() {
  return (
    <div>
      <Counter />
    </div>
  )
}
export default App;

```

现在，我们得到:

![Final Result](img/acd1c4da659e5c6c7dc6440ab338d410.png)

这证实了我们已经用 Vite、React 和 Tailwind CSS 成功地引导了我们的应用程序！

## 结论

在本文中，我们了解了 Vite 及其工作原理。我们将 Vite 与 webpack 等传统捆绑器进行了比较，发现 Vite 具有一些明显的性能优势，并了解了如何通过引导 React 和 Tailwind 应用程序来使用 Vite。

通过这样做，我真的希望你准备好在下一个 React 应用程序中尝试一下 Vite。

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