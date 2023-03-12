# SWR v1 在这里:有什么新的？- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-swr-v1/>

## 简介:什么是 SWR？

SWR 是一个钩子库，它为远程数据获取提供了 React 钩子。[正如 SWR 文档所述](https://pankod.github.io/superplate/docs/react/hooks/swr/),“SWR”这个名字来源于`stale-while-revalidate`，这是一种 HTTP 缓存失效策略。

简而言之，`stale-while-revalidate`策略仅仅意味着在重新验证(获取)更新的数据的同时，提供或返回陈旧(缓存)的数据，并在更新的数据返回后将其提供给`UI`。过时数据的重新验证发生在用户关注或网络重新连接的情况下。

其结果是，我们的应用程序组件不断获得新的数据流，使我们的`UI`速度极快，反应非常灵敏，响应速度更快。

我们提供的另一项有助于提升应用性能的出色功能是[重复数据删除](https://swr.vercel.app/docs/advanced/performance#deduplication)。重复数据删除是一种消除冗余数据的技术，通过实施这种技术，SWR 实现了智能抓取。

这意味着，当我们呈现一个多次重用`useSWR`钩子的组件时，如果键是相同的，SWR 将防止多次使用`useSWR`钩子来重新验证数据，从而消除不必要的网络请求，因为只会发出一个请求，过时的数据将被提供给后续的请求。

在这篇文章中，我们将看到 SWR 版本 1 中令人惊叹的新增功能，但是如果你是 SWR 的新手，你可以通过阅读我们以前的 useSWR 文章开始。

## SWR v1 中的新功能

### 1.更小的尺寸和更高的性能

SWR 版本 1 提供了一个明显更小的库和改进的性能特性，比如树抖动。[根据文档](https://swr.vercel.app/blog/swr-v1#smaller-size)，版本 1 要小得多——内核小了 41 %, gzip 压缩时小了 24%。

SWR 计划是不可动摇的，因为性能是 SWR 最重要的特征之一。

版本 1 支持路径导入，如下所示:

```
import useSWR from 'swr'
import useSWRInfinite from 'swr/infinite'

```

对于路径导入，在我们不使用`useSWRInfinite`钩子的情况下，它不会被捆绑在我们的应用程序中。移除死代码使得我们最终的包大小显著变小。

通过使库更加树摇和轻量级，我们的应用程序将有一个更小的包，一个更精简的运行时，和一个更小的`node_modules`目录。

### 2.自定义缓存提供程序

默认情况下，SWR 使用全局缓存。然而，版本 1 通过提供提供者 API 进一步发展了这一点。provider API 由一个缓存提供程序组成，该缓存提供程序是一个类似映射的对象，与以下 TypeScript 定义相匹配:

```
interface Cache<Data> {
  get(key: string): Data | undefined
  set(key: string, value: Data): void
  delete(key: string): void
}

```

缓存提供者被传递给`SWRConfig`的提供者选项，如下所示:

```
import useSWR, { SWRConfig } from 'swr'

function App() {
  return (
    <SWRConfig value={{ provider: () => new Map() }}>
      <Blog/>
    </SWRConfig>
  )
}

```

这样做的结果是我们的定制缓存提供者将被`SWRConfig`中的所有 SWR 钩子使用。换句话说，`Blog`中的所有 SWR 钩子将从那个`map`实例中读取和写入，但是没有嵌套在缓存提供者中的 SWR 钩子退回到默认缓存，这是一个空的`map`。因此，缓存提供程序应该放在组件树的更高位置或渲染之外。

缓存提供者旨在调整 SWR 以支持更多的存储选项，根据我们的用例，它们可以是 JavaScript Map 实例或其他缓存提供者实现。

SWR v1 为扩展当前的缓存提供者提供了实验性的支持。因此，我们可以将缓存提供者与本地存储同步，在测试用例之间重置缓存，并使用扩展的缓存提供者执行[更多操作。](https://swr.vercel.app/docs/advanced/cache#experimental-extend-cache-provider)

此外，在版本 1 中，我们可以在 React 组件中通过使用如下所示的`[useSWRConfig](https://swr.vercel.app/docs/global-configuration#access-to-global-configurations)`钩子来访问当前的缓存提供者:

```
import { useSWRConfig } from 'swr'

const Post() {
  const { cache, ...extraConfig } = useSWRConfig()
  cache.get(key) // Get the current data for the specified key.
  cache.clear()  // Clear all the cache. SWR will revalidate upon re-render.
  // ...
}

```

> **注意**，直接写入缓存可能会导致 SWR 的未定义行为。推荐的做法是[使用 SWR API 比如突变](https://swr.vercel.app/docs/mutation)。

### 3.`useSWRConfig()`

虽然`SWRConfig`上下文为所有 SWR 钩子提供了全局配置选项，但是新的`useSWRConfig()`钩子使我们能够在 React 组件内部访问这些选项。

考虑下面的代码:

```
import { useSWRConfig } from 'swr'

function MyComponent () {
  const { refreshInterval, mutate, ...restConfig } = useSWRConfig()
  // ... do something
}

```

从我们的缓存示例中，我们看到我们也可以使用`useSWRConfig`访问缓存。

### 4.不可变模式

开箱即用，SWR 支持以下事件的自动重新验证:

#### 焦点上的重新验证

当我们重新聚焦一个页面或者在选项卡之间切换时，就会发生这种重新验证。这有助于为我们的应用程序提供新数据。默认情况下，此功能是打开的，但您可以通过`[revalidateOnFocus](https://swr.vercel.app/docs/options)`选项将其禁用。

#### 间隔重新验证

在许多情况下，由于许多设备、多个用户和多个打开的标签，我们的应用程序的数据会很快变得陈旧。该特性使 SWR 能够按时间间隔执行轮询，这提供了一种同步到最新状态并随时间更新 UI 上的数据的方法。

您可以通过用`fetcher`功能中的值设置`[refreshInterval](https://swr.vercel.app/docs/options)`选项来启用此功能:

```
useSWR('/api/todos', fetcher, { refreshInterval: 2000 })

```

上面的代码仅仅意味着每 2000 毫秒轮询一次。

此外，SWR 使我们能够在窗口不可见时——如果启用了`refreshInterval`——通过使用`refreshWhenHidden`选项来执行轮询，并且在浏览器离线时使用`refreshWhenOffline`选项。

#### 重新连接时重新验证

这种重新验证发生在网络恢复时，例如在用户重新在线的情况下。可以使用`[revalidateOnReconnect](https://swr.vercel.app/docs/options)`选项进行配置。

在版本 1 中，SWR 提供了一个助手，`useSWRImmutable`钩子，使状态不可变。这意味着当我们重新验证时，状态不会改变。

`useSWRImmutable`挂钩类似于`useSWR`挂钩，我们可以使用两者使状态不可变，如下所示:

```
import useSWR from 'swr'
import useSWRImmutable from 'swr/immutable'

useSWRImmutable(key, fetcher, options) // makes resourse immutable 

useSWR(key, fetcher, {
  revalidateIfStale: false,
  revalidateOnFocus: false,
  revalidateOnReconnect: false
})

// equivalent to
useSWRImmutable(key, fetcher) // makes resourse immutable 

```

在上面的代码中，这两个钩子使得数据一旦被缓存就不可改变。它会自动为我们完成。

### 5.中间件

[SWR 中间件 API](https://swr.vercel.app/docs/middleware#request-logger) 提供了一种抽象和重用逻辑的好方法。

中间件是一个闭包，它接收 SWR 钩子作为参数并包装`useSWR`钩子，从而使我们能够在调用`useSWR`钩子之前和之后执行逻辑。

考虑下面的代码:

```
function SWRMiddleware (useSWRNext) {
  return (key, fetcher, config) => {
    // beform operations before invoking useSWR()

    // Handle the next middleware, or invokes `useSWR` hook if this is the last middleware.
    const swr = useSWRNext(key, fetcher, config)

    // befrom operations after invoking useSWR()
    return swr
  }
}

```

SWR 中间件非常有用和强大，使用这个特性可以实现很多想法。文档中的一个例子是请求记录器。

记录器中间件是一个[函数装饰器](https://blog.logrocket.com/understanding-javascript-decorators/)，它用记录请求的能力来装饰`fetcher`函数——在本例中是 SWR 键:

```
function logger(useSWRNext) {
  return (key, fetcher, config) => {
    // Add logger to the original fetcher.
    const extendedFetcher = (...args) => {
      console.log('SWR Request:', key)
      return fetcher(...args)
    }

    // Execute the hook with the new fetcher.
    return useSWRNext(key, extendedFetcher, config)
  }
}

```

因此，当我们使用这个中间件时，SWR 密钥被记录到控制台:

```
...
// ... used from inside your component
useSWR(key, fetcher, { use: [logger] })
SWR Request: /api/user1
SWR Request: /api/user2
...

```

为了使用这个特性，我们将一组中间件传递给`SWRConfig`或`useSWR`:

```
<SWRConfig value={{ use: [middlewareA, middleWareB, middlewareC] }}>

// or...

useSWR(key, fetcher, { use: [middlewareA, middleWareB, middlewareC] })

```

当处理多个中间件时，每个中间件嵌套在另一个中间件中，最后一个中间件接收 SWR 钩子。

### 6.后备数据

这一新功能使我们能够为所有具有特定键的 SWR 提供任意预取数据:

```
<SWRConfig value={{
  fallback: {
    '/user': { name: 'Eagles', ... },
    '/posts': ...,
    '/pages': ...,
    ...
  }
}}>
  <App/>
</SWRConfig>

```

当应用程序重新验证时，这些预取的数据可以作为占位符内容显示在页面上。这改善了用户体验，在静态站点生成(SSG)和服务器端渲染(SSR)等场景中非常有用。

此外，为了一致性和避免混淆，旧的`initialData`已经被重命名为`fallbackData`，但是它仍然为指定的 SWR 钩子提供一个单独的回退值。

## 结论

SWR 是一个令人敬畏的，经过深思熟虑的，轻量级的，快速的数据获取库。它是与传输和协议无关的——它支持 REST 和 GraphQL，它支持`suspense`，并且它是类型脚本就绪的。在 SWR 实现的模式让开发人员的生活变得更加轻松。

SWR 版本 1 绝对是每个开发人员的必备品。你可以[在这里](https://swr.vercel.app/blog/swr-v1)了解更多关于版本 1 的信息。你也可以阅读[变更日志](https://github.com/vercel/swr/releases)了解更多信息。

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