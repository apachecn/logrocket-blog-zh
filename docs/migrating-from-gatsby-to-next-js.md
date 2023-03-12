# 从 Gatsby 迁移到 Next.js 

> 原文：<https://blog.logrocket.com/migrating-from-gatsby-to-next-js/>

## 介绍

像许多开发人员一样，我喜欢将我的博客作为个人游乐场来尝试最新的技术。我的博客是用 [Gatsby](https://www.gatsbyjs.org/) 写的，但最近我把它迁移到了 [Next.js](https://nextjs.org/) 。在这篇文章中，我将谈谈我的经历，哪些进展顺利，哪些进展不顺利。

请记住，虽然 Gatsby 和 Next.js 在功能上有重叠，但这两个工具在功能集、解决的问题和理念上有很大不同。如果你对这两个框架之间的全面比较感兴趣，我建议阅读 [Next.js vs. GatsbyJS:一个开发者的视角](https://blog.logrocket.com/next-js-vs-gatsbyjs-a-developers-perspective/)。

请注意，自从那篇文章发表以来，这两个工具都得到了很多很好的更新，尤其是 Next.js，所以一定要看看 [Gatbsy 的 changelog](https://github.com/gatsbyjs/gatsby/blob/7afdfceb4cbd36a545896998152b9e606dde0e79/packages/gatsby/CHANGELOG.md) 和 [Next.js 博客](https://nextjs.org/blog)。

## 要求

我最初开始使用 Gatsby 的原因是因为我想要编写代码的方式，并且我想要实现的设计需要对构建过程有更多的控制。

博客工具的问题是，虽然它们提供了很多现成的功能，但它们只提供了这么多的灵活性，所以对于没有包括在内的功能，我不得不不断说服自己，我并不真的*需要*它们，但我希望我唯一的约束是我有时间做什么，而不是我的工具支持什么。

除此之外，我想将多个帖子组合成一系列帖子，添加一个模糊搜索栏以便于查找帖子，并使用 [MDX](https://mdxjs.com) 将 markdown 不支持的自定义功能插入到我的帖子中。理想情况下，我想将我的博客作为一个 React 应用程序来创作，但将其构建为一个不依赖于 JavaScript 的静态网站。

最初，我尝试使用 Next.js，但当时它仍然是 v4.1.4，而在撰写本文时是 v9.3，它没有我需要的所有功能，如静态导出，所以我继续搜索。

最终，盖茨比引起了我的注意，随着时间的推移，我设法用它建立了我的博客。然而，学习盖茨比并不容易。

## 起点:盖茨比

我对 GraphQL 本身完全陌生，更不用说 Gatsby 构建网站的编程方法了，这对我来说是一个巨大的学习曲线。通过大量的实践，我对 Gatsby 已经驾轻就熟了，最终，它给了我迫切想要的东西——能够作为一个 React 应用程序来创作我的博客。

虽然 Gatsby 提取数据和构建页面的编程方法对我来说似乎有些间接，但我不能否认它有多么强大，我可以用它做几乎任何我想做的事情，这是我第一次不受工具的约束。我添加的一个功能是将相关的文章组合成系列，这样我就可以像这样显示单独的部分:

![mocking with jest](img/06a92982423f18dd76b7087b7008e580.png)

我的 [gatsby-node.js](https://github.com/silvenon/silvenon.com/blob/c2331c8b85b305501c5d5b1c5087526df7a21e5a/gatsby-node.js) 只有 200 行代码，它有各种各样的功能，像语言、类别、分页、文章系列、草稿，我甚至可以通过编程添加 [Netlify 重定向](https://docs.netlify.com/routing/redirects/)！

有很多盖茨比插件可以执行非常有趣的任务，我[甚至自己也做了一些。这个生态系统允许我轻松地为我的博客建立一个站点地图和一个提要。](https://www.npmjs.com/search?q=author%3Asilvenon%20gatsby-plugin)

但是，我对 Gatsby 的主要痛点是 GraphQL，尤其是结合类型检查。我经常不得不编写大型查询，然后为它们编写类型，如果我想要某种类型的安全，重复相同的数据结构，然后访问所有这些深度嵌套的数据，经常使用析构来使它更容易…我从来没有完全习惯它，它总是感觉冗长。

尽管我设法实现了我想要的每一个特性，但我开始希望有一个更简单的替代方案来帮助我更快地工作。这时候我开始注意到 Next.js，它只是在 9.3 版本更新，包括[静态站点生成](https://nextjs.org/blog/next-9-3#next-gen-static-site-generation-ssg-support)。

## 目的地:Next.js

从开发人员的角度来看，Next.js 要容易得多。然而，迁移所有这些逻辑是一个相当大的挑战，我不得不做出许多改变来适应这个新环境，但是主要的挑战无疑是 MDX。当我使用 Gatsby 的时候，我的博客文章在一个单独的目录中，在那里它们被 Gatsby 的 MDX 插件处理。这样，他们的数据就可以通过 GraphQL 输出到任何地方——front matter、exported values、body 等。我还附加了我自己的自定义字段，如 URL 和关于系列的各种数据。这让我对输出有了很大的控制权。

Next.js 也有一个[官方 MDX 插件](https://www.npmjs.com/package/@next/mdx)，但它所做的只是添加了 Webpack loader，而 [Gatsby 的 MDX 插件](https://github.com/gatsbyjs/gatsby/tree/00e08792ce08be904ff4a7a53c70ce70b7649f0b/packages/gatsby-plugin-mdx)做的要多得多。起初，我认为这可以在 Next.js 中通过动态路由和用于 markdown 的[静态站点生成](https://nextjs.org/blog/next-9-3#next-gen-static-site-generation-ssg-support)来实现，因为它可以在构建时编译成静态 HTML，但是 MDX 是一种不同的格式:虽然它看起来像是 Markdown 的超集，但它实际上是 JSX 的超集，因为它编译成 React 组件，并且它支持模块和定制组件，所以 MDX 文件应该像所有其他组件一样对待。这意味着我必须把我的文章放在`pages`目录中，所以没有办法添加像默认布局或列出文章这样的行为。或者有吗？

MDX 的官方 Next.js 插件并不是唯一的一个，[还有更多的](https://www.npmjs.com/search?q=next+mdx)，有些是专门为博客优化的。最受欢迎的选择是 [next-mdx-enhanced](https://github.com/hashicorp/next-mdx-enhanced) ，它支持默认布局和 frontmatter。如果这就是你所需要的，那么宾果！现在只剩下列表帖子了。

如果你需要的只是提取标题、发布日期和文章链接之类的标题信息，你可以通过遍历`[getStaticProps](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)`中的文件系统，从文件路径计算 URL，并使用 [gray-matter](https://github.com/jonschlinkert/gray-matter) 之类的工具来提取标题信息。

然而，我不想牺牲我和盖茨比在一起的任何特点，所以我必须有所创新。我知道理解并试图复制 Gatsby 的 MDX 插件的所有逻辑将花费我太多的时间，但我想尽可能多地了解它，以获得一些灵感，这使我开始更深入地研究[统一的](https://unifiedjs.com/)生态系统。

随着时间的推移，我意识到我可以通过创建自定义注释和重新键入插件来编辑内容，这也是 Gatsby 的插件所做的事情。我可以通过编辑 AST 来添加布局导出语句、JSX 摘录、关于系列的数据。

进入所有这些特性可能是一个单独的职位，所以我只是给你一个例子；这是我写的一个注释插件的简化版本，用来给我的帖子添加一个默认布局:

```
const u = require('unist-builder')

const defaultLayout = ({ importFrom }) => (tree) => {
  tree.children.unshift(
    u('import', `import DefaultLayout from ${JSON.stringify(importFrom)}`),
  )

  tree.children.push(
    u('export', {
      default: true,
      value: `export default DefaultLayout`,
    }),
  )
}

module.exports = defaultLayout
```

我在`next.config.js`中应用 MDX 插件时把它加到了`remarkPlugins`:

```
const defaultLayout = require('./plugins/remark-mdx-default-layout')
const withMdx = require('@next/mdx')({
  remarkPlugins = [
    [defaultLayout, { importFrom: '../../components/DefaultLayout' })],
  ],
})

module.exports = withMdx({
  pageExtensions: ['js', 'jsx', 'mdx'],
})
```

很简单，不是吗？

列出帖子是一个单独的挑战。起初，似乎可以在`getStaticProps`中完成，但是不可能在那里导入模块，这是访问导出元数据的唯一方式，并且为每个帖子维护一个导入语句列表是不可伸缩的。然而，有一个中间地带。用 [babel-plugin-codegen](https://github.com/kentcdodds/babel-plugin-codegen) 生成那些导入语句。

这个插件的好处一开始有点难以理解，这就是为什么像这样的真实世界用例是最好的理解方式。这是我想要的代码:

```
import * as PostOne from './posts/one'
import * as PostTwo from './posts/two'
import * as PostThree from './posts/three'
// ...

const posts = [
  PostOne,
  PostTwo,
  PostThree,
  // ...
]
```

我使用名称空间导入(用`* as`)，这样我就可以访问从帖子中导出的所有元数据。

我首先将 babel-plugin-codegen 添加到我的 babel 配置中，从而生成了这段代码:

```
module.exports = {
  presets: ['next/babel'],
  plugins: ['codegen'],
}
```

然后，我编写了一个脚本来生成上述代码:

```
const fs = require('fs')
const path = require('path')
const posts = fs.readdirSync(path.join(process.cwd(), 'pages/posts'))

const importStatements = []
const importNames = []
let result = ''

posts.forEach((post, index) => {
  importStatements.push(`import * as Post${index + 1} from './posts/${path.basename(post, '.mdx')}'`)
  importNames.push(`Post${index + 1}`)
})

result += importStatements.join('\n')
result += '\n\n'
result += `const posts = [${importNames.join(', ')}]`

module.exports = result
```

最后，我使用 babel-plugin-codegen 的导入注释导入了脚本:

```
import /* codegen */ posts from '../codegen/posts-list'
```

现在，我可以随心所欲地呈现我的帖子列表，例如:

```
<ul>
  {posts.map(({ frontmatter, path }) => (
    <li key={path}>
      <Link href={path}>{frontmatter.title}</Link>
    </li>
  ))}
</ul>
```

当然，您会希望在出版日期之前订购它们。

我想解决的最后两个问题是站点地图和提要。

为了生成站点地图，我找到了 [nextjs-sitemap-generator](https://www.npmjs.com/package/nextjs-sitemap-generator) ，它做得很好，也很容易使用:

```
const sitemap = require('nextjs-sitemap-generator')
const path = require('path')

await sitemap({
  baseUrl: 'https://silvenon.com',
  ignoreIndexFiles: true,
  pagesDirectory: path.join(process.cwd(), 'out'),
  targetDirectory: path.join(process.cwd(), 'out'),
})
```

生成提要需要更多的工作。幸运的是，我可以重用许多从我的帖子中提取元数据的功能，并使用 [feed](https://www.npmjs.com/package/feed) 包来创建提要。下面是一个添加提要的示例，为了简洁起见，省略了提取元数据的逻辑:

```
const feed = require('feed')

posts.forEach(({ frontmatter, path, excerpt }) => {
  feed.addItem({
    title: frontmatter.title,
    id: `https://silvenon.com${path}`,
    link: `https://silvenon.com${path}`,
    description: excerpt,
    author: [
      {
        name: 'Matija Marohnić',
        email: '[email protected]',
        link: 'https://silvenon',
      },
    ],
    date: new Date(frontmatter.published),
  })
})
```

阅读 readme 以获得创建提要的完整说明。

## 结论

博客是一种特殊类型的网站，在某些方面比较简单，在其他方面，它们可能相当复杂，尤其是我的博客。为工作选择正确的工具通常很困难，因为没有经验很难准确评估工具在实践中是否有效，这就是为什么在实际尝试之前阅读一下选项是好的。

然而，使用 Next.js 进行静态导出仍然是相当新的，特别是考虑到关键特性仅仅在几个月前才登陆，所以我很好奇我是否能完成它。MDX 被证明是唯一似乎无法克服的障碍，因为生态系统还没有准备好，但有许多伟大的事情即将到来 [MDX v2](https://github.com/mdx-js/mdx/issues/1041) 可能会解决我遇到的大多数问题，所以请密切关注！

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。