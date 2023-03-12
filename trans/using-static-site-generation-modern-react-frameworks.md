# 在现代 React 框架中使用静态站点生成

> 原文：<https://blog.logrocket.com/using-static-site-generation-modern-react-frameworks/>

如果你在 2022 年编写一个 web 应用程序，你可能会使用 React、Vue 和 Svelte 等现代前端技术。您还可能使用 API 来获取呈现页面所需的数据。

使用 API 网络请求很容易成为渲染页面所需的最慢步骤之一，运行缓慢的应用程序可能意味着糟糕的用户体验。拥有表现出色的页面也能显著提高你的搜索引擎优化(SEO)。

如果你拥有 API 并且知道如何让它更快，那太好了！但如果没有，你就无法控制你的应用程序的性能和速度。你甚至可以使用服务器端生成，将数据获取和页面渲染传递给服务器，然而，你的应用程序可能仍然不够快。

这就是静态站点生成发挥作用的地方。有不同的方法来实现 SSG，但首先解释一下它是如何工作的。

## 什么是静态站点生成？

软件开发人员优化其应用程序的方法之一是提前完成工作或使用缓存。

静态站点生成是将页面(预渲染)构建为静态资产并提供给用户的过程，而不是根据请求来完成，尤其是当我们的数据是静态的或不经常更改时。我们还可以创建任意多的构建，并且大部分工作都可以在托管服务器上完成，从而简化了这个过程。

幸运的是，有许多基于 React.js 的静态站点生成工具，其中一些最有趣的是 Gatsby、Next.js 和 Remix。这三家公司都以他们自己的方式服务于高性能的 web 应用程序。

例如，Gatsby 有一个很棒的插件生态系统，可以让你快速上手。Next.js 是灵活的，允许你在每页的基础上选择 SSG。虽然 Remix 目前不支持传统意义上的 SSG，但它通过在边缘服务器上缓存资产来提高页面性能。

在了解了 Gatsby、Next.js 和 Remix 如何通过缓存和 SSG 来实现性能之后，选择最适合的就变得更容易了，我们可以将所使用的技术应用到其他框架和工具中。

## 使用 Gatsby 生成静态站点

