# 在 React - LogRocket 博客中维护状态变量

> 原文：<https://blog.logrocket.com/react-url-state/>

如果你知道如何让事情井然有序，那么在 React 中管理页面之间的活动会很容易。每次有变化时，向下传递和向上传递道具的方式是有意义的，但很容易变得混乱。

慢慢来。

通过把你的组件分成两个简单的类别，并把每个页面的职责分开，你就不需要传递这么多的道具(并把它们放在地址栏里)。

## 页面组件与块组件

让我们从将应用程序分解成与应用程序本身的功能相关的 HTML 块开始。如果您曾经使用过 CMS，那么您可能已经熟悉了将“页面”与“内容块”分开管理的概念。

### 示例:

一个应用程序有一个博客文章对象。有一个特定于该单个博客帖子的页面，但也有该博客的简短形式的单独表示。也许有一个博客主页，每页有 10 篇文章，也许有一个主页的“最新文章”部分，也许有一个作者页面，上面有他们撰写的每篇文章。

博客*页面*是关于个人博客文章的所有内容，但是博客*区块*是可以在我们想要的任何地方使用的东西，不管上下文如何。为了在 React 中将您的功能分离到页面中，而不牺牲在页面之间传递信息的能力，您的应用程序必须由许多可以使用任意数量块组件的页面组件构成，这一点很重要。

稍后会有更多的介绍。

## 将页面绑定到 URL

有几种方法可以做到这一点，但没有一种是 React 自带的。有许多很好的选择，但我最喜欢的是反应路由器。因为我在为 web 做这个例子，我们将使用`react-router-dom`，但是也有 React Native 的选项。以下是其工作原理的基础。

*   一个组件(通常称为`App`)是拥有路由器并管理历史对象*作为其状态和道具的一部分的顶级组件。
*   多个(页面)组件或呈现函数根据当前地址栏中的 URL 选择在页面上放置什么。
*   其余的功能根据需要放入页面中。

*   这是重要的一点。

您知道 DOM 已经有了一个包含 URL 所有属性的对象吗？帮我个忙，在这个浏览器标签中进入你的控制台，输入`window.history`并检查一下。

很酷，对吧？它的伟大之处在于它可以管理你在哪里，你在哪里使用… (drumroll) state！有空的时候去看看文档吧，因为你可以用历史对象做一些很酷的事情。

大多数路由的工作方式是将你的顶层组件绑定到历史上，并用你的浏览器历史来管理*它的状态*。它还包含了很多很酷的能力，打破了网址段，和参数。

## 好吧，说真的，我该怎么办？

这就是酷的地方。通过将 history 对象传递到页面组件的 props 中，您可以将状态变量(即使它们在顶层发生了变化)向下维护到每个页面中，甚至维护到页面之间。利用这一点，您可以将其他块组件放在您想要的任何位置，并且您有一种整洁的方式来管理与应用程序页面相关的任何信息。

### 第一步:页面

让我们回到我们的博客例子。如果没有路由器，你需要在主页和博客文章页面之间创建一个单独的状态文件，但是有了路由器，你可以将参数传入 URL，甚至可以用它来动态设置 URL。

看看这个:

```
import React, { Component } from "react"
import { BrowserRouter as Router, Route } from "react-router-dom"

component App extends Component {
  render () {
    return (
      <div className="app">
        <Router>
          <Route path="/" exact component={HomePage} />
          <Route path="/blog" exact component={BlogPage} />
          <Route path="/blog/:id" exact component={BlogPostPage} />
        </Router>
      </div>
    )
  }
}
```

通过这三行代码，您已经建立了三个独立的页面，它们共享博客文章的内容，并且可以呈现相同的组件，而无需传递大量的道具。您甚至会注意到，我已经为名为`id`的博客文章 ID 包含了 URL 参数。

### 步骤 2:映射历史

通过获取 URL 的动态部分(博客文章的 ID)并将其移动到一个参数中，我们可以避免应用程序需要任何关于博客数据库的知识。

这意味着巨大的处理节省。如果在那个`/blog` URL 中有一个 CRUD 接口，`BlogPostPage`可以管理所有这些。更好的是，你可以通过 Redux 依赖一个缩减器来管理所有的本地商店，这样`App`只负责确保正确的页面显示。

这是`BlogPostPage`开始时的样子:

```
import React, { Component } from "react"

component BlogPostPage extends Component {
  state = {
    postId: this.props.match.params.id
  }

  componentDidMount () {

  }

  render () { ... }
}
```

### 第三步:有趣的东西

默认情况下,`react-router`包含了获取参数、散列或任何你想在 URL 中检查的东西的能力。其中的每一项都可以立即用于页面组件:

参数(对于命名变量):`this.props.match.params`

哈希(非常适合锚链接):`this.props.location.hash`

查询参数(用于搜索或其他查询):`this.props.location.search`

所有 URL 段(如果您需要对路径进行更精细的控制):`this.props.location.pathname`

如果您将*个路径嵌套在一起，这也是可行的:*

```
return (
  <div className="app">
    <Router>
      <Route path="/" exact component={HomePage} />
      <Route path="/blog" exact component={BlogPage} />
      <Route path="/blog/:id" exact component={BlogPostPage} />
      <Route path="/user" exact component={UserProfile} />
      <Route path="/user/settings" exact component={UserSettings} />
    </Router>
  </div>
)
```

## 结论

经过一些重构后，你可以把你的应用程序的每一页想象成独立的迷你应用程序，只负责它们自己的特性，并把页面之间重复的特性分解成在每一页上使用的块组件。这让你的状态和道具变少，责任变小。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

* * *