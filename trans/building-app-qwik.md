# 使用 Qwik 构建应用程序

> 原文：<https://blog.logrocket.com/building-app-qwik/>

***编者按**:本文由 Qwik 团队的一名成员进行了同行评审，以确保准确性。*

Qwik 是一个创建 web 应用程序的前端框架，无论你的站点大小和复杂程度如何，它都能提供闪电般的页面加载速度。

在本文中，我们将学习如何开始使用 Qwik ，并通过创建一个简单的示例应用程序来探索 Qwik 应用程序的构建模块。我们还将回顾水合作用的概念，了解它为什么会降低我们应用程序的速度，并了解 Qwik 如何避免水合作用，从而提高我们应用程序的性能。我们开始吧！

## JavaScript 框架中的渲染

在开始使用 Qwik 之前，我们首先需要了解 JavaScript 框架中可用的不同呈现选项。

### 客户端渲染

像 [AngularJS](https://angularjs.org/) 这样的早期 JavaScript 框架使用客户端渲染。在客户端呈现中，所有逻辑、数据获取、路由和模板化都发生在客户端。然而，客户端呈现的一个显著缺点是，随着应用程序的增长，呈现初始页面的时间会增加。

引入延迟加载的概念是为了加快第一次渲染的速度。通过延迟加载，你可以[将你的应用分成多个更小的部分，称为块](https://blog.logrocket.com/cross-browser-native-lazy-loading-javascript/)，而不是一次将整个应用发送到浏览器。当应用程序第一次加载时，您将只在客户端发送和执行所需的代码，从而加快第一次渲染的速度。

### 服务器端渲染

在服务器端渲染中，应用程序的第一次渲染已经在服务器端完成，并以 HTML 格式发送到浏览器，大大加快了初始渲染的速度。

大多数网络应用程序都不是静态网页。因此，客户端框架必须在服务器端呈现后重新创建页面，方法是附加事件侦听器、创建 DOM 树并恢复应用程序状态以使其具有交互性。这个过程[叫做水合](https://blog.logrocket.com/vue-3-lazy-hydration-from-scratch/)。水合作用的缺点是，即使页面呈现在用户的屏幕上，它也可能无法交互，直到水合作用再次下载并重新执行代码。

作为一个没有水化的框架，这正是 Qwik 试图解决的问题。

## Qwik 入门

要开始使用，您需要安装 Node.js ≥v16.8。

Qwik 附带了一个 CLI 工具，可以帮助您搭建应用程序。首先，使用以下命令生成您的应用程序:

```
npm create [email protected]

```

上面的命令将提示您命名应用程序；我们选`dice-roller`。它还会提示您选择应用程序的类型；选择**基础 App(qwikicity)**。这将在 Qwik 应用程序中生成一个名为`dice-roller`的目录。

让我们使用以下命令导航到`dice-roller`目录:

```
cd dice-roller

```

现在，让我们启动开发服务器来运行我们的应用程序:

```
npm start

```

要查看应用程序的外观，请打开`[http://localhost:5174](http://localhost:5174)`。

## Qwik 应用程序结构

如果在代码编辑器中打开项目目录，您将看到以下文件夹结构:

```
├── README.md
├── node_modules
├── package-lock.json
├── package.json
├── public
├── src
├── tsconfig.json
└── vite.config.ts

```

这里有几个重要的事情需要理解:

*   TypeScript:默认情况下，Qwik 应用程序支持 TypeScript，如`tsconfig.json`所示
*   Vite:在开发过程中，Qwik 使用[V](https://vitejs.dev/)iteT2
*   `src`:默认情况下，`src`目录用于存放源代码
*   `public`:`public`目录用于静态资产

## Qwik 应用示例:掷骰子应用

对于这个应用程序，我们使用的是 [QwikCity](https://qwik.builder.io/qwikcity/overview/) ，这是 Qwik 的元框架，就像 Next.js 是 React 一样。我们将使用 QwikCity 来提供基于目录的路由器和对组件树的访问。如果您打开`src/components/routes/index.tsx`，您将看到默认路由`/`导出一个带有一些内容的 Qwik 组件。

用下面的代码替换文件的内容，它模拟六面骰子的滚动:

```
import { component$, useStore } from "@builder.io/qwik";

export const randomValue = () => Math.floor(Math.random() * 6) + 1;

export default component$(() => {
  const dice = useStore({
    value: 1,
  });

  return (
    <div>
      <h2>Dice roller</h2>
      <h3>Dice value: {dice.value}</h3>
      <div>
        <button onClick$={() => (dice.value = randomValue())}>Roll</button>
      </div>
    </div>
  );
});

```

如果您保存文件并在`[https://localhost:5174](https://localhost:5174)`打开浏览器，您将看到以下应用程序:

![Qwik Dice Roller Example App](img/9b63e3266e73eba21b0ca47128b708bb.png)

忽略页眉和页脚，它们是默认应用程序的一部分。您更改的代码用红框标出。我们来详细了解一下。

顾名思义，`component$`方法创建一个组件。`component$`方法来自`builder.io/qwik`包。注意`$`符号；它有一个特殊的意义，我们很快会讨论。

同样，像 React 一样，一个组件可以有`props`，您可以用它向组件传递输入。一个 Qwik 组件使用`jsx`。JSX 模板是从创建 UI 的组件返回的。

[`useStore`](https://qwik.builder.io/docs/components/state/#usestore) 是一个 Qwik 钩子，可以让你创建一个类似 React 的`useState`的状态。每当应用程序读取或写入这些值时，Qwik 都会意识到这一点，并相应地重新呈现 UI。

[`onClick$`](https://qwik.builder.io/docs/components/events/) 是一个允许你在元素上绑定一个点击事件处理程序的属性。您可以为各种类型的事件创建事件处理程序。最后，`randomValue`方法生成一个 1 到 6 之间的随机值。

总之，在 Qwik 中，有组件、JSX、钩子和事件，比如 React。然而，与 React 相比有一个显著的不同，这就是 Qwik 如何避免水合作用。

## 美元符号`$`

Qwik 附带了一个[优化器](https://qwik.builder.io/docs/advanced/dollar/)，它负责为延迟加载提取代码。这个优化器检查应用程序代码，当它[遇到美元`$`符号](https://qwik.builder.io/docs/advanced/dollar/)时，它创建一个所谓的符号。这些符号可以很容易地被延迟加载。

在上面的组件示例中，`$`有两个位置；第一次在`component$`，第二次在`onClick$.`

Qwik 将为以下内容生成符号:

*   对于组件
*   对于`onClick`事件处理程序

`$`符号也是给开发人员的一个信号，表明代码有特殊的含义。

## 超文本标记语言

在服务器端呈现代码后，Qwik 将生成 HTML 并将其发送到浏览器。标记将如下所示:

```
<html>
  <body q:base="/build/">
    <!--qv q:id=0 q:key=Ncbm:0t_0-->
    <div>
      <h2>Dice roller</h2>
      <h3>
        Dice value:
        <!--t=1-->1<!---->
      </h3>
      <div>
        <button
          on:click="app_component_div_div_button_onclick_yuuzzqjuatw.js#app_component_div_div_button_onClick_YuUzZQjuATw[0]"
          q:id="2"
        >
          Roll
        </button>
      </div>
    </div>
    <!--/qv-->
    <script>
      /*Qwikloader script*/
    </script>
    <script type="qwik/json">
      {...json...}
    </script>
  </body>
</html>

```

您可能会注意到 HTML 元素上没有 JavaScript 事件处理程序。这也是 Qwik 在第一次渲染时速度很快的原因之一。没有 JavaScript 就意味着没有水化。那么，交互性是如何工作的呢？

## qwikiloader

查看`<button>`元素，您会注意到它有一个奇怪的属性`on:click`。浏览器会自行忽略该属性。然而，Qwik 通过使用 [Qwikloader](https://qwik.builder.io/docs/advanced/qwikloader/) 利用了这个属性，Qwikloader 是一个小的 JavaScript 代码，作为一个内联的`<script>`标签提供。

这个属性有一个看起来很奇怪的值:

```
app_component_div_div_button_onclick_yuuzzqjuatw.js#app_component_div_div_button_onClick_YuUzZQjuATw[0]

```

这个奇怪的值被称为 [QRL](https://qwik.builder.io/docs/advanced/qrl/) ，或者 Qwik URL。此 URL 提供以下信息:

*   要延迟加载的 JavaScript 块
*   要从块中获取的符号的名称

使用这个 URL，Qwikloader 将知道从优化器生成的块中获取和执行什么代码。但是国家呢？新获取的代码如何知道应用程序的当前状态？

## 状态

Qwik 还使用 HTML 中的一个`<script>`标签以序列化的形式保存应用程序的状态。它看起来像下面这样:

```
<script type="qwik/json">{
  "refs": {
    "8": "0!"
  },
  "ctx": {},
  "objs": [
    {
      "value": "1"
    },
    1
  ],
  "subs": [
    [
      "2 #6 0 #7 data"
    ]
  ]
}</script>

```

Qwik 使用序列化状态的原因如下:

*   在服务器上暂停执行后，在客户端重新开始执行
*   在客户端创建订阅以仅重新呈现已更改的组件

这种使用延迟加载恢复应用程序并访问序列化状态而无需下载所有应用程序代码的能力被称为[可恢复性](https://qwik.builder.io/docs/concepts/resumable/)。

## 部署

一旦您完成了应用程序，您就可以[将它部署到任何有 Node.js 可用的环境中](https://blog.logrocket.com/free-services-deploy-node-js-app/)。Qwik 提供内置集成，允许您快速部署到 Netlify、Vercel 等托管服务。

您可以通过运行以下命令来添加任何集成:

```
npm run qwik add

```

在撰写本文时，Qwik 支持以下集成:

例如，如果要在 Netlify 上部署，可以运行以下命令:

```
npm run qwik add netlify-edge

```

然后，您可以运行以下命令将您的应用程序部署到 Netlify:

```
npm run build
npm run deploy

```

## 结论

在本文中，我们学习了如何开始使用 Qwik。我们讲述了一些渲染技术，并探索了一些 Qwik 的关键 API，包括组件、钩子和事件。

我们学习了 Qwik 如何通过使用 Optimizer、Qwikloader 和 QRL 使应用程序可恢复。这只是冰山一角。了解更多，请前往 [https://qwik.builder.io](https://qwik.builder.io) 了解更多关于 [QwikCity](https://qwik.builder.io/qwikcity/overview/) 、 [Builder](https://www.builder.io/) 、 [Partytown](https://partytown.builder.io/) 等等。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。