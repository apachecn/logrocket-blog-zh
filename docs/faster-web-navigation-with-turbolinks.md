# 快速网络导航的 turbo links-log rocket 博客

> 原文：<https://blog.logrocket.com/faster-web-navigation-with-turbolinks/>

如果你是一名 Rails 开发人员，你很可能知道 [Turbolinks](https://github.com/turbolinks/turbolinks) 。Turbolinks 是一个灵活的轻量级 JavaScript 库，旨在让你更快地浏览网页。Turbolinks 通过在多页面应用程序中用普通的全页面加载代替部分加载来提高网页性能。

但是它到底是怎么做到的呢？Turbolinks 的工作原理是自动获取页面，交换它的 DOM 体，并将其与`head`混合。这使得库能够理解页面上发生了什么变化，而不必推断满载。

Turbolinks 拦截所有通常会将用户导向应用程序中某个页面的`<a href>`链接，并通过 AJAX 将它们作为请求发送出去，允许用接收到的内容替换正文。

## 特点和优势

Turbolinks 专注于自动导航优化，这意味着您的页面将自动变得更快，而不需要太多的更改。Turbolinks 将您访问的每个页面存储在一个内部缓存中，因此当您需要访问同一个页面时，Turbolinks 可以从缓存中检索它，以继续提高性能。您还可以在混合应用程序中调整您的页面以适应 iOS 和 Android，以确保导航控件能够跟进。

Turbolinks 库可以与 HTML 页面、Ruby on Rails、Node.js、Laravel、部分片段甚至 JSON 集成。它还吹嘘浏览器的适应性。Turbolinks 拦截点击以在幕后改变行为，同时还帮助浏览器了解如何通过[历史 API 来回切换。](https://developer.mozilla.org/en-US/docs/Web/API/History_API)

Turbolinks 还带来了避免从一个页面导航到另一个页面时著名的“闪烁”效应的直接好处。此外，由于 Turbolinks 依赖于 HTML5 历史 API，它既受所有现代浏览器的支持，也能够在旧浏览器中退化到正常导航，而不会导致任何副作用。

在本文中，我们将更深入地探索 Turbolinks 库，以了解它的优点和缺点，以及如何让您的应用程序受益。让我们开始吧！

## 设置和使用

如果您要使用普通的 HTML 和 JavaScript 应用程序，只需在您的`head`标签中导入 JavaScript 文件，就大功告成了。

因为这个库最初是针对 Rails 应用程序的，所以这是它被最广泛采用的语言。

要在任何 Ruby on Rails 应用程序中安装它，只需将相应的 gem 添加到您的`Gemfile`:

```
gem 'turbolinks', '~> 5.2.0'

```

在您运行了`bundle install`命令之后，Rails 会为您下载并安装 gem。

它还要求您向 JavaScript 清单文件添加以下注释:

```
//= require turbolinks

```

如果您的目标是将它与节点项目一起使用，安装就更简单了:

```
npm i turbolinks

```

在 JavaScript 包文件中，确保包含以下代码片段:

```
    var Turbolinks = require("turbolinks");
    Turbolinks.start();

```

#### 在服务器上重定向

值得注意的是，Turbolinks 在 Rails 环境中比在 Node 应用程序中工作得更顺畅，因为它源自那种环境。

以我们提到的自动重定向为例。当您通过`redirect_to`重定向到 Rails 中的一个新页面时，Turbolinks Rails 会自动为您的请求添加一个名为`Turbolinks-Location`的头。

当一个给定的请求来自一个没有服务器帮助的重定向流时，这个头对于 Turbolinks 识别是必不可少的。

另一方面，当处理基于节点的应用程序时，Turbolinks 除了接收这个头之外，没有办法识别这种类型的动作，这意味着您的客户机必须手动处理这个请求。

### JavaScript: `head` vs `body`方法

一般认为，出于性能考虑，`script`标记必须始终包含在`body`元素的底部，以便在加载和处理 JavaScript 文件之前呈现整个页面。

然而，Turbolinks 的要求恰恰相反，建议您将脚本放在`head`标记中。

这是因为 Turbolinks 获取并解析页面的主体，忽略了页面的头部。如果你把脚本留在主体中，那么每次导航发生时，相同的脚本将被一次又一次地加载。

为了避免阻塞页面加载并确保更快的第一页加载，您可以选择让脚本异步导入:

```
<script async src="..."></script>

```

或者，您可以添加大多数现代 web 浏览器都接受的 [defer](https://caniuse.com/script-defer) 属性，以确保脚本仅在页面加载后才被处理。

如果您的脚本经常改变，另一个好的做法是为您的`script`标签提供一个额外的`data-turbolinks-track`属性，这样它就可以在脚本包改变时强制整页重新加载。

### 分析库

就分析库而言，如谷歌分析(Google Analytics)，关注[不同元素](https://developers.google.com/analytics/devguides/collection/analyticsjs)很重要:

```
<!-- Google Analytics -->
<script>
window.ga=window.ga||function(){(ga.q=ga.q||[]).push(arguments)};ga.l=+new Date;
ga('create', 'UA-XXXXX-Y', 'auto');
ga('send', 'pageview');
</script>
<script async src='https://www.google-analytics.com/analytics.js'></script>
<!-- End Google Analytics -->

```

GA 通常要求用户加载一个`script`元素，后跟一个`analytics.js` JavaScript 文件。确保总是把前者放在身体里，后者放在脑袋里。通过这种方式，您可以保证每个导航事件都将为分析工具提供正确的变量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 默认进度条

好吧…但是当被请求的链接需要很长时间才能返回时会发生什么呢？这是一个常见的用例，可能有多种原因，其中一个更常见的原因是意外的网络延迟。

Turbolinks 通过启用默认进度条来解决这个问题。如果你的页面处理时间超过 500 毫秒，那么 Turbolinks 会显示带有内置 CSS 样式的进度条，你可以随意覆盖它。延迟也可以通过 [API](https://github.com/turbolinks/turbolinks) 定制。

最后用一个名为`turbolinks-progress-bar`的 CSS 类生成一个 div 元素。

### 自动缓存

假设您正在浏览一个应用程序，并且想要通过几次点击来访问同一个页面。为此，您可以转到浏览器历史记录，并单击上一页的链接。通常，你的应用程序会通过从服务器重新抓取整个页面来响应你的命令。

不再需要从后端请求所有东西不是更好吗？如果页面被缓存了呢？

现在想象一下，同一页面上显示的数据变化很快。返回历史记录并从缓存中检索页面是行不通的，因为缓存中的数据可能已经过时了。

对于这两种场景，Turbolinks 都提供了一种很好的方法，既可以直接选择并显示缓存的版本，也可以并行地从后端获取一个新的副本，并在请求完成时在屏幕上替换它。

由于其自动缓存机制，这一切都是可能的。

但是，如果您想在页面被缓存之前运行一些代码，它允许通过以下方式:

```
document.addEventListener("turbolinks:before-cache", function() {
  // your code here
});

```

如果您想禁用这个特性，只需在一个`meta`标签中添加一个`no-cache`指令:

```
<meta name="turbolinks-cache-control" content="no-cache">

```

### 定制请求

Turbolinks 的另一个很酷的特性是能够在提交请求之前拦截和定制请求。

例如，假设您希望为 Turbolinks 触发的每个请求发送一个任意的 id。为此，您只需映射以下事件侦听器:

```
document.addEventListener("turbolinks:request-start", function(event) {
  event.data.xhr.setRequestHeader("X-My-Custom-Request-Id", "MyId");
});

```

整洁，对不对？更多定制的可能性，请查看 [API 参考](https://github.com/turbolinks/turbolinks#api-reference)。

## 结论

Turbolinks 已经使用了相当长的时间，并且已经达到了很高的成熟水平。有了一个很棒的社区来支持它，它发展得很快，并帮助许多 web 开发者为他们的应用程序获得了更好的性能。

它的 [API 参考文献](https://github.com/turbolinks/turbolinks#api-reference)是必读的，所以请务必通读它，以了解这个小而强大的库的更多潜力，以及它改善导航时间和应用程序性能的能力。