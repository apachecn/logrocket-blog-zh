# React 18 流 SSR 指南

> 原文：<https://blog.logrocket.com/streaming-ssr-with-react-18/>

React 18 推出了很多令人兴奋的变化和功能。这可能是你已经听过很多的事情，而且理由很充分。尽管稍不引人注目，React SSR 架构中也有一些非常令人兴奋的发展。为了理解 React 18 带来的突破，有必要看看整个时间线以及导致它的增量步骤。

在我们深入了解 SSR 的前因后果之前，首先了解我们进行 SSR 的原因至关重要。特别是，我们将深入探讨它的重要性以及它的哪些方面决定了 React 团队决定改进他们的 SSR 架构的方式。

在本文中，我们将仔细研究 SSR，因为对这一主题有一个基本的了解以及它与其他技术的比较是很重要的，最重要的是客户端渲染(CSR)。不幸的是，我们无法在本文中涵盖全部内容，而将只关注 React 18 上下文中 SSR 的重要方面。虽然并非绝对必要，但我们建议您温习一下这个主题，以便充分利用本文。

*向前跳转:*

## SSR 的简短介绍

本质上，实施 SSR 的最重要原因是:

*   表演
*   搜索引擎优化
*   用户体验(UX)

本质上，存在一个使用 SSR 的 React 应用程序的特定渲染流程。首先，服务器接管客户端获取所有数据并呈现整个 React 应用程序的责任。这样做之后，产生的 HTML 和 JavaScript 从服务器发送到客户机。最后，客户端将 HTML 放到屏幕上，并用适当的 JavaScript 将其连接起来，这也被称为[水合](https://www.joshwcomeau.com/react/the-perils-of-rehydration/)过程。现在，客户机接收整个 HTML 结构，而不是它需要自己呈现的一大堆 JavaScript。

这种流程的好处包括网络爬虫更容易访问这些页面，从而提高 SEO，并且客户端可以快速向用户显示生成的 HTML，而不是一片空白，从而提高 UX。因为所有的渲染都发生在服务器上，所以客户端被免除了这一职责，并且没有成为低端设备场景中的瓶颈的风险，从而提高了性能。

然而，上述设置只是 SSR 的一个起点。基于上面的事情是如何实现的，在性能和 UX 方面有更多的收获。考虑到这两个方面，让我们沿着 React SSR 内存通道走一趟，深入 React 18 之前的问题，体验它随着时间的推移而发生的变化，并了解 React 18 及其流媒体功能如何改变一切。

## (流式)SSR，预反应 18

在 React 18、[悬念](https://blog.logrocket.com/react-suspense-data-fetching/)或任何新的流媒体功能出现之前，React 中典型的 SSR 设置如下所示。虽然不同的实现可能会有细微的差别，但大多数设置都遵循相似的架构。

```
// server/index.ts
import path from 'path';
import fs from 'fs';

import React from 'react';
import ReactDOMServer from 'react-dom/server';
import express from 'express';

import { App } from '../client/App';

const app = express();

app.get('/', (req, res) => {
    const appContent = ReactDOMServer.renderToString(<App />);
    const indexFile = path.resolve('./build/index.html');

    fs.readFile(indexFile, 'utf8', (err, data) => {
        if (err) {
                console.error('Something went wrong:', err);
                return res.status(500).send('Failed to load the app.');
        }

        return res.send(
                data.replace('<div id="root"></div>', `<div id="root">${app}</div>`)
        );
    });
});

app.use(express.static('./build'));

app.listen(8080, () => {
    console.log(`Server is listening on port ${PORT}`);
});

// build/index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>React App</title>
    <script src="main.js" async defer></script>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>

```

SSR 设置的最大部分是服务器，所以让我们从它开始。在这个例子中，我们使用 Express 启动一个服务器，在端口 8080 上提供来自我们的`build`文件夹的文件。当服务器在根 URL 接收到一个请求时，它会使用来自`ReactDOMServer`包的`renderToString`函数将 React 应用程序呈现为一个 HTML 字符串。

然后需要将结果发送回客户端。但在此之前，服务器需要用适当的 HTML 结构包围呈现的应用程序。为此，该示例在`build`文件夹中查找`index.html`文件，导入它，并将呈现的应用程序注入根元素:

```
// client/index.ts
import React from "react";
import ReactDOM from 'react-dom';
import { App } from './App';

// Instead of `ReactDOM.render(...)`
ReactDOM.hydrate(<App />, document.getElementById('root'));

```

然后，需要在客户端进行的主要更改是，它不再需要呈现应用程序。

正如我们在上一步中看到的，服务器已经呈现了应用程序。所以现在，客户端只负责给应用程序补水。它通过使用`ReactDOM.hydrate`函数而不是`ReactDOM.render`来实现。

虽然这是 React SSR 的一个工作设置，但它在性能和 UX 方面仍有一些主要缺点:

*   虽然服务器现在负责呈现 React 应用程序，但服务器端呈现的内容仍然是一大块 HTML，需要在呈现之前向客户端传输
*   由于 React 组件相互依赖的特性，服务器必须等待所有数据被获取，然后才能开始呈现组件、生成 HTML 响应并将其发送给客户端
*   客户端仍然需要加载整个应用程序的 JavaScript，然后才能开始合成服务器的 HTML 响应
*   水合过程需要一次完成，但是组件只有在水合之后才能交互，这意味着在水合完成之前用户不能与页面交互

最后，所有这些缺点归结为当前的设置，它仍然是从服务器到客户端的瀑布式方法。这就形成了一个从一端到另一端的要么全有要么全无的流程:要么将整个 HTML 响应发送到客户机，要么获取所有数据以便服务器可以开始呈现，要么整个应用程序是否被水合，要么整个页面有响应或没有响应。

在 React 16 中，在现有的`renderToString`之上引入了`[renderToNodeStream server rendering function](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)`。就设置和结果而言，除了这个函数返回一个 Node.js `ReadableStream`之外，没有太大变化。这允许服务器将 HTML 流式传输到客户机。

```
app.get('/', (req, res) => {
    const endHTML = "</div></body></html>";
    const indexFile = path.resolve('./build/index.html');

    fs.readFile(indexFile, 'utf8', (err, data) => {
            if (err) {
              console.error('Something went wrong:', err);
              return res.status(500).send('Failed to load the app.');   
        }

        // Split the HTML where the React app should be injected and send the first part to the client
        const beginHTML = data.replace('</div></body></html>', '');
        res.write(beginHTML);

        // Render the application into a stream using `renderToNodeStream`  and pipe that into the response
        const appStream = ReactDOMServer.renderToNodeStream(<App />);
        appStream.pipe(res, { end: 'false' });

        // When the server is done rendering, send the rest of the HTML
        appStream.on('end', () => {
            response.end(endHTML);
        )};
    });  
});

```

这个新功能部分解决了我们描述的一个缺点；也就是说，它必须将 HTML 响应作为一个大 blob 从服务器传输到客户机。但是，服务器仍然需要等待整个 HTML 结构生成，然后才能开始向客户端传输任何内容。因此，它并没有真正解决我们描述的任何其他缺点。

现在，让我们看看 React 18 推出新功能后的情况，以及它们如何解决这些缺点。

## 流式 SSR 后反应 18

SSR 体系结构 post-React 18 涉及几个不同的部分。这些都不能单独解决我们描述的任何缺点，但是它们的结合创造了奇迹。因此，要完全理解整个设置，有必要研究所有这些设置以及它们的作用。

### `Suspense`组件

这一切的中心是著名的`[Suspense component](https://reactjs.org/blog/2022/03/29/react-v18.html#new-suspense-features)`。它是我们将要描述的所有特性的主要入口，所以让我们从它开始吧。

```
// client/src/SomeComponent.js
import { lazy, Suspense } from 'react';

const SomeInnerComponent = lazy(() => import('./SomeInnerComponent.js' /* webpackPrefetch: true */));

export default function SomeComponent() {
    // ...
    return (
        <Suspense fallback={<Spinner />}>
          <SomeInnerComponent />
        </Suspense>
    );
}

```

简而言之，悬念是开发人员告诉 React 应用程序的某个部分正在等待数据的一种机制。与此同时，React 将在其位置显示一个回退 UI，并在数据准备好时更新它。

这听起来与以前的方法没有太大区别，但从根本上说，它以一种更优雅和集成的方式同步了 React 的渲染过程和数据获取过程。要了解更多细节，请看一下[这个悬念指南](https://www.chakshunyu.com/blog/a-fundamental-guide-to-react-suspense/)。

暂挂边界根据应用程序的数据获取需求将应用程序分割成块，然后服务器可以使用这些块来延迟呈现挂起的内容。同时，它可以预先呈现有数据可用的块，并将其传输到客户端。当先前挂起的块的数据准备好时，服务器将呈现它，并使用开放流再次将其发送给客户端。

与用于将 JavaScript 包代码分割成更小部分的`React.lazy`一起，它提供了解决剩余瀑布缺陷的第一个难题。

然而，问题是在 React 18 之前，使用`React.lazy`的悬念和代码分割还不兼容 SSR。

### `renderToPipeableStream` API

为了理解剩余的连接拼图，我们来看看 React 团队在他们的[工作组讨论](https://github.com/reactwg/react-18/discussions/37)中为 React 18 后的架构提供的[悬念 SSR 示例](https://codesandbox.io/s/kind-sammet-j56ro?file=/src/App.js)。

```
import ReactDOMServer from "react-dom/server";
import { App } from "../client/App";

app.get('/', (req, res) => {
    res.socket.on('error', (error) => console.log('Fatal', error));

    let didError = false;
    const stream = ReactDOMServer.renderToPipeableStream(
        <App />,
        {
            bootstrapScripts: ['/main.js'],
            onShellReady: () => {
                res.statusCode = didError ? 500 : 200;
                res.setHeader('Content-type', 'text/html');
                stream.pipe(res);
            },
            onError: (error) => {
                didError = true;
                console.log(error);
            } 
        }
    );
});

```

与之前的设置相比，最显著的变化是在服务器端使用了`renderToPipeableStream` API。这是 React 18 中新引入的服务器渲染功能，返回可管道化的 Node.js 流。虽然以前的`renderToNodeStream`不能等待数据，并且会缓冲整个 HTML 内容，直到流的末尾，但是`renderToPipeableStream`函数没有这些限制。

当`Suspense`边界以上的内容准备好了，就调用`onShellReady`回调。如果在此期间发生任何错误，都会反映在对客户端的响应中。然后，我们将开始通过管道将 HTML 传输到响应中，从而将 HTML 传输到客户端。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

之后，该流将保持打开，并将任何后续呈现的 HTML 块传输到客户端。这是相比其前一版本最大的变化。

这个渲染功能通过服务器端的`React.lazy`与悬念特性和代码分割完全集成，这就是启用 SSR 的流 HTML 特性的原因。这解决了前面描述的 HTML 和数据获取的瀑布，因为应用程序可以基于数据需求增量地呈现和传输。

```
// client/index.ts
import React from "react";
import ReactDOMClient from 'react-dom/client';
import { App } from './App';

// Instead of `ReactDOM.hydrate(...)`
ReactDOMClient.hydrateRoot(document.getElementById('root'), <App />);

```

### 引入`ReactDOMClient.hydrateRoot`进行选择性水合

在客户端，唯一需要做的改变是应用程序在屏幕上的显示方式。作为之前`ReactDOM.hydrate`的替代，React 团队在 React 18 中引入了新的`ReactDOMClient.hydrateRoot`。虽然变化很小，但它带来了许多改进和特性。对我们来说，最重要的是选择性水合作用。

如前所述，悬念根据数据需求将应用程序分割成 HTML 块，而代码分割将应用程序分割成 JavaScript 块。选择性补水允许 React 在客户端将这些东西放在一起，并在不同的时间和优先级开始补水。它可以在收到大量 HTML 和 JS 时立即开始水合，并对用户与之交互的部分的水合队列进行优先排序。

这解决了剩下的两个瀑布问题:必须等待所有 JavaScript 加载完毕才能开始水合，或者水合整个应用程序，或者一个都不水合。

选择性水合和其他提到的特性的组合允许 React 在加载必要的 JavaScript 代码后立即开始水合，同时还能够根据优先级分别水合应用程序的不同部分。

## 下一步是什么？

React 18 是其 SSR 架构经过几个主要版本和多年微调的长期发展变化的基础上的樱桃。悬念和代码分割是拼图的早期部分，但直到 React 18 中引入流 HTML 和选择性水合作用后，才能在服务器上发挥其全部潜力。

为了帮助您理解这些变化是如何实现的，我们查看了 React 18 前后的情况，探索了设置 SSR 的典型方法的代码示例，深入研究了 React SSR 架构面临的主要缺点，最后回顾了悬念、代码分割、流 HTML 和选择性水合的组合是如何解决这些问题的。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)