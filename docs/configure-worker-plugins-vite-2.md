# 如何在 Vite 2.8 中配置 worker 插件

> 原文：<https://blog.logrocket.com/configure-worker-plugins-vite-2/>

如果您已经使用了框架无关的 Vite 进行前端开发，那么您可能已经熟悉了服务工作者。在本文中，我们将探讨如何使用 Vite 为功能性渐进式 web 应用程序(PWA)正确配置 worker 插件。但是首先，我们有必要熟悉一些术语。我们开始吧！

## Vite 是什么？

如果您以前使用过 Vue 框架，那么您很可能使用 Vue CLI 来开发大型复杂的项目。虽然 Vue CLI 是 Vue 开发人员管理 webpack 内部的一个很好的构建工具，但 Vue 的创始人尤雨溪在 2020 年创造了一个名为 Vite 的快速构建工具。

与 [Vue CLI 不同，Vite 不是 Vue](https://blog.logrocket.com/vite-3-vs-create-react-app-comparison-migration-guide/#what-is-vite) 特有的；您还可以使用它来开发任何 JavaScript 或 TypeScript 应用程序。有了 Vite，当你保存你的代码时，你会很快在浏览器上看到变化，因为 Vite 只在浏览器请求时服务和改变源代码。

## 什么是 PWA？

PWA 代表 progressive web application，我认为这是一个概念，而不是一项实际的技术。 [PWAs 定义了 web 应用](https://blog.logrocket.com/building-pwa-react/),这些应用使用清单、服务人员和其他 web 平台功能，并结合渐进式增强，为用户提供与本地应用相对相似的体验。

使用 PWA 的一些优势包括易于安装、渐进增强、网络独立性、安全性和 SEO 好处。

## 什么是服务人员？

你可以把服务人员想象成一个代理服务器，它位于 web 应用程序、浏览器和网络(如果可用的话)之间。服务人员的目标是创建有效的离线体验，更新服务器上的资产，拦截网络请求，并根据网络是否可用采取行动。

服务人员还可以访问推送通知和执行后台 API 同步，包括添加到屏幕功能。

## 什么是货单？

Web 应用程序清单是 pwa 集合的一部分，它在 JSON 文本文件中提供有关 web 应用程序的信息。PWA 清单包括其名称、版本、描述、图标和任何其他与应用程序相关的必要资源。

## 项目设置

现在我们知道了所有关于渐进式 web 应用程序的技术术语，我们将学习如何用 Vite 配置 worker 插件。

在我们的演示中，我们将使用 Anthony Fu 创建的 [VitePWA 插件](https://vite-pwa-org.netlify.app/)。此插件有助于在 Vite 应用程序中添加服务人员，以处理:

*   缓存资源
*   离线支持
*   新内容可用时推送通知

### 搭建 Vite 项目

如果您还没有想要用来配置 PWA 的基于 Vite 的项目，请随意从任何可用的[模板](https://vitejs.dev/guide/#community-templates)中[创建一个](https://vitejs.dev/guide/#scaffolding-your-first-vite-project)。您还可以通过运行以下命令来创建新的 Vite 项目:

```
npm create [email protected]

```

在创建我的 Vite 应用时，我选择了 **Vue** 框架和 **JavaScript** 变体。app scaffold 完成后，使用下面的命令在项目中安装应用程序依赖项:

```
npm install

```

现在，我们可以通过运行以下命令来加速本地服务器:

```
npm run dev

```

至此，我们已经启动并运行了 Vite 应用程序。

### 配置

让我们继续在应用程序中配置 workers 插件 VitePWA。首先，我们将通过运行以下命令，将该包作为开发依赖项进行安装:

```
npm i vite-plugin-pwa -D

```

## 为我们的 Vite 应用程序注册工人

现在我们已经安装了 workers 插件，我们需要注册服务工作者。`vite-plugin-pwa`插件使用可选的`injectRegister`配置选项自动注册服务人员。

要注册服务人员，根据您在项目搭建时选择的变量，更新`vite.config.js`或`vite.config.ts`文件。从`vite-plugin-pwa`和插件中导入`VitePWA`，使其看起来像下面的代码:

```
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
  plugins: [vue(), VitePWA({ registerType: 'autoUpdate' })],
});

```

现在我们已经注册了`vite-plugin-pwa`，我们的应用程序能够生成它的 web 应用程序清单，并在应用程序构建时在入口点注入它。

您可以通过运行以下命令构建您的 Vite 应用程序:

```
npm run build

```

有了这个`vite-plugin-pwa`插件的最小配置，我们的应用程序现在能够生成 [web 应用程序清单](https://developer.mozilla.org/en-US/docs/Web/Manifest)，在应用程序构建时在入口点注入它，生成服务工人，并在浏览器中注册它。

如果您的`vite-plugin-pwa`版本≤v0.12.2，请确保您自己的配置使用`injectRegister`而不是`registerType`，如下所示:

```
...
import { VitePWA } from 'vite-plugin-pwa'

export default defineConfig({
  plugins: [
    ...,
    VitePWA({
      injectRegister: 'auto'
    })
  ]
})

```

现在，我们将使用下面的命令构建 Vite 应用程序:

```
npm run build

```

在构建应用程序时，会创建一个保存各种文件的`dist`文件夹，包括 web 应用程序清单和服务人员文件的 JavaScript 变体。

至此，我们已经成功地用我们的 Vite 应用程序配置了服务人员。该应用程序现在可以作为一个功能完整的渐进式网络应用程序运行，并可以离线工作。我们确保构建我们的 Vite 应用程序，因为该应用程序只能在生产环境中作为 PWA 使用，而不是在开发环境中。

## 结论

`vite-plugin-pwa`提供了一个超级方便和易于集成的选项，将一个用 Vite 构建的标准 web 应用程序变成一个完全成熟的 PWA。

虽然构建一个渐进式 web 应用程序最初看起来是一项艰巨的任务，但我们有像 VitePWA 插件这样的工具来简化我们的工作，这真是太棒了。只需安装插件，看着它完成所有繁重的工作。

我们没有深入更复杂的开发活动，但我们可以肯定的是，在构建离线应用程序时，可以将 VitePWA 插件作为起点。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。