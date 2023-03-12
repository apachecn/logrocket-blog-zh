# Fetch API 终于来到 Node.js 了

> 原文：<https://blog.logrocket.com/fetch-api-node-js/>

Fetch API 姗姗来迟，它的引入预示着一个实时互动网页的新时代。2022 年 2 月 1 日，Node.js 核心团队合并了一个 pull 请求，将 Fetch API 添加到 Node。为了理解这一点为何如此重要，让我们探索一下 Fetch 是如何产生的，以及这一新增功能对 Node.js 开发人员意味着什么。

## 背景

### 开始的时候…

在 web 的早期，很难跨网站执行异步请求；开发人员不得不使用笨拙的方法来跨多个网络进行交互。

Internet Explorer 5 在 1998 年引入了`XMLHttpRequest`，改变了这一点，这是一个旨在克服这一限制的 API。最初，`XMLHttpRequest`被设计成通过 HTTP 获取 XML 数据，因此得名。然而，在它发布后的某个时候，增加了对其他数据格式的支持——主要是 JSON、HTML 和明文。

那个时候，API 工作起来很有魅力，但是随着 web 的发展，它变得很难使用，JavaScript 框架，特别是 jQuery，不得不将其抽象，以使实现更容易，成功/错误处理更顺畅。

### Fetch API 简介🔥

2015 年，Fetch API 作为`XMLHttpRequest`的现代继任者推出，随后它成为了[在 web 应用](https://blog.logrocket.com/axios-vs-fetch-best-http-requests/)中进行异步调用的事实上的标准。Fetch 相对于`XMLHttpRequest`的一个显著优势是它利用了承诺，允许更简单和更干净的 API，同时避免了回调地狱。

尽管 Fetch API 已经存在了一段时间，但由于一些限制，它还没有包含在 Node.js 核心中。在黑客新闻上由 Node 的核心贡献者之一回答的一个[问题中，注意到浏览器的 Fetch API 实现依赖于基于浏览器的 Web Streams API 和`AbortController`接口(用于中止 Fetch 请求)，这在 Node.js 中直到最近才可用。因此，很难决定将它包含在 Node.js 核心中的最佳方法。](https://news.ycombinator.com/item?id=30162332)

在添加 Fetch API 之前很久，[请求模块](https://www.npmjs.com/package/request)是在 Node 中进行 HTTP 请求的最流行的方法。但是 JavaScript 生态系统迅速发展，新引入的模式使得 request 过时了。这里一个重要的例子是 async/await；在请求 API 中没有这方面的规定，由于这些限制，该项目后来被否决了。

### 十一个

2018 年， [Undici](https://undici.nodejs.org/#/) 作为 Node.js 的更新更快的 HTTP/1.1 客户端推出，支持管道和池化等功能。在核心团队的大量辛勤工作之后，Undici 使 Node.js 中的`fetch()`实现成为可能。

## 如何使用 Fetch

Fetch API 是作为一个高级函数提供的，在其最基本的版本中，它接受一个 URL 并生成一个解析为响应的承诺:

```
fetch("http://example.com/api/endpoint")
  .then((response) => {
    // Do something with response
  })
  .catch(function (err) {
    console.log("Unable to fetch -", err);
  });
```

您还可以通过在 URL 后面附加一个可选对象来改变`fetch`过程的执行方式，这允许您改变请求方法、请求头和其他选项。请求的响应是一个包含请求返回的元数据的对象，它由响应数据、头、请求日期等元素组成。

## 在 Node.js 中使用 Fetch API 的好处

Fetch API 现在被预先打包成一个内置的节点模块，这对开发人员社区非常有益。这些优势包括:

Node.js 的内置 Fetch 可能意味着 node-fetch、got、cross-fetch 和许多其他为相同目的而构建的包的终结。这意味着在 Node 中执行网络操作之前，您不必执行`npm install`。

此外，node-fetch(目前 Node.js 最流行的 fetch 包)最近被切换为仅支持 ESM 的包。这意味着您无法使用节点`require()`功能导入它。原生获取 API 将使节点环境中的 HTTP 获取感觉更加流畅和自然。

### 跨平台熟悉度

以前在前端使用 Fetch API 的开发人员在 Node 中使用内置的`fetch()`方法时会感觉如鱼得水。这将比使用外部包在节点环境中实现相同的功能更加简单和直观。

### 更快的实施

如前所述，新的 Fetch 实现也基于 Undici，这是一个快速、可靠且符合 Node.js 规范的 HTTP 客户端。

## 缺点

浏览器的 Fetch API 本身有一些缺点，这些缺点无疑会转移到新的 Node.js Fetch 实现中:

### 错误处理

Fetch 在错误管理方面表现不佳；只有网络故障才被认为是真正的错误。这意味着 Fetch 只会拒绝一个承诺，如果用户没有连接到互联网或如果一个罕见的网络故障发生；忽略服务器传递的错误。

### 设置超时

在 Fetch 中，设置超时以在特定时间段后取消特定请求同样很难实现。

### 没有请求进度

另一个主要缺点是 Fetch 没有包含一个容易监控请求进度的方法，而像 Axios 这样的库可以很好地处理这个问题。

## 现在可以在 Node.js 中使用 Fetch 了吗？

Fetch 已经是 Node v17 中的一个实验性特性。如果你有兴趣在主版本发布前试用它，你需要先下载并[升级你的 Node.js 版本到 17.5](https://nodejs.org/dist/v17.5.0/) 。

或者，您可以使用 [nvm](https://github.com/nvm-sh/nvm) 直接从命令行/终端进行更新:

```
nvm install 17.5
```

我们现在可以通过使用`node`命令添加`--experimental-fetch`参数来运行与获取相关的代码:

```
node app.js --experimental-fetch
```

### 从其他库迁移

在撰写本文时，还没有正式的迁移指南。因为 API 目前还处于试验阶段，当它达到稳定版本时，可能会经历一些突破性的变化或其他更新。因此，最好等到 API 稳定后再迁移生产中的任何项目。

## 结论

总的来说，Fetch 终于来到 Node.js 核心令人兴奋，因为这是开发人员社区长期以来的要求。在本文中，我们研究了异步 web 请求是如何发展的，以及 Fetch 最终是如何出现在 Node.js 中的。

可能需要一段时间(大约一两年)Fetch API 才能在 Node.js 中完全稳定，因为要使它符合标准还有很多工作要做。许多有趣的更新也将在不久的将来发生，其中之一包括为 Undici 添加 HTTP/2 支持，最终是 Fetch API 本身。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.