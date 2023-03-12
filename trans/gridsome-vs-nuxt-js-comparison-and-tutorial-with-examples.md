# Gridsome vs. Nuxt.js:示例比较和教程

> 原文：<https://blog.logrocket.com/gridsome-vs-nuxt-js-comparison-and-tutorial-with-examples/>

Vue.js 在过去两年里越来越受欢迎，在撰写本文时，GitHub 上已有近 180，000 位明星。这种越来越多的采用迫使世界各地的开发人员围绕 Vue.js 构建无数的工具和框架，包括 Nuxt.js 和 Gridsome。

在本指南中，我们将比较 Gridsome 和 Nuxt.js，这两个框架都是基于 Vue.js 构建的，可以用来构建快速的 JAMstack 应用程序。我们将从开发人员体验、一般用途和最佳实践、结构和社区采用等方面对每一项进行评估。为了展示 Nuxt.js 和 Gridsome 是如何工作的，我们将使用这两种框架构建一个示例博客。

以下是我们将要介绍的内容:

要了解 Gridsome 与 Nuxt 的比较和教程，您应该具备以下条件:

*   [节点](https://nodejs.org/en/) > = v8.3 和 NPM 安装
*   安装的纱线卷装管理器:`npm install -g yarn` (Gridsome 推荐使用纱线)
*   JavaScript 的基础知识
*   Vue.js 的基础知识
*   [Gridsome CLI](https://gridsome.org/docs/#how-to-install) 已安装:`npm install -g @gridsome/cli`。
*   GraphQL 知识(非强制性)
*   命令行的基本知识

## 什么是 Gridsome？

Gridsome 是一个数据驱动的静态站点生成器，它从本地文件、CMSs 和外部 API 生成 HTML 文件。HTML 文件最初作为静态文件加载到浏览器中，然后合并到功能齐全的 Vue.js 应用程序中。这改善了 SEO，同时提供了 Vue 的所有好处。数据被预取并存储在 GraphQL 数据层中。

下面是对 Gridsome 的目录结构的概述。

## Nuxt.js 是什么？

Nuxt.js 是一个非常强大的 Vue.js 框架，允许你以你选择的任何方式构建现代 web 应用。js 为您提供了构建 web 应用程序的灵活性，既可以是单页的、服务器端呈现的，也可以是静态生成的。

查看 Nuxt.js 文档，了解关于 [Nuxt 目录结构](https://nuxtjs.org/docs/2.x/get-started/directory-structure/)的概述。

## Gridsome 示例:构建博客应用程序

为了展示 Gridsome 的工作原理，并建立一个与 Nuxt.js 相比较的参考框架，让我们探索一下如何使用 Gridsome 构建一个博客。

要创建新的 Gridsome 项目，请运行:

```
gridsome create my-gridsome-blog

```

在代码编辑器中打开新创建的文件夹，并运行`gridsome develop`来启动开发服务器。

在你的浏览器中，打开`[http://localhost:8080](http://localhost:8080)`，你应该会看到你那令人敬畏的——尽管还没有完成——博客。

![Gridsome Blog Example](img/6e71947525d35665894e7a1cfd85aa6e.png)

### 在 Gridsome 中构建 UI

`/src`目录是您处理`.vue`文件的地方。从`/pages`目录开始，目录中的每一个`.vue`文件都成为浏览器中的一个页面，所以`About.vue`的内容会在浏览器中的`/about`上。

在 Gridsome 中有两种创建页面的方法:您可以使用`.vue`文件构建基于[文件的页面](https://gridsome.org/docs/pages/#file-based-pages)，或者您可以使用[页面 API](https://gridsome.org/docs/pages/#programmatic-pages) (稍后将详细介绍)。

将下面的代码片段粘贴到`/pages/index.vue`中的`<Layout></Layout>`组件内，以替换最初的内容。

```
<h1 class="blog-title">
  Welcome my awesome blog
</h1>
<p>I love Vue.js so I write about it.</p>
<div class="articles-list">
  <h2 class="article-list__section-heading">New Articles</h2>
  <!-- articles will be listed here -->
</div>

```

在我们继续之前，您需要为博客全局导入 CSS。使用以下目录结构创建文件:

```
/src/assets/css/main.css

```

将[这个要点](https://gist.github.com/Edmund1645/2c0d527e9f0d66c42b275a2200c0a46d)的内容粘贴到`main.css`文件中，然后导入到`main.js`文件的顶部，该文件位于`/src`目录的根目录下:

```
import '~/assets/css/main.css`

```

`main.js`文件用于导入全局样式和脚本，您还会注意到我们可以访问导出函数中的 Vue 实例，我们可以使用它来安装 Vue.js 插件、注册全局组件(就像默认布局组件)和指令。

### 生成博客内容

Gridsome 还有许多可重用的包，叫做[插件](https://gridsome.org/plugins/)。这些插件可用于在您的应用中实现一些自定义功能。Gridsome 插件页面上目前列出了超过 180 个插件。

我们将使用 [`@gridsome/source-filesystem`](https://gridsome.org/plugins/@gridsome/source-filesystem) 插件来转换我们在 markdown 中编写的内容，并将它们加载到 GraphQL 层中，以便我们可以在组件中进行查询。

该插件还需要`[@gridsome/transformer-remark](https://www.npmjs.com/package/@gridsome/transformer-remark)`来处理降价文件。

要安装文件系统源代码插件:

```
yarn add @gridsome/source-filesystem

```

要安装变压器:

```
yarn add --dev @gridsome/transformer-remark

```

要配置插件，请修改`gridsome.config.js`文件，如下所示:

```
module.exports = {
  siteName: 'Gridsome',
  plugins: [
    {
      use: '@gridsome/source-filesystem',
      options: {
        typeName: 'BlogPost',
        path: './blog/**/*.md',
      },
    },
  ],
  templates: {
    BlogPost: '/blog/:title',
  },
};

```

[`gridsome.config.js`](https://gridsome.org/docs/config/) 文件是我们注册 Gridsome 插件和配置项目的地方:

*   插件选项中的`typeName`是我们给博客文章和模板的 GraphQL 集合起的名字(我们在`/src/templates`中临时创建的`.vue`填充)
*   `path`是插件应该寻找降价文件以生成内容的地方
*   `templates`是定义模板文件路径外观的对象；`:title`，这里是博文的标题，是动态的。

在项目的根目录下创建一个`blog`文件夹，添加一个 markdown 文件`hello.md`，并粘贴以下内容:

```
---
title: Hello World
description: This is the first article for my awesome blog
date: 2021-01-14
published: true
---
# A h1 header
============
Paragraphs are separated by a blank line.
2nd paragraph. *Italic*, **bold**, and `monospace`. Itemized lists
look like:
  * this one
  * that one
  * the other one
Note that --- not considering the asterisk --- the actual text
content starts at 4-columns in.
> Block quotes are
> written like so.

## An h2 header
------------
Here's a numbered list:
 1\. first item
 2\. second item
 3\. third item

```js
  // loop
  for(i= 0; i<=4; i++){
    console.log(i)
  }
```

```

接下来，在`/src/templates`中创建一个`BlogPost.vue`文件。该文件负责从 GraphQL 集合中呈现类型为`BlogPost`的数据，这与我们在`gridsome.config.js`文件中设置的名称相同。`gridsome.config.js`中的`typeName`值必须与`/src/templates`中创建的文件相匹配。

重启服务器并在浏览器中打开`[http://localhost:8080/___explore](http://localhost:8080/___explore)`。你应该看到一个 [GraphQL 游乐场](https://blog.logrocket.com/complete-guide-to-graphql-playground/)。

将下面的查询粘贴到左侧选项卡中，然后单击“播放”按钮。

```
query {
  allBlogPost (filter: { published: {eq: true } } ) {
    edges {
      node {
        path,
        title,
        date(format: "DD MMM YYYY"),
        timeToRead,
        content,
      }
    }
  }
}

```

上面的查询获取与类型`BlogPost`相关联的所有数据，并根据将`published`设置为`true`的数据进行过滤。

### 列出博客文章

现在我们有了内容，是时候展示它了。

用下面的代码片段更新`/src/pages`中的`index.vue`文件。

将下面的查询添加到文件中的`<script>`标记之前:

```
<page-query>
query {
  allBlogPost (filter:{ published: {eq: true } } ) {
    edges{
      node{
        path,
        title,
        date(format: "DD MMM YYYY"),
        timeToRead,
        description,
        content,
      }
    }
  }
}
</page-query>

```

上面的代码片段与我们之前在 GraphQL playground 中运行的查询相同，只是这次数据是作为一个名为`$page`的计算属性提供的，我们可以在`<script>`和`<template>`中访问它。

接下来，将以下代码片段粘贴到同一个文件中的`<h2>`下面:

```
<div class="article-list__item" v-for="({ node: article }, index) in $page.allBlogPost.edges" :key="index">
  <h3>
    <g-link :to="article.path">{{ article.title }}</g-link>
    <!-- g-link is the Gridsome equivalent of router-link for Vue, but with some magic  ✨ -->
  </h3>
  <p>
    Published on <strong>{{ article.date }}</strong>
  </p>
  <p>{{ article.description }}</p>
</div>

```

现在你应该看到你的新文章被列出来了。

### 显示帖子

现在是时候在`/src/templates`中创建一个`.vue`文件了。

在`src/templates`中创建一个名为`BlogPost.vue`的文件，并粘贴以下代码片段的内容:

```
<template>
  <div>
    <Layout>
      <h1>{{ $page.post.title }}</h1>
      <p>
        Published on <strong>{{ $page.post.date }}</strong
        >, <strong>{{ $page.post.timeToRead }} min.</strong> read
      </p>
      <div v-html="$page.post.content"></div>
    </Layout>
  </div>
</template>

<page-query>
query blogPost ($path: String!) {
  post: blogPost (path: $path) {
    id
    title
    content
    date (format: "D MMMM YYYY")
    timeToRead
  }
}
</page-query>

<script>
export default {
  metaInfo() {
    return {
      title: this.$page.post.title,
    };
  },
};
</script>

```

Gridsome 自动将这篇名为`path`的文章的 URL 传递给查询，并用于获取这篇文章的剩余数据，这些数据现在显示在页面的`<template>`标记中。

当你从主页点击你的文章时，你现在应该看到你的新文章的内容。恭喜你！你刚刚用 Gridsome 建了一个博客。

## Nuxt.js 示例:构建博客应用程序

现在，我们已经用 Gridsome 构建了一个博客，让我们用 Nuxt.js 做同样的事情，然后比较开发人员的体验。

要设置一个新的 Nuxt 项目，运行下面的命令并遵循下面截图中的步骤(在设置过程中不要选择任何 Nuxt.js 模块；跳过这一部分):

```
npx create-nuxt-app my-nuxt-blog

```

请遵循以下步骤:

![Creating a Nuxt.js Project](img/4acaf9c52f44befff6bdae379551133c.png)

在代码编辑器中打开新创建的文件夹，并运行以下命令:

```
npm run dev

```

在浏览器中打开`[http://localhost:3000](http://localhost:3000)`以查看您的新博客。

![Nuxt.js Blog Example](img/a4482f681699b1059715712d82671642.png)

### 建筑物

我们将首先使用`/pages`目录。这个目录中的每一个`.vue`文件在浏览器中都会转换成一个页面，所以`index.vue`会变成`/`。在 Nuxt 中，这被称为[文件系统路由](https://nuxtjs.org/docs/2.x/features/file-system-routing)。

删除`<style></style>`标签，并将`/pages/index.vue`中`<template>`的内容修改为:

```
<div class="container">
  <h1 class="blog-title">Welcome my awesome blog</h1>
  <p>I love Vue.js so I write about it.</p>
  <div class="articles-list">
    <h2 class="article-list__section-heading">New Articles</h2>
    <!-- articles will be listed here -->
  </div>
</div>

```

接下来，用这个目录结构`assets/css/main.css`创建一个 CSS 文件，并将[这个](https://gist.github.com/Edmund1645/2c0d527e9f0d66c42b275a2200c0a46d)Gist 的内容粘贴到文件中:

在`nuxt.config.js`文件中全局导入 CSS:

```
export default = {
   css: ['~/assets/css/main.css'],
}

```

现在，您应该可以在浏览器中看到这些变化。

### 生成博客内容

js 有一个很大的可重用包集合，称为模块，可以用来为应用程序添加定制特性。我们将使用`[nuxt-content](https://content.nuxtjs.org/)`模块。

安装模块:

```
npm install @nuxt/content # or using yarn, anyone is fine

```

在`nuxt.config.js`内为您的应用程序注册模块:

```
{
  modules: [
    '@nuxt/content'
  ],
  content: {
    // Options
  }
}

```

在项目的根目录下创建一个新文件夹`/content`。在该文件夹中，创建一个名为`hello.md`的文件，并粘贴下面的代码片段:

```
---
title: Hello World
description: This is the first article for my awesome blog
published: true
---

# A h1 header
============
Paragraphs are separated by a blank line.
2nd paragraph. *Italic*, **bold**, and `monospace`. Itemized lists
look like:
  * this one
  * that one
  * the other one
Note that --- not considering the asterisk --- the actual text
content starts at 4-columns in.
> Block quotes are
> written like so.

## A h2 header
------------
Here's a numbered list:
 1\. first item
 2\. second item
 3\. third item

```js
  // loop
  for(i= 0; i<=4; i++){
    console.log(i)
  }
```

```

开与关(`---`)之间的内容称为[前事](https://assemble.io/docs/YAML-front-matter.html)。你可以创建更多的降价文件。

**清单** **博客** **帖子**

现在我们有了要处理的内容，我们可以开始创建页面来列出我们所有的博客文章，并显示一篇文章的内容。

用下面的代码片段更新`/pages/index.vue`文件的`<script></script>`标签:

```
export default {
  async asyncData({ $content, params, error }) {
    const path = '/' // to fetch all the content
    const articles = await $content(path)
      .where({ published: { $eq: true } })
      .fetch()
      // eslint-disable-next-line node/handle-callback-err
      .catch((err) => {
        error({ statusCode: 404, message: 'Page not found' })
      })
    return {
      articles,
    }
  },
}

```

js 内容模块提供了一个类似 MongoDB 的查询 API 来获取数据，所以我们发出的请求看起来类似于 [MongoDB](https://blog.logrocket.com/setting-up-mongodb-in-deno/) 查询。

上面的代码片段通过指定一个根路径(`/`)并添加一个过滤器来获取我们拥有的所有内容，该过滤器只获取在 YAML frontmatter 中将`published`键设置为`true`的内容。

接下来，更新评论下面的`<template>`来呈现文章:

```
<div
  v-for="(article, index) in articles"
  :key="index"
  class="article-list__item"
>
  <h3>
    <nuxt-link :to="`/article${article.path}`">{{ article.title }}</nuxt-link>
    <!-- nuxt-link is the Nuxt equivalent of router-link for Vue -->
  </h3>
  <p>
    Published on
    <strong>{{
      new Date(article.createdAt).toLocaleDateString()
    }}</strong>
  </p>
  <p>{{ article.description }}</p>
  <hr />
</div>

```

在您的浏览器中查看应用程序。它应该是这样的:

![Rendering Blog Articles in Nuxt.js](img/3e84aba96f38da076810cf397c69b59f.png)

接下来，我们将演示如何渲染一篇文章。

### 显示帖子

在`/pages`目录中，创建一个结构如下的文件:`/article/_path.vue`。将以下片段的内容粘贴到新创建的`_path.vue`文件中:

```
<template>
  <article class="container">
    <h1>Title: {{ article.title }}</h1>
    <hr />
    <br />
    <br />
    <nuxt-content :document="article" />
  </article>
</template>

<script>
export default {
  async asyncData({ $content, params, error }) {
    const { path } = params
    const article = await $content(path)
      .fetch()
      // eslint-disable-next-line node/handle-callback-err
      .catch((err) => {
        error({ statusCode: 404, message: 'Page not found' })
      })
    return {
      article,
    }
  },
}
</script>

```

上面的代码片段做了两件事:通过路径从文章中获取数据，并在模板中呈现它。

在组件的`<script>`标签中，我们使用 [`asyncData`](https://nuxtjs.org/docs/2.x/features/data-fetching/#async-data) 钩子来获取服务器端的数据，因为我们还不能访问`this`。值得一提的是`asyncData`只能在页面中使用。

钩子中的第一个参数是 [Nuxt `context` object](https://nuxtjs.org/docs/2.x/internals-glossary/context) ，我们正在析构页面中需要的属性。我们使用内容模块中的`$content`进行查询，而`params`是客户端的`$route.params`的别名。我们从`params`获取`path`属性，并对文章数据使用该查询。

在`<template>`标签中，我们通过将文章传递给由内容模块添加的全局`<nuxt-content/>`组件来呈现文章，该组件知道如何呈现传递给它的数据——您可能已经注意到，文章的内容不是 HTML 格式的。

瞧啊。你的新的 Nuxt.js 博客已经准备好了。

## Gridsome vs. Nuxt.js:哪个更好？

现在我们已经演示了如何用 Gridsome 和 Nuxt.js 构建博客，让我们比较一下使用每个框架的开发人员的体验。

### 开始一个新项目

Gridsome 和 Nuxt.js 提供了构建新项目的不同方法。使用 Nuxt.js，您可以使用`[create-nuxt-app](https://github.com/nuxt/create-nuxt-app)`工具来启动一个新项目。Nuxt 为您提供了很多选项，让您在安装依赖项之前定制项目。

另一方面，要用 Gridsome 启动一个新项目，您需要使用 [Gridsome CLI](https://gridsome.org/docs/gridsome-cli/) 。使用 Gridsome，您可以选择使用一个[启动模板](https://gridsome.org/docs/starters/)。

### 发展模式

使用 Nuxt.js，您几乎可以构建任何类型的 web 应用程序，包括:

*   在浏览器中呈现的单页应用程序(spa)。通常，JavaScript 被发送到浏览器并执行以生成一个完整的应用程序
*   服务器端渲染(SSR)****应用程序，每次浏览器发出请求时在服务器上生成。正如您在前面使用 Nuxt.js 构建时可能注意到的那样，首先在服务器上获取数据，在将所有内容发送回浏览器之前编译模板。
*   静态生成的(SG)应用程序，在部署期间在服务器上只生成一次。所有数据都是预先获取的，并相应地生成 HTML 文件。当浏览器发出请求时，HTML 文件和相关资源就会被发送出去

使用 Gridsome，您只能构建静态生成的应用程序，这是框架的唯一目的。

### 获取数据

有了 Nuxt.js，你不需要学习一个不同语法的新工具，只需要 JavaScript。使用 Gridsome，您确实需要一些关于 GraphQL 的知识。你可以在构建和引用应用浏览器中的[文档](https://graphql.org/)时了解它。

本教程中使用的 [Gridsome 示例](https://github.com/Edmund1645/gridsome-blog-demo)和 [Nuxt.js 示例](https://github.com/Edmund1645/nuxt-blog-demo)的存储库都可以在 GitHub 上获得。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)