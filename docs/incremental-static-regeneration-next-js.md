# 使用 Next.js - LogRocket 博客进行增量静态再生

> 原文：<https://blog.logrocket.com/incremental-static-regeneration-next-js/>

***编者按:**本文于 2022 年 4 月 12 日更新，以解决最新版本 Next.js 中对 ISR 的 beta 支持。*

Next.js 是一个多页面 React 框架，捆绑了 web 应用程序生产所需的所有功能，是构建 React 应用程序的领先平台之一。

传统上，两种服务方法很流行:静态站点生成(SSG)和服务器端呈现(SSR)。Next.js v9.5 引入了一种新策略，称为增量静态再生(ISR)，这是两者的混合版本。

在 Next.js 12 中，我们有另一个称为按需 ISR 的特性，我们可以按需重新验证给定的页面，忽略下一次重新验证的剩余时间——这是开发人员长期以来一直要求的，我们将在本文中探讨它的用途。

这篇文章将涵盖这个新策略的相关部分，并解释为什么它是 web 开发的游戏规则改变者。

## 静态站点生成与服务器端呈现

SSG 和 SSR 的区别在于页面的 HTML 是何时生成的。

使用 SSG 时，HTML 是在构建时生成的。SSG 预渲染使其易于缓存和快速交付。术语“静态”来源于 HTML 是静态的这一事实，但它并不一定意味着页面本身是静态的。您仍然可以使用客户端 JavaScript 获取数据并创建动态交互。

