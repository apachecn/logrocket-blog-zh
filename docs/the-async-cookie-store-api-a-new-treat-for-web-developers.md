# 异步 Cookie Store API:web 开发人员的新享受

> 原文：<https://blog.logrocket.com/the-async-cookie-store-api-a-new-treat-for-web-developers/>

## 背景

Cookies 是在浏览器中存储信息的最古老的方式之一。它们被设计成一种为无状态 HTTP 协议存储有状态或会话信息的可靠机制。这种信息通常包括经过身份验证的用户的浏览活动或行为，例如他们访问过的页面或他们点击过的链接。

简单来说，cookies 是这样工作的:在接收到一个 HTTP 请求后，服务器可以将一个或多个 [`Set-Cookie`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie) 头连同响应一起发送回客户端。cookie 通常由这个客户端存储，然后它可以在一个 [`Cookie`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cookie) HTTP 头中与对同一个服务器的请求一起发送。因此，cookies 用于判断请求是否来自同一个浏览器客户端会话。

## 饼干的问题是

虽然 cookies 解决了一个重要的用例，但它们也带来了许多问题。他们的接口相当复杂，因为保存所有的 cookie(DOM 的一部分)是有问题的。没有办法知道执行写操作的结果。文档脚本需要发出一个 read 或 get 请求来验证前一个 write 或 create 请求的结果。

另一个问题是，当 cookie 的属性被读取时，它们仍然需要被解析/序列化。这是因为它们都作为单个字符串返回，每个 cookie 的名称-值对连接成一个对列表，每个列表项用分号分隔。

这在通常管理 cookie 或执行诸如获取 cookie、检查 cookie 的存在、检查 cookie 数据中特定值的存在等操作时提出了另一个挑战。

如今，开发者被强烈鼓励使用现代存储 API,如 IndexedDB 或`localStorage`,在浏览器中存储会话数据。这是因为它们具有比 cookie 更大的存储限制，并且来自客户端的 cookie 信息从不发送到服务器。

但是，如果您仍然有很强的理由在项目中使用 Cookie，那么您很幸运:async Cookie Store API 提供了一种新的改进的做事方式。

## cookie 如何工作:网络 cookie 行为初级读本

在这里，我们将探索与 cookies 相关的行为和复杂性，以便我们可以开始欣赏新的异步 API 的有用性。

虽然 cookies 现在被广泛使用，但是它们的接口已经成为复杂性和性能问题的来源。 [`document.cookie`](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie) 属性让我们读写与文档相关的 cookies。该文档充当 cookies 实际值的获取器和设置器。

然而，每当我们使用`document.cookie` getter 时，浏览器必须停止执行 JavaScript，直到它获得我们请求的 cookie 信息。当然，这可能会导致用户界面/UX 出现问题。下面我们来探讨一下读写 cookie 的操作，这样可以有更好的感受。

### 获取饼干

获取特定的 cookie 值似乎总是一件非常困难的事情。我们可以选择迭代`document.cookie`值，这是一个包含所有 cookies 的完整字符串。让我们看一个使用特定名称访问 cookie 值的示例:

```
document.cookie = "name1=value1";
document.cookie = "name2=value2";
document.cookie = "name3=value3";

// to get a cookie value with name value2
const cookieValue = document.cookie
  .split('; ')
  .find(row => row.startsWith('name2'))
  .split('=')[1];

console.log(cookieValue) // value2

```

