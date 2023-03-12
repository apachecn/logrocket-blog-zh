# Next.js - LogRocket 博客中的 getInitialProps 与 getServerSideProps

> 原文：<https://blog.logrocket.com/getinitialprops-vs-getserversideprops-nextjs/>

# Next.js 中的`getInitialProps`与`getServerSideProps`

## 

2022 年 2 月 22 日 2 分钟阅读 678

在 [Next.js v9.3 包含的变化中有`getServerSideProps`](https://blog.logrocket.com/the-latest-features-in-next-js/) 的引入。当涉及到为服务器端呈现的页面定义道具时， [`getServerSideProps`](https://nextjs.org/docs/api-reference/data-fetching/get-server-side-props) 实质上取代了`[getInitialProps](https://nextjs.org/docs/api-reference/data-fetching/get-initial-props)`。

如今，一些开发人员偶尔会遇到关于使用哪种方法的的[困惑。在这篇文章中，我们将探索`getServerSideProps`和`getInitialProps`是如何工作的，并分别回顾一个例子。我们开始吧！](https://github.com/vercel/next.js/discussions/11211)

## `getServerSideProps`和`getStaticProps`的内部工作原理

本质上，如果您需要在发出任何请求之前在构建时呈现页面，那么您应该使用`getStaticProps`。`getStaticProps`会将页面标记为静态呈现，这意味着在下一次构建之前不会重新呈现。虽然这种方法对于速度和 SEO 来说很好，但是对于经常变化的动态数据来说就不太好了。

如果希望在请求时呈现页面，可以在响应请求之前使用`getServerSideProps`在服务器上呈现页面。`getServerSideProps`将在每个请求上标记要呈现的页面。在这种方法中，服务器每次都必须构建页面，这会降低整个过程的速度。然而，这种方法仍然比使用普通的 React 提供了更多的 SEO 好处，后者可以在客户端呈现你的内容。

![Next Getserversideprops Getstaticprops Diagram](img/5029a14b89ce7c658aae836429d8b83a.png)

关于语言和框架的更新，向后兼容性很重要。即使将来的项目不再需要旧的模式和方法，它们仍然被维护以避免破坏遗留代码和偶尔的边缘情况。这就是`getInitialProps`的情况，它的行为几乎与`getServerSideProps`相同。

## `getInitialProps`和`getServerSideProps`有区别吗？

传统的`getInitialProps`和新的`getServerSideProps`之间的主要区别是如何在过渡期间使用该功能，当用户点击一个`Link`来访问你网站的不同部分。

使用`getInitialProps`，转换将在初始页面加载时在服务器端执行，然后在页面转换期间在客户端执行。但是，如果逻辑涉及到数据库之类的东西，而这些东西在客户机上可能是不可访问的，这就可能产生问题。

例如，在下面的代码片段中，直接来自数据库的用户的`fetch`将处理初始页面加载。但是，它可能会在转换时失败，因为`User`模型在客户机上不可用。

作为解决方案，我们可以使用 Next.js API 端点。但是，这种方法比直接访问服务器端的数据库效率低:

```
// Import a User model
import User from "../models/User"
function Page({ User }) {
    return <div>Username: {User.username}</div>
  }

  Page.getInitialProps = async (ctx) => {
    // Get user id
    const User = await User.findOne(ctx.query.id)
    // return props
    return {
        User
    }
  }

  export default Page

```

另一方面，`getServerSideProps`将在初始页面加载时执行服务器端的转换。在页面转换时，`getServerSideProps`将对服务器进行 API 调用，在服务器上再次运行逻辑，并将结果作为 JSON 返回。

通过这种改变，我们解决了使用`getInitialProps`时出现的上下文切换问题。在下面的例子中，您可以直接调用数据库，它将在初始页面加载和转换时正常工作:

```
// Import a User model
import User from "../models/User"
function Page({ User }) {
    return <div>Username: {User.username}</div>
  }
  export async function getServerSideProps(context) {
    // Get user id
    const User = await User.findOne(ctx.query.id)
    return {
      props: {User}, // will be passed to the page component as props
    }
  } 

  export default Page

```

## 你应该用哪一个？

如果您正在创建更新的 Next.js 应用程序，可能没有理由使用像`getInitialProps`这样的遗留方法，因为`getServerSideProps`和`getStaticProps`提供了更好的功能和语义。

在本文中，我们阐明了`getServerSideProps`和`getInitialProps`之间的区别，看一个我们如何用`getServerSideProps`解决`getInitialProps`中的上下文切换的例子。

我希望你喜欢这篇文章，如果你有任何问题，请留下评论。要了解更多关于 Next.js 和其他元框架的信息，请务必查看这篇比较 [Next.js 和 Remix](https://blog.logrocket.com/react-remix-vs-next-js-vs-sveltekit/) 的文章。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。