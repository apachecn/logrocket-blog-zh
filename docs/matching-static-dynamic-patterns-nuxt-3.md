# Nuxt 3 中静态和动态模式的匹配

> 原文：<https://blog.logrocket.com/matching-static-dynamic-patterns-nuxt-3/>

自 2021 年 10 月发布以来，Nuxt 3 获得了广泛的好评。这个版本有一些很酷的特性，比如匹配动态路由的括号符号，以及服务器路由和一般工作流程的改进。

这篇文章将讨论 Nuxt 3 中的路由模式。我们将涵盖:

作为本教程的先决条件，你应该具备 Vue.js 框架的[基础知识。](https://blog.logrocket.com/supercharge-vue-js-nuxt-js-apps-vueuse/)

## 在路线中混合和匹配静态和动态文本

在 Nuxt 3 中，我们可以通过使用`:`字符来表示动态段，从而在路径中混合和匹配静态和动态段。例如，如果我们想定义一条路由，其静态路径为`/products`，动态分段为`id`，我们可以这样实现:

```
{
  path: '/products/:id',
  component: ProductPage
}

```

在本例中，`id`段将被视为动态段，这意味着它可以是我们传递给路由的任何值。例如，如果我们导航到`**/products/123**` URL，`id`段将被设置为`123`。

要访问组件中动态段的值，我们可以使用 Nuxt.js 提供的`$route`对象。例如，如果我们想要访问`ProductPage`组件中的`id`段，我们可以像这样使用`$route.params`属性:

```
export default {
  computed: {
    productId() {
      return this.$route.params.id
    }
  }
}

```

然后，我们可以使用组件中的`productId` computed 属性来访问`id`段的值。请记住，`$route`对象是反应性的，这意味着每当路线改变时，它都会自动更新。

总之，为了在 Nuxt.js v3 中混合和匹配路由路径中的静态和动态段，我们可以使用`:`字符来表示动态段。然后，我们可以使用组件中的`$route`对象来访问这些段的值。

我们可以在 Nuxt 2 中使用相同的策略实现这一点，但是对动态文件使用不同的命名模式。例如，在 Nuxt 3 中，动态文件用`[]`符号表示，而在 Nuxt 2 中，我们将使用`_`符号。

## 在子路由上创建部分匹配

在 Nuxt 3 中，可以使用动态参数在子路由中实现部分匹配。

动态参数是以冒号`:`开头的路径段，匹配任何值。例如，如果您想创建一个匹配任何用户 ID 的路由，您可以像这样定义一个路由:

```
/users/:id

```

该路由将匹配任何以`/users/`开头、后跟任何值的 URL。例如，此路由将匹配以下所有 URL:

```
/users/123
/users/abc
/users/user123

```

要访问 route 组件中动态参数的值，可以使用`$route.params`对象。例如，如果 URL 是`/users/123`，您可以像这样访问`id`参数的值:

```
const id = this.$route.params.id

```

您也可以在子路由中使用动态参数。例如，如果您有一个显示用户详细信息的路由，您可以像这样定义子路由:

```
/users/:id/details

```

仅当 URL 是`/users/:id`路由的子级时，才会匹配此路由。它将从父路由继承`id`参数的值。但是，它与下面的 URL 不匹配，因为它们没有`:child`参数的值:

```
/parent
/parent/

```

为了使路由匹配部分 URL，我们可以在路径中使用`_`语法，如下所示:

```
{
    path: '/parent/_*',
    component: './parent/child.vue'
}

```

上述路由将匹配以下 URL:

```
/parent/abc
/parent/xyz
/parent
/parent/

```

现在，`_`参数将匹配 URL 的`/parent/`部分之后的任何值，包括一个空字符串。

## 使用多个动态参数进行路线匹配

在 Nuxt 3 中，可以使用多个动态参数来实现复杂的路由匹配。

例如，如果您想要创建一个与[产品详细信息页面](https://blog.logrocket.com/using-shopify-hydrogen-build-modern-ecommerce-storefronts/)相匹配的路线，您可以像这样定义一个路线:

```
/products/:category/:productId

```

上面的路由将匹配任何以`/products/`开头，后跟一个`category`参数值，再后跟一个`productId`参数值的 URL。例如，此路由将匹配以下所有 URL:

```
/products/books/123
/products/clothing/abc
/products/electronics/xyz

```

要访问 route 组件中的动态参数值，可以使用`$route.params`对象。例如，如果 URL 是`/products/books/123`，您可以像这样访问`category`和`productId`参数的值:

```
const category = this.$route.params.category
const productId = this.$route.params.productId

```

您也可以在子路由中使用动态参数。例如，如果您有一个显示产品评论的路由，您可以像这样定义一个子路由:

```
/products/:category/:productId/reviews

```

只有当 URL 是`/products/:category/:productId`路由的子级时，上述路由才会匹配。此外，它将从父路由继承`category`和`productId`参数的值。

您也可以在`nuxt.config.js`文件中定义多条动态路线。例如，如果您想要在`/profile/:id` URL 上提供一个用户配置文件列表，我们可以像这样定义一个动态路由:

```
// nuxt.config.js

export default {
  // ... other Nuxt.js configuration options

  // define the dynamic routes
  generate: {
    routes: [
      '/profile/:id',
      // add additional dynamic routes here as needed
    ]
  }
}

```

然后，在`pages/users/[id].vue`页面组件中，您可以使用 Nuxt.js 提供的`$route`对象来访问`id`参数。例如:

```
// pages/products/[id].vue

export default {
  data() {
    return {
      product: null
    }
  },
  async asyncData({ $route }) {
    // fetch the user data using the `id` parameter from the URL
    const product = await fetchProducts($route.params.id)

    return {
      product
    }
  }
}

```

当呈现页面时，Nuxt.js 将在适当的 URL(例如，`/users/123`)自动生成页面的静态版本，用户数据已经包含在页面中。这允许我们使用动态路由，同时受益于静态站点生成的[性能优势。](https://blog.logrocket.com/ssg-vs-ssr-in-next-js/)

## 结论

最后，让我们快速总结一下本文所涉及的内容。

Nuxt 是一个类似 Next.js 的[框架，用于构建服务器渲染的 Vue.js 应用。它提供了一种强大的方法，使用页面和布局的概念为您的应用程序创建路线。在 Nuxt 3 中，可以使用静态模式和动态参数来定义匹配特定 URL 模式的路由。](https://blog.logrocket.com/next-js-vs-nuxt-js/)

静态模式是与特定 URL 完全匹配的简单字符串模式。例如，您可以定义一个类似于`/about`的路由，它与`/about` URL 完全匹配。

另一方面，动态参数是以冒号`:`开始并匹配任何值的路径段。您可以使用动态参数来创建匹配任何可能值的路径，从而更容易创建可处理动态数据的路径。

在这篇文章中，我们回顾了 Nuxt 3 中一些流行的路由改进和一些实际的实现例子，期待下一个版本。让我知道你对 Nuxt 团队有什么期望，或者通过阅读更多关于使用 Nuxt 开发[的内容来磨练你的技能。](https://blog.logrocket.com/tag/nuxtjs/)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)