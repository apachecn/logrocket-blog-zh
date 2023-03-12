# 为什么大家不喜欢 PHP？- LogRocket 博客

> 原文：<https://blog.logrocket.com/why-dont-people-like-php/>

PHP 是超文本预处理器上的递归播放器，是一种面向 web 开发的通用脚本语言。尽管有些人认为 PHP 已经过时，但它仍然在互联网上使用，例如，包括脸书、维基百科、Slack 和 Etsy 在内的大公司。

随着 JavaScript 的兴起以及 React、Angular 和 Vue 等框架和库的激增，许多新开发人员开始转向这些工具而不是 PHP，原因有几个。受这个 [Twitter 帖子](https://twitter.com/wesbos/status/1442509273305690114)的启发，我们将从特性、功能、性能和协作方面讨论 PHP 和 JavaScript。我们开始吧！

*向前跳转:*

## PHP 简史

PHP 最初由[拉斯马斯·勒德尔夫于 1994 年](https://en.wikipedia.org/wiki/Rasmus_Lerdorf)开发，随着时间的推移变得越来越强大，从 2011 年开始定期发布，并引入了支持类型、JSON、命令行实用程序和即时编译等功能。作为一种灵活的服务器端技术，PHP 适用于各种各样的用例。

鉴于 JavaScript 在 21 世纪初的状态，PHP 被如此广泛采用的原因就显而易见了。随着 JavaScript 作为一种语言的成熟以及其他库和框架的引入，开发人员开始寻求用 JavaScript 代替 PHP 来满足他们的 web 开发需求。

PHP 提供了许多 JavaScript 自带的特性，比如处理条件、循环、类型、模块性和对象。PHP 的一个主要障碍是 JavaScript 的门槛较低。例如，您可以只使用浏览器和您喜欢的文本编辑器来编写和运行 JavaScript。然而，要使用 PHP 这种脚本语言，你必须使用解释器和运行时。

## 实现 PHP

有几种不同的方法来实现 PHP，但是最流行的一种是 [Zend Engine](https://en.wikipedia.org/wiki/Zend_Engine) 。要使用 Zend 引擎，您需要一台运行 PHP 编译器的机器，然后您可以从托管位置为 Zend 引擎提供服务。

使用 JavaScript，您可以使用 Node.js 运行时处理前端和后端开发。前端项目直接在客户端浏览器中运行 JavaScript，这意味着 JavaScript 代码可以从 CDN 或静态文件位置提供，因此您不需要一个成熟的服务器来托管您的内容。

类似地，使用 npm，您的后端项目也会留下非常少的足迹；您可以在服务器上运行它们，也可以在不同的云提供商处使用无服务器功能。

## 表演

JavaScript 和 PHP 可以执行相似的功能，但是在幕后，你会看到它们真正的不同之处。

JavaScript 是事件驱动的，具有允许异步活动的事件循环。JavaScript 可以一次运行代码的多个部分，防止 JavaScript 程序的主线程被阻塞，而不是同步运行 JavaScript 代码，一次运行一行。此外，像回调函数和事件循环这样的特性允许您同时处理多个活动。

另一方面，PHP 是同步的，但允许多线程。PHP 要求每一行代码都要按顺序运行，理论上这会降低程序的速度。然而，有些人可能会认为 PHP 的同步特性使得它更容易使用。

当您考虑 JavaScript 的非阻塞实现的好处时，我会说 JavaScript 在这方面优于 PHP。

## 集成

JavaScript 可以与许多不同的 web 技术结合，包括 HTML、XML、Ajax，甚至带有 React 组件的 JSX。有了 npm 和 Node.js 运行时，JavaScript 开发人员就有了大量可用的软件包。领先的前端框架和库，如 Angular、React 和 Vue，都有自己的特性，这些特性更多地建立在这些集成之上。

另一方面，你可以把 PHP 和 HTML 整合起来，HTML 是用 WordPress 和 Drupal 这样的 CMS 平台推广的。此外，PHP 与传统的 LAMP 堆栈配合得很好。与 SQL 的集成是通过 MySQLi 的扩展实现的。

一般来说，JavaScript 和 PHP 都可以在任何带有网络浏览器的设备上运行。此外，JavaScript 具有 Node.js 运行时的后端功能。有几个 npm 包和框架允许您构建可以在服务器端运行的 JavaScript APIs。

JavaScript 和 PHP 都可以适应不同的需求，但是特别的是，JavaScript 不需要太多的额外工作就可以在各种平台上很好地工作。

如果您想在网页中使用 JSON 数据或注入代码，设置 PHP 需要一个后端，以及必要的标记来包装您的语句。

## 特征

JavaScript 和 PHP 提供了许多相同的内置特性，包括:

1.  条件式
2.  环
3.  转换，即字符串到整数等。
4.  模块化，即功能和/或方法
5.  目标

PHP 和 JavaScript 的区别在于各自实现其行为的方式。利用 JavaScript 的非阻塞特性，您的程序可以具有低延迟并同时处理许多操作。此外，你可以用承诺甚至是观察来控制行为。

JavaScript 还包括范围的概念，允许您控制变量和数据点。当你有一个包含许多模块或功能的大型项目时，作用域是非常强大的。

我们用一个例子来试试。`helloWorld`函数有一个局部变量`message`，它只在`helloWorld`函数中可用。当您试图在函数之外访问变量时，您将得到一个未定义的值，这允许您在多个地方使用相同的变量名，只要您尊重该变量的范围和使用，这是 JavaScript 的一个主要优势。

```
function helloWorld() {
  const message = "hello this is a locally scoped variable";
  console.log(message);
}

// this will output "hello this is a locally scoped variable"
helloWorld();
// this will output an error saying that the message variable is not defined
console.log(message);

```

对于 PHP，通常使用`$`作为变量名，并遵循类似于 JavaScript 的结构。PHP 还允许您使用类似于`echo`的东西写出如下代码片段所示的值:

```
function helloMessage(string $messageValue): string
{
  return 'hello '.$messageValue;
}

echo helloMessage('first message");

```

## 合作

PHP 和 JavaScript 都有强大的社区支持。开发人员在开始使用或解决问题时，可以求助于网上无数的博客和视频。所有主要的 JavaScript 框架和库，包括 Angular、React 和 Vue，都是开源的，并且有开发团队定期更新他们的库。PHP 也是开源的，很多 GitHub 项目都包含 PHP。

从开发者体验来说，JavaScript 一般需要更多的背景知识。要使用 JavaScript，您通常必须了解事件循环以及 CSS 和 HTML 等其他 web 技术。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这些技术都是运行互联网的框架的一部分，因此，学习它们并不一定是一项糟糕的投资。一旦您熟悉了 JavaScript，您的项目就有了更多的选择。

另一方面，PHP 要简单得多。它不仅是同步的，而且它的模型遵循更传统的后端技术，如 Java 和 c。然而，您仍然需要一个服务器或一些运行 PHP 解释器的环境，通常还有 LAMP 堆栈的其余部分。

就协作而言，我会说在 JavaScript 项目中与团队合作更容易，主要是因为您用来完成工作的工具很少。随着在线编码平台和环境的出现，这变得更加容易。如果你想在一个 PHP 项目上合作，你必须在运行任何代码之前先建立一个工作环境。

## JavaScript 和 PHP:哪个更好？

JavaScript 和 PHP 都可以满足广泛的需求。PHP 仍然被广泛使用的事实表明，它对许多网站来说有着可爱的价值。争论不一定是哪个更好，而是哪个更适合你的需求。

如果您的团队已经在 LAMP 堆栈中工作，PHP 可能最适合您。如果你的团队不太了解 LAMP 栈，也没有多少后端经验，那么 JavaScript 是一个不错的选择。

我个人喜欢在项目中使用 JavaScript。我喜欢我可以轻松地将技能从 React 转移到后端 Node.js API。在浏览器中直接运行代码也很棒，可以很容易地跨不同平台运行 JavaScript。

许多开发人员仍然喜欢 PHP，并为它找到了很好的用例。这实际上取决于你的个人团队和项目。

## 包扎

在这篇文章中，我们讨论了 PHP 和 JavaScript 的高级别，回顾了各自的优缺点。我希望你喜欢这篇文章，并且对每一篇都有更好的理解。归根结底，您选择的技术取决于对您和您的项目起作用的技术。两者都是伟大的技术，为许多人提供了巨大的价值。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.