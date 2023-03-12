# Nuxt.js - LogRocket 博客中嵌套页面的故障排除

> 原文：<https://blog.logrocket.com/troubleshooting-nested-pages-nuxt/>

简单地说，嵌套页面是页面中的页面。当我们需要在页面之外添加单独的分支或部分时，我们使用嵌套页面，展示网页的组织并突出它们之间的关系。

比如在 URL `[https://blog.logrocket.com/author/idorenyinudoh/](https://blog.logrocket.com/author/idorenyinudoh/)`中，`idorenyinudoh`是`author`中的嵌套页面，`author`是`blog.logrocket.com`中的嵌套页面。`idorenyinudoh`是`author`版块的作者。

js 以非常直观的方式实现了嵌套页面。Nuxt.js 应用程序中的`pages`目录包含该应用程序的路线。Nuxt.js 会自动读取`pages`目录下的`.vue`、`.js`或`.ts`文件，并设置路由器配置。

`pages`目录中的其他目录也为其各自目录名的页面内的嵌套页面存储文件，遵循以下结构:

```
pages directory
  directory #1
    page #1
    page #2
directory #2
  filenames (nested pages)

```

尽管这种结构很有用，但是在向 Nuxt.js 应用程序添加嵌套页面时，还是会出现一些常见的问题。在本文中，我们将探索在 Nuxt.js 中构建嵌套页面，用简单的解决方案解决其中的一些问题。我们开始吧！

## 创建根级页面

在创建嵌套页面之前，我们首先要创建一个根级页面，它是包含其他嵌套页面的父页面。在前面的例子中，`blog.logrocket.com`是根级页面。

要创建根级页面，在`pages`目录的根目录下创建一个`.vue`文件，并随意命名。在下面的结构中，`author.vue`的内容呈现在`/author`页面上，`index.vue`的内容呈现在`homepage (/)`页面上。注意`index.vue`始终用于主页:

```
pages/
  author.vue
  index.vue

```

## 嵌套页面

要创建嵌套页面，我们首先需要在`pages`目录中创建一个新目录。我们将根据我们想要存储嵌套页面的页面来命名它。例如，如果我们想为`/author/idorenyinudoh`创建一个页面，我们将创建一个包含`idorenyinudoh.vue`文件的`author`目录:

```
pages/
  author/
    idorenyinudoh.vue
  author.vue
  index.vue

```

因此，当我们导航到`/author/idorenyinudoh`时，`idorenyinudoh.vue`的内容就会加载到浏览器上。

对于要在`/author/idorenyinudoh`上显示的`idorenyinudoh.vue`的内容，`author.vue`中必须有一个`<NuxtChild>`元素，并且`/author/idorenyinudoh`页面要链接到`<NuxtLink>`。

## 深度嵌套的页面

深度嵌套的页面实际上只是嵌套页面中的页面。如果`a/b/c`是一个页面，`a`将是根级页面，`b`是嵌套页面，`c`是深层嵌套页面。

在 Nuxt.js 应用程序中实现深度嵌套页面类似于创建嵌套页面。让我们用这个例子的一个修改版本来解释这一点。假设我们想要创建一个名为`/author/idorenyinudoh/this-article`的页面。

将`idorenyinudoh.vue`的内容移动到一个名为`idorenyinudoh`目录下的`index.vue`文件中。在`idorenyinudoh`中，创建一个名为`this-article.vue`的文件。在该文件中包含以下内容:

```
pages
  author
    idorenyinudoh
      this-article.vue
      index.vue
    index.vue
  index.vue

```

既然我们已经熟悉了 Nuxt.js 中的嵌套页面、根级页面和深度嵌套页面，那么让我们回顾一下开发人员遇到的一些常见问题。

## Nuxt.js 嵌套页面没有呈现

有时，尽管我们将正确的文件放在各自的目录中，它们可能不会显示。通常，当 Nuxt.js 不知道在哪里呈现嵌套页面的内容时，就会出现问题。

在我们创建了必要的文件和目录之后，我们需要为嵌套页面的内容包含一个`<NuxtChild>`组件。我们还必须确保嵌套页面与一个`<NuxtLink>`组件相链接。让我们通过一个例子更深入地探讨这个问题。

假设我们使用以下目录结构:

```
pages
  author
    idorenyinudoh.vue
  author.vue
  index.vue

```

我们的`author.vue`文件如下:

```
<template>
  <h1>Authors</h1>
  <p>Our authors mean a lot to us. Without them, we wouldn't be where we are today. If you check them out, we will appreciate that gesture. Thank you very much.</p>
  <NuxtLink to="/author/idorenyinudoh">Check out Idorenyin Udoh</NuxtLink>
  <NuxtChild />
</template>

```

嵌套页面`[https://blog.logrocket.com/author/idorenyinudoh](https://blog.logrocket.com/author/idorenyinudoh)`的内容将在`<NuxtChild>`组件中呈现。`<NuxtLink>`组件触发它的渲染。

然而，使用这种方法，`[https://blog.logrocket.com/author](https://blog.logrocket.com/author)`的内容将总是出现在嵌套页面`[https://blog.logrocket.com/author/idorenyinudoh](https://blog.logrocket.com/author/idorenyinudoh)`中。让我们看看如何解决这个问题。

## 父页面的内容出现在嵌套页面中

父页面的内容有时会无意中出现在嵌套页面中。`<NuxtChild>`组件出现在父页面中，但是父页面上的其他内容也是如此。

为了解决这个问题，我们希望避免在父页面中呈现嵌套页面的内容。然而，如果我们省略了`<NuxtChild>`组件，嵌套页面的内容将在任何地方都不可见。

让我们尝试以下步骤:

1.  在嵌套页面的目录中创建一个`index.vue`文件
2.  将父`.vue`文件的内容移动到新创建的`index.vue`文件中
3.  删除父文件`.vue`

之前我们提到过`index.vue`总是被指定为首页。`author`目录下的`index.vue`文件将用于`author`页面的首页，`[https://blog.logrocket.com/author](https://blog.logrocket.com/author)`。

在我们的示例中实现这种方法，我们将得到以下结果:

```
pages
  author
    index.vue
    idorenyinudoh.vue
  index.vue

```

现在，当我们导航到`/author/idorenyinudoh`时，`idorenyinudoh.vue`将被加载。在`/author`上可以找到的`author/index.vue`的内容将不会出现。我们也不再需要在任何地方包含`<NuxtChild>`组件。

## 深度嵌套的页面没有呈现

当创建一个深度嵌套的页面时，我们可能会遇到与之前相同的问题，即嵌套页面无法呈现。在实现了上面的目录结构之后，如果你没有看到你期望看到的页面，交叉检查必要的`<NuxtLink>`和`<NuxtChild>`组件是否已经就位。

## 结论

在本文中，我们讨论了如何在 Nuxt.js 中创建、配置和排除嵌套页面的故障。如果您遇到任何与 Nuxt.js 应用程序中的页面相关的意外结果，首先，您应该确认必要的`<NuxtLink>`和`<NuxtChild>`组件存在。

我们讨论的概念也适用于 Nuxt.js 中的动态页面，唯一的区别是目录或`.vue`文件名前的下划线。我希望这个教程对你有用。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)