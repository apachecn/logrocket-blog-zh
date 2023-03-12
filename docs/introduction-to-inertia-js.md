# Inertia.js - LogRocket 博客简介

> 原文：<https://blog.logrocket.com/introduction-to-inertia-js/>

构建 web 应用程序可能是一个非常令人生畏的过程。在从众多框架和库中进行选择之前，你必须考虑它是传统的服务器端渲染应用程序(SSR)还是单页面应用程序(SPA)。

虽然服务器端和客户端渲染各有利弊，但[惰性](https://inertiajs.com/)结合了两者的优点。

## 什么是 Inertia.js？

Inertia 是一个库，它结合了服务器端渲染(SSR)和客户端渲染(CSR)的优点，允许开发人员使用服务器端路由和控制器来构建 spa。

根据其[官方文件](https://inertiajs.com/):

> 惯性是构建经典服务器驱动的 web 应用程序的一种新方法。我们称之为现代巨石柱。惯性使你可以创建完全客户端渲染的单页应用程序，而没有现代水疗所带来的复杂性。它通过利用现有的服务器端框架来做到这一点。

有些人可能会问，这是另一个 JavaScript 框架吗？文档这样写道:

> 惰性不是一个框架，也不是现有服务器端或客户端框架的替代品。相反，它是为与他们合作而设计的。把惯性想象成连接两者的胶水。

## Inertia.js 解决的问题

惰性解决了开发人员在构建现代应用程序时面临的许多问题。像这样的问题:

*   水疗中心的复杂性——建造现代水疗中心伴随着一些麻烦。开发人员在有效管理应用程序的状态、计算应用程序的路径、设置导航保护和身份验证方面一直存在问题
*   **构建 API**—使用 Inertia.js，您不需要构建 REST 或 GraphQL API，因为 Inertia 是为使用传统的服务器端框架(如 Laravel、Ruby on Rails 或 Django)而创建的
*   **浏览器问题** — Inertia 具有内置服务，可解决构建 spa 时面临的浏览器问题，例如:
    *   浏览器历史管理和滚动位置—它提供了`remember`、`preserveState`和`preserveScroll`属性来缓存本地组件状态
    *   加载Indiation—b由于惯性请求是通过 AJAX 调用进行的，没有默认的浏览器加载指示器，所以惯性包含了 [NProgress.js](https://ricostacruz.com/nprogress/) ，一个进度条库。只有当请求时间超过 250 米时，装载指示器才会显示
    *   [资产重新加载和版本化](https://inertiajs.com/asset-versioning) —惰性提供了一个选项来跟踪和存储网站上资产的当前版本

## 为什么要用 Inertia.js？

### 不使用 GraphQL 的好处

在使用服务器端 ORM 作为数据源时，Inertia 提供了在数据库上运行特定查询以获取页面所需数据的完全权限。

### 有限的 AJAX 调用

在传统的 spa 中，每次页面访问都要调用 AJAX 来获取数据。在惯性中，一个 AJAX 调用启动应用程序，然后它维护一个持久的 Vue.js 实例，每个后续的页面访问都是通过 XHR 进行的，一个特殊的`X-Inertia`头设置为`true`。这会触发服务器以 JSON 的形式发送惰性响应，而不是进行整页访问。

它还创建了一个自动防故障组件，该组件包装了一个标准的锚链接，它拦截点击事件并防止整个页面重新加载发生。

### 安全性

当构建 API 驱动的应用程序时，我们必须将 CORS 支持添加到我们的应用程序中，以便能够访问其他来源的资源。

有了惯性，你不必担心设置 CORS，因为你的数据是通过你的控制器提供的，并且和你的 JavaScript 组件在同一个域中。

您可以在服务器端设置授权并执行授权检查，绕过令牌作为页面组件的道具，这有助于降低暴露重要信息的风险，因为在客户端处理[授权会使用户面临 XSS 攻击的风险(跨站点脚本)。](https://blog.logrocket.com/jwt-authentication-best-practices/)

### 框架不可知

惰性是服务器端和客户端框架不可知的。您可以在任何支持动态组件的服务器端框架和客户端框架中使用惯性。

惯性适配器是帮助惯性在特定框架中良好工作的服务(包)。官方适配器支持目前仅限于后端的 [Rails](https://github.com/inertiajs/inertia-rails) 和 [Laravel](https://github.com/inertiajs/inertia-laravel) ，前端的 [React](https://github.com/inertiajs/inertia-react) 、 [Vue.js](https://github.com/inertiajs/inertia-vue) 和 [Svelte](https://github.com/inertiajs/inertia-svelte) 。

还有一些其他框架的非官方适配器，比如 [Symfony](https://github.com/rompetomp/inertia-bundle) 、 [Django](https://github.com/jsbeckr/inertia-django) 、 [CakePHP](https://github.com/ishanvyas22/cakephp-inertiajs) 和 [Adonis](https://github.com/MZanggl/inertia-adonis) 。

## 惰性还有未来吗？

网络永远在发展，我们已经看到了从传统的服务器端构建的整体应用到 API 驱动的应用的转变。按照目前的趋势，惰性还有未来吗？

当然，问题的答案取决于用例和偏好。

惯性是为那些想要构建整体应用程序的人构建的——他们通常更喜欢控制器和视图之间的紧密耦合，但也希望使用现代客户端框架来构建他们的应用程序。大多数开发者仍然属于这一类别，但是随着[的兴起和行业对 API 驱动的应用](https://code.tutsplus.com/articles/the-increasing-importance-of-apis-in-web-development--net-22368)的支持，我们可能会看到它的使用减少。

当然，有些时候使用惯性可能不是最好的选择。当你需要多客户端支持、面向客户/营销页面和 SEO 驱动的网站时。对这些使用惯性可能不是一个好主意。但是构建支持仪表盘之类的 web 应用程序是非常有用的。

## 服务器端渲染有惯性可能吗？

惯性目前不支持服务器端渲染，但有工具可以预渲染惯性网站，它们生成并缓存网站特定路线的静态 HTML 版本，然后提供这些内容。

## 开始在项目中使用 Inertia.js

### 先决条件

此安装过程在服务器端使用 Laravel，在客户端使用 Vue.js，本节要求遵循以下内容:

创建新的 Laravel 项目:

```
laravel new inertia-example
```

或使用 composer 创建:

```
composer create-project --prefer-dist laravel/laravel inertia-example
```

`cd`进入项目:

```
$ cd inertia-example
```

使用 composer 安装惯性的服务器端适配器:

```
composer require inertiajs/inertia-laravel
```

将你的`resources/views`文件夹中的`welcome.blade.php`文件重命名为`app.blade.php`。

将您的`app.blade.php`内容替换为:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    <link href="{{ mix('/css/app.css') }}" rel="stylesheet" />
    <script src="{{ mix('/js/app.js') }}" defer></script>
  </head>
  <body>
    @inertia
  </body>
</html>
```

`@inertia`指令是一个助手，它创建一个包含页面信息的`id`为`app`的基`div`，它告诉 Laravel 视图是使用惯性生成的。

接下来，通过在终端中运行以下命令来设置客户端适配器:

```
npm install @inertiajs/inertia @inertiajs/inertia-vue

#or, Using Yarn

yarn add @inertiajs/inertia @inertiajs/inertia-vue
```

打开在`resources/js`中找到的`app.js`文件，用以下内容替换`app.js`文件的内容:

```
import { InertiaApp } from '@inertiajs/inertia-vue'
import Vue from 'vue'

Vue.use(InertiaApp)

const app = document.getElementById('app')

new Vue({
  render: h => h(InertiaApp, {
    props: {
      initialPage: JSON.parse(app.dataset.page),
      resolveComponent: name => require(`./Pages/${name}`).default,
    },
  }),
}).$mount(app)
```

`resolveComponent`回调告诉惯性如何加载页面组件。它接收一个字符串作为页面名称，并返回一个页面实例。

## 动态导入

为了实现代码分割，我们使用了一个用于动态导入的 babel 插件。

首先，通过运行以下命令安装它:

```
npm install @babel/plugin-syntax-dynamic-import
#or, Using Yarn
yarn add install @babel/plugin-syntax-dynamic-import
```

接下来，在项目根目录下创建一个`.babelrc`文件，如下所示:

```
{
  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```

最后，在你的应用初始化中更新`resolveComponent`回调以使用`import`而不是`require`。回调返回包含组件实例的承诺，如下所示:

```
......

new Vue({
  render: h => h(InertiaApp, {
    props: {
      initialPage: JSON.parse(app.dataset.page),
      resolveComponent: name => import(`./Pages/${name}`).then(module => module.default),
    },
  }),
}).$mount(app)
```

## 结论

惯性是建造“混合”温泉的伟大图书馆。在本文中，我们研究了它在不久的将来的可行性，它所具有的优势，以及如何在 Laravel 和 Vue 项目中使用它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在 [Github](https://github.com/inertiajs) 和[上查看惯性本文](https://reinink.ca/articles/introducing-inertia-js)由 [Jonathan Reinink](https://reinink.ca/) 撰写，了解更多信息。官方[文档](https://inertiajs.com/)也写得很好，是一个很好的入门资源。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。