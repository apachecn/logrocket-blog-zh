# JQuery 死了吗？- LogRocket 博客

> 原文：<https://blog.logrocket.com/using-jquery-in-2019/>

## 介绍

关于流行的 JavaScript 库 [jQuery](https://jquery.com/) 是如何死亡的，已经有过讨论。

随着现代库、框架、浏览器 API 标准化的兴起，以及需要 jQuery 技能的职位发布的减少，人们担心开发人员会继续学习 jQuery。

在这篇文章中，我将介绍 jQuery，它的历史，优势，弱点，为什么你可能仍然想使用它，以及如何在 2019 年开始使用。

## 什么是 jQuery？

根据 jQuery 官方[文档](https://jquery.com/):

jQuery 是一个快速、小巧、功能丰富的 JavaScript 库。它通过一个跨多种浏览器工作的易于使用的 API，使 HTML 文档遍历和操作、事件处理、动画和 Ajax 变得更加简单。jQuery 结合了多功能性和可扩展性，改变了数百万人编写 JavaScript 的方式。

基本上，它是一个 JavaScript 库，在旧浏览器中访问 DOM 和用 JavaScript 做某些事情几乎不可能的时候，它的建立使 DOM 操作成为可能。这使得编写 JavaScript 和 HTML 变得更加容易和简单。

当微软和诺基亚在 2008 年 9 月宣布公开支持该库时，该库的受欢迎程度增加了，并且它在 JavaScript 库和框架方面一直保持着领先地位[开发者的兴趣](https://trends.google.com/trends/explore?date=all&geo=US&q=%2Fm%2F0268gyp,%2Fm%2F012l1vxv,%2Fm%2F0j45p7w,%2Fg%2F11c0vmgx5d)直到最近，当它看到许多著名的公司和使用 jQuery 作为依赖(GitHub，Bootstrap 5)的库放弃对它的支持时。这导致了使用率的急剧下降，以及在像 [Reddit](https://www.reddit.com/r/javascript/) 和 [StackOverflow](https://insights.stackoverflow.com/trends?tags=jquery%2Cangular%2Cangularjs%2Creactjs%2Cvue.js) 这样的热门平台上提出的许多问题。

你可以在这里了解更多关于 jQuery [的历史和遗产。](https://blog.logrocket.com/the-history-and-legacy-of-jquery/)

## jQuery 提供了什么？

*   **遍历 DOM**–jQuery 使得遍历 DOM 变得更加容易(因为没有标准的方法可以这么做)。在旧浏览器中遍历 DOM 是一件复杂的事情。
*   **操作 DOM 元素**–jQuery 通过使用选择器引用目标元素并包含一个应用所需更改的方法，使更改元素的样式和行为变得更加容易。
*   动画元素是 jQuery 的主要卖点之一。
*   **提出更好的 HTTP 请求**——jQuery 的 [AJAX 方法](https://api.jquery.com/jquery.ajax/)可以轻松处理 HTTP 请求，这为它赢得了许多粉丝，因为用 JavaScript 提出 HTTP 请求的老方法——使用[XMLHttpRequest(XHR)](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)——是一个繁琐的过程。
*   **跨浏览器兼容性问题的解决方案**–jQuery 的主要卖点是它对跨浏览器兼容性问题的解决方案。旧浏览器做事情的方式非常不同，因为没有标准的方式来做事情，开发人员必须进行几次检查，以确保 JavaScript 在 IE、Firefox、Opera、Safari 和 Chrome 中的行为符合需要。jQuery 的引入确保了无论用户使用何种浏览器，网页都能正确呈现。
*   **丰富的文档** — jQuery 的[文档](https://api.jquery.com/)、资源和它的[生态系统](https://forum.jquery.com/)可以帮助开发者很快从新手提升到专家。jQuery 是开源的(任何人都可以贡献或修改它，或者建议更新的特性),它在互联网上也有一个非常大的用户社区。

## 为什么开发者还在用 jQuery？

它有几个很好的特性，人们会觉得很有帮助。
**其中包括:**

*   **jQuery 有可扩展的插件系统** — jQuery 有即插即用的插件系统。从下拉菜单到导航系统、图像上传器和旋转滑块，几乎所有你能想到的东西都有插件。你所需要做的就是下载插件，并在需要的地方使用或定制以满足你的需求。
*   ***“写得更少，做得更多”*** —正如它的标语所说，使用 jQuery，您可以用更少的代码行做更多的事情。各种复杂的动作都可以轻松完成，这鼓励编写模块化代码。
*   jQuery 提供了简单性 — jQuery 不需要学习任何先决条件。它的学习曲线很低，任何人都相对容易掌握，并在很短的时间内成为这方面的专家。它满足了开发人员和设计人员的需求，因为它大大减少了构建时间。
*   **跨浏览器兼容性** — jQuery [支持与现代工具、框架或库不兼容的旧浏览器](https://jquery.com/browser-support/)。jQuery 支持的应用程序在所有浏览器上都运行良好。
*   jQuery 是稳定的(T1)——它是稳定的，因为有一个由终生贡献者组成的[专用团队](https://jquery.org/team/),除了它的大型生态系统之外，他们还自愿保持这个工具的最佳状态。

## 开发人员对 jQuery 的不满

使用 jQuery 是有代价的。开发人员对 jQuery 兴趣减弱的一些原因是:

*   **渲染性能** — jQuery 牺牲了性能，以便能够做许多其他令人惊叹的事情。这使得网站运行缓慢，因为在处理我们的代码之前，它必须不断地直接访问 DOM 并加载整个库，这经常导致 jank。在一个基本的或普通的网站中，缓慢的性能几乎不明显，并且可能是更多功能的完美折衷，但是在一个每一毫秒都很重要的大型复杂网站中，jQuery 经常会降低这类网站的性能。
*   相对易用性 jQuery 的败笔之一是在它里面写意大利面条式的代码非常容易。由于其相对易用性、jQuery 选择器链的不正确使用、非描述性变量名的使用以及试图编写复杂的函数，jQuery 可能会导致编写最终无法维护的代码。
*   **增加的包大小**—gzip 压缩后的大小为 86.1 Kb 或 28kb，jQuery 为您的网站增加了一点大小，即使大多数时候只需要它库中的几个函数。
*   **现代浏览器和不断发展的网络趋势**—jQuery 解决的大量跨浏览器兼容性问题和标准化问题现在[已经解决](https://www.w3.org/wiki/Special:RecentChanges)，因为网络浏览器正在改进。最值得注意的是 ECMAScript 2015(也称为 ES6，JavaScript 遵循的规范)的引入，新框架和专用库的创建，这些都使 jQuery 变得相当过时。

## jQuery 死了吗？

据 web 技术统计聚合器的 [BuiltWith](https://builtwith.com) 称， [jQuery 为世界上 100 万个顶级网站中的 79%提供支持](https://trends.builtwith.com/javascript/jQuery)，并且占据了 [65%的 JavaScript 库使用量](https://trends.builtwith.com/javascript/javascript-library)。

它仍然如此受欢迎的一个原因是[许多项目仍然依赖于它](https://trends.builtwith.com/websitelist/jQuery)(例如: [Bootstrap 4.0 和更低版本，大多数 WordPress 插件和主题都是使用 jQuery 构建的](https://getbootstrap.com/docs/4.3/getting-started/introduction/#js))并且也有依赖于 jQuery 的遗留代码库。

## jQuery 函数的现代替代品

下面是 jQuery 常用函数的一些合适的替代函数。

#### DOM 选择

要在 jQuery 中选择某个内容，我们通常会这样做:

```
// The selector name can be replaced with either an ID, a Class name or tag name
$("selectorName")  //  #selectorName or .selectorName
```

这可以通过使用 HTML5 DOM API 来实现:

```
document.querySelector("selectorName") //gets a single item
```

或者

```
document.querySelectorAll("selectorName") //gets a group of items
```

#### DOM 操作

`.append()`方法将括号中的内容插入到选择器指定的元素的末尾。

```
$("selectorName").append( "Your content goes here")
```

它的普通等价物可以这样做:

```
let element = document.querySelector('selectorName');
let text = document.createTextNode("your content");
element.appendChild(text);
```

将指定的类添加到由选择器指定的元素集合中的每个元素的方法。

```
$('selectorName').addClass('className');
```

它的普通等价物:

```
let element = document.querySelector("selectorName");
element.classList.add('className')
```

#### 事件

监听`click`事件:

```
$('selectorName').on('click',function(e) {
    //do something
});
```

它的普通等价物:

```
let clickedMe = document.querySelector('button');

clickedMe.addEventListener('click', (e) => {
    //do something
})
```

#### HTTP 请求

jQuery Ajax HTTP 请求是这样生成的:

```
$.ajax({
  url: 'http://example.com/movies.json',
  type: 'GET'
  success: (response) => {
    console.log(response)
  }
  error: (errors) => {
    console.log(error)
  }
})
```

这可以用 JavaScript `fetch` API 来代替，它允许您进行异步请求。它将数据作为“承诺”返回。

#### 取得

```
fetch('http://example.com/movies.json',
          {method: 'GET',
          headers: {'Content-Type': 'application/json'},
          }){
        .then(response => response.json())
        .catch(error => console.log(error))
    }
```

虽然 [Fetch](https://blog.logrocket.com/axios-or-fetch-api/) 比 AJAX 好，但它们是不同的，因为 Fetch 使用承诺，并且返回的承诺不会在 HTTP 状态错误时拒绝。Fetch 也不会从服务器发送或接收 cookies。

HTTP 请求也可以使用像`[axios](https://github.com/axios/axios)`这样的专用库来实现。

Axios 是一个基于 promise 的开源库，用于发出 HTTP 请求。

要使用 axios，您可以通过 npm 安装它:

```
npm install axios --save-dev
```

然后将它导入到文件中，如下所示:

```
import axios from 'axios'
```

或者

您可以使用 CDN 包含 axios。

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

#### 履行

```
axios({
  method: 'get',
  url: 'http://example.com/movies.json',
  responseType: 'json'
})
  .then(function (response) {
  });
```

#### 浏览器支持和交叉兼容性

为了让你的应用或网站得到跨浏览器的支持，并在旧浏览器上工作，你可以使用转换工具，如 [Babel](https://babeljs.io/) 将 ES6 代码转换(传输)为浏览器兼容的代码，这些代码可以在特定功能的 [polyfills](https://polyfill.io/v3/) 以及其他工具如 [Autoprefixer](https://github.com/postcss/autoprefixer#browsers) 、 [PostCSS](https://github.com/postcss/postcss) 等的帮助下跨浏览器理解。

#### 动画

jQuery 中的动画是通过`.animate`方法实现的。

```
$(selectorName).animate(
    {parameters},
    speed,
    callback
);
```

使用 CSS 动画可以实现网站内容的动画化。

制作内容动画的一个重要 CSS 工具是`@keyframes`,用于定义在舞台上应用的样式，以及绑定到选择器的`animation`属性或其子属性，它指定如何以及何时应用关键帧样式。

```
p{
    animation-name: animated;
    animation-duration: ;  
}

@keyframes animated{
    0% {
    opacity: 0;
  }        

  50% { 
    opacity: 1;
  }
  100%  {
    opacity: 0;
  }
}

```

参见 [CodePen](https://codepen.io) 上 Anjolaoluwa([@卫英琦 _ 阿德巴约](https://codepen.io/jola_adebayor) )
的笔 [OeGpNE](https://codepen.io/jola_adebayor/pen/OeGpNE/) 。

也可以使用`[animate.css](https://github.com/daneden/animate.css)`等库来实现动画。

## jQuery 与现代前端库和框架相比如何

jQuery 使用较少的一个主要原因是 JavaScript 库和框架的兴起，如 React JS、Angular JS 和 Vue JS，在这一节中，我们将看看是什么使它们与众不同。

| **jQuery** | **反应 JS** | **角度 JS** | **检视 JS** | **类型** |
| --- | --- | --- | --- | --- |
| 图书馆 | 用户界面库 | 功能齐全的框架 | 在库和全功能框架之间扩展。 | **架构** |
| 模块化的 | 一个基于组件的库，只处理你的应用程序的视图 | 成熟的基于组件的 MVC 框架 | 基于组件，侧重于 MVVM 模式的视图模型层 | **DOM 交互** |
| 直接更新 DOM | 使用与真实 DOM 接口的虚拟 DOM | 直接更新 DOM | 使用与真实 DOM 接口的虚拟 DOM | **数据绑定** |
| 使用插件实现双向数据流的数据绑定方法
 | 单向数据流 | 使用 ngModel 可以实现双向数据绑定 | 更多来自 LogRocket 的精彩文章: | Reactive data binding system

* * *

### 使用 V-model 可以实现双向数据

* * *

**状态管理** |
| 可以使用专门的库来实现 | 上下文 API，Redux | NGRX、NGXS 等第三方库 | 武契特 | **模板化** |
| Java Script 语言 | JavaScript (JSX) | 打字稿和角度指令 | HTML、CSS、JavaScript 和 Vue 指令 | **学习曲线** |
| 低的 | 需要具备较低但很强的 JavaScript 知识 | 高的 | ●你需要的只是 JavaScript 的基础知识 | 为什么你在 2019 年仍然想使用 jQuery |

## 当构建产品原型时——由于 jQuery 有助于快速构建原型并在短时间内完成新特性，因此您不需要深入了解它就能完成工作。有了[文档](https://learn.jquery.com/)，一个人可以立刻得到一个工作原型。

*   通过普通方法制作动画内容仍然相对困难。如果你的项目需要大量的动画，jQuery 可能是一个合适的选择，因为有大量的可定制插件可以帮助补充简单易用的`.animate`方法。
*   为几种浏览器构建。如果你正在构建一个可以在多种浏览器上无缝运行的网站或 web 应用，jQuery 可能是最适合你的。它支持所有现代浏览器，在解决交叉兼容性问题方面做得更好，它也解决了 IE6 等旧浏览器的问题。
*   引导不需要框架的小项目和简单站点。
*   同时处理用 jQuery 构建的遗留代码库。
*   2019 年如何入门 jQuery

## 要在项目中使用 jQuery，有两种方法可以开始。

撰写本文时的最新版本是版本 3.4.1 下载压缩版，[生产版](https://code.jquery.com/jquery-3.4.1.min.js)或未压缩版，[开发版](https://code.jquery.com/jquery-3.4.1.js)。

您可以将它包含在项目中，如下所示:

作为替代，您可以使用**包管理器**:

```
<script type="text/javascript" src="js/jquery-3.4.1.js"> </script>
```

然后像这样导入它:

```
npm install jquery
or
yarn add jquery
```

或者通过在您的项目 **:** 中包含以下任一项来使用**CDN 版本**

```
import * as jQuery from 'jquery';
```

**谷歌 CDN**

**微软 CDN**

```
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
```

结论

```
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.4.1.js"> </script>
```

## 虽然现代趋势表明开发人员正在从 jQuery 转向新的库和框架，但是这个库仍然非常活跃，并被积极地使用，因为它具有比普通方法相对更容易实现的功能。

低技能需求率也表明使用新的库/框架会更好，因为会有更多的工作机会。

我建议如果在项目中需要使用 jQuery，应该了解 jQuery 以及如何在其中实现基本的东西(增加一些知识不会有坏处)。[文档](https://learn.jquery.com/)提供了一个很好的入门资源。

通过理解上下文，更容易地调试 JavaScript 错误

## 调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.