通过[这个持久的堆栈溢出线程](https://stackoverflow.com/questions/4825683/how-do-i-create-and-read-a-value-from-cookie)，一个更通用的方法是:

```
const getCookie = (name) => {
  return document.cookie.split('; ').reduce((r, v) => {
    const parts = v.split('=')
    return parts[0] === name ? decodeURIComponent(parts[1]) : r
  }, '')
}

```

### 设置 cookies

设置 cookie 值的 API 似乎太过时了——为什么？好吧，在设置 cookie 数据之后，我们没有办法知道 cookie 是否被成功创建。

一个解决方法是使用一个类似上面的`getCookie`函数，循环遍历 cookie 字符串，找到我们为 cookie 数据设置的值。

```
document.cookie = "name=value";

```

设置 cookie 时的另一个问题是没有明确定义的机制来报告 cookie 存储错误。这当然是因为`document.cookie`是同步的。

## 异步 Cookie 存储 API 简介

厌倦了从`document.cookie`获取饼干的怪异方式？不确定您设置的 cookie 是否是实际创建的？本节将解决这些问题。

新的 Cookie Store API 旨在通过提供对`document.cookie`的异步替代并将这些 Cookie 暴露给[服务人员](https://blog.logrocket.com/every-website-deserves-a-service-worker/)来改善使用 Cookie 的所有缺点。该 API 为 cookie 管理提供了一种健壮且合理的方法。总的来说，API 使以下工作变得更加容易:

*   通过异步访问 cookies 来避免主线程上的过多活动
*   避免轮询 cookie，因为现在可以观察或监控对 cookie 的更改
*   从服务人员处访问 cookies

> **注意**:由于`document.cookie` API 的同步设计，cookies 以前是服务人员无法访问的。

服务工作者需要能够读取和修改他们范围内的页面可访问的 cookies，因为他们打算充当某种 HTTP 代理。此外，他们需要对会话状态的变化做出快速反应，这将有助于清除旧的或过时的缓存数据。

新的 Cookie Store API 包含了在文档和服务工作者中观察 Cookie 变化的方法([而不是轮询](https://github.com/WICG/cookie-store/blob/main/explainer.md#reacting-to-session-state-changes))。观察 cookie 变化的方法包括当 cookie 改变其值时激活服务工作者的能力。

## 现在使用异步 Cookie 存储

为了今天使用 Cookie Store API，我们可以启用 origin 试用标志。然而，在撰写本文时，origin 试用标志已经关闭，因为 API 目前正在根据社区反馈进行重大改进。origin 试验预计将在维护和改进完成后重新开放。你可以在这里找到更多细节[。](https://developers.google.com/web/updates/2018/09/asynchronous-access-to-http-cookies#status)

尽管如此，您仍然可以在本地进行尝试——可以在 CLI 上启用 API，这可以在 Chrome 中为当前会话全局启用 API。请参见下面的命令:

```
chrome --enable-blink-features=CookieStore

```

或者，我们可以启用`chrome://flags`中的`#enable-experimental-web-platform-features`标志。

### 使用 Cookie Store API 查询、修改和监控 Cookie 的介绍

#### 查询/读取 cookie

文档窗口和服务工作者通过[全局对象](https://wicg.github.io/cookie-store/#globals)上的 [`cookieStore`](https://wicg.github.io/cookie-store/#dom-window-cookiestore) 属性访问相同的查询 API。`[CookieStore](https://wicg.github.io/cookie-store/#cookiestore)`上的 [`get()`](https://wicg.github.io/cookie-store/#dom-cookiestore-get-options) 和`[getAll()](https://wicg.github.io/cookie-store/#dom-cookiestore-getall-options)`方法用于查询 cookies。不要忘记，这将返回一个承诺，让我们可以轻松地检查错误。

他们采用相同的论点，可以是:

*   一个名字
*   选项列表(这对于`getAll()`是可选的)

`get()`方法本质上是只返回第一个结果的`getAll()`的一种形式。这里有一个例子:

```
try {
    const cookie = await cookieStore.get('session_id');
    if (cookie) {
    console.log(`Found ${cookie.name} cookie: ${cookie.value}`);
} else {
    console.log('Cookie not found');
}
} catch (error) {
  console.error(`Cookie store error: ${error}`);
}

```

由`get()`和`getAll()`返回的对象包含了 cookie 存储中的所有相关信息，而不仅仅是旧的`document.cookie` API 中的[名称](https://wicg.github.io/cookie-store/#cookie-name)和[值](https://wicg.github.io/cookie-store/#cookie-value)。

#### 修改/写入 cookie

此外，文档和服务工作者都通过全局对象上的`cookieStore`属性访问相同的修改 API。使用`[set()](https://wicg.github.io/cookie-store/#dom-cookiestore-set)`方法创建或修改(写入)Cookies。

```
try {
    await cookieStore.set('opted_out', '1');
} catch (error) {
    console.error(`Failed to set cookie: ${error}`);
}

```

请记住，只有在`cookieStore.set`返回的承诺解决后，才能保证应用更改。

#### 删除 cookie

使用`[delete()](https://wicg.github.io/cookie-store/#dom-cookiestore-delete)`方法删除(过期)Cookies。

```
try {
  await cookieStore.delete('session_id');
} catch (error) {
  console.error(`Failed to delete cookie: ${error}`);
}

```

在幕后，删除 cookie 是通过将 cookie 的到期日期更改为过去来完成的，这仍然有效。

#### 监控 cookies

Cookie Store API 提供了另一种观察 Cookie 变化的方法，这种方法不需要轮询。从 JavaScript 访问 cookies 的一个流行应用程序是检测用户何时注销，并相应地更新 UI。

所有相关的 cookie 更改都会触发更改事件。注册`change`事件的简单示例如下所示

```
cookieStore.addEventListener('change', event => {
    console.log(`${event.changed.length} changed cookies`);
 for (const cookie in event.changed)
    console.log(`Cookie ${cookie.name} changed to ${cookie.value}`);
  for (const cookie in event.deleted)
    console.log(`Cookie ${cookie.name} deleted`);
});

```

这个 API 还被设计成允许浏览器出于性能原因批量改变事件。更多细节可以在这里找到[。](https://github.com/WICG/cookie-store/blob/main/explainer.md#the-change-events-api)

#### 延伸至服务人员

需要访问 cookies [的服务人员不能依靠同步的](https://github.com/w3c/ServiceWorker/issues/707)，阻塞了`document.cookie`接口。这是因为服务人员不能阻止事件循环，因为这会干扰其他事件的处理。

然而，Cookie 存储 API 是异步的，因此在服务人员中是允许的。请注意，服务人员有时可能需要 cookie 访问权限，例如:

*   确保仅在代表未到期会话中的用户身份验证的 cookies 仍然存在时，才显示或发送私人数据
*   为了确保一个用户帐户的数据不会在会话 cookie 更改后意外显示

在文档上下文和服务人员中，与 cookies 的交互方式是相同的。然而，在服务人员中观察 cookie 变化有点不同。这是因为叫醒一个服务人员可能相当昂贵；因此，需要对工作人员感兴趣的 cookie 更改进行明确的描述。

更多细节可在草案文件[这里](https://wicg.github.io/cookie-store/#service-worker-extensions)找到。另外，我们可以参考讲解文档中关于服务人员的[变更事件的部分。](https://github.com/WICG/cookie-store/blob/main/explainer.md#get-change-events-in-service-workers)

## 结论

这个令人兴奋的提议为以下文档事件提供了异步 cookie API:设置、删除和读取操作。也许最重要的是，它将允许服务人员读取 cookies，这在今天是不可能的；从文档中读取和写入 cookies 目前是一个同步的过程，这导致了缓慢而恼人的页面加载时间。对 cookies 的异步访问满足了这些需求。

API 包括一个`cookiechange`事件，它将唤醒服务人员。服务工作者中的 Cookie 更改事件是针对全局范围触发的，但是需要与服务工作者的注册相关联的显式订阅。更多详情[此处](https://github.com/WICG/cookie-store/blob/gh-pages/explainer.md#get-change-events-in-service-workers)。

该 API 还将有一个定义良好的机制来报告 cookie 存储错误。它还解决了一些已知的跨浏览器不兼容性以及规范和浏览器行为之间的差异。

目前，浏览器需要在每个 HTTP 请求中包含 cookie 的快照，这导致 cookie 更改在存储和网络堆栈之间传播。现代浏览器有高度优化的 cookie 存储实现，但我们永远无法使 cookie 像其他存储机制一样高效，因为其他存储机制不需要与网络堆栈对话。然而，该提议并不旨在改变网络层处理 cookie 的方式或 cookie 的一般安全模型

有关 Cookie Store API 的更多详细信息，您可以在此处查看提案文档/草案、[讲解材料](https://github.com/WICG/cookie-store/blob/main/explainer.md#introduction)，以及 [GitHub repo](https://github.com/WICG/cookie-store) 。关于 cookie 变更事件的细节可以在[这里](https://github.com/patrickkettner/cookie-change-events)找到。此外，您还可以将这个提议与另一个实现进行比较，这是一个简单、轻量级的 JavaScript API，用于处理浏览器 cookies。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.