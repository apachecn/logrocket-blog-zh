# Node.js 的挫折

> 原文：<https://blog.logrocket.com/frustrations-with-node-js/>

## 介绍

只是澄清一下，我并不讨厌 Node.js，我其实很喜欢 Node.js，也很享受做一个[全栈 JavaScript 开发者](https://www.smashingmagazine.com/2013/11/introduction-to-full-stack-javascript/)。然而，这并不意味着我不会因此而沮丧。在我对 Node.js 感到沮丧之前，让我先说 Node.js 最擅长的一些事情:

但是，您应该知道 Node.js 的一些奇怪之处:

*   类型检查— Node.js 从 JavaScript 继承了动态类型检查。但是，有时在现实生活的应用程序中编写 Node.js 代码会让您希望进行更严格的类型检查，以便更快地发现错误。您可能使用过静态类型检查工具之一，如 [Flow](https://flow.org/) 或 [TypeScript](https://www.typescriptlang.org/) ，但是 Flow 因[性能、兼容性和智能感知问题](https://medium.com/inato/migrating-from-flow-to-typescript-why-how-worth-it-5b7703d12089)和 TypeScript(尽管它在社区中很受欢迎)而令许多开发人员感到沮丧，尽管 TypeScript 在社区中很受欢迎，但它往往很笨重，并且[会在您意想不到的地方引起问题](https://dev.to/ryansolid/the-trouble-with-typescript-4fpp)
*   调试——我不是这方面的专家，但我总是在正确调试 Node.js 应用程序时遇到问题。我并不是说调试不被支持或不可能，但是代码检查和断点往往会被忽略，与其他框架相比，您可能会因为缺乏对这项重要任务的支持而感到沮丧。为此，我通常会在代码中放置`console.log`和`debugger`语句，这并不理想

以上痛点绝不仅限于 Node.js。然而，在我今天使用 Node.js 的经验中，我发现了两个突出的问题，我认为需要更详细地澄清。如果您对 Node.js 有类似或更多的不满，以及您是如何应对的，请发表评论。

## 错误处理

### 挫折

Node.js 中抛出错误不像其他语言(和框架)那么直接。我们在 Node.js 中有许多异步代码,它要求你在回调和承诺中传递错误，而不是抛出异常或简单地使用 try/catch 块。当你不得不深入进行几次回调，或者无法弄清楚一个[未处理的异常是如何导致你的应用程序无声地失败](https://2ality.com/2016/04/unhandled-rejections.html)时，调试错误的真实性质就变得更加困难，这时你就希望有一个更平稳的错误处理过程。

### 背景

在开始错误处理之前，我们需要定义一些基础知识。

Node.js 构建在单线程语言 JavaScript 之上。当进行函数调用时，你会得到一个叫做[调用栈](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)的东西。如果您的任何函数调用需要时间来解决，我们必须在等待结果返回时阻塞整个线程，这在我们必须在浏览器中与 web 应用程序交互的场景中并不理想。在我们等待一些数据返回给我们的同时，用户仍然希望使用应用程序。

在这里我们得到了[异步 JavaScript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous) 的概念，它帮助我们处理阻塞代码。简而言之，这是一种在解析注册的函数调用时分配要执行的回调的机制。有几种方法可以解决这个问题:

*   **使用函数回调** —这个想法很简单。您将名为 callback 的函数传递给异步函数调用。当异步函数调用的结果返回时，我们触发回调。async `[addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)`就是一个很好的例子，它将回调作为第二个参数:

```
function clickHandler {
  alert('Button is clicked');
}

btn.addEventListener('click', clickHandler);
```

*   **使用 promise** —当在异步函数上使用`[promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`时，你得到一个表示操作状态的对象。我们不知道承诺什么时候会带着结果或错误回到我们身边，但是我们有处理任何一种情况的机制。例如，调用`[node-fetch](https://www.npmjs.com/package/node-fetch)`会生成一个 promise 对象，我们可以用它的方法来处理它:

```
const fetch = require("node-fetch");

fetch("https://jsonplaceholder.typicode.com/todos/1")
  .then(res => res.json())
  .then(json => console.log(json))
  .catch(error => console.log("error", error));

// { userId: 1, id: 1, title: 'delectus aut autem', completed: false }
```

我们还有其他选项，如[异步迭代器和生成器](https://javascript.info/async-iterators-generators)或 [ES2017](https://node.green/) 中的[新异步/等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)功能，它只是`promise`之上的*语法糖*。但是为了简单起见，我们只坚持上面的选项。让我们看看如何为回调和承诺维护错误处理。

## 异步错误处理

函数回调——这种方法的错误处理是使用`[Error First Callback](https://nodejs.org/api/errors.html#errors_error_first_callbacks)`方法完成的。当 async 函数返回一个结果时，回调被调用，第一个参数是`[Error Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)`。如果我们没有错误，这将被设置为空。让我们看一个例子:

```
// setTimeout is faking an async call which returns an error after 0.5 seconds
const asyncFunction = (callback) => {
  setTimeout(() => {
    callback(new Error('I got an error'))
  }, 500)
}

// callback for our async function
const callbackFunction = (err, data) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(data);
}

asyncFunction(callbackFunction);
```

当我们调用上面的`asyncFunction`时，它首先接近`[setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)`，不能同步处理。因此，它要求 [`window API`](https://developer.mozilla.org/en-US/docs/Web/API/Window) 解决它并继续程序。当结果返回时(在本例中是一个`Error Object`)，它将调用函数回调。令人沮丧的部分来了。

我们不能在异步函数调用的上下文中使用`[try/catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)`来捕捉错误。所以我们不能仅仅是在我们的`Error First Callback`方法中的`throw`一个错误:

```
const callbackFunction = (err, data) => {
  if (err) {
    throw err;
  }
  console.log(data);
}

try {
  asyncFunction(callbackFunction);
} catch(err) {
  // we are not catching the error here
  // and the Node.js process will crash
  console.error(err);
}
```

*   在我们的回调函数中忘记`return`会让程序继续，导致更多的错误。这里的要点是，这里有太多需要记住和处理的问题，可能会导致代码进入难以推理和调试的状态

```
if (err) {
    console.error(err);
    return;
  }
```

Promises 非常擅长将多个异步函数链接在一起，并帮助您避免使用前面的方法可能导致的 [`callback hell`](http://callbackhell.com/) 。对于错误处理，promises 使用链中的 [`.catch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法来处理异常。然而，在其中处理错误仍然会带来一些问题:

*   如果你忘记在你的承诺链中使用`.catch`方法，你可能会被错误吞噬。这将导致这样的错误被归类为 [`unhandled error`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 。在这种情况下，我们需要在 Node.js 中有一个机制来处理没有被处理的承诺拒绝。这是在 Node.js 中发出`[unhandledRejection event](https://nodejs.org/api/process.html#process_event_rejectionhandled)`时完成的:

```
const fetch = require("node-fetch");
const url = "https://wrongAPI.github.com/users/github";

const unhandledRejections = new Map();
process.on("unhandledRejection", (reason, promise) => {
  unhandledRejections.set(promise, reason);
  console.log("unhandledRejections", unhandledRejections);
});

const asyncFunction = () => fetch(url);

asyncFunction()
  .then(res => res.json())
  .then(json => console.log(json))
```

*   另一个问题是大型异步函数链的可跟踪性。简而言之，抛出错误的来源、起源和上下文是什么？例如，如果您有一个很长的异步函数调用链来处理一个 API 获取请求和几个依赖于它的高级组件。这些更高级别的组件下面还有几个子组件。其中任何一个错误都会使问题的可追溯性变得困难

在 Node.js 中如何处理这一点并不简单，但一个常见的模式是在更高级别的组件中向异步任务添加一个立即`.catch`方法，并在其中再次抛出错误。这极大地有助于在错误发生在它们的任何子节点时跟踪错误，因为我们将另一个`.catch`链接到调用更高级异步任务的实例。让我们看一个例子:

```
const fetch = require("node-fetch");
const url = "https://wrongAPI.github.com/users/github";

// higher level async task
const asynFunction = () => {
  return fetch(url).catch(error => {
    // re-throwing the error
    throw new Error(error);
  });
};

// error thrown in this intacen 1 is much bette traceable
// returns: instace 1 error: invalid json response body at https://wrongapi.github.com/users/github reason: Unexpected token < in JSON at position 0
try {
 return await asyncFunction();
} catch(error) {
  console.error("instace 1 error:", error.message)
}
```

## 包管理器

### 挫折

Node.js 中有几个用于包管理的工具，比如 [npm](https://www.npmjs.com/) 、 [yarn](https://pnpm.js.org/) 和 [pnpm](https://pnpm.js.org/) ，它们可以帮助你为你的应用安装工具、包和依赖项，使软件开发过程更快更简单。

然而，与其他语言和框架相比，JavaScript 社区定义良好的通用标准的情况越来越少。仅仅在谷歌上搜索“JavaScript 标准”就显示出缺乏标准，因为人们倾向于不同意如何处理 JavaScript，除了在少数情况下像 [Mozilla JS 参考](https://developer.mozilla.org/en-US/docs/Web/JavaScript)——这是非常可靠的。因此，很容易对需要为 Node.js 中的项目选择哪个包管理器感到困惑。

此外，Node.js 社区中有关于[低质量包的抱怨，这使得开发人员更难决定他们是否需要重新发明轮子并自己构建所需的工具，或者他们是否可以信任维护的包。](https://www.reddit.com/r/typescript/comments/dds6wk/i_am_concerned_about_the_low_quality_of_npm/)

最后，随着 [JavaScript 的快速变化](https://medium.com/@madasamy/javascript-brief-history-and-ecmascript-es6-es7-es8-features-673973394df4)，我们的应用程序所依赖的许多包也在变化也就不足为奇了。这需要 Node.js 中更平滑的包版本管理，这有时会很麻烦。

这绝不意味着 Node.js 在包和包管理方面比其他框架差，而仅仅是 Node.js 包管理器带来的一些挫折的反映。我们将更详细地讨论这些挫折，比如缺乏标准、包的质量和版本管理，但是首先，我们需要了解一些最著名的 Node.js 包管理器的背景。

### 背景

*   **NPM**——这是 Node.js 的[官方包管理器](https://nodejs.org/en/knowledge/getting-started/npm/what-is-npm/)，通过[它的库](https://www.npmjs.com/)，你可以发布、搜索、安装包。具体来说，在 Node.js 项目的上下文中，它还可以帮助您使用 [CLI 命令](https://docs.npmjs.com/cli/npm)和`[package.json](https://docs.npmjs.com/files/package.json)`文档来管理您的项目依赖项，并为它们处理版本管理
*   **yarn** —将 yarn 视为 NPM CLI 的改进版本，具有相同的包安装模型。此外，它还有其他一些优点:
    *   比较靠谱。与 NPM 不同，它默认使用双注册中心([npmjs.com](https://www.npmjs.com/)和[https://bower.io/search/](https://bower.io/search/))，以确保如果任何一个注册中心关闭，服务仍然可用
    *   它更快。它可以在并行实例中下载包，并缓存所有已安装的包，因此下次想要下载时可以更快地检索它们。尽管 NPM 也通过 [NPM 缓存](https://docs.npmjs.com/cli-commands/cache.html)做了一些性能改进
*   **pnpm** —这是三人中最新的玩家。PNPM 官方将自己描述为[“快速、磁盘高效的包管理器”](https://pnpm.js.org/)，通过只使用符号链接构建一次依赖项并重用它们，它似乎比其他两个更有效

## 与包管理器打交道

*   **缺乏标准**——正如我们在上面看到的，对于包管理器有多种选择。当你想开始一个项目时，你可能会对选择哪一个有点困惑，这是很常见的。它们在 99%的场景中都有相似之处，但在 1%的情况下也有一些小的怪癖，这些怪癖会导致项目维护过程中出现问题。在生产应用程序中使用了上述所有选项后，我希望在这方面有更多的一致性
*   **包的质量**——尽管你可以在 Node.js 中找到很多有用的包，但是还有相当数量的[选项是过时的、测试不佳的或者没有维护的](https://npms.io/search?q=is%3Adeprecated)。因为在 NPM 注册表中发布包并不困难，所以我们开发者要确保为我们的项目选择正确的包。我们可以简单地通过检查它的 GitHub repo 来检查一个包，并检查它的整体状态和维护。这可以是检查许多问题和开放拉取请求之间的良好平衡，在报告的问题中来自维护者的良好沟通，以及在许多星星和叉子中反映的软件包的整体使用和它的受欢迎程度。为了使这项工作更加容易，你可以在 [NPMS、](https://npms.io/)输入你的包裹名称，你就可以看到它的整体概况
*   **版本管理** —包管理者使用`[semver](https://semver.org/)`来处理包的版本控制。用这种方法，一个样例包版本看起来像这个`Major.Minor.Patch`，例如`1.0.0`。让我们来看一个实际的`package.json`和依赖项列表以及它们的运行版本:

```
{
  "name": "app",
  "version": "1.0.0",
  "description": "Node.js example",
  "main": "src/index.js",
  "scripts": {
    "start": "nodemon src/index.js"
  },
  "dependencies": {
    "node-fetch": "~2.6.0"
  },
  "devDependencies": {
    "nodemon": "^1.18.4"
  },
}
```

这已经很混乱了，因为我们在包版本前面有两个不同的符号。它们是什么意思？

`~`或*波浪号*显示了一个软件包可接受的补丁版本范围。例如，我们将为从`2.6.0`到`2.7.0`的`node-fetch`更新所有未来的补丁

`^`或*插入符号*显示了一个包可接受的次要/补丁版本的范围。例如，我们将为从`1.18.4`到`2.0.0`的`nodemon`更新所有未来的补丁

[对于这样一个简单的任务来说，这似乎已经是一大堆麻烦了。此外，我们需要考虑这样一个事实，即在定义依赖版本的正确范围时犯了一个错误可能会在某个时候破坏应用程序。然而，像](https://hackernoon.com/inconsistency-as-a-feature-f5f1a28356d4)`[package.json.lock](https://docs.npmjs.com/configuring-npm/package-lock-json.html)`或 [`yarn.lock`](https://classic.yarnpkg.com/en/docs/yarn-lock/) 这样的概念的形成是为了通过帮助跨机器进行一致的依赖安装来帮助避免犯这样的错误。然而，我希望有更多的标准方法来确保不会因为 Node.js 中有缺陷的版本控制和管理系统而发生严重的问题。

## 结论

这些是我在 Node.js 上经历的一些挫折。但是，这里有一些事情需要记住:

*   Node.js 的大部分挫折来自于不熟悉作为底层语言的 JavaScript。让自己更加熟悉[的基本和高级主题](https://developer.mozilla.org/en-US/docs/Web/JavaScript)，作为 Node.js 开发人员的生活将会更加轻松
*   确保 Node.js 应用程序的用例是有效的。例如，聊天应用程序是使用 Node.js 的绝佳选择，但 CPU 密集型计算应用程序则不然。[熟悉常见用例](https://medium.com/the-node-js-collection/why-the-hell-would-you-use-node-js-4b053b94ab8e)
*   最后，要知道任何框架都有一定的痛点。使用本文和参考列表中的类似文章来了解常见问题以及处理这些问题的最佳方法

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.