Gatsby 是一个基于 React 的框架，用于构建静态生成的 web 应用程序。这是当今开发者在网络上执行 SSG 最流行的方式之一，拥有一个巨大的社区和[插件生态系统](https://www.gatsbyjs.com/plugins/)。

它是这样工作的。开发人员在 Node.js 中编写函数来获取数据，创建页面，并用内容填充它们。将这些页面与 React 组件相匹配(Gatsby 称这些为“模板”)是 Gatsby 如何知道呈现什么以及何时呈现的。

构建可以在开发人员的本地机器上创建，也可以在许多 SaaS 托管解决方案上创建，如 Gatsby Cloud、Netlify 等。一旦构建完成，就可以部署到 CDN 上，为用户提供超快服务。

![Gatsby Architecture](img/b948b2fb97ab48f5580fc008ab6f1bd6.png)

Gatsby 最好的一个方面就是它的开发者生态系统是广泛的。它有数据源插件，可以非常容易地从外部 API(比如 Shopify)和图像优化插件获取数据，这只是其中的几个例子。

盖茨比似乎什么都有插件——超过 2000 个，而且还在增加！它不仅有几个插件可供选择，而且文档内容丰富，有许多[初学者模板可以从](https://www.gatsbyjs.com/starters/)中获得灵感。

有了 Gatsby，在你快速开发的同时，默认情况下你会得到一个非常快速的网站。如果你正在为一个 CMS 构建一个前端或者创建一个商业后端，Gatsby 是一个很好的选择。

下面是一个用于在 Gatsby 中生成页面的代码示例:

```
exports.createPages = async function ({ actions }) {
  const { data } = await graphql(`
    query {
      allMarkdownRemark {
        nodes {
          fields {
            slug
          }
        }
      }
    }
  `)
  data.allMarkdownRemark.forEach(node => {
    const slug = node.fields.slug
    actions.createPage({
      path: slug,
      component: require.resolve(`./src/templates/blog-post.js`),
      context: { slug: slug },
    })
  })
};

```

当然，所有的框架都有其缺点，盖茨比也不例外。这里有一些使用盖茨比的缺点。

### 使用 Gatsby 生成静态站点的缺点

*   服务器端渲染(SSR)对 Gatsby 来说相对较新，所以文档并不全面
*   构建过程很难调试
*   开发人员必须使用 GraphQL 将数据放入 React 组件，这可能会使一些人的学习曲线变得陡峭
*   Gatsby 的许多方面，包括数据获取，都是特定于 Gatsby 框架的，所以将来切换到不同的框架是一个挑战
*   构建可能会变得很慢，这取决于您需要处理的数据量——与大多数基于 SSG 的技术一样，您的数据增加得越多，要创建的页面和要处理的图像的数量也会增加

然而，考虑到权衡，Gatsby 仍然是你的项目的一个很好的选择。

## 使用 Next.js 生成静态站点

Next.js 是一个基于 React 的框架，用于构建动态和静态呈现的 web 应用。它非常受欢迎，有许多开发人员每天都在使用它。

现在有一个相对较新的特性，在 Next.js 中添加了 SSG 支持，其独特之处在于它可以在每页的基础上完成。构建过程类似于传统的 SSG 框架，如 Gatsby，但是您可以选择 SSG 出现的页面。

开发人员还可以选择使用增量静态生成(ISR ),这是一种高级方法，如果页面不存在或者由于开发人员设置的失效而需要重新构建，则可以在运行时构建页面。

如果您只选择使用 SSG 作为您的页面，部署类似于 Gatsby，可以通过各种供应商来完成。它使用 Node.js 获取数据并构建页面。如果您使用[任何 SSG 不支持的特性](https://nextjs.org/docs/advanced-features/static-html-export#unsupported-features)，比如使用服务器在运行时获取数据和构建页面，您将有一个更复杂的部署过程。

![Next.js Architecture](img/6afaf26e2331e4a5803dcd354509c5e9.png)

Next.js 最好的一个方面是它给了你做你想做的事情的自由。如前所述，您可以选择每页使用 SSG、SSR 或新方法 ISR。更好的是，在将数据放入 React 组件时，它没有像 GraphQL 那样规定任何东西。

下面是 Next.js 静态生成页面所需的代码示例:

```
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export async function getStaticProps() {
  const res = await fetch('<https://example.com/posts>')
  const posts = await res.json()

  return {
    props: {
      posts,
    },
  }
}

export default Blog

```

虽然 Next.js 是一个很好的选择，但它并不适合所有人。

### 使用 Next.js 生成静态站点的缺点

*   对于没有多少编写 web 应用程序经验的新开发人员来说，它提供的自由可能会令人望而生畏
*   与 Gatsby 不同，它没有任何现成的插件，所以如果您获取的数据来自 Shopify 或 WordPress 等流行供应商，您可能需要编写样板代码
*   如果您混合搭配各种功能，并且不使用 Next.js 部署解决方案背后的公司 Vercel，那么部署和基础设施可能会很麻烦。

但是，如果您想要 Next.js 提供的自由，并且不介意使用 Vercel 的部署平台，那么对于需要 SSG 的 web 应用程序来说，Next.js 是一个很好的选择。

## 使用现代 React 工具进行混音

Remix 是一个基于 React 的框架，主要用于构建动态呈现的 web 应用程序。对许多人来说，这是一个非常新的框架，因为它是最近才开源的。

Remix 很有趣，因为它不像 Gatsby 或 Next.js 那样提供将页面预编译成静态资产包的选项。没有 SSG 或构建的概念，默认情况下数据获取是按需进行的，HTML 是根据请求在服务器上编译的。

相反，Remix 依赖于[分布式计算](https://remix.run/docs/en/v1/guides/performance)来执行与 SSG 相同的功能。Remix 服务器与边缘计算供应商兼容，如 Cloudflare Workers 和 [Fly.io](http://Fly.io) 。

获取数据和编译 HTML 的服务器部署在这些靠近用户的边缘服务器上，从而比传统的 web 应用程序设置有更快的响应时间。默认情况下，我们的数据仍然是根据请求获取的，这对于您的需求来说可能还是太慢了。Remix 团队建议您为此创建自定义缓存代码。

有一些与边缘服务器兼容的数据库通常位于内存中，如 SQLite、Redis 和 LRU 缓存。当数据发生变化时，您可以手动或自动清除这些缓存中的数据，就像在传统的 SSG 设置中触发新的构建一样。

还建议使用 [HTTP 缓存](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)并在 CDN 上提供静态内容，以服务数据不经常改变的网页。这意味着您将获得与使用 SSG 的框架相似的性能，而无需将其耦合到特定的框架或担心构建。

![Remix.js Architecture](img/898fa7c532d6f17c821457036fd89831.png)

像 Next.js 一样，在提供快速 web 应用程序体验方面，Remix 比 Gatsby 等规定框架给了开发人员更多的自由。Remix 的一个伟大之处在于，它严重依赖边缘计算和浏览器等平台来实现其大部分功能。

这意味着当你学习 Remix 的时候，你学到了更多关于 web 的东西，而不是如何在一个特定的框架中开发。尽管是新的，但 Remix 有令人惊讶的大量部署选项，所以你可以货比三家选择最好的。

下面是一个 React 组件的示例，它在边缘服务器上获取数据并编译 HTML，然后将其显示在页面上:

```
export const loader: LoaderFunction = async ({ request }) => {
  const userId = await requireUserId(request);
  const noteListItems = await getNoteListItems({ userId });
  return json<LoaderData>({ noteListItems });
};

export default function NotesPage() {
  const data = useLoaderData() as LoaderData;

  return data.noteListItems.map((note) => (
    <li key={note.id}>
      <NavLink to={note.id}>📝 {note.title}</NavLink>
    </li>
  ));
}

```

### 使用混音的缺点

*   像 Next.js 一样，Remix 提供的自由可能会让刚入门的开发人员望而生畏，但是文档到目前为止都很棒，所以您可以了解它必须提供什么
*   为您的 web 内容创建自定义缓存机制可能会产生样板代码，这并不好
*   这个框架仍然是新的，只是提供了如何在服务器上执行数据缓存的例子

总的来说，如果你不怕在这个领域接受一个相对新的选择，混音是一个很好的选择。

## 结论

在 Gatsby、Next.js 和 Remix 之间选择构建您的应用程序时，需要考虑很多因素。您应该选择哪一个取决于您的理想设置、您的经验水平、您想要托管的供应商以及您想要编写的代码量。无论您选择什么，这三个选项都提供了向用户快速提供网页的方法。

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