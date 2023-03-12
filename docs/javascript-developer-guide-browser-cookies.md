# 浏览器 cookies 的 JavaScript 开发者指南

> 原文：<https://blog.logrocket.com/javascript-developer-guide-browser-cookies/>

如果您是一名 JavaScript 开发人员，并且想了解浏览器 cookies 以及可以用它们做什么，那么您来对地方了。本文将介绍浏览器 cookies 的工作原理，如何从客户端和服务器端访问和操作它们，以及如何使用它们的属性来控制它们在浏览器中的可见性。

## 什么是 cookies，它们是如何工作的？

浏览器 cookie 是存储在浏览器上的一小段数据，由客户端 JavaScript 或服务器在 HTTP 请求期间创建。然后，浏览器可以将该 cookie 和请求一起发送回同一服务器，和/或让网页的客户端 JavaScript 在用户再次访问该页面时访问该 cookie。

Cookies 通常用于会话管理、个性化(如主题或类似设置)以及跨网站跟踪用户行为。

曾几何时，cookies 被用于各种客户端存储，但这种方法存在一个问题。

由于所有域 cookies 都随每个请求一起发送到该域上的服务器，因此它们会显著影响性能，尤其是在低带宽移动数据连接的情况下。出于同样的原因，浏览器通常还会为特定域设置 cookie 大小和 cookie 数量的限制(通常每个域 4kb 和 20 个 cookie)。

对于现代网络，我们有了新的[网络存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)(`localStorage`和`sessionStorage`)用于客户端存储，它允许浏览器以键值对的形式存储客户端数据。

因此，如果您只想在客户端持久化数据，最好使用 API，因为它们比 cookies 更直观、更易于使用，并且可以存储更多数据(通常高达 5MB)。

## 设置和访问 cookies

您可以通过服务器和客户端设置和访问 cookies。Cookies 也有各种属性，这些属性决定了在哪里以及如何访问和修改它们。但是，首先，让我们看看如何在客户机和服务器上访问和操作 cookies。

### 客户端(浏览器)

每当你访问一个网站时，下载并在浏览器上执行的 JavaScript 通常被称为客户端 JavaScript。它可以通过`Document`属性`cookie`访问 cookies。

这意味着您可以使用`document.cookie`读取当前位置可访问的所有 cookies。它给出了一个字符串，其中包含一个以分号分隔的 cookies 列表，格式为`key=value`:

```
const allCookies = document.cookie;
// The value of allCookies would be something like
// "cookie1=value1; cookie2=value2"

```

同样，要设置 cookie，我们必须设置`document.cookie`的值。设置 cookie 也是用一个用分号分隔属性的`key=value`格式的字符串来完成的:

```
document.cookie = "hello=world; domain=example.com; Secure";
// Sets a cookie with key as hello and value as world, with
// two attributes SameSite and Secure (We will be discussing these
// attributes in the next section)

```

为了不使你感到困惑，上面的声明没有覆盖任何现有的 cookies 如果同名的 cookie 已经存在，它就创建一个新的 cookie 或者更新现有 cookie 的值。