[根据 Next.js 团队的](https://nextjs.org/docs/basic-features/pages#two-forms-of-pre-rendering), SSG 是继续进行的推荐方式。只要合适，应该是默认选择。一些 SSG 用例可能包括博客、投资组合网站、电子商务应用程序或文档网站。

另一方面，SSR 为每个请求生成页面的 HTML。它比 SSG 灵活得多，因为你可以改变 HTML 而不用每次都构建应用程序。所以，如果你有持续更新的数据，SSG 可能不是一个很好的选择。

想象你的 Twitter 主页是一个静态页面。听起来很奇怪，不是吗？这种灵活性对性能有很大的影响，并使缓存变得更加困难。

当 SSG 不适合你的时候，SSR 是一个不错的选择。

## ISR 适合做什么？

ISR 是一个新发布的特性，允许在运行时重新生成静态页面。这是 SSG 和 SSR 的混合解决方案。

第一次请求时生成页面。不像在 SSR 中，访问者必须等待数据获取，回退页面会立即提供。

在回退阶段，我们可以展示占位符和一个框架页面，直到所有问题都解决。框架页面是一种常见的模式，几乎随处可见。

一旦数据被解析，最终的页面将被缓存，后续的访问者将立即收到缓存的版本，就像 SSG 一样。我们还可以设置 Next.js 应该何时重新验证页面并更新页面。

即使在重新验证时，访问者首先收到缓存的版本，然后才是更新的版本。

这种缓存策略通常被称为“重新验证时失效”当然，这在以前就有可能实现，但是 ISR 已经将这种能力进一步民主化，使其更容易获得。

将您的 SSR 页面升级为 ISR 可以显著提高您的应用程序的性能，并将您的 Lighthouse 分数提高数十个百分点，同时让您的访问者享受更快的体验。

## 比较 SSG、SSR 和 ISR 示例

Next.js 使得在 SSR 和 SSG(以及 ISR)之间切换变得轻而易举。每个页面都应该导出一个函数，该函数获取呈现页面所需的属性。

页面的 React 组件甚至不知道属性来自哪里。让我们看一个 SSG 和 SSR 的例子，摘自 Next.js 文档。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这里有一个 SSG 的例子:

```
export async function getStaticProps() {
  const res = await fetch('https://...');
  const data = await res.json();

  return { props: { data } };
}

```

每个静态页面都应该导出一个`getStaticProps`函数。该函数在构建时被调用，返回的`props`作为`props`被转发给 React 组件。在这个例子中，我们调用我们的远程 API 端点并返回它的响应。

现在，这里有一个等价的 SSR 例子:

```
export async function getServerSideProps() {
  const res = await fetch('https://...');
  const data = await res.json();

  return { props: { data } };
}

```

唯一的区别是函数名。还有另一个根本的区别，那就是这一次，在每次请求时都调用该函数，而不仅仅是在构建时。

使用 ISR 很像 SSG，只是多了一个`revalidate`属性。这个新属性规定了重新验证页面的频率(以秒为单位):

```
export async function getStaticProps() {
  const res = await fetch('https://...');
  const data = await res.json();

  return { props: { data }, revalidate: 60 };
}

```

事情就是这么简单——没什么特别的！

检测回退模式就像使用 React 钩子一样简单。Next.js 路由器为我们保存了这些信息。我们所要做的就是获取路由器并提取这些信息，如下所示:

```
const { isFallback } = useRouter();

```

`isFallback`是一个布尔值，当回退模式改变时，它会自动更新。你可以把这个片段放到你想要的每个组件中。

## ISR、SSG 和 SSR 的性能方面

推荐 SSG 的主要原因是减少第一次喷漆的时间和阻塞时间(以 lighthouse 衡量)，从而为你的访问者提供更好的用户体验。

一旦你走上了 SSR 的道路，你增加了阻断时间，结果，第一次油漆。这是因为服务器必须获取一些数据，然后在将 HTML 发送回客户端之前动态生成 HTML。

为了提供最佳体验，我们必须仔细选择在服务器端获取哪些数据。我的经验是只获取元标签所需的数据。

例如，当有人在社交媒体上分享链接时，正确的元标签可以影响链接的外观，它们也可以改善你的 SEO。你不希望搜索机器人依赖 JavaScript 来正确地索引你——让他们容易抓取和理解你的网站。

ISR 有点不同，但我的一般经验法则仍然适用。

使用 ISR 时，第一次喷漆和阻塞时间可能不会因回退阶段而受到影响。

甚至在获取服务器端数据之前，Next.js 就立即发送回 HTML。根据页面的设计，服务器端数据提取可能会延迟第一次有意义的绘制。

## ISR、SSG 和 SSR 如何工作的现场演示

在结束之前，这里有一个将我们所学的一切付诸实践的例子。我们的演示将随机选择一个公共 API，以三种方式使用这个令人敬畏的 API:ISR、SSG 和 SSR。

如果你想亲自尝试，可以在 GitHub 上找到代码。

让我们从构建页面组件开始。感谢 Next.js，只要我们提供相同的道具，我们就可以编写一次并将其用于所有方法:

```
import { useRouter } from 'next/router';

export default function Page(props) {
    const { isFallback } = useRouter();

    if (isFallback) {
        return <></>;
    }

    return <div>
        <h1>{props.name}</h1>
        <p>{props.description}</p>
    </div>
}

```

我们的 props 对象包含公共 API 的名称及其描述。我们相应地使用标题和段落元素来显示它们。一个警告是，我们必须检查 ISR 回退模式，如上所述。

在这个模式中，道具可能没有被定义，所以我们必须小心。为了简单起见，我在这里返回一个空元素，但更好的方法是显示加载器或框架页面。

我们的页面已经准备好了，现在我们可以从 API 中获取一些数据。为此，我们将使用[节点获取](https://www.npmjs.com/package/node-fetch)，因为所有调用都是在浏览器外部进行的，所以浏览器的内置`fetch`功能不可用。

```
import fetch from 'node-fetch';

export async function getRandomAPI() {
    const res = await fetch('https://api.publicapis.org/random');
    const json = await res.json();
    return {
        name: json.entries[0].API,
        description: json.entries[0].Description,
    };
}

```

我们调用返回随机公共 API 的 API 端点，并将其组织成一个更好的结构，包括名称和描述，就像我们的 props 对象一样。

基本元素已经准备好了，剩下要做的就是创建实际的 Next.js 页面——每种呈现方法一个页面。

SSR:

```
import Page from '../Page';
import { getRandomAPI } from '../publicApis';

export default Page;

export async function getServerSideProps() {
    const props = await getRandomAPI();
    return { props };
}

```

SSG:

```
import Page from '../Page';
import { getRandomAPI } from '../publicApis';

export default Page;

export async function getStaticProps() {
    const props = await getRandomAPI();
    return { props };
}

```

ISR:

```
import Page from '../Page';
import { getRandomAPI } from '../publicApis';

export default Page;

export async function getStaticProps() {
    const props = await getRandomAPI();
    return { props, revalidate: 30 };
}

```

您可以看到所有这些实现看起来都很相似。

SSR 版本用`getServerSideProps`代替`getStaticProps`。ISR 版本将重新验证周期设置为`30s`。很简单，对吧？

你可以在这里看到它的作用。请注意，SSR 版本将在每个请求中随机选择一个 API。SSG 版本将在构建时随机选择一个。最后，ISR 版本将每 30 秒刷新一次选定的 API。

您还可以使用按需 ISR，这是 Next.js 12 中作为测试版特性引入的。这意味着您可以通过 API 路由触发重新验证，忽略剩余的重新验证时间:

```
export default async function handler(req, res) {
  await res.unstable_revalidate('/path-to-revalidate')
}

```

## 结论

现在，您应该对 SSG、SSR 和 ISR 的细微差别有所了解。如果你优化性能，你应该首先考虑使用 SSG，其次是 ISR，最后是 SSR。

SSG 和 ISR 都有一些局限性，这也是为什么它们不像 SSR 一样适合所有用例的原因。我们还讨论了这三者之间的一些性能方面。

我的经验是服务器端只获取页面的 meta 标签所需的数据。编码快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。