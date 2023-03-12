# 如何在 Nuxt.js 2.13 中充分利用全静态模式

> 原文：<https://blog.logrocket.com/how-to-take-full-advantage-of-full-static-mode-in-nuxt-2-13/>

[Edmund Ekott Follow](https://blog.logrocket.com/author/edmundekott/) Frontend engineer who specializes in building complex UIs with JavaScript and CSS.

# 如何充分利用 Nuxt.js 2.13 中的全静态模式

## 

2020 年 10 月 6 日 2 分钟阅读 768

js 是一个用于构建快速而强大的 web 应用程序的开源框架。6 月 18 日，Nuxt 2.13 版本引入了对静态应用的改进支持，使得指定如何处理通用模式(`{ mode:'universal'}`)下的应用成为可能。现在，普通的服务器端渲染应用程序和静态应用程序的处理方式有了明显的区别。

让我们放大这个新的全静态模式，并探索在使用 Nuxt.js 构建下一个静态应用程序时利用其功能的一些方法。

## Nuxt.js 的已知问题

在我们深入研究 Nuxt 2.13 之前，让我们简单地了解一下驱动上述变化的一些问题。

*   运行`nuxt generate`会重新构建你的整个应用程序，即使它不是必需的，这会减慢部署时间。
*   您不能在开发中测试 [SPA 回退](https://nuxtjs.org/guides/concepts/static-site-generation#spa-fallback)，因为只有当您运行`nuxt generate`时页面才会被排除
*   为静态应用程序构建模块或插件是乏味的，因为只有当`nuxt generate`运行时`process.static`才是`true`

## 解决方案:`target`选项

Nuxt 2.13 中的`target`选项可以让你明确地指定你希望 Nuxt 如何处理你的应用。

要在您的`nuxt.config.js`文件中设置它:

```
export default {
  mode: 'universal',
  target: 'static' /* or 'server' */
}

```

现在，用`nuxt dev`在开发模式下运行您的项目将会:

*   退回到 404、错误和重定向的客户端呈现
*   将`process.static`设置为`true`

注意:当`mode`设置为`spa`时，全静态模式不起作用。要使用它，你必须把你的`mode`换成`universal`，把`target`换成`static`。`target`的默认值是`server`，所以如果省略了`target`选项，它将始终是`server`。

让我们看看 Nuxt 2.13 帮助解决上面列出的问题的一些其他方式。

### 不再有构建延迟

对于全静态应用程序，如果代码没有变化，Nuxt 不需要重新构建整个项目。当`nuxt generate`运行时(在> = v2.14 中可用)，Nuxt 足够聪明，可以在部署期间使用之前的构建缓存。这一改进[将部署速度提高了大约 3.6 倍](https://nuxtjs.org/blog/nuxt-static-improvements#generate-time-cache-vs-full-webpack-build)。

### 更快的加载时间

现在，所有页面都用`mode:'universal'`和`target:'static'`预渲染为 HTML。带有`asyncData`和`fetch`的 HTTP 请求被生成并存储在 JS 文件中，用于客户端导航，所以当您导航到这些页面时，实际上没有任何 HTTP 请求。

### 在本地运行生产站点

当您运行`nuxt generate`在`/dist`文件夹中生成静态页面时，您可以使用`nuxt start`为您的静态应用程序启动一个生产服务器。这非常适合在部署到静态主机之前进行本地测试。

### 生成配置

现在，您可以使用`nuxt.config.js`中的`generate`属性配置静态应用程序的生成方式。

#### 从缓存中排除文件

您可以选择忽略项目中的特定目录，这样当发生更改时，它们将不会触发任何构建。

```
export default {
  generate: {
    cache: {
      ignore: ['guides'] // changes in the guides folder will not cause a re-build
    }
  }
}

```

默认情况下，缓存会忽略以下文件和目录。

*   `dist/`
*   `.nuxt/`
*   `static/`
*   `.env`、`.npmrc`等点文件
*   `node_modules`
*   `README.md`

#### 爬行者

在以前的版本中(<= v2.12), you had to manually add dynamic links in the [`generate.routes`](https://nuxtjs.org/guides/configuration-glossary/configuration-generate#routes) )选项在你的`nuxt.config.js`文件中。Nuxt 2.13 自带一个内置的爬虫，可以自动检测相对链接并为其生成页面。

如果你想让爬虫跳过某些路线的生成，你可以使用 [`generate.exclude`](https://nuxtjs.org/guides/configuration-glossary/configuration-generate#exclude) 并指定路线的正则表达式或字符串。如果要禁用爬虫，将 [`generate.crawler`](https://nuxtjs.org/guides/configuration-glossary/configuration-generate#crawler) 设置为`false`。

```
// in nuxt.config.js file
export default {
  generate: {
    crawler: false
  }
}

```

如果由于某种原因，爬虫无法生成一些页面，可以使用`generate.routes`选项自己添加。

## 后续步骤

现在我们已经介绍了 Nuxt 2.13 中引入的新特性的静态部分，是时候用它们来改进你的应用了。

下面是你可以在下一个 Nuxt 项目中实现全静态的一些步骤。

1.  升级到 Nuxt v2.14
2.  确保在您的`nuxt.config.js`文件中`target`被设置为`static`
3.  在`package.json`文件的`scripts`选项中切换命令，如下所示:

    ```
    "scripts": { "dev": "nuxt", "generate": "nuxt generate", "start": "nuxt start" }
    ```

*   *   `nuxt dev`将启动开发服务器，并会知道您是否在静态模式下运行
    *   `nuxt generate`将静态页面生成到一个名为`/dist`的文件夹中
    *   `nuxt start`将启动生产服务器为您的静态应用提供服务

前往 [GitHub](https://github.com/nuxt/nuxt.js/releases) 获取更多关于最新 Nuxt 版本中引入的特性和错误修复的信息。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)