# Next.js - LogRocket 博客中 SSG vs SSR

> 原文：<https://blog.logrocket.com/ssg-vs-ssr-in-next-js/>

## 介绍

Next.js 是最重要和最广泛使用的 React 框架之一。今天，它已经成为构建令人惊叹的 web 应用程序的统一框架。

使用 Next.js 时可以列出很多优点，性能、SEO、丰富的开发者体验、TypeScript 支持、智能捆绑、路由预取只是其中的几个例子。

除了 Next.js 提供的所有令人惊叹的特性之外，还有一个特别强大和令人惊叹的特性:使用不同预渲染技术的能力。

我们可以在同一个 Next.js 应用程序中使用服务器端呈现(SSR)和静态站点生成(SSG)。我们也可以有我们的服务器端渲染应用程序，仍然有一些内部生成的页面。

在本文中，我们将学习更多关于这两种预渲染技术的工作原理以及如何使用它们。我们还将进一步了解它们的具体使用情形。

## 服务器端渲染

现代 JavaScript 框架让我们作为开发人员的生活变得更加轻松。我们可以使用许多不同的渲染技术创建强大、丰富的 web 应用程序。

你可能以前听说过单页应用程序。单页应用程序是在客户端呈现的应用程序，即使数据可能是从服务器获取的。

服务器端渲染(SSR)与此完全相反。SSR 描述了在服务器上预先呈现页面的过程，然后根据每个用户请求生成页面。

当预先呈现的页面到达浏览器时，运行 JavaScript 代码使页面具有交互性。这整个过程使得加载速度更快。对于依赖 SEO 的应用程序来说，这也使得使用服务器端呈现更加容易和可取。

Next.js 是开箱即用的。默认情况下，它会尝试检测您的应用程序正在使用哪种预渲染技术，并获取您的数据。

多年前，在 JavaScript 变得如此成熟和强大之前，开发人员通常基于 HTTP 调用返回 HTML 文件。使用服务器端语言(通常是 PHP)在服务器端处理响应并返回静态 HTML 文件是一种非常常见的技术。

## SSG(静态站点生成)

静态生成的网站对开发者来说并不新鲜。自从网络出现以来，我们就一直在建造它们。构建丰富的 web 体验可能很难，但有了 Next.js，我们可以轻松做到。

Next.js 向我们介绍了一种更好的方法来构建静态生成的具有更高动态性能的网站。

SSG 描述了在构建时渲染网站的过程。输出是一个 HTML 文件、JavaScript 和 CSS 等资源以及一些其他静态文件。

在 Next.js 中使用 SSG 时，页面会在编译时预呈现。这意味着用户不必等待页面在浏览器中加载；页面将被简单地呈现。

对于数据获取，Next.js 提供了三个不同的函数:

*   `getStaticProps`:页面将在构建时预渲染
*   `getServerSideProps`:页面将在运行时预渲染
*   `getStaticPaths`:这个函数生成一个页面列表，这些页面将在构建时被预渲染

使用 SSG 的最大缺点是构建时间会很长。当您只有几个静态生成的页面时，您不会有问题，但是随着应用程序的增长，构建时间会增加。

最坏的情况是当您有数百个静态生成的页面时。构建时间将花费很长时间，如果您在那些页面上有动态内容，您可能会有太多的构建。

## 我应该使用哪一个？

Next.js 让我们很容易为每个页面选择正确的预渲染技术。

记住，我们知道 Next.js 在默认情况下会生成静态站点。它只是开箱即用。但是，它会尝试检测每个页面使用哪种预渲染方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在某些情况下，选择正确的函数来获取数据肯定会有所不同。应该考虑到这一点，因为它会对用户体验造成巨大影响。

假设我们正在创建一个简单的博客网站。内容将完全是静态生成的，对吗？我们将只有几个页面，内容将从我们的服务器上获取，所以在这种情况下使用 SSG 是完全有意义的。

我们可以假设每个博客至少有两个基本页面:

*   这个页面负责获取和显示所有的博客文章
*   这个页面负责获取和显示一篇特定的博客文章

在我们的`posts`页面中，我们可以使用 SSG 获取我们所有的博客文章，使用`getStaticProps`函数:

```
export default function Posts({ posts }) {
  return (
    <div>
      <h1>My latest posts</h1>
      {posts.map((post) => (
        <h2>{post.title}</h2>
      ))}
    </div>
  );
}

export async function getStaticProps() {
  return {
    props: { 
      posts: await fetchPosts(),
    }
  };
}

```

`fetchPosts`函数将在构建时运行。它将获取我们的外部数据，并为我们的页面预渲染内容。

现在，假设我们想在简单的博客网站上添加一个小功能。

假设我们是一个非常有名的人，我们的追随者想从我们的商店购买商品。我们想把我们简单的博客网站变成一个电子商务应用程序，并开始销售衬衫，杯子和贴纸等物品。

电子商务应用程序包含的所有组件都可以使用相同的预渲染技术。我们可以使用`getStaticProps`函数在构建时获取数据。

现在，我们可以用什么来结帐呢？每个电子商务应用程序必须有一个用户将结束他们的购买结账会话。在这种情况下，可以在构建时从我们的数据库中获取数据，但是对于每个用户来说可能是不同的。

我们可以在 SSG 之上使用客户端抓取来呈现我们的页面。我们可以创建简单的组件，而不使用任何函数在构建时获取数据，在组件内部，我们可以使用数据获取库，如 [SWR](https://swr.vercel.app/) 在客户端获取数据:

```
import useSWR from "swr";
export default function CheckoutSession() {
  const { data, error } = useSWR("/api/checkout", fetcher)
  if (error) return <div>Something went wrong...</div>
  if (!data) return <div>Loading...</div>
  return <div>Cart: {data.items.length}</div>
}

```

使用带有`getServerSideProps`的服务器端渲染可以在大多数时候完成这项工作。问题是您可能会失去 SSG 为 Next.js 应用程序带来的一些特性和优势。页面将使用返回的数据在每个请求上预先呈现。另一个缺点是你不能使用一个`[fetch()]([https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch))`方法来调用一个`getServerSideProps`内部的 API。

页面在构建时预先呈现的事实提高了性能，并使页面感觉更快。

CDN 服务器也可以获取 HTML，这也可以提高性能。

SSG 的另一个优点是不需要向数据库发出请求。页面是在构建时预先呈现的，因此即使数据库关闭，您仍然可以呈现页面。

## 结论

Next.js 已经成为 web 社区中最重要的 React 框架之一。它给开发者带来了如此多的功能，帮助他们构建更复杂、更丰富的网站。

使用像 Next.js 这样的框架允许我们在应用程序上使用不同的预渲染技术。我们可以使用静态站点生成来做一些更简单和非动态的事情。我们可以对动态内容和更复杂的页面使用服务器端呈现。有了这些工具，构建富 web 应用程序变得更加容易和有趣。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。