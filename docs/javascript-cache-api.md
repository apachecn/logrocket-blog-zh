# 使用 JavaScript 缓存 API 

> 原文：<https://blog.logrocket.com/javascript-cache-api/>

## 介绍

> [缓存 API](https://web.dev/cache-api-quick-guide/) 提供了一种机制，用于存储网络请求并在运行时检索相应的响应。它可以在没有互联网连接的情况下使用(或存在不稳定的连接)，这使它成为构建[渐进式网络应用](https://blog.logrocket.com/how-to-build-a-progressive-web-app-pwa-with-node-js/)(完全优化的网络应用，像本地应用一样离线工作)的一部分。

因为不可能在开发时预先确定您的用户群，所以重要的是构建可以被广大用户访问的 web 服务，这些用户可能没有最好的硬件或者可能具有较慢的互联网连接。

渐进式 web 应用程序的创建是为了确保 web 服务可以在所有设备上运行。在移动设备上，它们旨在提供接近原生应用程序的用户体验。在幕后，pwa 使用[服务工作者](https://developers.google.com/web/fundamentals/primers/service-workers)来实现理想的行为，并且它们利用缓存 API 来额外控制网络资源。

这个谷歌网络基础[页面](https://developers.google.com/web/fundamentals/primers/service-workers)是这样描述服务人员的:

> 服务人员是您的浏览器在后台运行的脚本，独立于网页，为不需要网页或用户交互的功能打开了大门。如今，它们已经包括了像[推送通知](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web)和[后台同步](https://developers.google.com/web/updates/2015/12/background-sync)这样的功能。在未来，服务人员可能会支持其他东西，如定期同步或[地理围栏](https://developer.android.com/training/location/geofencing)。服务工作者的一个核心特性是能够拦截和处理网络请求，包括以编程方式管理响应缓存。

我们可以看到缓存在服务人员的工作流程中扮演着重要的角色。本文展示了如何在服务工作者中使用缓存 API，并将其作为资源存储的通用机制。

本教程中的所有代码都可以在这个[资源库](https://github.com/Jordanirabor/Working_With_The_Cache_API)中找到，可以随意分叉或发送 PR。

## 检测缓存 API

在现代浏览器中，每个原点都有一个缓存存储，我们可以通过打开浏览器开发工具来检查它:

*   在 Chrome 上:*应用* > *缓存* > *缓存存储*
*   火狐上:*存储* > *缓存*

> 专业提示:在 Chrome 中，你可以访问`chrome://inspect/#service-workers`并点击“inspect”选项(在任何已经打开的标签页的正下方),以查看`service-worker.js`脚本动作的日志记录语句。

缓存 API 在所有现代浏览器中都可用:

*   边> = 17
*   歌剧> = 27
*   野生动物园> = 11.1
*   火狐> = 39
*   铬> = 40
*   iOS Safari = 11.4 >
*   UC 浏览器 11.8 >=
*   Android 版 chrome > = 67

因为旧的浏览器可能不支持该 API，所以在尝试引用它之前检查它的可用性是一个好习惯。`caches`属性在`window`对象上可用，我们可以用下面的代码片段检查它是否在浏览器中实现:

```
if ('caches' in window){
    // you can safely insert your snippet here
}
```

## 使用

缓存 API 是缓存 URL 可寻址资源的最佳选择，也就是说，当您使用加载应用程序所需的网络资源时，应该使用缓存 API。如果您的应用程序处理大量数据，您可以缓存用户在页面加载时最可能需要的数据。这些资源可能包括基于文件的内容、资产、API 响应和网页。

对于大量结构化数据(包括文件/blob)的存储，您应该理想地使用 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) API。

缓存 API 附带了几种方法来执行以下(CRUD)操作:

1.  创建新的缓存
2.  向缓存中添加(更新)项目
3.  从缓存中检索项目
4.  从缓存中删除项目

让我们回顾一下在代码中使用这些方法的一些方法。

## 创建新的缓存

在我们开始将**请求-响应**对存储到缓存之前，我们需要创建一个缓存实例。每个源在其高速缓存存储器中可以有多个高速缓存对象。我们可以使用`caches.open()`方法创建一个新的缓存对象:

```
const newCache = await caches.open('new-cache');
```

上面的代码片段将缓存的名称作为单个参数接收，然后使用该名称创建缓存。`caches.open()`方法首先检查具有该名称的缓存是否已经存在。如果没有，它就创建它并返回一个用`Cache`对象解析的`Promise`。

在代码片段执行之后，我们现在将有一个新的缓存对象，可以用名称 *new-cache* 来引用它。

## 向缓存中添加项目

有三种主要方法可以将项目添加到缓存中:

1.  `add`
2.  `addAll`
3.  `put`

所有这些方法都返回一个`Promise`，现在让我们来看看它们之间有什么不同。

### Cache.add()

第一个方法是`cache.add()`，它接受一个参数，这个参数可以是一个 URL 字符串或者是一个`Request`对象。对`cache.add()`方法的调用将向网络发出[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)请求，并将响应存储在相关联的缓存对象中:

```
newCache.add('/cats.json')
```

或者为了获得更多的控制权，我们可以使用一个请求对象:

```
const options = {
    method: "GET",
    headers: new Headers({
        'Content-Type': 'text/html'
    }),
  }  
newCache.add(new Request('/cats.json', options))
```

> 注意:如果获取不成功并返回错误响应，则缓存中不会存储任何内容，并且
> `Promise`会拒绝。

### Cache.addAll()

这个方法的工作方式类似于`cache.add()`方法，除了它接受一个请求 URL 字符串或`Request`对象的数组，并在所有资源都被缓存后返回一个承诺:

```
const urls = ['pets/cats.json', 'pets/dogs.json'];
newCache.addAll(urls);
```

> 注意:如果请求数组中的一项或多项没有被缓存，则 promise 拒绝。此外，在缓存数组中的项目时，一个新条目会覆盖任何匹配的现有条目。

### Cache.put()

`Cache.put`方法的工作方式与其他方法完全不同，因为它允许一个额外的控制层。`put()`方法有两个参数，第一个可以是 URL 字符串或`Request`对象，第二个是来自网络或在代码中生成的`Response`:

```
// Retrieve cats.json and cache the response
newCache.put('./cats.json')

// Create a new entry for cats.json and store the generated response
newCache.put('/cats.json', new Response('{"james": "kitten", "daniel": "kitten"}'))

// Fetch a response from an external address and create a new entry for cats.json
newCache.put('https://pets/cats.json');
```

`put`方法允许额外的控制层，因为它允许您存储不依赖于 CORS 的响应或依赖于服务器响应状态代码的其他响应。

> 专业提示:前两种方法——`add()`和`addAll()`——取决于请求数据的服务器上 CORS 的状态。如果 CORS 检查失败，什么都不会被缓存，`Promise`会拒绝。另一方面，使用`put()`会给你额外的信心，因为你可以设置内部响应。

## 从缓存中检索项目

在我们将一些项目添加到缓存中之后，我们需要能够在运行时检索它们。我们可以使用`match()`方法来检索缓存的响应:

```
// retrieve a new response
const request = '/cats.json';
const response = await newCache.match(request);
```

在上面的代码中，我们向`match`方法传递了一个`request`变量，如果`request`变量是一个 URL 字符串，它将被转换为一个`Request`对象并用作参数。如果找到匹配的条目，`match`方法将返回解析为`Response`对象的`Promise`。

浏览器使用不同的因素来确定两个或多个`Requests`是否匹配。一个`Request`可能与另一个具有相同的 URL，但是使用不同的 HTTP 方法。浏览器认为两个这样的请求是不同的。

当使用`match`方法时，我们也可以传递一个 options 对象作为第二个参数。这个对象有键值对，告诉`match`在匹配请求时忽略特定的因素:

```
// create an options object
const options = {
        ignoreVary: true, // ignore differences in Headers
        ignoreMethod: true, // ignore differences in HTTP methods
        ignoreSearch: true // ignore differences in query strings
    }

// then we pass it in here
const response = await newCache.match(request, options);
```

如果有多个缓存项匹配，则返回最早的缓存项。如果我们想要检索所有匹配的响应，我们可以使用`matchAll()`方法。

## 从缓存中移除项目

我们可能不再需要缓存条目，并希望将其删除。我们可以使用`delete()`方法删除缓存条目:

```
// delete a cache entry
const request = '/cats.json';
newCache.delete(request);
```

在上面的代码中，我们在请求变量中保存了一个 URL 字符串，但是我们也可以将一个`Request`对象传递给`delete`方法。在我们有不止一个匹配条目的情况下，我们可以像对待`match`方法一样传递一个类似的选项`Object`。

## 删除缓存

最后，我们可以通过调用`window`对象的缓存属性上的`delete()`方法来删除缓存。让我们删除下面代码片段中的缓存:

```
// delete an existing cache
caches.delete('new-cache');
```

> 注意:当一个缓存被删除时，`delete()`方法返回一个`Promise`(如果缓存确实被删除了),如果出错或缓存不存在则返回一个 false。

## 结论

在本文中，我们浏览了缓存 API，并讨论了它对渐进式 web 应用程序开发的有用性。我们还探索了它的 CRUD 方法，并看到了检索响应和存储请求是多么容易。

> 注意:出于安全原因，缓存被绑定到当前源，其他源不能访问为其他源设置的缓存。

本教程中的所有代码都可以在这个[资源库](https://github.com/Jordanirabor/Working_With_The_Cache_API)中找到，可以随意分叉或发送 PR。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.