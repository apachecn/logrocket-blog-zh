# Sapper 和 Svelte:快速教程

> 原文：<https://blog.logrocket.com/sapper-svelte-tutorial-2021/>

***编者按**:这个苗条和工蜂的教程最后一次更新是在 2021 年 7 月 2 日，包括工蜂的继任者[苗条装备](https://kit.svelte.dev/)的信息。关于 SvelteKit 的更多信息，请查看"[探索 SvelteKit，最新的基于 SvelteKit 的框架](https://blog.logrocket.com/exploring-sveltekit-the-newest-svelte-based-framework/)"*

不久前，[我们探索了 Svelte.js](http://We'll%20be%20taking%20a%20high-level%20look%20at%20Sapper,%20what%20it%20is,%20how%20it%20helps%20you%20build%20full-fledged,%20lightweight%20apps,%20and%20why%20you%20should%20consider%20using%20it.%20Note:%20This%20post%20is%20a%20followup%20to%20the%20intro%20to%20Svelte%20article%20published%20a%20while%20back.%20I%20strongly%20urge%20you%20to%20read%20it%20here:%20https://blog.logrocket.com/truly-reactive-programming-with-svelte-3-0-321b49b75969/.) ,看到了它如何帮助你编写真正的反应式应用程序，同时比许多其他前端框架交付更少的代码。虽然你完全可以单独用[苗条的](https://blog.logrocket.com/how-to-build-a-simple-svelte-js-app/)构建一个更复杂的应用程序，但它可能会很快变得一团糟。进入工兵！

在本教程中，我们将对 Sapper 进行一个高层次的观察，展示它如何帮助您构建成熟的轻量级应用程序，并分解服务器渲染的应用程序。以下是我们将要介绍的内容:

## 萨帕尔死了吗？

在 2020 年 10 月的[svelet 峰会](https://sveltesummit.com/)上，svelet 和 Sapper 的创造者 Rich Harris 在他题为“未来主义 Web 开发”的演讲中宣布，他和他的团队正在开发[svelet kit](https://kit.svelte.dev/)来取代 Sapper。

[https://www.youtube.com/embed/qSfdtmcZ4d0](https://www.youtube.com/embed/qSfdtmcZ4d0)

视频

在他的后续博客文章中，哈里斯解释了这一转变背后的原因。首先，SvelteKit 旨在简化入门，减少维护和支持负担，并提供可预测的项目结构。

在更高的层面上，SvelteKit 是为了应对“非捆绑开发”工作流的兴起而构建的，在这种工作流中，开发服务器按需提供模块，而不是捆绑应用程序。这使得启动几乎是瞬间的，不管你的应用程序有多大。

最后，SvelteKit 将支持所有主要的无服务器提供商，并包括一个适配器 API，使其与非官方平台兼容。

## 为什么 2021 年不应该用 Sapper

虽然在撰写本文时它仍处于公开测试阶段，但 SvelteKit 现在已经有了[完整的文档](https://kit.svelte.dev/docs)，看起来[正在向版本 1.0](https://github.com/sveltejs/kit/issues?q=is%3Aopen+is%3Aissue+milestone%3A1.0) 冲刺。

另一方面，工兵将不再被维持。如[正式文件](https://sapper.svelte.dev/docs/#Introduction)所述:

> Sapper 的 succesor，SvelteKit，目前可以使用。所有向前发展的努力将集中在 SvelteKit 上。

对于苗条的开发者来说，这意味着，在大多数情况下，你应该[考虑从 Sapper 迁移到 svelet kit](https://dev.to/danawoodman/when-to-use-svelte-vs-sveltetkit-vs-sapper-4o6a)。Sapper 文档包括一个有用的[迁移指南](https://sapper.svelte.dev/migrating/)，供希望做出改变的苗条开发者使用。

也就是说，如果你对使用 beta 软件的潜在障碍感到不安，或者如果你想直接访问 Express/Polka，你可能仍然想使用 Sapper。如果你仍然想使用 Sapper，尽管它缺乏支持，请继续阅读了解如何使用。

## 什么是 Sapper？

Sapper 是 Svelte 的配套组件框架，可以帮助你快速高效地构建更大更复杂的应用。

在当今时代，构建 web 应用程序是一项相当复杂的工作，需要进行代码拆分、数据管理、性能优化等。这也是为什么现在有无数的前端工具，但是它们都有自己的复杂程度和学习曲线。

建一个 app 应该没那么难吧？还能比现在更简单吗？有没有一种方法可以在保持理智的情况下勾选所有的选项？当然有——那是一个反问句！

先说名字:工兵。我将直接引用官方文件来解释为什么选择这个名字:

> 在战争中，那些在战斗条件下架桥、修路、扫雷和爆破的士兵被称为工兵。

对于网络开发人员来说，风险通常低于战斗工程师。但我们面临着自己的不利环境:功能不足的设备、糟糕的网络连接以及前端工程固有的复杂性。Sapper 是苗条应用制造商的简称，是你勇敢而尽职的盟友。

嗯，很有道理🤓。

Sapper(或者说，Svelte)的设计是轻量级的、高性能的、易于推理的，同时还提供了足够的功能来将你的想法转化为出色的网络应用。

基本上，以下是 Sapper 在以苗条的方式构建 web 应用程序时帮助你处理的事情:

*   按指定路线发送
*   服务器端渲染
*   自动代码分割
*   离线支持(使用服务人员)
*   高层项目结构管理

我相信你会同意，自己管理这些会很快变成一件杂务，让你从实际的业务逻辑中分心。

但是空谈是廉价的——代码是有说服力的！让我们通过一个使用 Svelte 和 Sapper 的小型服务器渲染应用程序。

## 工蜂示例

我不会告诉你 Sapper 如何帮助你轻松构建应用程序，我们将探索当你搭建一个新项目时得到的演示应用程序，并看看它是如何在幕后工作的。

首先，运行以下命令来引导一个新项目:

```
$ npx degit "sveltejs/sapper-template#rollup" my-app
$ cd my-app
$ npm install
$ npm run dev
```

这样做将得到一个基本的项目，但是对于本文的目的来说已经足够了。我们应该能够探索 Sapper 如何用这个简单的项目处理路由和服务器端渲染，而不必太深入。

让我们开始吧！

## 项目结构

Sapper 是一个固执己见的框架，意味着某些文件和文件夹是必需的，项目目录必须以某种方式构建。让我们看看一个典型的 Sapper 项目是什么样子的，所有的东西都放在哪里。

### 入口点

每个 Sapper 项目都有三个入口点和一个`src/template.html`文件:

1.  `src/client.js`
2.  `src/server.js`
3.  `src/service-worker.js`(这个是可选的)

#### `client.js`

```
import * as sapper from '@sapper/app';

sapper.start({
  target: document.querySelector('#sapper')
});
```

这是客户端渲染应用程序的入口点。这是一个相当简单的文件，您在这里需要做的就是从`@sapper/app`导入主 Sapper 模块，并从中调用`start`方法。它接受一个对象作为参数，唯一需要的键是`target`。

目标指定应用程序将被挂载到哪个 DOM 节点上。如果你来自 React.js 背景，就把这个当做`ReactDOM.render`。

#### `server.js`

我们需要一个服务器来为用户提供我们的应用程序，不是吗？因为这是一个 Node.js 环境，所以有大量选项可供选择。你可以使用 Express.js 服务器、Koa.js 服务器、Polka 服务器等等。，但是有一些规则要遵循:

1.  服务器必须提供`/static`文件夹的内容。Sapper 不在乎你怎么做。把那个文件夹端上来！
2.  你的服务器框架必须支持中间件(我个人不知道有哪个不支持)，而且必须使用从`@sapper/server`导入的`sapper.middleware()`。
3.  您的服务器必须监听`process.env.PORT`。

只有三条规则——如果你问我，我觉得还不错。看一看为我们生成的`src/server.js`文件，看看它实际上是什么样子。

#### `service-worker.js`

如果你需要重温一下什么是服务人员，[这篇文章应该很适合](https://developers.google.com/web/fundamentals/primers/service-workers/)。现在，你用 Sapper 构建一个全功能的 web app 不需要`service-worker.js`文件；它只允许您访问离线支持、推送通知、后台同步等功能。

由于服务人员习惯于应用程序，因此没有关于如何编写应用程序的硬性规定。您可以选择完全忽略它，或者利用它来提供更完整的用户体验。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

#### `template.html`

这是应用程序的主要入口点，所有的组件、样式引用和脚本都是在这里根据需要注入的。除了极少数情况下需要通过从 HTML 链接到 CDN 来添加模块，这几乎是一劳永逸的。

#### `routes`

每个工蜂 app 的 MVP。这是你大部分逻辑和内容的所在。我们将在下一节中进行更深入的探讨。

## 按指定路线发送

如果你运行了**动手体验**部分的所有命令，导航到`[http://localhost:3000](http://localhost:3000)`应该会带你到一个简单的网络应用，有主页、关于页面和博客页面。到目前为止，很简单。

现在让我们试着理解 Sapper 是如何将 URL 与相应的文件相协调的。在 Sapper 中，有两种类型的路由:页面路由和服务器路由。

让我们进一步分解一下。

### 寻呼路线

当你导航到一个页面——比如说，`/about` — Sapper 会呈现一个位于`src/routes`文件夹中的`about.svelte`文件。这意味着该文件夹中的任何`.svelte`文件都会自动“映射”到同名的路径。因此，如果在`src/routes`文件夹中有一个名为`jumping.svelte`的文件，导航到`/jumping`将导致该文件被渲染。

简而言之，页面路由就是`src/routes`文件夹下的`.svelte`文件。这种方法的一个非常好的副作用是你的路线是可预测的，并且很容易推理。你想要一条新路线？在`src/routes`中创建一个新的`.svelte`文件，你就成功了！

如果您想要一个类似于下面这样的嵌套路由怎么办:`/projects/sapper/awesome`？您需要做的就是为每个子路由创建一个文件夹。因此，对于上面的例子，你将有一个这样的文件夹结构:`src/routes/projects/sapper`，然后你可以在`/sapper`文件夹中放置一个`awesome.svelte`文件。

记住这一点，让我们看看我们的引导应用程序，并导航到“关于”页面。你认为这个页面的内容是从哪里呈现的？好吧，我们来看看`src/routes`。果然，我们在那里找到了一个`about.svelte`文件——简单且可预测！

请注意，`index.svelte`文件是一个保留文件，当您导航到一个子路由时会呈现出来。例如，在我们的例子中，我们有一个`/blogs`路由，在那里我们可以访问它下面的其他子路由，例如`/blogs/why-the-name`。

但是请注意，当`/blogs`本身是一个文件夹时，在浏览器中导航到`/blogs`会呈现一个文件。如何为这样的路线选择要渲染的文件？

要么我们在`/blogs`文件夹外定义一个`blog.svelte`文件，要么我们需要一个`index.svelte`文件放在`/blogs`文件夹下，但不能同时放在两个文件夹下。当您直接访问`/blogs`时，该`index.svelte`文件被渲染。

有动态 slugs 的 URL 怎么办？在我们的例子中，手动创建每一篇博客文章并将它们存储为`.svelte`文件是不可行的。我们需要的是一个模板，用于渲染所有的博客帖子，而不管蛞蝓。

再看一下我们的项目。在`src/routes/blogs`下面，有一个`[slug].svelte`文件。你认为那是什么？是的——它是渲染所有博客文章的模板，而不考虑 slug。这意味着任何跟在`/blogs`后面的 slug 都会被这个文件自动处理，我们可以在 page mount 上获取页面内容，然后将它呈现给浏览器。

这是否意味着`/routes`下的任何文件或文件夹都会自动映射到一个 URL？是的，但是这条规则有一个例外。如果您在文件或文件夹前添加下划线，Sapper 不会将其转换为 URL。这使得您可以轻松地在 routes 文件夹中放置助手文件。

假设我们需要一个助手文件夹来存放我们所有的助手函数。我们可以有一个类似于`/routes/_helpers`的文件夹，然后任何放在`/_helpers`下的文件都不会被当作一个路径。很漂亮，对吧？

### 服务器路由

在上一节中，我们看到有一个`[slug].svelte`文件可以帮助我们匹配像这样的任何 URL:`/blogs/<any_url>`。但是它实际上是如何让页面的内容呈现出来的呢？

您可以从静态文件中获取内容，或者通过 API 调用来检索数据。无论哪种方式，您都需要向路由(或者端点，如果您只考虑 API)发出请求来检索数据。这就是服务器路由的用武之地。

来自官方文档:“服务器路由是写在`.js`文件中的模块，这些模块导出对应于 HTTP 方法的函数。”

这只是意味着服务器路由是端点，您可以调用它们来执行特定的操作，比如保存数据、获取数据、删除数据等。它基本上是你的应用程序的后端，所以你可以在一个项目中拥有你需要的一切(当然，如果你愿意，你可以把它分开)。

现在回到我们的自举项目。如何获取`[slug].svelte`中每篇博文的内容？好吧，打开文件，你看到的第一段代码是这样的:

```
<script context="module">
  export async function preload({ params, query }) {
    // the `slug` parameter is available because
    // this file is called [slug].html
    const res = await this.fetch(`blog/${params.slug}.json`);
    const data = await res.json();
    if (res.status === 200) {
      return { post: data };
    } else {
      this.error(res.status, data.message);
    }
  }
</script>
```

我们所看到的只是一个简单的 JS 函数，它发出一个 GET 请求并从该请求中返回数据。它接受一个对象作为参数，然后在第 2 行将其析构以获得两个变量:`params`和`query`。

`params`和`query`包含什么？为什么不在函数的开头加一个`console.log()`，然后在浏览器中打开一篇博文呢？这样做后，您会在控制台中记录如下内容:

```
{slug: "why-the-name"}slug: "why-the-name"__proto__: Object {}
```

嗯。因此，如果我们打开第 5 行的“why-the-name”帖子，我们的 GET 请求将是对`blog/why-the-name.json`的请求，然后在第 6 行将其转换为 JSON 对象。

在第 7 行，我们检查我们的请求是否成功，如果成功，就在第 8 行返回它，否则调用一个名为`this.error`的[特殊方法](https://sapper.svelte.dev/docs#this_error)，并给出响应状态和错误消息。

很简单。但是实际的服务器路由在哪里，它看起来像什么？查看`src/routes/blogs`内部，您应该会看到一个`[slug].json.js`文件——这是我们的服务器路由。注意到它的名字和`[slug].svelte`一样吗？这就是 Sapper 如何将服务器路由映射到页面路由。因此，如果您在名为`example.svelte`的文件中调用`this.fetch`，Sapper 将寻找一个`example.json.js`文件来处理请求。

现在我们来解码[slug].json.js，好吗？

```
import posts from './_posts.js';

const lookup = new Map();
posts.forEach(post => {
  lookup.set(post.slug, JSON.stringify(post));
});

export function get(req, res, next) {
  // the `slug` parameter is available because
  // this file is called [slug].json.js
  const { slug } = req.params;
  if (lookup.has(slug)) {
    res.writeHead(200, {
      'Content-Type': 'application/json'
    });
    res.end(lookup.get(slug));
  } else {
    res.writeHead(404, {
      'Content-Type': 'application/json'
    });
    res.end(JSON.stringify({
      message: `Not found`
    }));
  }
}
```

我们真正感兴趣的是从第 8 行开始。第 3–6 行只是为要使用的路线准备数据。还记得我们如何在页面路由中发出 GET 请求:`[slug].svelte`？这是处理该请求的服务器路由。

如果您熟悉 Express.js APIs，那么您应该对此很熟悉。这是因为这只是一个简单的端点控制器。它所做的就是获取从`Request`对象传递给它的 slug，在我们的数据存储中搜索它(在本例中是`lookup`，并在`Response`对象中返回它。

如果我们使用数据库，第 12 行可能看起来像`Posts.find({ where: { slug } })` (Sequelize，有人吗？).你知道了。

服务器路由是包含端点的文件，我们可以从页面路由中调用这些端点。因此，让我们快速总结一下目前为止我们所知道的:

*   页面路由是`src/routes`文件夹下的`.svelte`文件，用于向浏览器呈现内容。
*   服务器路由是包含 API 端点的`.js`文件，通过名称映射到特定的页面路由。
*   页面路由可以调用服务器路由中定义的端点来执行特定的操作，如获取数据。
*   萨帕尔是相当深思熟虑的。

## 服务器端渲染

服务器端渲染(SSR)是 Sapper 如此吸引人的一个重要原因。如果你不知道什么是 SSR，也不知道你为什么需要它，这篇文章很好地解释了这个问题。

默认情况下，在客户端挂载动态元素之前，Sapper 会先在服务器端渲染所有的应用程序。这让你可以两全其美，而不必做出任何妥协。

不过，这里有一个警告:虽然 Sapper 在支持第三方模块方面做得近乎完美，但是有些模块需要访问`window`对象，正如您所知，您不能从服务器端访问`window`。简单地导入这样一个模块将导致你的编译失败，世界将变得有点暗淡🥺.

不过，不要担心；对此有一个简单的解决方法。Sapper 允许你动态地导入模块(嘿，更小的初始包大小)，所以你不需要在顶层导入模块。相反，您所做的将是这样的:

```
<script>
  import { onMount } from 'svelte';

  let MyComponent;

  onMount(async () => {
    const module = await import('my-non-ssr-component');
    MyComponent = module.default;
  });
</script>

<svelte:component this={MyComponent} foo="bar"/>
```

在第 2 行，我们导入了`onMount`函数。`onMount`函数内置在 Svelte 中，只有当组件安装在客户端时才会被调用(可以把它想象成 React 的`componentDidMount`)。

这意味着当只在`onMount`函数中导入我们有问题的模块时，该模块永远不会在服务器上被调用，我们也不会有丢失`window`对象的问题。那里！您的代码编译成功，一切又都好了。

哦，这种方法还有另一个好处:因为您对这个组件使用了动态导入，所以实际上最初向客户端发送的代码更少。

## 结论

我们已经看到了使用 Sapper 是多么直观和简单。路由系统非常容易掌握，即使对于绝对的初学者来说也是如此，创建一个 API 来驱动你的前端相当简单，SSR 非常容易实现，等等。

这里有很多我没有提到的特性，包括预加载、错误处理、正则表达式路径等等。真正受益的唯一方法是用它来建造一些东西。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)