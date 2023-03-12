# Open Graph:如何创建可共享的社交媒体预览

> 原文：<https://blog.logrocket.com/open-graph-sharable-social-media-previews/>

开放图协议已经成为在 web 上共享丰富内容的标准机制。

在本指南中，我们将向您展示如何为可共享预览(通常称为社交媒体预览)实现 Open Graph 标签，向您介绍您可以使用的工具，并研究不同平台的渲染问题。

![Open Graph Logo and Screenshot](img/982b0ea69050c19defebe0a5105ec32f.png)

## 开放图协议与共享

您可能已经注意到，当您共享一个 URL 时，您共享的平台会显示该链接的预览。

以下是我在 Twitter 上分享博客链接时发生的一个例子:

![Open Graph Protocol: Sharable Tweet Examples](img/a206e8027a739e6ac963e29d5b749f62.png)

分享链接会自动在推文底部生成预览卡。它包含一张图片、博客的标题以及文章的描述。

除了看起来非常精彩之外，这个预览给了推特的读者一些关于帖子中可能会有什么的相当丰富的信息。如果他们显然对这个帖子不是特别感兴趣，这可能会节省他们的点击量。相反，如果它看起来很吸引人，它会增加读者点击的可能性。分享预览是一种资产。

Twitter 使用 Open Graph meta 标签和我的博客上的一些自定义标签的组合制作了这张卡片。

