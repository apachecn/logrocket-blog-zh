# 拦截 JavaScript 获取 API 请求和响应

> 原文：<https://blog.logrocket.com/intercepting-javascript-fetch-api-requests-responses/>

拦截器是可以用来预处理或后处理 HTTP 调用的代码块，有助于全局错误处理、身份验证、日志记录等等。在本文中，您将学习如何[拦截 JavaScript Fetch API 调用](https://blog.logrocket.com/axios-vs-fetch-best-http-requests/)。

有两种类型的事件您可能想要拦截 HTTP 调用，请求和响应事件。请求拦截器应该在发送实际的 HTTP 请求之前执行，而响应拦截器应该在到达发出调用的应用程序代码之前执行。

在深入研究代码之前，我们需要理解几个重要因素。首先， [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 本身不支持拦截器。此外，需要额外的包来[使用 Node.js](#node) 中的 Fetch API。

## JavaScript 获取 API

首先，让我们介绍一下 Fetch API 的一些基础知识，例如语法:

```
const fetchResponsePromise = fetch(resource [, init])

```

`resource`定义你想要获取的资源，可以是一个 [`Request`对象](https://developer.mozilla.org/en-US/docs/Web/API/Request))或者一个 URL。`init`是一个可选对象，它将包含您想要应用于这个特定请求的任何定制配置。

获取 API 是基于承诺的。因此，当您调用 Fetch 方法时，您将得到一个响应承诺。这里称为`fetchResponsePromise`，如上例所示。

默认情况下，Fetch 使用`GET`方法进行 API 调用，如下所示:

```
fetch('https://jsonplaceholder.typicode.com/todos/1')
.then((response) => response.json())
.then((json) => console.log(json));

```

下面是一个带有 Fetch 的`POST`请求的例子:

```
fetch('https://jsonplaceholder.typicode.com/todos', {
  method: 'POST',
  body: JSON.stringify({
    completed: false,
    id: 1,
    title: 'New Todo',
    userId: 1,
  }),
  headers: new Headers({
    'Content-Type': 'application/json; charset=UTF-8',
  }),
})
.then((response) => response.json())
.then((json) => console.log(json));

```

`POST`调用必须有一个`body`。请看一下[获取文档](https://developer.mozilla.org/en-US/docs/Web/API/fetch)了解更多细节。

## 实现拦截器

有两种方法可以将拦截器添加到我们的 Fetch API 调用中；我们可以使用猴子补丁或者 [`fetch-intercept`库](https://github.com/werk85/fetch-intercept)。

## 用获取进行猴子修补

为任何 JavaScript 函数或方法创建拦截器的一种方法是用猴子修补它。Monkey patching 是一种用您的函数版本覆盖原始功能的方法。

让我们一步一步地看看如何使用 monkey 补丁为 Fetch API 创建一个拦截器:

```
const { fetch: originalFetch } = window;

window.fetch = async (...args) => {
    let [resource, config ] = args;
    // request interceptor here
    const response = await originalFetch(resource, config);
    // response interceptor here
    return response;
};

```

上面的代码用一个自定义实现覆盖了原始的 Fetch 方法，并在其中调用原始的 Fetch 方法。您将使用这个样板代码来创建请求和响应拦截器。

### 请求拦截器

在下面的例子中，我们将创建一个简单的请求拦截器来改变一个插图的资源 URL:

```
const { fetch: originalFetch } = window;
window.fetch = async (...args) => {
    let [resource, config ] = args;

    // request interceptor starts
    resource = 'https://jsonplaceholder.typicode.com/todos/2';
    // request interceptor ends

    const response = await originalFetch(resource, config);

    // response interceptor here
    return response;
};

fetch('https://jsonplaceholder.typicode.com/todos/1')
.then((response) => response.json())
.then((json) => console.log(json));

// log
// {
//   "userId": 1,
//   "id": 2,
//   "title": "quis ut nam facilis et officia qui",
//   "completed": false
// }

```

这个 API 调用将从`[https://jsonplaceholder.typicode.com/todos/2](https://jsonplaceholder.typicode.com/todos/2)`而不是`[https://jsonplaceholder.typicode.com/todos/1](https://jsonplaceholder.typicode.com/todos/1)`获取数据，如`todo`的 ID `2`所示。

> 注意:请求拦截器最常见的用例之一是更改身份验证的头。

### 响应拦截器

响应拦截器会在 API 响应被传递给实际的调用者之前拦截它。让我们来看看下面的代码:

```
const { fetch: originalFetch } = window;
window.fetch = async (...args) => {
  let [resource, config] = args;

  let response = await originalFetch(resource, config);

  // response interceptor
  const json = () =>
    response
      .clone()
      .json()
      .then((data) => ({ ...data, title: `Intercepted: ${data.title}` }));

  response.json = json;
  return response;
};

fetch('https://jsonplaceholder.typicode.com/todos/1')
  .then((response) => response.json())
  .then((json) => console.log(json));

// log
// {
//     "userId": 1,
//     "id": 1,
//     "title": "Intercepted: delectus aut autem",
//     "completed": false
// }

```

在上面的代码中，我们更改了 JSON 方法以返回一些定制数据，而不是原始数据。查看文档以了解更多关于可以更改的[属性。](https://developer.mozilla.org/en-US/docs/Web/API/Response)

> 注意:回复只允许消费一次。因此，每次想要使用响应时，都需要用[克隆它。](https://developer.mozilla.org/en-US/docs/Web/API/Response/clone)

### 处理错误

通过检查`response.ok`和`response.status`的值，可以很容易地处理请求的错误。在下面的代码片段中，您可以拦截`404`错误:

```
const { fetch: originalFetch } = window;
window.fetch = async (...args) => {
  let [resource, config] = args;
  let response = await originalFetch(resource, config);
  if (!response.ok && response.status === 404) {
    // 404 error handling
    return Promise.reject(response);
  }
  return response;
};
fetch('https://jsonplaceholder.typicode.com/todos/1000000')
  .then((response) => response.json())
  .then((json) => console.log(json))
  .catch((error) => console.error(error));

```

### 节点. js

您可以在 Node.js 中使用相同的方法。但是，Node.js 本身不支持 Fetch API(尽管对 Fetch API 的本机支持将在 Node.js 的未来版本中[可用)。现在，您需要安装](https://github.com/nodejs/node/pull/41749)[节点获取](https://github.com/node-fetch/node-fetch)包，然后 monkey 修补`fetch`方法。

## 使用提取截取库

如果你不喜欢做`dirty`工作(双关语)，那么`[fetch-intercept](https://github.com/werk85/fetch-intercept)`库允许你用一个更干净的 API 注册拦截器。您可以使用 npm 或 Yarn 安装库，如下所示:

```
npm install fetch-intercept whatwg-fetch --save
// or
yarn install fetch-intercept whatwg-fetch
```

> 注意:fetch-intercept 库只支持浏览器，在 Node.js 中不起作用，而且它需要 [whatwg-fetch](https://github.com/whatwg/fetch) 作为依赖才能工作。

使用下面的代码，我们可以实现与我们的 monkey 补丁示例中相同的请求和响应拦截器:

```
import * as fetchIntercept from 'fetch-intercept';

const unregister = fetchIntercept.register({
  request: function (url, config) {
    const modifiedUrl = `https://jsonplaceholder.typicode.com/todos/2`;
    return [modifiedUrl, config];
  },

  requestError: function (error) {
    return Promise.reject(error);
  },

  response: function (response) {
    const clonedResponse = response.clone();
    const json = () =>
      clonedResponse
        .json()
        .then((data) => ({ ...data, title: `Intercepted: ${data.title}` }));

    response.json = json;
    return response;
  },

  responseError: function (error) {
    return Promise.reject(error);
  },
});

fetch('https://jsonplaceholder.typicode.com/todos/1')
  .then((response) => response.json())
  .then((json) => console.log(json));

// unregister interceptors
unregister();

```

`register`方法允许您为 Fetch API 调用注册拦截器。它接受一个带有`request`、`requestError`、`response`和`responseError`回调的对象。`register`方法返回另一个可以用来注销拦截器的方法。

Fetch API 本身不支持拦截器。然而，还有其他支持拦截器的用于进行 HTTP 调用的库。看看 [Axios](https://axios-http.com/docs/interceptors) ，它提供了开箱即用的功能。

## 摘要

在本文中，我们介绍了什么是 JavaScript 拦截器，学习了如何通过 monkey 修补 Fetch API 和使用 fetch-intercept 库来创建拦截器。

拦截器最初是由 Angular 引入的，它有助于各种各样的用例，比如帮助进行全局错误处理、身份验证、日志记录等等。您可以使用本文中描述的方法将拦截器添加到您的 JavaScript 应用程序中，但是，请记住 Node.js 还需要额外的依赖项

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.