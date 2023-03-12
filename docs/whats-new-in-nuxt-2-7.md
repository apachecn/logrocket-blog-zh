# Nuxt 2.7 的新特性

> 原文：<https://blog.logrocket.com/whats-new-in-nuxt-2-7/>

NuxtJS 是基于[Vu](https://vuejs.org)[e . js](https://vuejs.org)[Node](https://nodejs.org)[的免费开源 web 应用框架。js](https://nodejs.org) 、 [Express.js](https://expressjs.com) 、 [Webpack](https://webpack.js.org) 和 [Babel.js](https://babeljs.io) 。它是创建 VueJS 应用程序最流行的框架之一。使用 NuxtJS，您可以在通用、静态生成或单页面应用程序之间进行选择。
github 上有超过 20，000 个⭐️，并且有许多专门的社区成员不断提供支持，构建具有 SSRA 和 SPA 特性的高性能、稳定和高效的 VueJS 应用从未如此简单。

自其最初发布以来，其维护者随后也发布了几个解决问题和添加特性的版本。最近，2.7 版本发布了几个更新，解决了各种痛点，并对现有功能进行了改进。在本教程中，我们将回顾一些最近的变化，以及它们如何帮助我们使用 NuxtJS 开发更好的应用程序。

注意:本教程假设您对 VueJS 和 NuxtJS 有基本的了解。要了解更多信息，请访问链接[这里](https://vuejs.org)和[这里](https://nuxtjs.org/)。

## 浏览器中的 SSR(通用渲染)日志

通常，在调试通用应用程序时(**s*erver+****c***lient**)，查看一段代码结果的常用方法是使用`console.log`来完成的。这是有效的(它完成了工作)，但是一个大的难点是它将日志的结果分割到**终端**(当记录特定于服务器的代码时)或者**浏览器**(当记录特定于客户端的代码时)，这可能是非常令人讨厌的，因为您必须从浏览器切换到客户端才能看到结果。

从 NuxtJS v2.7 开始，情况不再如此。所有 SSR 日志现在都报告到您的浏览器控制台。你不再需要切换屏幕，现在你可以在浏览器中看到所有日志的结果。

![](img/dcc11f980cc215513263bb74ddcaac2c.png)

## 存储目录创建的自动检测

存储帮助管理大型应用程序中的状态。NuxtJS 默认实现 Vuex。以前，要激活商店，您需要创建一个商店目录，NuxtJS 将:

*   Import Vuex(导入 Vuex)
*   向根 Vue 实例添加`store`选项。

但是，要实现这一点，您必须重新启动它。在 v2.7 中，你不再需要这样做，因为 NuxtJS 现在可以检测到你何时创建了一个`store/`目录，并且会自动重新加载，所以你不再需要重新启动它。
`serverMiddleware`手表已经被改进，可以重启 NuxtJS 并清理它们的缓存。

![](img/1532ade8cfdf6b35037b63ace0312576.png)

## 扩展 babel.presets 和 babel envName API

预设可以作为一个巴别塔插件阵列或可共享的[选项](https://babeljs.io/docs/en/options)配置。当你不想组装自己的一套插件时，`babel-preset-app`是 Nuxt 使用的默认预置，它主要是围绕`@babel/preset-env`预置的一个包装器。通常定制 babel 配置是通过一个`.babelrc`或配置文件来完成的。在 NuxtJS v2.7 中，默认情况下忽略`babel.rc`文件，而使用`build.extend` API。现在，向 babel 传递选项是使用这个新的 API 完成的，如下所示:

```
// nuxt.config.js
export default {
  build: {
    babel: {
      presets({ isServer }, [preset, options]) {
        // change options directly
        options.targets = ...
        options.corejs = ...

        // or return whole preset list
      }
    }
  }
}
```

还添加了一个用于`configFile`的`envName` API:

```
// nuxt.config.js
export default {
  build: {
    babel: {
      configFile: path.resolve(rootDir, 'babel.config.js')
    }
  }
}

// babel.config.js
module.exports = function (api) {
  const env = api.env()
  api.cache.using(() => env)

  const presets = []
  const plugins = []

  if (env === 'server') {
    // set server presets/plugins
  } else if (env === 'client') {
    // set client presets/plugins
  } else if (env === 'modern') {
    // set modern presets/plugins
  }

  return { presets, plugins }
}
```

## 可配置别名

使用 webpack 时，在 webpack 配置中创建别名并不罕见。在 v2.7 之前，从配置文件配置别名是不可配置的。您必须在别名前面使用`@`符号。这意味着如果您的配置中已经有了类似的配置，就会出现冲突。

```
// packages/webpack/src/config/base.js
alias() {
    const { srcDir, rootDir, dir: { assets: assetsDir, static: staticDir } } = this.buildContext.options

    return {
      '~': path.join(srcDir),
      '~~': path.join(rootDir),
      '@': path.join(srcDir),
      '@@': path.join(rootDir),
      [assetsDir]: path.join(srcDir, assetsDir),
      [staticDir]: path.join(srcDir, staticDir)
   }
}
```

这个问题在 2.7 版中不再存在，因为它现在是可配置的。别名现在将在`options.js`文件的选项对象中定义，并在`base.js`文件中引用，从而消除潜在的冲突。

## 忽略“未找到”警告

使用 TypeScript 时`export ... was not found ...`是一个常见的错误。尽管它不会阻止你的应用程序运行，但看到它弹出来还是很烦人的。
以前，这种错误可以通过给`nuxt.config.ts`添加一个自定义插件来抑制。然而，从 NuxtJS v2.7 开始，该特性已经被添加到 NuxtJS 核心中。

注意:关于所有变更的具体列表，请查看发布[页面](https://github.com/nuxt/nuxt.js/releases)

## 贬值

以下 API 在 NuxtJS v2.7 中已被弃用:

## 未来使用警告

*   过渡属性在 NuxtJS v3 中将不再可用。

**迁移提示:**对于任何效果，您都不需要更改项目中的任何一行代码。一旦升级到 NuxtJS v2.7，一切都会自动迁移。

## 结论

在本教程中，我们探索了 NuxtJS v2.7 中的一些新特性和改进。我们还研究了一些不推荐使用的特性和未来警告。要查看所有变更的具体列表并了解更多关于新版本的信息，请务必查看版本[页面](https://github.com/nuxt/nuxt.js/releases)。请关注我们关于 NuxtJS v2.8 的下一篇文章。祝编码愉快！

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).