由脸书提出的[开放图协议](https://ogp.me/)这样描述自己:

> 开放图协议使得任何网页都能够成为社交图中的丰富对象。例如，这被用在脸书上，允许任何网页具有与脸书上的任何其他对象相同的功能。

Open Graph 完全是关于元标记的——具体来说，就是将元标记添加到 HTML 页面中，以明确定义标准化信息。这样做有很多目的，但是对于本教程，我们将只关注共享。

现在我们已经了解了共享预览给我们带来了什么，让我们来演示一下它们是如何工作的。

[Open Graph 网站](https://ogp.me/#metadata)对 Open Graph 的最低要求有一个很好的演示:

> *   `og:title`是图形中显示的对象的标题，例如“石头”
> *   `og:type`是对象的类型，例如“video.movie”。根据您指定的类型，可能还需要其他属性
> *   `og:image`是一个图像 URL，它应该代表图形中的对象
> *   `og:url`是您的对象的规范 URL，将在图中用作其永久 ID，例如，“[https://www.imdb.com/title/tt0117500/&# 8221](https://www.imdb.com/title/tt0117500/&#8221)；。

作为一个例子，下面是 IMDB 上的 Rock 的开放图协议标记:

> ```
> <html prefix="og: https://ogp.me/ns#">   <head>     <title>The Rock (1996)</title>     <meta property="og:title" content="The Rock" />     <meta property="og:type" content="video.movie" />     <meta property="og:url" content="https://www.imdb.com/title/tt0117500/" />     <meta       property="og:image"       content="https://ia.media-imdb.com/images/rock.jpg"     />     ...   </head>   ... </html> 
> ```

共享预览的需求非常相似，但关键是略有不同。生成可共享的预览需要五个标签:

*   `og:title`，你页面的标题
*   `og:description`，对该页面内容的描述
*   `og:image`，应该出现在社交媒体共享中的图片 URL
*   `og:url`，你的网页的规范 URL。
*   `twitter:card`，一个[定制标签，只有 Twitter](https://developer.twitter.com/en/docs/twitter-for-websites/cards/guides/getting-started#started) 需要它来指示共享类型，可以是`"summary"`、`"summary_large_image"`、`"app"`或`"player"`——对于大多数用例可能是`"summary"`或`"summary_large_image"`

如果我们实现了这些，我们的页面将提供可共享的预览。

## 在 React 网站中实现开放图形

有了这样的理解，让我们看看在网站上添加可共享的预览会是什么样子。我们将为自己打造一个 React 网站，包括:

```
npx react-static create
```

出现提示时，将站点命名为`demo`并选择`blank`模板。

我们在这里所做的一切都不是特定于 React 的；它适用于所有的网站，不管它们是用什么技术构建的；这只是一个简单的演示网站的方法。

我们在这个演示中使用了 [React Static](https://github.com/react-static/react-static) ,因为它是一个静态站点生成器。这很重要，因为一般来说，许多支持共享的平台不会抓取动态生成的元标签。这里我们指的是 JavaScript 在运行时生成的标签。

相反，这些标签必须放入提供的 HTML 中。解决这个问题的一个方法是通过从服务器动态提供元标签。另一种确保标签首先出现在构建的 HTML 中的方法。这正是像 React Static 这样的静态站点生成器所做的。

我们将用我们自己的`App.js`替换脚手架上的`App.js`:

```
import * as React from 'react';
import { Head } from 'react-static';
import './app.css';

function App() {
  const openGraphData = {
    title: 'Open Graph: a guide to sharing previews',
    description:
      'This page features the Open Graph protocol markup for sharing previews.',
    url: 'https://johnnyreilly.github.io/open-graph-sharing-previews/',
    image:
      'https://upload.wikimedia.org/wikipedia/commons/7/72/Open_Graph_protocol_logo.png',
  };
  return (
    <div className="App">
      <Head>
        <meta property="og:title" content={openGraphData.title} />
        <meta property="og:description" content={openGraphData.description} />
        <meta property="og:url" content={openGraphData.url} />
        <meta property="og:image" content={openGraphData.image} />
        <meta name="twitter:card" content="summary" />
      </Head>
      <h1>{openGraphData.title}</h1>
      <img src={openGraphData.image} alt="The Open Graph protocol logo" />
      <h2>Share it and see!</h2>
    </div>
  );
}

export default App;

```

上面的代码呈现了共享预览所需的 meta 标签。当我们构建和部署它时，我们可以看到它们是这样显示的:

![Open Graph Meta Tags](img/8d3e259296e7c4a027ef68fae2987348.png)

现在我们有了一个演示，如果能够测试出来，那将是非常棒的。以下社交平台有官方工具来测试你的网址:

也有一些非官方的聚合工具，试图在多个平台上呈现你的社交预览的外观，以节省你依次访问每个工具的麻烦。一些突出的例子包括:

让我们测试一下 Twitter 验证器:

![Open Graph Protocol: Social Preview Test on Twitter](img/afe4bb0b37b82051a705eda727a790c0.png)

太棒了。我们已经为我们制作的网站启用了可共享预览。现在，我们对共享预览看起来像什么，它们的动力是什么，以及如何实现它们有了一个概念。

## 可共享预览渲染:尚不标准

到目前为止，我们只在 Twitter 上看到了分享预览的例子。然而，对开放图形共享预览的支持是普遍的。

其他可以使用分享预览的地方包括脸书、Polywork、Slack、Teams、LinkedIn、Outlook.com、Discord——这个列表确实很长，而且还在增长。

但是每个平台都是按照自己的标准实现共享预览的。什么意思？嗯，在 Twitter 上分享的链接看起来和在 Outlook 上分享的不同。例如:

![Open Graph Protocol: Preview Test in Outlook](img/86b7f2cef326909705822eac3cdb9779.png)

上面，我分享了一篇博文的链接。图像在左边，标题和描述在右边。

现在我们来看看 Twitter 上分享的同一个链接:

![Open Graph Protocol Demo on Twitter](img/fda6ec31c406cd18c05339e5735a57a3.png)

这里，图片在标题和描述的上面。更糟糕的是，图像被裁剪了，这使得标题有点不可读。因此，虽然共享的机制已经大致标准化，但是渲染却没有。

这和 2000 年的网络没什么不同。当时，根据浏览器的不同，一段 HTML 可以用许多不同的方式呈现。现在开放的图形共享也是如此。根据显示预览的平台不同，共享看起来会有很大的不同。

目前避免这种情况的唯一方法是在你想要分享链接的所有平台上进行彻底的测试，以确保可分享的预览看起来可以接受。

## 结论

在本指南中，我们向您介绍了可共享预览的概念，演示了如何向网站添加可共享预览以及如何测试它们，并回顾了一些需要注意的粗糙边缘。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

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