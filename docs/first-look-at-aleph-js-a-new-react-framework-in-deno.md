# 首先看看 aleph . js:Deno-log rocket 博客中的一个新的 React 框架

> 原文：<https://blog.logrocket.com/first-look-at-aleph-js-a-new-react-framework-in-deno/>

[Aleph.js](https://alephjs.org/docs) 是受流行框架 [Next.js](https://nextjs.org) 启发的开源 JavaScript 框架。该框架比 Next.js 更新，目前处于 alpha 阶段。

该框架有一个专门的修复错误的开源社区。本文对 Aleph.js 进行了概述，并对其特性进行了介绍。

## Aleph.js 概述

Aleph.js 的构建考虑到了安全性，因此它使用 JavaScript/TypeScript 框架 [Deno](https://blog.logrocket.com/what-is-deno/) 作为其运行时环境。使用 Deno 意味着 Aleph.js 用户可以完全控制他们的程序可以使用什么，并且必须显式地允许访问，无论是使用服务器上的文件还是更改环境变量。

开发人员不需要在安装后配置 Aleph.js，因为它是为了简单而构建的，并且在幕后做了大量工作。Aleph.js 不要求开发人员使用第三方 JavaScript bundler，而是使用标准 ES6 语法处理导入，允许开发人员随意放置和删除 JavaScript 片段。

这种级别的安全性和简单性是可能的，因为 Deno 是用 [Rust](https://www.rust-lang.org/) 语言编写的，这些特性随后扩展到 Aleph.js. Rust 是作为常见的 [web 开发安全问题](https://www.youtube.com/watch?v=NQBVUjdkLAA)的简单解决方案开发的，如未经授权的文件访问和跨站点脚本，因此 Aleph.js 可以很好地防止这些问题。

在撰写本文时，Aleph.js 的创建者明确表示，该框架将在最新版本的 Google Chrome、Microsoft Edge、Mozilla Firefox、Safari 和 Opera 上运行。它也可以在其他浏览器上运行，但是还没有其他浏览器被 Aleph.js 的创建者验证过。在其他浏览器被创建者验证之前，你应该小心谨慎。

## Aleph.js 功能

Aleph.js 有许多有用的特性，允许开发人员定制他们的网页并以动态方式部署它们。这种灵活性使得 Aleph.js 对任何技能水平或任何职业轨迹的开发人员都很有用。

### 按指定路线发送

Aleph.js 使用通用路由框架来构建网页和 API 端点。页面或 API 端点的地址直接来自其数据文件名。这种做法鼓励简单明了的命名约定，帮助网站和 API 用户快速找到他们需要的东西。

数据文件可以有多种格式，包括 JavaScript(。js)、ECMAScript 模块文件(。mjs)、JavaScript XML(。jsx)、TypeScript(。ts)、TypeScript XML(。tsx)和 Markdown(。md)。路由可以是静态的或动态的，路由框架支持嵌套，因此用户可以创建页面或 API 层次结构。

路由框架还通过一个 locale 选项简化了国际化。Aleph.js routes 为您提供了一种灵活的方式，将网站用户指引到最需要他们的地方。

### SSR 和 SSG 支持

该框架支持服务器端呈现(SSR)和静态站点生成(SSG)。默认情况下，Aleph.js 会预先呈现页面，因此客户端的浏览器不必做所有的工作。如果您愿意，可以关闭这个 SSR，但是它可能会导致您的页面或 API 端点运行缓慢。

另一方面，SSG 选项创建了一个可以在任何服务器上运行的独立网站，但是依赖于客户端来完成 JavaScript 的繁重工作。SSG 还要求您在生成网站之前将动态路径重新定义为静态路径。同时支持 SSR 和 SSG 使 Aleph.js 能够轻松处理大型和小型站点。

Aleph.js 允许在一个名为 **`aleph.config.js`** 的文件中进行 SSR 和 SSG 配置。通过该文件进行配置需要编写有效的 JavaScript，因此一些 SSR 和 SSG 功能需要仔细编码。

例如，从 SSR 中排除路径需要一个 JavaScript 导出语句:

```
export default {
  "ssr": {
    "exclude": [
      "/path/from/root"
      "/path/from/root/two/"
    ]
  },
  ...
}

```

类似地，在 SSG 中指定动态路径需要了解异步 JavaScript 和静态路径以返回 SSR:

```
export default async () => {
  const data = await (await fetch("https://.../data")).json()
  return {
    ssr: {
      exclude: [...],
      staticPaths: data.map(({id}) => `/data/${id}`)
    }
  }
}

```

### 热模块更换

Aleph.js 还包含一个名为[热模块替换](https://alephjs.org/docs/basic-features/hmr-with-fast-refresh)的功能，可以快速刷新，或者简称为 HMR。此功能允许您通过刷新当前正在使用的 Aleph.js 应用程序来刷新浏览器中的页面。您可以看到 JavaScript XML、TypeScript XML 和 Markdown 文件的更改结果。

由于页面样式也会影响读者对内容的理解，HMR 使用 CSS 文件和为低预处理器创建的文件。使用 HMR 的一个主要优点是，当你进行修改时，你不必担心在多个地方刷新你的工作。Aleph.js 可以节省您的时间，并确保您总是看到最新的工作成果。

### 轻松定制

该框架在 JavaScript 应用程序和应用程序生成的页面中为您提供了许多定制选项。通过定制默认的 React 应用程序，您可以使用模板化元素和目标错误处理等技术来控制页面或 API 调用的初始化方式。

Aleph.js 还支持许多重要页面元素的注入，包括页面头、页面上任何地方的脚本和异步导入。默认情况下，Aleph.js 在页面加载时显示一个空白屏幕，但是您可以自定义加载屏幕。

虽然框架在找不到文件时提供了默认的 404 页面，但是您也可以使用一个名为 E404 的函数来自定义出现此错误时显示的页面。与 SSR 和 SSG 定制一样，错误页面在 **`aleph.config.js`** 中指定。

```
export default function E404() {
  return <h1>LogRocket Page Missing</h1>
}

```

## Aleph.js 设置和安装

Aleph.js 需要用两个简单的命令进行最少的设置。

1.  安装 Deno 运行时。你可以安装它作为一个可执行文件，或者使用许多程序中的一个从命令 line.Mac/Linux 外壳:

```
curl -fsSL https://deno.land/x/install/install.sh | sh

```

Windows PowerShell:

```
iwr https://deno.land/x/install/install.ps1 -useb | iex

```

1.  从命令行使用 Deno 安装 Aleph.js。在撰写本文时，版本号是 0.2.28。

```
deno install -A -f -n aleph https://deno.land/x/[email protected]/cli.ts

```

## 结论

Aleph.js 是一个正在积极开发的 JavaScript 框架，但显示出很大的前景。它的社区努力使部署变得简单，以便于采用。

该框架已经因其对安全性的关注而脱颖而出，社区计划随着它添加更多的特性而继续这一趋势。一旦 Aleph.js 走出 alpha 阶段，你可能会看到它出现在更多的讨论中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。