# 使用 Vitest - LogRocket Blog 以最少的配置测试 Vite

> 原文：<https://blog.logrocket.com/testing-vite-minimal-config-using-vitest/>

自 2021 年初以来，Vite 的风靡全球。它极大地简化了开发人员的工具，并成为了[几个流行元框架](https://v3.nuxtjs.org/)的基础。现在，它已经准备好通过 Vitest 接管我们的测试套件。😮

让我们讨论一下 Vitest 是如何工作的，将它与一个流行的测试套件配置(Jest 和 Babel)进行比较，探索 Vitest 简化测试套件配置的方法，并检查性能增益如何加快您的`watch`模式。如果你想跳到感兴趣的部分，请点击下面的链接:

## Vite 是什么？

Vite 的价值主张变得如此宽泛，以至于很难确定。总结一下:Vite 是一个网站捆绑器，可以处理你的 JavaScript、CSS、静态资产，以及你加载到 HTML 文档中的任何东西。

还记得苹果最初对 iPhone 的价值定位吗，“一个 iPod，一部电话，一个网络通讯器…”我认为 Vite 是一个类似的命题，因为它将三个相关的工具捆绑成一个:

维特是怎么做到的？嗯，它不是将单个`main.js`文件作为我们网站的入口，而是抓取 HTML 文件来跟踪所有 JavaScript、样式和您使用的各种资产。这使得 Vite 能够管理整个构建管道，包括一个快如闪电的开发服务器。⚡️

在开发中，Vite 可以智能地决定在将更改保存到文件时实际需要刷新哪些资产。编辑一个只在你主页上使用的 React 组件，比如？

Vite 不会像 webpack 那样重新捆绑你的整个站点，它只会把一个 React 组件发送到你的浏览器。它甚至可以避免刷新页面，保持状态变量不变。

这个[热模块重新加载](https://vitejs.dev/guide/features.html#hot-module-replacement)是 Vite 的秘密酱到积极的开发者体验，这给我们带来了 Vitest 的核心价值主张。

## 什么是 Vitest？

> 🚨注意:截至 2022 年 2 月，仍不建议将 Vitest 用于生产。[在使用之前，请查看 Vitest 的主页](https://vitest.dev/)获取最新建议。

Vitest 是一个 Vi 测试框架，建立在 Vite 之上，着眼于速度和最小配置。我们将在接下来的章节中探讨每一个问题。

## Vitest 测试套件的整体方法:Jest、Babel 和 webpack 同时测试

Vitest 在测试领域的方法类似于 Vite 在捆绑领域的方法:让工具控制您的整个环境，从上到下。Vitest 是一套工具的替代品:

*   Jest、Mocha 或 Chai 用于测试实用程序
*   用于传输 ESM、TypeScript 等的 Babel
*   捆绑测试依赖项的 webpack 或 Rollup(如果需要)

这意味着您需要管理的配置、依赖性不匹配和性能监控要少得多。只需安装`vitest`并让奇迹发生。✨

## Vitest 入门:与 Jest 相比如何？

尽管 Vitest 的价值主张包罗万象，但它的测试运行程序 API 几乎与 [Jest](https://jestjs.io/) 完全相同:`describe`、`expect`、`it.each`、模拟函数、间谍、并行测试的`concurrent`标志……所有人都在这里([API 的完整列表](https://vitest.dev/api/))！

事实上，Vitest 和 Jest 非常相似，几乎不值得在这里使用代码示例。我建议去 Vitest 的[游乐场](https://vitest.dev/new)看看测试跑步者的表现。但是为了完整起见，这里有一个用 Vitest 的 API 编写的简单单元测试:

```
// example.test.js
import { expect, test } from 'vitest';

test('Math.sqrt()', () => {
  expect(Math.sqrt(4)).toBe(2);
  expect(Math.sqrt(144)).toBe(12);
  expect(Math.sqrt(2)).toBe(Math.SQRT2);
});

```

> 如果您想知道，“等等，为什么那些测试助手不是全球可用的？”这只是默认。您可以通过配置文件使 Vitest 包在全局可用。[更多](https://vitest.dev/config/#globals)信息参见 Vitest 文档。

如果您在本地创建这个文件，您可以通过运行`npx vitest`来启动 Vitest 的`watch`模式。您应该看到一个带有三个通过断言的`Math.sqrt()`测试。

## 为 Vitest 构建一个示例项目

简单的测试用例很好，但是让我们探索一些更复杂的用例。

我使用 React 创建了一个全栈 web 应用程序作为前端，并使用一个 [Netlify 无服务器函数](https://www.netlify.com/products/functions/)来处理表单请求。然后，我编写了两个测试文件:

你可以[在这里探索完整的 GitHub 回购](https://github.com/Holben888/react-netlify-serverless-with-form-handlers)👀

请注意，这些工具都使用了不常用的工具组合: [TypeScript](https://www.typescriptlang.org/) 、 [ESM](https://css-tricks.com/life-with-esm/) 用于服务器和客户端逻辑，以及 [JSX](https://reactjs.org/docs/introducing-jsx.html) 用于集成测试。这应该涵盖了单个项目中一系列流行的 JavaScript 用例。

让我们使用 Jest + Babel 作为基线来讨论速度和配置的改进。

## 使用 Vitest 的最小配置—从一个巴别塔到一个依赖项

> TL；DR: Vitest 比 Jest 和 Babel 从零开始配置要容易得多。如果你已经使用了 Vite，那么使用 Vitest 几乎是一件容易的事情。即使您不这样做，在 Vite 配置中复制您的 Babel 构建过程也只需要很少的前期成本。不过，如果你想让你的配置和你的 web 应用 100%一致，你可能倾向于使用 Jest 来附带配置。

好吧，让我们谈谈配置。我尝试使用最流行的测试套件建议作为参考框架:Jest 用于测试实用程序和 Babel 插件来转换 Jest 不理解的内容。我还避免了对 ESM 使用 Jest 的“实验性”标志，因为此时使用它更不可靠，可能会影响性能。

此外，我们在配置测试套件时没有考虑应用本身。如果你的网站已经有了一个 Babel 和 webpack 配置(比如说， [CRA](https://create-react-app.dev/) )，你可能会有更好的时间来配置 Jest。不过，为了便于比较，让我们忽略这一点。

下面是让我们的测试从绝对零度开始运行所需的所有开发依赖:

```
{
  "devDependencies": {
    // babel presets for ESM, typescript, and React
    "babel-jest": "^27.5.0",
    "@babel/core": "^7.17.0",
    "@babel/preset-env": "^7.16.11",
    "@babel/preset-react": "^7.16.7",
    "@babel/preset-typescript": "^7.16.7",
    // Jest types for global "describe," "it," etc
    "@types/jest": "^27.4.0",
    // Helper to stub out CSS modules when present
    // per Jest docs recommendation
    // https://jestjs.io/docs/webpack#mocking-css-modules
    "identity-obj-proxy": "^3.0.0",
    "jest": "^27.5.0",
    "typescript": "^4.4.4"
  }
}

```

总共有九个依赖项。尽管使用这种工具组合已经很多年了，但还是花了将近一个小时来筛选过时的包和堆栈溢出的帖子。

清除 CSS 模块尤其棘手。是的，您的里程数在这里会有所不同，但我想特别指出新开发人员的这个症结。

现在，让我们看看我们对 Vitest 的依赖:

```
{
  "devDependencies": {
    "@testing-library/react": "^12.1.2",
    "typescript": "^4.4.4",
    "vitest": "^0.2.7",
    // optional plugin - auto-inject "React" import when it's missing
    "@vitejs/plugin-react": "^1.0.7",
  }
}

```

我们刚刚从使用九个依赖项减少到四个——三个，不包括可选的 React 插件。从[阅读 Vitest 的文档](https://vitest.dev/config/)开始，我们第一次尝试就让它工作了。

现在，如果你在想，“当然，但是你必须写多少配置”，不要担心。越来越好了。

首先，让我们看看 Jest plus Babel 的必要配置。我们需要一些 Jest 配置来清除 CSS 模块导入:

```
// jest.config.js
module.exports = {
  coverageProvider: "v8",
  moduleNameMapper: {
    // stub out CSS imports per Jest's recommendation
    "\\.(css)$": "identity-obj-proxy",
  },
};

```

以及 React 和 TypeScript 的一些 Babel 预设:

```
// babel.config.js
module.exports = {
  presets: [
    ['@babel/preset-env', {targets: {node: 'current'}}],
    ['@babel/preset-react', {
      "runtime": "automatic"
    }],
    '@babel/preset-typescript',
  ],
};

```

现在，让我们来看看最新的配置。因为 Vitest 将测试和代码捆绑集成到一个工具中，所以我们只需要一个配置文件:

```
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
})

```

看，这里没有用于 TypeScript、CSS 模块、环境预置等的配置！这是因为 Vite 用合理的缺省值解决了这个问题，我们不需要调整。此外，因为 Vitest 只是扩展了 Vite 的功能，所以现有的 Vite 项目不需要任何配置。

## 测试维特的速度

在我们开始之前，我将在这一部分添加一个免责声明:Vitest 目前仍处于早期阶段，因此除了 Matti Bar-Zeev 的初步探索之外，几乎没有什么基准可言。总结一下调查结果:

1.  在`watch`模式下运行比 Jest 模式要快得多
2.  一次性的跑步，就像你可能使用的自动测试跑步一样，速度差不多

我的发现非常相似。

### 示例项目的结果

我会说这纯粹是轶事证据。这些结果是基于我的本地机器的性能，可能会因那些克隆 repo 的人而异。我建议持怀疑态度的早期用户自己尝试，但以下是我的发现:

1.  Vitest 的实时重新加载要快得多——文件改变时重新运行该套件只需 39 毫秒，而 Jest 只需 250 毫秒。不过，CLI 的控制台日志确实有些滞后，这使得性能提升在这个较小的范围内变得更加微不足道。
2.  Jest 在`watch`模式之外的运行速度几乎是两倍——每次运行大约 1 秒，而 Vitest 在使用`vitest run`模式时为 1.88 秒。

让我们来探讨一下为什么会出现这种情况。

### Vitest 的`watch`模式怎么这么快？

我认为 [Anthony Fu 的 Twitter 帖子](https://twitter.com/antfu7/status/1468233216939245579)说得最好:

> 就像 Vite 在浏览器中的工作方式一样，Vitest 也知道你的模块的图形，这使得它能够进行智能检测，并且只重新运行相关的测试。感觉几乎像 HMR，但测试😍

这是将您的开发环境捆绑到单个工具中的最大优势。因为 Vitest 知道你的应用程序所依赖的每个模块，所以它可以智能地决定在文件改变时应该重新运行哪些测试。这在我们的集成测试示例中特别有用。

每当我们编辑我们的`Form.tsx`时，Vitest 可以快速发现哪些测试依赖于这个文件，瞬间重新处理，只重新运行相关的测试。相比之下，在像这样编辑测试文件依赖项时，我根本无法让 Jest 重新运行。

### 还需要做哪些工作来加速 Vitest？

好了，让我们来解决`watch`模式之外的性能问题。这是 Vitest 社区中的一个已知问题，源于与 Jest 的一个核心区别:Vitest 是[ESM](https://css-tricks.com/life-with-esm/)——首先，Jest 是基于[更老的 CommonJS 标准](https://nodejs.org/docs/latest/api/modules.html#modules-commonjs-modules)，你可能从 Node 那里知道。

换句话说，你可以用`import x from` `'path'`来代替开箱即用的`const x = require('path')`。

这是现代开发人员工具的巨大胜利。随着越来越多的模块放弃对 CommonJS 的支持，我们的开发工具与时俱进是至关重要的。

然而，这种新的 ESM 思维方式迫使 Vitest 使用不同的测试运行策略。总结一下:

*   Jest 为每个测试套件创建了一个“全局环境”
*   由于 ESM 支持，Vitest 不能以同样的方式使用全局模块，所以默认情况下，它为每个测试文件创建隔离的“workers”

这种隔离意味着要花更多的时间来启动测试套件。遗憾的是，这对小项目和大规模项目的性能有影响。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

> 注意:您可以从他们的 CLI 中[禁用这种隔离。您需要格外小心测试之间共享的全局状态，但是这对大多数用户来说应该会提高性能。](https://vitest.dev/guide/features.html#threads)

但请记住，Vitest 仍处于早期阶段。因此，根据他们的建议，我现在会避免将 Vitest 用于生产应用程序。嗯，除非最低配置和快速开发服务器已经足够了。😉

## 结论

我希望这篇文章能让你对 Vitest 的未来感到兴奋！它仍处于开发阶段，因此我强烈建议您访问 [Discord 社区](https://chat.vitest.dev/)来提问、贡献 PRs，并随时关注绩效讨论。“简化开发人员工具”是一个值得关注的主要趋势，我希望 Vitest 能进一步推动这一愿景。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。