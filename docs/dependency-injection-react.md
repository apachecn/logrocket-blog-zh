# React - LogRocket 博客中的依赖注入

> 原文：<https://blog.logrocket.com/dependency-injection-react/>

依赖注入(DI)是一种模式，其中代码运行所需的组件是可热插拔的。这意味着您的依赖关系在您的实现中不是硬编码的，而是可以随着环境的变化而变化。

通过继承，DI 是一种在面向对象编程中广泛使用的模式 (OOP ),旨在使代码可以跨不同的对象和类重用。然而，在 React 中使用依赖注入的主要原因是为了方便地模拟和测试 React 组件。不像在 [Angular](https://blog.logrocket.com/how-dependency-injection-works-in-angular/) 中，DI 在使用 React 时并不是必需的，而是当你想整理东西时使用的一个方便的工具。

## JavaScript 中的依赖注入

为了说明 DI 的原理，假设一个 npm 模块公开了下面的`ping`函数:

```
export const ping = (url) => {
  return new Promise((res) => {
    fetch(url)
      .then(() => res(true))
      .catch(() => res(false))
  })
}

```

在现代浏览器中使用`ping`功能就可以了。

```
import { ping } from "./ping"

ping("https://logrocket.com").then((status) => {
  console.log(status ? "site is up" : "site is down")
})

```

但是在 Node.js 中运行这段代码会抛出一个错误，因为 Node.js 中没有实现`fetch`。但是，我们可以使用 Node.js 的许多`fetch`实现和 polyfills。

DI 允许我们将`fetch`转换成`ping`的可注入依赖，就像这样:

```
export const ping = (url, fetch = window.fetch) => {
  return new Promise((res) => {
    fetch(url)
      .then(() => res(true))
      .catch(() => res(false))
  })
}

```

我们不需要给`fetch`一个默认值`window.fetch`，但是不需要我们在每次使用`ping`的时候都包含它，这样会有更好的开发体验。

现在，在节点环境中，我们可以结合使用`[node-fetch](https://www.npmjs.com/package/node-fetch)`和`ping`函数，如下所示:

```
import fetch from "node-fetch"
import { ping } from "./ping"

ping("https://logrocket.com", fetch).then((status) => {
  console.log(status ? "site is up" : "site is down")
})

```

## 使用多个从属关系

如果我们有多个依赖项，将它们作为参数添加是不可行的。相反，更好的选择是拥有一个依赖对象:

```
const ping = (url, deps) => {
  const { fetch, log } = { fetch: window.fetch, log: console.log, ...deps }

  log("ping")

  return new Promise((res) => {
    fetch(url)
      .then(() => res(true))
      .catch(() => res(false))
  })
}

ping("https://logrocket.com", {
  log(str) {
    console.log("logging: " + str)
  }
})

```

我们的参数`deps`将被扩展到一个实现对象中，并将覆盖它所提供的函数。通过从这个修改的对象进行析构，幸存的属性将被用作依赖项。

使用这种模式，我们可以选择覆盖一个依赖项，而不是其他的。

## React 中的依赖注入

在使用 React 时，我们大量使用定制钩子来获取数据、跟踪用户行为和执行复杂的计算。不用说，我们不希望(也不能)在所有环境下运行这些钩子。

测试期间跟踪页面访问会破坏我们的分析数据，从真实的后端获取数据会导致测试运行缓慢。

测试并不是唯一这样的环境。像 [Storybook](https://storybook.js.org/) 这样的平台简化了文档，不需要使用我们的许多钩子和业务逻辑。

### 通过 props 的依赖注入

以下面的组件为例:

```
import { useTrack } from '~/hooks'

function Save() {
  const { track } = useTrack()

  const handleClick = () => {
    console.log("saving...")
    track("saved")
  }

  return <button onClick={handleClick}>Save</button>
}

```

如前所述，运行`useTrack`(推而广之，`track`)是要避免的。因此，我们将通过 props 将`useTrack`转换为`Save`组件的依赖:

```
import { useTracker as _useTrack } from '~/hooks'

function Save({ useTrack = _useTrack }) {
  const { track } = useTrack()

  /* ... */
}

```

通过别名化我们的`useTracker`以避免名称冲突，并将其用作道具的默认值，我们保留了应用程序中的挂钩，并能够在需要时覆盖它。

名称`_useTracker`是许多命名约定中的一个:`useTrackImpl`、`useTrackImplementation`和`useTrackDI`都是在试图避免冲突时广泛使用的约定。

在 Storybook 中，我们可以用一个模拟的实现来覆盖钩子。

```
import Save from "./Save"

export default {
  component: Save,
  title: "Save"
}

const Template = (args) => <Save {...args} />
export const Default = Template.bind({})

Default.args = {
  useTrack() {
    return { track() {} }
  }
}

```

#### 使用 TypeScript

当使用 TypeScript 时，让其他开发人员知道依赖注入特性就是这样，并使用精确的`typeof`实现来保持类型安全是很有用的:

```
function App({ useTrack = _useTrack }: Props) {
  /* ... */
}

interface Props {
  /**
   * For testing and storybook only.
   */
  useTrack?: typeof _useTrack
}

```

### 通过上下文 API 的依赖注入

使用上下文 API 使依赖注入感觉像 React 的一等公民。在切换环境时，能够在组件的任何级别上重新定义钩子运行的上下文是很方便的。

出于测试目的，许多著名的库提供了其提供者的模拟实现。React 路由器 v5 有`[MemoryRouter](https://v5.reactrouter.com/web/api/MemoryRouter)`，而 Apollo 客户端提供了一个`[MockedProvider](https://www.apollographql.com/docs/react/development-testing/testing/#the-mockedprovider-component)`。但是，如果我们采用阿迪驱动的方法，这种嘲笑供应商是没有必要的。

React 查询就是一个很好的例子。我们可以在开发和测试中使用同一个提供者，并将其提供给每个环境中的不同客户端。

在开发中，我们可以使用一个裸的`queryClient`，所有默认选项保持不变。

```
import { QueryClient, QueryClientProvider } from "react-query"
import { useUserQuery } from "~/api"

const queryClient = new QueryClient()

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <User />
    </QueryClientProvider>
  )
}

function User() {
  const { data } = useUserQuery()
  return <p>{JSON.stringify(data)}</p>
}

```

但是在测试我们的代码时，像重试、重新获取窗口焦点和缓存时间这样的特性都可以相应地进行调整。

```
// storybook/preview.js
import { QueryClient, QueryClientProvider } from "react-query"

const queryClient = new QueryClient({
  queries: {
    retry: false,
    cacheTime: Number.POSITIVE_INFINITY
  }
})

/** @type import('@storybook/addons').DecoratorFunction[] */
export const decorators = [
  (Story) => {
    return (
      <QueryClientProvider client={queryClient}>
        <Story />
      </QueryClientProvider>
    )
  },
]

```

React 中的依赖注入不仅仅是钩子的专利，JSX、JSON 以及任何我们希望在不同环境下抽象或改变的东西都是如此。

### 依赖注入的替代方案

根据上下文的不同，依赖注入可能不是合适的工具。例如，使用拦截器(如 [MSW](https://mswjs.io/) )而不是在你的测试代码中注入钩子，可以更好地模拟数据获取钩子，对于更大的问题，彻底的[模拟函数](https://jestjs.io/docs/mock-functions)仍然是一种先进而笨重的工具。

## 为什么应该使用依赖注入？

**使用 DI 的理由:**

*   开发、测试或生产中没有开销
*   非常容易实现
*   不需要模仿/存根库，因为它是 JavaScript 的原生库
*   满足您所有的存根需求，如组件、类和常规函数

**不使用 DI 的理由:**

*   弄乱你的进口和组件的道具/API
*   可能会让其他开发人员感到困惑

## 结论

在本文中，我们看了一个关于 JavaScript 依赖注入的无库指南，并在 React 测试和文档中使用了它。我们用 Storybook 来说明我们对 DI 的使用，最后，思考为什么你应该和不应该在你的代码中使用 DI。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)