我知道这不是你见过的最干净的 API。这就是为什么我建议使用一个包装器或者像 [js-cookie](https://github.com/js-cookie/js-cookie) 这样的库来处理客户端 cookie:

```
Cookies.set('hello', 'world', { domain: 'example.com', secure: true });
Cookies.get('hello'); // -> world

```

它不仅为 cookies 上的 CRUD 操作提供了一个干净的 API，[它还支持 TypeScript](https://blog.logrocket.com/a-simple-guide-for-migrating-from-javascript-to-typescript/) ，帮助您避免属性的任何拼写错误。

### 计算机网络服务器

服务器可以通过 HTTP 请求的响应和请求头来访问和修改 cookies。每当浏览器向服务器发送 HTTP 请求时，它会将所有相关的 cookies 附加到带有`cookie`头的站点上。

检查您使用的几乎任何 web 应用程序的请求头，您会发现发送到服务器的 cookies，请求头是以分号分隔的字符串。

![Cookies Sent Server Request Headers](img/d222d6378f49d90aae64939a154a74ce.png)

Cookies being sent to the server with request headers

然后，您可以从请求头中读取服务器上的这些 cookies。例如，如果您在服务器上使用 [Node.js，您可以从请求对象中读取 cookies，如下面的代码片段，并获得分号分隔的`key=value`对，类似于我们在上一节中看到的:](https://blog.logrocket.com/tag/node/)

```
http.createServer(function (request, response) {
  var cookies = request.headers.cookie;
  // "cookie1=value1; cookie2=value2"
  ...
}).listen(8124);

```

类似地，要设置 cookie，您可以添加一个带有响应头的`Set-Cookie`头，响应头采用`key=value`格式，属性用分号分隔(如果有的话)。在 Node.js 中，您可以这样做:

```
response.writeHead(200, {
   'Set-Cookie': 'mycookie=test; domain=example.com; Secure'
});

```

此外，您可能不会使用普通的 Node.js 相反，您可以将它与 Express.js 这样的 web 框架一起使用。

通过添加中间件，使用 [Express 访问和修改 cookies 变得更加容易。对于读取，添加](https://blog.logrocket.com/express-middleware-a-complete-guide/)`[cookie-parser](https://www.npmjs.com/package/cookie-parser)`以一个带有 [`req.cookies`](https://expressjs.com/en/4x/api.html#req.cookies) 的 JavaScript 对象的形式获取所有的 cookies。您也可以使用 Express 自带的内置`res.cookie()`方法来设置 cookies:

```
var express = require('express')
var cookieParser = require('cookie-parser')

var app = express()
app.use(cookieParser())

app.get('/', function (req, res) {
  console.log('Cookies: ', req.cookies)
  // Cookies: { cookie1: 'value1', cookie2: 'value2' }

  res.cookie('name', 'tobi', { domain: 'example.com', secure: true })
})

app.listen(8080)

```

是的，TypeScript 支持所有这些，所以服务器上也不会出现打字错误。

## JavaScript cookie 属性

现在你知道如何设置和访问 cookie 了，让我们深入研究一下 cookie 的属性。

除了名称和值之外，cookie 还有控制各种方面的属性，包括 cookie 安全性、cookie 生存期以及在浏览器中访问它们的位置和方式。

### `Domain`属性

根据 MDN，[`Domain`属性](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#define_where_cookies_are_sent)告诉浏览器哪些主机被允许访问 cookie。如果未指定，则默认为设置 cookie 的主机。

因此，当使用客户端 JavaScript 访问 cookie 时，只能访问与 URL 栏中的 cookie 具有相同域的 cookie。

类似地，只有与 HTTP 请求的域共享同一个域的 cookies 才与请求头一起发送到服务器。

请记住，拥有这个属性并不意味着您可以为任何域设置 cookies，因为这显然会带来巨大的安全风险。(想象一下，当用户访问他们的网站时，evil.com 的攻击者修改了 awesome.com 网站的 cookies。)

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

所以，这个属性存在的唯一原因是减少域的限制，使 cookie 在子域上可访问。

例如，如果您当前的域是`abc.xyz.com`，并且您在设置 cookie 时没有指定域属性，那么它将默认为`abc.xyz.com`，并且 cookie 将只限于该域。

但是，您可能希望相同的 cookie 也可以在其他子域上使用。如果是这种情况，设置`Domain=xyz.com`使其在其他子域如`def.xyz.com`和主域`xyz.com`上可用。

![Domain Attribute](img/bc223ea5b03c8652a734f7b729688439.png)

Domain attribute allowing cookies to be accessed via subdomains

但是，这并不意味着您可以为 cookies 设置任何域值；像`.com`这样的顶级域名(TLD)和像`.co.uk`这样的伪 TLD 会被安全的浏览器忽略。

最初，浏览器供应商在内部维护这些公共域的列表，这不可避免地导致跨浏览器的不一致行为。

为了解决这个问题，Mozilla 基金会启动了一个名为[公共后缀列表](https://publicsuffix.org/)的项目，该项目记录了所有的公共域名，并在供应商之间共享。

这个列表还包括像`github.io`和`vercel.app`这样的服务，这些服务限制任何人为这些域设置 cookie，使得`abc.vercel.app`和`def.vercel.app`被视为拥有自己独立 cookie 集的独立站点。

### `Path`属性

`Path`属性指定请求 URL 中的路径，该路径必须存在才能访问 cookie。除了将 cookies 限制在域中，您还可以通过 path 来限制它们。path 属性为`Path=/store`的 cookie 只能在`/store`路径及其子路径、`/store/cart`、`/store/gadgets`等路径上访问。

### `Expires`属性

属性设置 cookies 被销毁的截止日期。当你使用 cookie 来检查用户是否看到了插播广告时，这就派上了用场；您可以将 cookie 设置为一个月后过期，这样广告就可以在一个月后再次显示。

你猜怎么着？它还通过[将`[Expires]`日期设置为过去的](https://stackoverflow.com/questions/5285940/correct-way-to-delete-cookies-server-side/53573622#53573622)来删除 cookies。

### `Secure`属性

带有`Secure`属性的 cookie 只通过安全的 HTTPS 协议发送到服务器，从不通过 HTTP 协议发送(除了在本地主机上)。这有助于防止中间人通过不安全的连接访问 cookie 来攻击 T2。

除非你是通过一个不安全的 HTTP 连接([你不应该](https://web.dev/why-https-matters/))来服务你的网站，否则你应该总是对你所有的 cookies 使用这个属性。

### `HttpOnly`属性

顾名思义，这个属性允许 cookies 只能通过服务器访问。因此，只有服务器可以通过响应头来设置它们。如果它们和每个后续请求的头一起发送到服务器，它们将无法通过客户端 JavaScript 访问。

![HTTPOnly attribute example](img/e6f9c38d26a45235bfe0c919b8463a44.png)

HTTPOnly attribute example

这可以部分地帮助保护带有敏感信息的 cookie，如身份验证令牌，免受 [XSS 攻击](https://owasp.org/www-community/attacks/xss/)，因为任何客户端脚本都无法读取 cookie。但是，请记住，它不能保证完全安全，免受 XSS 的攻击。

这是因为如果攻击者可以在您的网站上执行第三方脚本，他们可能无法访问 cookie，而是可以直接执行对您的服务器的任何相关 API 请求，从而导致浏览器轻松地将您的安全`HttpOnly`cookie 与请求头附加在一起。

假设您的一个用户访问了一个页面，黑客将他们的恶意脚本注入到您的网站中。他们可以使用该脚本执行任何 API，并在用户不知情的情况下代表用户行事。

因此，当人们说`HttpOnly` cookies 导致 XSS 攻击无效时，他们并不完全正确，因为如果黑客可以在你的网站上执行脚本，你就有[更大的问题要处理。](https://twitter.com/benawad/status/1264974111165550593?lang=en)有办法让[阻止](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)的 XSS 攻击，但它们不在本文讨论范围之内。

### `SameSite`属性

在本文的开头，我们看到了特定域的 cookies 是如何随每个请求一起发送到相应域的服务器的。

这意味着，如果您的用户访问一个第三方站点，并且该站点向您的域上的 API 发出请求，那么您的域的所有 cookies 将随该请求一起发送到您的服务器。根据您的使用情况，这可能是好事也可能是坏事。

在 YouTube 嵌入这样的情况下，这可能是一个福音。

例如，如果在浏览器上登录 YouTube 的用户访问包含 YouTube 嵌入内容的第三方网站，他们可以点击嵌入视频上的**稍后观看**按钮，并将其添加到他们的库中，而无需离开当前网站。

这是因为浏览器将 YouTube 的相关 cookies 发送到服务器，以确认它们的身份验证状态。这些类型的 cookie 也称为第三方 cookie。

这可能导致的诅咒基本上是在你不希望它发生的任何其他用例中。

例如，如果用户访问一个恶意网站，该网站向您的服务器发出请求，如果服务器没有正确验证该请求，那么攻击者可以在用户不知情的情况下代表用户采取行动。这基本上是一次 [CSRF 攻击](https://owasp.org/www-community/attacks/csrf)。

为了帮助防止这种类型的攻击，[IETF 在 2016 年](https://datatracker.ietf.org/doc/html/draft-west-first-party-cookies-07)提出了 cookies 中一个名为`SameSite`的新属性。该属性允许您将 cookies 限制在第一方上下文中，从而有助于解决上述问题。

这意味着只有当您的 URL 栏中的域与 cookie 的域匹配时，您才应该将 cookie 附加到请求中。

![Same Site Attribute Strict](img/058695921e06a09ba74f018e4c563b5f.png)

SameSite attribute with Strict

您可以为`SameSite`属性设置三种类型的值:`Strict`、`Lax`和`None`。

当设置为`Strict`时，您的 cookies 将仅在第一方上下文中发送。

`Lax`值比`Strict`的限制稍微少一点，因为它发送带有顶级导航的 cookie，这意味着 cookie 随页面请求一起发送到服务器。

当用户从谷歌搜索结果中点击你的网站或者通过一个缩短的 URL 被重定向时，这是很有帮助的。

然后`None`，顾名思义，允许您通过发送每个请求的相关 cookie 来创建第三方 cookie。然而，这与网站用户无关，就像我们之前讨论的 YouTube 嵌入那样。

在 web.dev 上的这篇文章中，你可以了解更多关于[`SameSite`cookie 以及它们在现代浏览器](https://web.dev/samesite-cookies-explained/)中的表现

## 隐私和第三方 cookies

我们在上一节中简要解释了第三方 cookies。简而言之，任何由您当前所在网站之外的网站设置的 cookie 都是第三方 cookie。

你可能也听说过臭名昭著的第三方 cookies 是如何在网站间追踪你并显示个性化广告的。现在你知道了饼干的规则，你大概能猜到他们会怎么做。

基本上，每当网站使用脚本或通过 IFrame 为第三方服务添加嵌入对象时，第三方服务可以为该服务的域设置一个带有 HTTP 响应头的 cookie。

这些 cookies 还可以在使用相同的第三方服务嵌入的网站上跟踪你。最后，这些第三方服务通过 cookies 识别您的身份来收集数据，然后可以向您显示个性化广告。

为了解决这个问题，许多浏览器，如 Firefox，开始通过一种叫做增强跟踪保护(ETP)的新功能来阻止流行的第三方跟踪 cookies。虽然这可以保护用户免受 3000 种最常见的已识别跟踪器的攻击，但它的保护依赖于完整的最新列表。

浏览器目前正计划最终摆脱第三方 cookies。Firefox 正在实现状态分区，这将导致每个第三方 cookie 对每个网站都有一个单独的容器。

现在，你可能认为除了跟踪之外，像状态划分这样的东西也会破坏第三方 cookies 的合法用例，你是对的。

因此，浏览器正在开发一个名为[存储访问](https://privacycg.github.io/storage-access/)的新 API。该 API 允许第三方上下文通过询问用户权限来请求第一方存储访问，这使得服务可以对其第一方状态进行非分区访问。你可以在 Mozilla 的博客上阅读关于它如何在[工作的更多细节。](https://hacks.mozilla.org/2021/02/introducing-state-partitioning/)

## 结论

我希望这篇文章能帮助您了解一些关于 JavaScript cookies 的新知识，并简要概述了它们是如何工作的，如何从服务器和客户端访问和修改它们，以及最后，cookie 的不同属性如何让您控制它们在浏览器中的可见性和生命周期。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.