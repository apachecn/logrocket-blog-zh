# jQuery 的历史和遗产

> 原文：<https://blog.logrocket.com/the-history-and-legacy-of-jquery/>

[jQuery](https://jquery.com/) 是世界上使用最广泛的JavaScript 库。web 开发社区在 21 世纪后期接受了它，创建了一个丰富的网站、插件和框架生态系统，在幕后使用 jQuery。

但是在过去的几年中，jQuery 作为 web 开发的头号工具的地位已经下降。让我们来看看 jQuery 为什么会流行，为什么它已经有点失宠，以及它在什么时候仍然是现代网站的一个好选择。

## jQuery 简史

John Resig 在 2005 年开发了 jQuery 的初始版本，[在 2006 年的 BarCampNYC 发布了它。在](https://johnresig.com/blog/barcampnyc-wrap-up/)[原 jQuery 网站](https://web.archive.org/web/20060203025710/http://jquery.com/)上，他写道:

> jQuery 是一个 Javascript 库，它将这句格言铭记于心:**编写 Javascript 代码应该很有趣。jQuery 通过提取常见的、重复的任务，去掉所有不必要的标记，让它们变得简短、智能和易于理解来实现这个目标。**

jQuery 有两个主要的价值主张。第一个是为操作网页提供一个符合人体工程学的 API。特别是，它提供了选择元素的强大方法。

除了根据 id 或类选择元素，jQuery 还支持复杂的表达式，比如根据元素与其他元素的关系选择元素:

```
// Select every item within the list of people within the contacts element
$('#contacts ul.people li');
```

选择引擎最终被提取到它自己的名为 [Sizzle](https://sizzlejs.com/) 的库中。

第二个卖点是它抽象出了浏览器之间的差异。那时候，很难写出能在所有浏览器上稳定运行的代码。

缺乏标准化意味着开发者必须考虑许多不同的浏览器行为和边缘情况。只要看一下[这个早期的 jQuery 源代码](https://github.com/daniellmb/jquery-archive/blob/master/jquery.2006-07-01.js)，搜索`jQuery.browser`就能看到一些例子。这里有一个:

```
// If Mozilla is used
if ( jQuery.browser == "mozilla" || jQuery.browser == "opera" ) {
        // Use the handy event callback
        jQuery.event.add( document, "DOMContentLoaded", jQuery.ready );

// If IE is used, use the excellent hack by Matthias Miller
// http://www.outofhanwell.com/blog/index.php?title=the_window_onload_problem_revisited
} else if ( jQuery.browser == "msie" ) {

        // Only works if you document.write() it
        document.write("<scr" + "ipt id=__ie_init defer=true " + 
                "src=javascript:void(0)><\/script>");

        // Use the defer script hack
        var script = document.getElementById("__ie_init");
        script.onreadystatechange = function() {
                if ( this.readyState == "complete" )
                        jQuery.ready();
        };

        // Clear from memory
        script = null;

// If Safari  is used
} else if ( jQuery.browser == "safari" ) {
        // Continually check to see if the document.readyState is valid
        jQuery.safariTimer = setInterval(function(){
                // loaded and complete are both valid states
                if ( document.readyState == "loaded" || 
                        document.readyState == "complete" ) {

                        // If either one are found, remove the timer
                        clearInterval( jQuery.safariTimer );
                        jQuery.safariTimer = null;

                        // and execute any waiting functions
                        jQuery.ready();
                }
        }, 10);
}
```

通过使用 jQuery，开发人员可以让 jQuery 团队来处理这些缺陷。

后来，jQuery 使得采用更复杂的技术变得容易，比如动画和 Ajax。jQuery 几乎成了网站的标准依赖。今天，它继续为互联网的大部分提供动力。W3Techs 估计大约 74%的 T2 网站使用 jQuery T3。

jQuery 的管理也变得更加正式。2011 年，jQuery 团队正式[创建了 jQuery Board](https://blog.jquery.com/2011/11/18/getting-board-of-jquery/) 。2012 年，jQuery 董事会[成立了 jQuery 基金会](https://blog.jquery.com/2012/03/06/announcing-the-jquery-foundation/)。

2015 年，jQuery Foundation 与 Dojo Foundation 合并[成立 JS Foundation](https://blog.jquery.com/2015/09/01/jquery-foundation-and-dojo-foundation-to-merge/) ，随后在 [2019](https://medium.com/@nodejs/introducing-the-openjs-foundation-the-next-phase-of-javascript-ecosystem-growth-d4911b42664f) 与 Node.js Foundation 合并成立 [OpenJS Foundation](https://openjsf.org/) ，jQuery 作为其[影响项目](https://openjsf.org/projects/#impact)之一

## 变化的环境

然而，jQuery 在最近几年[的受欢迎程度](https://trends.google.com/trends/explore/TIMESERIES/1564319400?hl=en-US&tz=240&date=all&geo=US&q=jquery&sni=3)有所下降。GitHub [从他们的网站前端](https://github.blog/2018-09-06-removing-jquery-from-github-frontend/)移除了 jQuery，Bootstrap V5[将会放弃 jQuery](https://github.com/twbs/bootstrap/pull/23586) ，因为它是 Bootstrap 的“[对常规 JavaScript](https://blog.getbootstrap.com/2019/02/11/bootstrap-4-3-0/) 最大的客户端依赖(目前是 30KB，minified 和 gzipped)。web 开发的几个趋势削弱了 jQuery 作为必备工具的地位。

### 浏览器

由于几个原因，浏览器的差异和限制变得不那么重要了。首先是标准化程度提高了。主要的浏览器供应商(苹果、谷歌、微软和 Mozilla)通过[网络超文本应用技术工作组](https://whatwg.org/)在[网络标准](https://spec.whatwg.org/)上进行合作。

虽然浏览器仍然有很大的不同，但厂商们至少有办法找到并发展共同点，而不是相互之间无休止的战争。相应地，浏览器 API 变得更加强大。例如，[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 可以代替 jQuery 的 Ajax 函数:

```
// jQuery
$.getJSON('https://api.com/songs.json')
    .done(function (songs) {
        console.log(songs);
    })

// native
fetch('https://api.com/songs.json')
    .then(function (response) {
        return response.json();
    })
    .then(function (songs) {
        console.log(songs);
    });
```

[`querySelector`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) 和 [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) 方法复制了 jQuery 的选择功能:

```
// jQuery
const fooDivs = $('.foo div');

// native
const fooDivs = document.querySelectorAll('.foo div');
```

现在可以用`[classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)`操作元素类:

```
// jQuery
$('#warning').toggleClass('visible');

// native
document.querySelector('#warning').classList.toggle('visible');
```

你可能不需要 jQuery 网站列出了更多可以用本地代码替换 jQuery 代码的例子。一些开发人员总是使用 jQuery，因为他们不知道这些 API 是可用的，但是随着开发人员对它们的了解，他们对 jQuery 的依赖程度会降低。

使用原生功能也可以提高网页的性能。许多 jQuery 动画效果现在可以用 CSS 更有效地实现。

第二个原因是浏览器比过去更新更快。大多数浏览器现在都有一个[常青树更新策略](https://www.techopedia.com/definition/31094/evergreen-browser)，苹果的 Safari 是主要的例外。它们可以在没有用户干预的情况下自动更新，并且不依赖于操作系统更新。

这意味着新的浏览器功能和错误修复会更快地被用户采用，开发人员不必再等待[我可以使用](https://caniuse.com/)的使用率达到可接受的水平。他们可以放心地使用特性和 API，而无需加载 jQuery 或 [polyfills](https://en.wikipedia.org/wiki/Polyfill_(programming)) 。

第三个原因是，Internet Explorer 越来越接近完全无关紧要。IE 长期以来一直是各地网络开发者的克星。IE 特有的错误是出了名的普遍，因为 IE 是 2000 年代的主导浏览器，缺乏持久的更新，旧版本顽固地徘徊不前。

微软加速了 IE 的弃用，[在 2016 年结束了对 IE 10 及以下版本的支持](https://www.microsoft.com/en-us/microsoft-365/windows/end-of-ie-support)，剩下 IE 11 成为最后一个支持的版本。web 开发人员忽视 IE 兼容性的情况越来越普遍。

甚至随着 2013 年[版本 2.0](https://blog.jquery.com/2013/04/18/jquery-2-0-released/) 的发布，jQuery 也放弃了对 IE 8 及以下版本的支持。虽然一些特殊情况，如传统网站仍然需要 IE，但这种情况越来越少了。

### net 框架

自从 jQuery 发布以来，出现了大量的 web 框架，其中一些目前的领先者是 [React](https://reactjs.org/) 、 [Angular](https://angular.io/) 和 [Vue](https://vuejs.org/) 。与 jQuery 相比，这些框架有两个显著的优势。

首先，它们使得将 UI 分解成组件变得容易。它们被设计用来处理页面的呈现和更新。jQuery 通常只用于更新页面，依赖服务器提供初始页面。

另一方面，React、Angular 和 Vue 组件允许 HTML、代码甚至 [CSS](https://blog.logrocket.com/a-guide-to-css-pseudo-elements/) 之间的紧密耦合。就像我们可以将一个代码库分解成多个自包含的函数和类一样，将一个 UI 分解成可重用的组件使得构建和维护一个复杂的网站变得更加容易。

第二个优势是新的框架鼓励声明性范例，在这种范例中，开发人员描述 UI 应该是什么样的，并让框架做出必要的更改。这种方法与以 jQuery 代码为特征的命令式方法形成对比。

使用 jQuery，您可以显式地编写进行任何更改的步骤。对于声明式框架，你会说，“基于这些数据，这就是 UI 应该的样子。”这可以大大减少你为了写无错误代码而必须做的脑力劳动。

开发人员已经接受了这些构建网站的新方法，降低了 jQuery 的相关性。

## 何时使用 jQuery

那么什么时候*应该*选择使用 jQuery 呢？

如果所讨论的项目预计会越来越复杂，那么最好从一个不同的库或框架开始，它将允许您理智地处理这种复杂性，比如通过将 UI 分解成组件。在这样的网站上使用 jQuery 起初可能没什么问题，但是它会很快演变成杂乱无章的代码，您无法确定哪些代码会影响页面的哪些部分。

我以前处理过这种情况，每当你想做出改变时，这种情况都会产生一种不安的感觉。很难确定没有破坏任何东西，因为 jQuery 选择器依赖于服务器生成的 HTML 结构。

另一方面，简单的网站只需要少量的交互性或动态内容。对于这些情况，我仍然默认不使用 jQuery，因为我们现在可以用原生 API 做更多的事情。

即使我确实需要更强大的东西，我也会为用例寻找一个特定的库，比如 Ajax 的 [axios](https://github.com/axios/axios) 或者动画的 [Animate.css](https://daneden.github.io/animate.css/) 。使用这样的库通常比只加载 jQuery 的一部分功能更轻量级。

我认为使用 jQuery 的最好理由是它提供了支持网站前端的全面功能。您不必学习所有各种本机 API 或专用库，只需阅读 jQuery 文档就能立即提高工作效率。

它的命令式方法不能很好地扩展，但是它也比其他库的声明式方法更容易学习。对于一个范围明显有限的网站，加入 jQuery 并继续前进是合理的；它不需要任何复杂的构建或编译过程。

因此，当您有理由相信网站不会变得更加复杂，并且您不想为原生功能而烦恼时，jQuery 是一个不错的选择，因为原生功能肯定会比同等的 jQuery 代码更加冗长。

当你必须支持旧版本的 IE 时，另一个用例出现了。在这种情况下，jQuery 就像 IE 成为主流浏览器时一样好用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 展望未来

jQuery 不会很快消失。它还在积极开发中，许多开发者更喜欢使用它的 API，即使有本地方法可用。

它帮助了一代开发者制作出可以在任何浏览器上运行的网站。尽管 jQuery 在许多方面已经被新的库、框架和范例所取代，但它在让网络成为今天的样子方面发挥了巨大而积极的作用。

除非 jQuery 的功能发生重大变化，否则在接下来的几年中，jQuery 的使用率很可能会持续缓慢下降。新网站往往从一开始就用更现代的框架构建，jQuery 的良好用例越来越少。

有些人对 web 开发工具的流失率不满意，但对我来说，这是快速进步的标志。jQuery 给了我们更好的做事方法。它的继任者也做了同样的事情。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.