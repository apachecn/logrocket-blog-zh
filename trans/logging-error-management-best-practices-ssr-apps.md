# SSR 应用程序中的日志记录和错误管理最佳实践

> 原文：<https://blog.logrocket.com/logging-error-management-best-practices-ssr-apps/>

服务器端渲染(SSR)是一种应用程序在将网站发送到客户端由您的 JavaScript bundle 处理之前，在服务器中预加载网站的能力。水合是指 JS 接管服务器发送的静态 HTML，将其转化为动态 DOM，然后可以对客户端数据变化做出反应的过程。

SSR 为 web 应用程序添加了一个全新的层，尽管它有许多好处，但它可能会给开发人员带来问题。SSR 意味着一些工作是在服务器上完成的，这可能是 bug 的来源；因此，这些错误需要像在任何正常的 web 应用程序中一样得到适当的处理。

因为有多种工具可以帮助开发人员构建 SSR web 应用程序，所以在本文中，我们将重点关注优雅地处理错误的最佳实践。我还将在 [Next.js](https://blog.logrocket.com/tag/nextjs/) 或 [Nuxt.js](https://blog.logrocket.com/tag/nuxtjs/) 中展示一些例子，展示如何做到这一点。

## 处理 SSR 应用程序中错误的最佳实践

一个优雅地处理错误的应用程序将以可控的方式支持已知或未知的错误流，这将有助于用户和开发人员知道出错了，而不会完全阻止应用程序的进程。

SSR 应用程序既有客户端又有服务器端，因此我们需要知道在客户端和服务器端做什么，以便拥有一个完整的端到端错误处理系统。

### 在客户端处理 SSR 错误

正如我们所知，我们的客户端将主要处理前端。以下是你可以在 SSR 应用前端优雅地处理错误的方法列表。

#### `Try…catch`街区

块有助于捕捉异步调用中的错误。这些错误在`catch`块中输出，可以用来通知用户潜在的错误，甚至通过错误服务发送出去:

```
const handleSignUp = async (email) => {
  try {
    await backend.emailVerificationLink(email)
  } catch(error) {
    console.error(error) // you may also 
    setHasErrors(true)
  }
}

```

#### 网络错误拦截器

如果您使用的是像 [Axios](https://blog.logrocket.com/axios-or-fetch-api/) 这样的 HTTP 客户端，您可以使用拦截器轻松处理应用程序中的错误，拦截器将根据从请求或响应中收到的错误执行特定的操作:

```
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });

```

#### 处理客户端错误的更多工具

借助 react 中的 [react-error-boundary](https://blog.logrocket.com/error-handling-react-error-boundary/) 等工具，客户端错误处理变得很容易。它提供了一个包装器，您可以在组件周围使用该包装器来优雅地处理在该上下文中发生的任何错误:

```
function ErrorFallback({error, resetErrorBoundary}) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{error.message}</pre>
    </div>
  )
}

const ui = (
  <ErrorBoundary
    FallbackComponent={ErrorFallback}
    onReset={() => // reset the state of your app so the error doesn't happen again }}
  >
    <ComponentThatMayError />
  </ErrorBoundary>
)

```

Vue.js 还提供了一个`errorCaptured`钩子，每当它的层次结构中的任何组件捕捉到错误时，这个钩子就会像魔咒一样工作。这些错误将被传送到一个全局错误处理程序，您可以在其上附加一个记录器:

```
// in component.vue
errorCaptured(err, instance, info) {
  // handle inside component (/!\ caution component state still editable)
}

// in main.ts
Vue.config.errorHandler = function (err, vm, info) {
  // handle error
  // `info` is a Vue-specific error info, e.g. which lifecycle hook
  // the error was found in. Only available in 2.2.0+
}

```

## LogRocket :处理客户端错误的终极解决方案

虽然使用像 react-error-boundary 这样的选项会显示错误，但它不会让您深入了解问题发生的原因。如果您对自动处理客户端错误、监控和跟踪 Redux 状态、跟踪缓慢的网络请求和组件加载时间感兴趣，请尝试 LogRocket 。【T2![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://www2.logrocket.com/react-performance-monitoring)

LogRocket 就像是网络应用程序的 DVR，记录 React 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://www2.logrocket.com/react-performance-monitoring)。

### 在服务器端处理 SSR 错误

#### 捕获数据提取中的错误

早期返回模式是指尽可能早地返回值，以帮助结束一个函数并获得一个结果。

例如，在接下来的代码中，您可以这样编写您的`getInitialProps`函数:

```
import Error from "next/error"

function Page({ stars, statusCode}) {  
  if (statusCode !== 200) {
    <Error statusCode={statusCode} />
  } 
  return <div>Next stars: {stars}</div>
}

Page.getInitialProps = async (ctx) => {
  try{
    const res = await fetch('https://api.github.com/repos/vercel/next.js')
    const json = await res.json()

    if (res.status >= 400) {
      return { stars: json.stargazers_count, statusCode: res.status }
    }
    return { stars: json.stargazers_count, statusCode: 200 }    
  } catch(error) {
    return {stars: null, statusCode: 503}
  }
}

```

如果结果正确，这将确保您能够正确渲染`Page`。如果在请求过程中出现错误，它将被优雅地处理，并使用 Next 的`Error`组件显示给用户。

在 Nuxt 中，有`fetch`和`asyncData`钩子，它们能够自己处理错误。这些钩子都用来获取数据，然而，[它们并不以相似的方式处理进程](https://nuxtjs.org/docs/features/data-fetching)。

![Diagram showing lifecycle of Nuxt hook](img/236386a6baf32665f4eb4c361ad195cf.png)

Source: [Nuxt docs](https://nuxtjs.org/docs/concepts/nuxt-lifecycle/)

在应用程序的获取点处理错误无疑是 SSR 应用程序中最重要的错误管理任务之一。

#### 错误状态代码

有很多[可识别的 HTTP 错误码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)。通常，您可能希望向用户显示的代码是客户端错误(400-500)。否则，仍然应该捕获并记录每个错误代码。

如果您的 SSR 应用程序使用服务器或 API 中间件，您也需要返回适当的错误，因为它们正好作为应用程序的后端。

#### 重定向至错误页面

万一错误导致页面混乱(加载或未找到)，您总是希望有一个后备页面或组件来帮助向用户描述错误，并隐藏可能无法帮助用户修复错误的任何不必要的技术细节。

Next 已经提供了一个静态的 404 页面，当用户试图访问一个不存在的页面时，就会弹出这个页面。但是，您可能需要一个自定义的 404 页面，以防您需要添加信息，或者满足您的品牌需求。

这是通过创建一个`pages/404.js`文件来完成的，如果需要的话，该文件能够在构建时获取数据。请注意，您可以对表示服务器错误的状态代码 500 执行相同的操作:

```
// pages/404.js
export default function Custom404() {
  return <h1>404 - Page Not Found</h1>
}

// pages/500.js
export default function Custom500() {
  return <h1>500 - Server-side error occurred</h1>
}

```

如果你需要一个不同的错误描述页面，你可以求助于更多的[高级错误页面定制](https://nextjs.org/docs/advanced-features/custom-error-page#more-advanced-error-page-customizing)，它使用 Next 的`error`组件。您可以通过定制`pages/_error.js`来覆盖它

在 Nuxt 中，你被赋予了一个在`layouts/error.vue`文件中定义的特殊布局。这个布局基本上是一个 Vue 组件，能够捕捉错误及其状态代码。这为开发人员提供了极大的灵活性:

```
<!-- layouts/error.vue -->
<template>
  <div>
    <h1 v-if="error.statusCode === 404">Page not found</h1>
    <h1 v-else>An error occurred</h1>
    <NuxtLink to="/">Home page</NuxtLink>
  </div>
</template>

<script>
  export default {
    props: ['error'],
    layout: 'error' // you can set a custom layout for the error page
  }
</script>

```

#### 处理和记录一切

因为您处于 SSR 环境中，所以涉及的不仅仅是用户界面。有时你会在 [Nuxt](https://nuxtjs.org/docs/configuration-glossary/configuration-servermiddleware/) 或 [Next.js](https://nextjs.org/docs/api-routes/api-middlewares) 中处理中间件和`serverMiddlewares`，它们可能会在渲染之前、之中或之后对你的页面执行一些工作。这些应该会返回适当的错误，因为它们是代码的一部分。

在某些 SSR 网站中，服务器或 API 中间件充当成熟的后端。它们可以像 Express 服务器一样处理 API 调用。这些可以很容易地连接到您的日志引擎，[处理 API 请求错误，就像在节点](https://expressjs.com/en/guide/error-handling.html)中一样。

从头到尾处理您的错误将使您能够钻透错误，并找到它们的起源和根本原因。

## 报告和记录提示

一旦您的应用程序到达用户手中，通过日志引擎处理和提取日志将会对您有所帮助。如果你设置正确，这些日志将包含你应用程序上几乎所有互动的信息。

因为您可以实时分析它们，所以您还能够找出错误、异常或任何其他可能来自应用程序使用的问题。

今天，有太多的日志分析和管理工具。例如，您可以在这里看到 Next 是如何与 Sentry 一起使用来捕捉和处理错误的。

大多数现有的日志管理工具都与 LogRocket 无缝集成，在错误报告期间为您提供详细的会话信息。

## 包扎

错误处理是开发中最重要的部分之一。优雅地做到这一点需要开发人员有一个良好的计划，并对错误如何在他们的 web 应用程序中流动有一个坚实的理解。

SSR 应用程序不亚于 web 应用程序，只是多了一个服务器步骤。这些服务器的增加增加了在你的应用程序中拥有一个优雅的端到端错误处理过程所需的工作。然而，现代框架让我们能够按照简单的步骤毫不费力地处理错误。

通常，在客户端，您可以将异步调用封装在`try…catch`块中，以阻止浏览器试图自己处理错误。这样，您将把它转换成可以理解的东西，并让它流向用户和您的日志引擎。在服务器端，您可以使用传统的代码捕捉并返回错误，这样您的客户端就可以很容易地理解哪里出错了。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。