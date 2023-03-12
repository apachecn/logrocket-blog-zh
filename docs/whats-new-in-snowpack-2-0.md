# Snowpack 2.0 中的新内容- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-snowpack-2-0/>

Snowpack 是一个 web 应用程序的构建工具，可以在开发中不捆绑使用。这完全归功于原生浏览器 ES 模块的强大功能，它允许该工具在开发环境中删除捆绑步骤。因此，Snowpack 有助于加快开发环境的构建速度，即使在代码库增长时也能保持快速。

Snowpack 还引入了在开发中拆分应用程序的概念，这有助于避免每次保存单个文件时都需要重新构建和重新绑定整个应用程序。它只是通过热模块替换对更改的文件进行即时更新。

[官方网站](https://snowpack.dev)将 Snowpack 描述为“一个现代的、轻量级的 web 应用开发工具链。”

## Snowpack 2.0 中的新功能

当 1 月向公众发布 1.0 版本时，它提供了令人兴奋的功能，例如:

*   在 50 毫秒或更短时间内启动的开发环境
*   能够在浏览器中即时反映变化
*   与您最喜欢的捆扎机集成，实现生产优化构建
*   对 TypeScript、JSX、CSS 模块等的现成支持。
*   能够将您喜爱的工具与定制构建脚本和第三方插件连接起来

Snowpack 团队于 5 月 26 日发布了 2.0 版本，其中包含许多令人兴奋的新功能，包括:

*   O(1)文件构建
*   改进的开发环境
*   改进的自动 HMR 支持
*   能够使用构建脚本定制您的构建
*   零配置原型制作
*   创建 Snowpack 应用程序(CSA)模板

### O(1)文件构建

官方网站将 snowpack 2.0 描述为“一个为网络开发新时代设计的构建系统。”O(1)文件构建只涉及重建或重新捆绑在开发过程中更改的文件，而不重建整个应用程序。换句话说，如果您对开发中的文件进行了更改，只有该文件会被重新生成，而不会影响其他文件。

这不同于其他构建工具，如 webpack，它是一个 O(n)构建工具。它们被称为 O(n)构建工具，因为传统的捆绑具有 O(n)的复杂度。当一个文件在传统的捆绑中被改变时，构建工具将在应用程序中重建整个块，因为只有一个文件。

Snowpack 2.0 附带了对 O(1)文件构建的支持，这允许它以闪电般的速度进行更新，因为当文件在开发过程中发生更改时，只有该文件会被重建。

O(1)文件构建包括传统 O(n)捆绑的许多好处，包括:

1.  更快的构建
2.  项目的文件大小不会影响构建的性能
3.  单个文件在第一次构建时被缓存，只有在发生更改时才会重新构建

### 改进的开发环境

由于 O(1)文件构建，开发服务器现在比以往任何时候都快。当你运行`snowpack dev`时，你会注意到启动变得有多快。因为没有执行捆绑工作，所以在<50 毫秒后启动。

Snowpack 只构建对特定页面至关重要的文件，而不管项目中包含多少文件。这是可能的，因为[动态导入](https://javascript.info/modules-dynamic-imports)，它将只在需要的时候加载一个模块。

### 改进的自动 HMR 支持

Snowpack 使用热模块替换在浏览器中进行即时更新，这是一个非常酷的功能。

在版本 2 中，Snowpack 附带了对以下开箱即用的自动支持:

*   半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   CSS 模块
*   JSON
*   通过配置做出反应(快速刷新)
*   通过配置预动作(预刷新)
*   通过插件的 Vue
*   通过插件保持苗条

Snowpack 团队在通用 ESM 原生`import.meta` HMR API 上附加了一个`hot()`函数。

### 使用构建脚本定制您的构建

Snowpack 2.0 改进了构建脚本的使用，将您最喜欢的构建工具连接到 Snowpack。这看起来和感觉起来就像是在一个`package.json`文件中配置脚本。

使用构建脚本，您可以完全控制整个构建过程。您可以调整它以适应您的需要，或者构建您自己的构建过程。Snowpack 提供了第三方 JavaScript 插件，让你可以毫无困难地构建自己的项目插件。

构建脚本通常保存为`snowpack.config.json`。这个构建脚本将通过我们上面指定的 Babel 插件运行每个 run `*.js,*.jsx,*.mjs,*.cjs`文件。它还将通过 PostCSS 命令行界面运行每个`*.css`文件。

```
{
  "scripts": {
    // Run every "*.css" file through the PostCSS CLI
    "build:css": "postcss",
    //Run every "*.js,*.jsx,*.mjs,*.cjs" file through the babel plugin
    "build:js,jsx,mjs,cjs": "@snowpack/plugin-babel",
  },
  "plugins": [
    "@snowpack/plugin-babel",
  ],
}

```

### 零配置原型制作

Snowpack 2.0 中最酷的特性之一是零配置原型，它使您无需配置基本的东西就可以使用该工具。换句话说，除非你真的需要，否则你不会需要一个`snowpack.config.json`文件。

默认情况下，Snowpack 提供开箱即用的配置:

*   为浏览器重写您的包导入
*   将 JSX 和 TS 文件传输到 JS
*   通过 ES 模块代理 CSS 和 JSON 导入

如果你需要做额外的处理，你可以创建一个脚本或者添加一个插件来帮助你。

### 创建 Snowpack 应用程序(CSA)模板

Snowpack 2.0 提供了新的和改进的入门模板来满足您的需求。CSA 是一个入门模板，由一个预配置的、基于 Snowpack 的开发环境组成。它类似于流行的 [Create React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) ，只是由 Snowpack 而不是 webpack 驱动。

CSA 模板可用于一些流行的 JavaScript 框架和静态站点生成器，包括:

您还可以创建自己的 Create Snowpack 应用程序模板，并与社区共享。

## 结论

Snowpack 2.0 提供了大量新功能和对现有功能的改进，有助于简化构建过程。

要了解更多信息，请查看官方 Snowpack 2.0 发布帖子。

Snowpack 2.0 中你最喜欢的新功能是什么？请在评论区告诉我们。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。