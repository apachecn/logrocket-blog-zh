# 缓存头:前端开发人员实用指南

> 原文：<https://blog.logrocket.com/caching-headers-a-practical-guide-for-frontend-developers/>

开发人员和运营人员可以使用多个头文件来操作缓存行为。

旧规范和新规范混杂在一起:有许多设置需要配置，并且您会发现多个用户报告了不一致的行为。

在这篇文章中，我将重点解释不同的头如何影响浏览器缓存，以及它们如何与代理服务器相关联。

您将看到一个 Nginx 配置示例和运行 Express 的 Node.js 代码。最后，我们将研究在 React 中创建的流行服务如何服务于它们的 web 应用程序。

对于单页面应用程序，我感兴趣的是无限缓存 JavaScript、CSS、字体和图像文件，并防止缓存 HTML 文件和服务人员(如果有)。

这个策略是可行的，因为我的资产文件在文件名中有唯一的标识符。

您可以在 WebPack 中实现相同的配置，在资产的文件名中包含一个`[hash]`，或者更好的是一个`[chunkhash]`。这种技术被称为[长期缓存](https://developers.google.com/web/fundamentals/performance/webpack/use-long-term-caching)。

但是当你阻止重新下载时，你如何更新你的网站呢？保持更新网站的能力是永远不缓存 HTML 文件如此重要的原因。

每次你访问我的网站时，浏览器都会从服务器获取一份新的 HTML 文件，只有当有新的脚本 src 或链接 hrefs 时，浏览器才会从服务器下载新的资源。

### 缓存控制

```
Cache-Control: no-store
```

当浏览器被告知`no-store`时，它不应该存储任何关于请求的信息。您可以将它用于 HTML 和服务工作者脚本。

```
Cache-Control: public, no-cache

or

Cache-Control: public, max-age=0, must-revalidate
```

这两者是等效的，尽管有无缓存的名称，但允许提供缓存的响应，只是浏览器必须验证缓存是否是新的。

如果您正确地设置了 ETag 或 Last-Modified 头，以便浏览器可以验证它已经缓存了最新的版本，那么您和您的用户将会节省带宽。您可以将它用于 HTML 和服务工作者脚本。

```
Cache-Control: private, no-cache

or

Cache-Control: private, max-age=0, must-revalidate
```

以此类推，这两者也是等价的。公共和私有的区别在于共享缓存(例如 CDN)可以缓存公共响应，但不能缓存私有响应。

本地缓存(例如浏览器)仍然可以缓存私有响应。在服务器上呈现 HTML 时使用 private，呈现的 HTML 包含特定于用户的信息或敏感信息。

就框架而言，对于典型的 Gatsby 博客，您不需要设置 private，但是对于需要授权访问的页面，您应该考虑使用 Next.js。

```
Cache-Control: public, max-age=31536000, immutable
```

在本例中，浏览器将根据 max-age 指令(60 *60* 24*365)缓存一年的响应。

不可变指令告诉浏览器这个响应(文件)的内容不会改变，并且浏览器不应该通过发送 If-None-Match (ETag 验证)或 If-Modified-Since(Last-Modified 验证)来验证其缓存。

使用静态资产来支持长期缓存策略。

### 杂注和过期

```
Pragma: no-cache
Expires: <http-date>
```

Pragma 是在 [HTTP/1.0 规范](https://www.w3.org/Protocols/HTTP/1.0/spec.html#Pragma)中定义为请求头的旧头。

后来， [HTTP/1.1 规范](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.32)规定`Pragma: no-cache`响应应该作为`Cache-Control: no-cache`来处理，但是这并不是一个可靠的替代，因为它仍然是一个请求头。

我也一直使用`Pragma: no-cache`作为 OWASP [安全建议。](https://www.owasp.org/index.php/OWASP_Application_Security_FAQ#How_do_I_ensure_that_sensitive_pages_are_not_cached_on_the_user.27s_browser.3F)

包含`Pragma: no-cache`头是一种预防措施，可以保护不支持较新的缓存控制机制的遗留服务器，这些服务器可能会缓存您不打算缓存的内容。

有些人会说，除非你必须支持 Internet Explorer 5 或 Netscape，否则你不需要 Prama 或 Expires。归结起来就是支持遗留软件。

代理普遍理解 Expires 头，这给了它一点优势。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于 HTML 文件，我禁用 Expires 头，或者将其设置为过去的日期。对于静态资产，我通过 Nginx expires 指令将它与 Cache-Control 的 max-age 一起管理。

### ETags

```
ETag: W/"5e15153d-120f"

or

ETag: "5e15153d-120f"
```

ETags 是缓存验证的几种方法之一。ETag 必须唯一地标识资源，通常情况下，web 服务器会从资源内容中生成一个指纹。

当资源改变时，它会有不同的 ETag 值。

有两种类型的电子标签。弱 ETags 等式表示资源在语义上是等价的。强 ETags 验证表明资源是完全相同的。

您可以通过为弱 ETags 设置的“W/”前缀来区分这两者。

弱 ETags 不适合字节范围的请求，但是它们很容易动态生成。

实际上，您不打算自己设置 ETags 并让您的 web 服务器处理它们。

```
curl -I <http-address>
curl -I -H "Accept-Encoding: gzip" <http-address>
```

您可能会看到，当您从 Nginx 请求一个静态文件时，它会设置一个强 ETag。当启用了 gzip 压缩，但您没有上传压缩文件时，动态压缩会导致弱 ETags。

通过发送带有缓存资源的 ETag 的“If-None-Match”请求头，浏览器期望带有新资源的 200 OK 响应，或者空的 304 Not Modified 响应，这指示您应该使用缓存资源而不是下载新资源。

同样的优化也适用于 API GET 响应，并且不限于静态文件。

如果您的应用程序接收到大量的 JSON 有效负载，您可以配置您的后端，根据有效负载的内容来计算和设置 ETag(例如，使用 md5)。

在将其发送到客户端之前，将其与“如果不匹配”请求报头进行比较。

如果有匹配，而不是发送有效负载，发送 304 未修改，以节省带宽和提高 web 应用程序的性能。

### 最后修改的

```
Last-Modified: Tue, 07 Jan 2020 23:33:17 GMT
```

最后修改响应报头是另一种高速缓存控制机制，并且使用最后修改日期。Last-Modified 标头是更准确的 ETags 的后备机制。

通过发送具有缓存资源的最后修改日期的“If-Modified-Since”请求报头，浏览器期望具有较新资源的 200 OK 响应或者空的 304 Not Modified 响应，这指示应该使用缓存的资源而不是下载新的资源。

### 排除故障

当您设置标头然后测试配置时，请确保您在网络方面靠近服务器。我的意思是，如果您的服务器已经 Dockerized，那么运行容器并在本地测试它。

如果您配置了一个虚拟机，那么 ssh 到该虚拟机并在那里测试头。如果您有一个 Kubernetes 集群，请启动一个 pod 并从集群中调用您的服务。

在生产设置中，您将使用负载平衡器、代理和 cdn。在这些步骤中的每一步，您的头都可能被修改，所以知道您的服务器首先发送了正确的头，调试会容易得多。

如果您启用了电子邮件地址混淆或自动 HTTPS 重写，意外行为的一个示例可能是 Cloudflare 删除 ETag 标头。

祝你通过改变你的服务器配置来调试它好运！在 Cloudflare 看来，这种行为[被很好地记录了下来](https://support.cloudflare.com/hc/en-us/articles/218505467-Using-ETag-Headers-with-Cloudflare)并且非常有意义，因此了解您的工具是您的责任。

```
Cache-Control: max-age=31536000
Cache-Control: public, immutable
```

在这篇文章的前面，我在代码片段的标题之间加了“or”来表示这是两个不同的例子。有时，您可能会注意到 HTTP 响应中有多个相同的头。

这意味着两个标题都适用。一些代理服务器可以一路合并报头。上面的例子相当于:

```
Cache-Control: max-age=31536000, public, immutable
```

使用`curl`将会给你最一致的结果和在多种环境中运行的便利性。

如果您决定使用 web 浏览器，请确保在调试缓存问题时查看服务人员。服务工人调试是另一篇文章的复杂主题。

要解决缓存问题，请确保在 DevTools 应用程序选项卡中启用了绕过服务工作器。

### Nginx 配置

既然您已经了解了不同类型的缓存头的作用，那么是时候将您的知识付诸实践了。

下面的 Nginx 配置将服务于一个支持长期缓存的单页面应用程序。

```
gzip on;
gzip_disable "msie6";
gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
```

首先，我为最有利于单个页面应用程序的内容类型启用了 gzip 压缩。有关每个可用 gzip 设置的更多细节，请参阅 [nginx gzip 模块](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)文档。

```
location ~* (\.html|\/sw\.js)$ {
  expires -1y;
  add_header Pragma "no-cache";
  add_header Cache-Control "public";
}
```

我想用`/sw.js`将所有 HTML 文件匹配在一起，这是一个服务工作者脚本。

都不应该被缓存。Nginx `expires`指令设置为负值，设置超过`Expires`头，并添加一个额外的`Cache-Control: no-cache`头。

```
location ~* \.(js|css|png|jpg|jpeg|gif|ico|json)$ {
  expires 1y;
  add_header Cache-Control "public, immutable";
}
```

我想最大限度地缓存我所有的静态资产，包括 JavaScript 文件、CSS 文件、图像和静态 JSON 文件。如果您托管您的字体文件，您也可以添加它们。

```
location / {
  try_files $uri $uri/ =404;
}

if ($host ~* ^www\.(.*)) {
  set $host_without_www $1;
  rewrite ^(.*) https://$host_without_www$1 permanent;
}
```

这两项与缓存无关，但它们是 Nginx 配置的重要组成部分。

因为现代的单页面应用程序支持漂亮 URL 的路由，而我的静态服务器不知道它们。我需要为每个不匹配静态文件的路由提供一个默认的`index.html`。

我还对从带`www.`的 URL 到不带`www`的 URL 的重定向感兴趣。您可能不需要最后一个，因为您的应用程序已经由您的服务提供商为您托管了。

### 快速配置

有时我们无法使用 Nginx 这样的反向代理服务器来提供静态文件。

可能是因为您的无服务器设置/服务提供商限制您使用一种流行的编程语言，并且性能不是您主要关心的问题。

在这种情况下，您可能希望使用像 Express 这样的服务器来提供静态文件。

```
import express, { Response } from "express";
import compression from "compression";
import path from "path";

const PORT = process.env.PORT || 3000;
const BUILD_PATH = "public";

const app = express();

function setNoCache(res: Response) {
  const date = new Date();
  date.setFullYear(date.getFullYear() - 1);
  res.setHeader("Expires", date.toUTCString());
  res.setHeader("Pragma", "no-cache");
  res.setHeader("Cache-Control", "public, no-cache");
}

function setLongTermCache(res: Response) {
  const date = new Date();
  date.setFullYear(date.getFullYear() + 1);
  res.setHeader("Expires", date.toUTCString());
  res.setHeader("Cache-Control", "public, max-age=31536000, immutable");
}

app.use(compression());
app.use(
  express.static(BUILD_PATH, {
    extensions: ["html"],
    setHeaders(res, path) {
      if (path.match(/(\.html|\/sw\.js)$/)) {
        setNoCache(res);
        return;
      }

      if (path.match(/\.(js|css|png|jpg|jpeg|gif|ico|json)$/)) {
        setLongTermCache(res);
      }
    },
  }),
);

app.get("*", (req, res) => {
  setNoCache(res);
  res.sendFile(path.resolve(BUILD_PATH, "index.html"));
});

app.listen(PORT, () => {
  console.log(`Server is running http://localhost:${PORT}`);
});
```

这个脚本模仿了我们的 Nginx 配置正在做的事情。使用压缩中间件启用 gzip。

Express 静态中间件为你设置`ETag`和`Last-Modified`头。我们必须自己处理发送`index.html`的问题，以防请求不匹配任何已知的静态文件。

### 例子

最后，我想探索流行的服务是如何利用缓存头的。

我分别检查了 HTML 和 CSS 或 JavaScript 文件的头。我还查看了服务器头(如果有的话),因为它可能会让我们对底层基础设施有一个令人兴奋的了解。

### 推特

Twitter 非常努力地不让他们的 HTML 文件出现在你的浏览器缓存中。看起来 Twitter 正在使用 Express 为我们提供 React 应用程序的一个`<div id="react-root">`入口。

不管什么原因，Twitter 使用了`Expiry`头，而`Expires`头不见了。

我已经查过了，但是我没有发现任何有趣的东西。

可能是打印错误吗？如果知道，请留言评论。

```
cache-control: no-cache, no-store, must-revalidate, pre-check=0, post-check=0
expiry: Tue, 31 Mar 1981 05:00:00 GMT
last-modified: Wed, 08 Jan 2020 22:16:19 GMT (current date)
pragma: no-cache
server: tsa_o
x-powered-by: Express
```

Twitter 没有 CSS 文件，可能正在使用一些 CSS-in-JS 解决方案。看起来像是运行在 Amazon ECS 上的容器化应用程序在服务静态文件。

```
etag: "fXSAIt9bnXh6KGXnV0ABwQ=="
expires: Thu, 07 Jan 2021 22:19:54 GMT
last-modified: Sat, 07 Dec 2019 22:27:21 GMT
server: ECS (via/F339)
```

### 照片墙

Instagram 也不希望你的浏览器缓存 HTML，使用设置为 2000 年初的有效 Expires 头；任何早于当前日期的日期都是有效的。

```
last-modified: Wed, 08 Jan 2020 21:45:45 GMT
cache-control: private, no-cache, no-store, must-revalidate
pragma: no-cache
expires: Sat, 01 Jan 2000 00:00:00 GMT
```

Instagram 提供的 CSS 和 JavaScript 文件都支持长期缓存，并且有一个 ETag。

```
etag: "3d0c27ff077a"
cache-control: public,max-age=31536000,immutable
```

### 纽约时报

《纽约时报》也在使用 React，并以服务器端渲染页面的形式提供文章。最后修改日期似乎是一个真实的日期，不会随着每个请求而改变。

```
cache-control: no-cache
last-modified: Wed, 08 Jan 2020 21:54:09 GMT
server: nginx
```

纽约时报的资产也会被缓存很长时间，同时提供 Etag 和 Last-Modified date。

```
cache-control: public,max-age=31536000
etag: "42db6c8821fec0e2b3837b2ea2ece8fe"
expires: Wed, 24 Jun 2020 23:27:22 GMT
last-modified: Tue, 25 Jun 2019 22:51:52 GMT
server: UploadServer
```

### 结论

我创建这个部分是为了组织我的知识，但我也打算用它作为配置当前和未来项目的备忘单。我希望你喜欢阅读，也发现它很有用！

如果你有任何问题或想提出改进建议，请在下面留下评论，我很乐意回答！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)