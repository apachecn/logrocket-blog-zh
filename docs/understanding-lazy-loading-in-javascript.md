# 理解 JavaScript - LogRocket 博客中的延迟加载

> 原文：<https://blog.logrocket.com/understanding-lazy-loading-in-javascript/>

## 介绍

在这篇文章中，我们将看看网络上的惰性加载是如何工作的。我们将介绍本机惰性加载 API——惰性加载是如何实现的，惰性加载的重要性和优点，最后，惰性加载 web 内容的一个简单用例。为了有效地学习本教程，我们假设读者已经对使用 JavaScript 构建 web 应用程序有了基本的了解。

理解延迟加载 API 及其工作原理将有助于已经使用实现这些技术的库和框架的开发人员理解幕后发生的事情。此外，如果他们打算实现自己的延迟加载库，他们将能够进行更多的指导性研究，并应用他们所学的技术。

就现实世界的使用情况而言，通过平台上的广告获得收入的营销和广告公司可以很容易地优化和应用惰性加载，以便很容易地告诉访问他们平台的用户看到了哪些广告，从而做出更好的商业决策。

## 什么是懒装？

根据[维基百科](https://en.wikipedia.org/wiki/Lazy_loading)的说法，惰性加载是一种模式，旨在推迟元素或对象的初始化，直到需要它的时候。这意味着，只有当用户在网页上滚动目标 DOM 元素时，相对于父 DOM 元素的目标 DOM 元素才会被加载并变得可见(当两个元素之间有交集时，基于设置的阈值)。

不采用这种模式的缺点可能导致:

*   由于多个同步网络请求或批处理请求从一个或多个来源获取一些图像或其他 web 资源，导致页面性能严重滞后
*   由于要下载/获取的包的大小，页面加载时间增加
*   用户留存率低，大多适用于互联网连接差的地区。当我们开发人员犯了没有在早期实现延迟加载的错误时，用户完全避开一个平台的情况并不少见
*   图像、iframes 和视频等资源或资产处理不当会对 web 性能和可访问性造成巨大影响

目前，对于大多数现代和更新的浏览器来说，web 上本来就支持延迟加载。然而，对于还不提供这种支持的浏览器，实现这种技术的 polyfills 或库在它们之上提供了简单的 API 层。

> 惰性加载解决了减少初始页面加载时间的问题——只显示用户在初始化网页时和随后滚动页面时需要看到的资源，如图像或其他内容。

众所周知，Web 性能和可访问性问题是多方面的；减少页面大小、内存占用和一般加载时间可以为 web 平台做出巨大贡献。当我们有一堆图像和视频，并且在浏览器 DOM 初始化时一次性加载它们时，延迟加载的优势就变得很明显。当然，正如我们之前所讨论的，你现在应该对这会导致什么有所了解。

根据数据判断，大多数网站严重依赖图像和其他网络内容，如视频或 iframes，向目标受众传递信息。虽然这看起来琐碎而简单，但是我们向用户显示这些内容的方式决定了我们的平台最终的性能。

此外，有助于优化页面加载时间的操作，如依赖于用户是否滚动到我们网页的特定部分的事件，是惰性加载的一些用例。随着本文的继续，我们将了解更多真实环境中的其他用例。

## 本机惰性加载 API

延迟加载构建在交叉点观察器 API 之上，这是一个浏览器 API，它提供了一种检测或了解元素何时成为目标、父元素或在浏览器视口中变得可用或可见的方法。

当这种情况发生时，将调用一个处理函数来帮助处理代码逻辑的其他部分，我们将在后面看到。有了这个新的和改进的浏览器 API，我们还可以知道两个 DOM 元素何时相交——我们的意思是，当一个目标 DOM 元素进入浏览器的视口或与另一个元素相交时，这个元素很可能是它的父元素。

为了更好地理解惰性加载是如何工作的，我们首先必须理解如何创建一个交叉点观察器。为了创建交叉点观察器，我们需要做的就是监听交叉点观察器事件的发生，并在这种事件发生时触发回调函数或处理程序运行。交叉点观察者事件是一种几乎类似于文档事件类别的浏览器事件，它包括`[DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/API/Document/DOMContentLoaded_event)`事件。

**注意**:对于交集事件，我们需要指定想要应用交集的元素。这个元素通常被称为根元素。但是，如果没有指定根元素，这意味着我们打算将整个浏览器视口作为目标。

此外，我们还需要为根元素(如果提供的话)指定一个边距，这样我们就可以很容易地改变它的形状或大小，如果有必要的话。让我们看一个例子来更好地理解它:

```
let options = {
root: null,
rootMargin: 10px,
threshold: 1.0
}

let observer  = new IntersectionObserver (options, callback);

```

在上面的代码片段中，我们看到了一个创建观察者的简单用例。`options`对象帮助我们为目标定义定制属性。

这里，`options`对象中的 threshold 属性表示何时触发回调。它的默认值为零，这意味着一旦用户接近目标元素并且它变得可见，回调就会被触发。

另一方面，`root`是父元素，当用户滚动网页时目标元素变得对用户可见时，该父元素充当目标元素的视口。注意，如果`root`设置为空，父元素将自动成为视口。

最后，`rootMargin`帮助设置根元素周围的边距。例如，在计算目标和父元素/视口之间的交集之前，我们可能需要调整它的大小、边距或尺寸。

此外，接受两个输入参数的回调包括一个我们打算应用于目标元素的`intersectionObserverEntry`对象列表，以及调用回调的观察者。

回调的签名如下所示:

```
let callback = (entries, observer) => {
entries.forEach(entry => {
If (entry.isIntersection) {
// do some magic here
}
// and some other methods
})
}

```

`intersectionObserverEntry`对象表示父元素和目标元素之间何时有交集。它的 API 中有一堆属性，包括`isIntersection`、`intersectionRatio`、`intersectionRect`、`target`、`time`等。关于这些属性的详细解释，您可以参考 MDN 文档中的[部分。](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry#Properties)

我们需要将特定的 DOM 元素作为目标，并在它与父元素相交时触发回调函数。下面的代码片段显示了一个 DOM 元素的例子:

```
let target = document.querySelector("#targetElement");

```

在上面的代码片段中，我们创建了一个目标元素，并为它分配了一个变量。之后，我们在`intersectionObserver`构造函数/函数签名上使用 observe 方法观察了目标元素，如下所示:

```
// start observing for changes on the target element

observer.observe(target);

```

当达到观察者为目标设置的阈值时，将触发回调。简单吧？

最后，`observe()`方法告诉观察者要观察什么目标元素。请注意，交叉点观察器的 API 中同样有许多方法:`unObserve()`、`takeRecords()`、`observe()`等。举几个例子。

## 惰性加载技术的优势

到目前为止，我们必须更好地理解为什么延迟加载 web 内容和资产是必要的。让我们来看看使用这种技术的其他一些优点:

*   构建高度可访问的 web 应用程序。关于网页可访问性的讨论是今天的首要议题。使用这种技术肯定有助于构建一个覆盖面更广的平台
*   用户留存率高。如果一个 web 平台与驱动业务目标相关联，并反过来提供价值，那么实现这种技术将会对平台的用户友好性有很大帮助。网络标准以后会感谢你的！
*   作为一名开发人员，您可能需要在 web 平台上实现无限滚动。理解这个概念会有很大帮助，从而提供直接的商业价值

## 实现延迟加载

让我们来看一个简单的在网页上加载图片的例子。我们将从定制目标元素的`options`对象开始，我们打算观察与目标元素的交集:

```
let  options = {
root: document.querySelector('.root'),
rootMargin: '0px, 0px, 100px, 0px'
};

```

现在，对于目标元素，让我们瞄准几个图像:

```
let  images = [...document.querySelectorAll('.targetImages')];

```

现在，让我们看看回调的实现:

```
const callback = (entries) => {

entries.forEach(entry => {
 If (entry.isIntersecting) {
    observer.unObserve('entry.target');
}
// handle other code logic here 
})
}

```

我们可以继续调用交叉点观察器构造函数，根据在目标元素的`options`对象中指定的定制来观察目标元素:

```
let observer = new intersectionObserver(options, callback);

```

最后，我们可以观察被观察的目标元素:

```
images.forEach(image => {
observer.observe(image);
})

```

**注意:**为了简单起见，这里不包括 HTML 和 CSS 代码。您可以通过查看 MDN 文档中的这个[示例](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#A_simple_example)来详细了解如何实现这项技术。

## 摘要

现在，当我们在网页上有一堆图像或视频，并在浏览器 DOM 初始化时将它们一起加载时，这种技术的优势应该非常明显。作为开发人员，确保我们管理或维护的平台的最佳性能是我们的职责，尤其是在它们与业务目标相关的情况下。惰性加载作为一种 web 性能技术有助于解决这类问题。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.