# React 悬念:React 中的异步渲染

> 原文：<https://blog.logrocket.com/async-rendering-react-suspense/>

***编者按**:这篇博文更新于 2021 年 8 月 30 日，包含了关于 React 悬念的最新信息。*

悬念是 React 16.6 中引入的 React 新特性。它旨在帮助处理异步操作，让您等待一些代码加载，并在等待时声明性地指定加载状态(像一个微调器)。

它允许您推迟呈现应用程序树的一部分，直到满足某些条件(例如，加载了来自端点或资源的数据)。虽然本文将重点关注悬念的数据获取应用程序，但悬念也可以用于获取其他资源，如图像、脚本或其他异步工作。

在本文中，我们将探索悬念，看看这个特性将对 React 应用程序的构建方式产生什么样的潜在影响。

在我们继续这篇文章之前，请注意 React 悬念是一个实验性的功能，仍然在经历变化，特别是随着 React 18 的即将发布。

## 为什么反应悬疑？

您很有可能遇到过利用加载图标作为正在获取数据的指示器的 spa。这是一种常用方法，用于确保从外部来源获取数据的应用程序具有良好的 UX。您所要做的就是检查数据是否已经成功获取，如果没有，就显示一个微调器。

但是，当数据获取过程变得复杂时，这可能无法扩展:

*   当父组件和子组件都有加载状态时
*   当您需要一个组件在其他(子)组件加载后才加载时

要看到悬疑在行动，让我们来对比一下经典的条件渲染方法对悬疑的实现。

我已经在这个 [GitHub 库](https://github.com/yomete/react-suspense)中构建了经典的条件渲染方法，您可以查看一下。代码库是一个从 [TVMaze](https://www.tvmaze.com/api) 获取数据的 React 应用程序，这是一个免费的公共 API，可以让你搜索关于电视节目的信息。

这里感兴趣的主要文件是`Shows/index.js`文件，它负责获取所需的数据并呈现出来:

```
import axios from "axios";
import { useEffect, useState } from "react";
import * as Styles from "./styles";
const formatScore = (number) => {
 return Math.round(number * 100);
};
const Shows = () => {
 const [isLoaded, setIsLoaded] = useState(false);
 const [shows, setShows] = useState([]);

 useEffect(() => {
   axios(`https://api.tvmaze.com/search/shows?q=heist`)
     .then((r) => {
       console.log(r);
       setShows(r.data);
       setIsLoaded(true);
     })
     .catch((e) => {
       setIsLoaded(false);
       console.log(e);
     });
 }, []);

 return (
   <Styles.Root>
     {!isLoaded && <p>loading...</p>}
     {isLoaded && (
       <Styles.Container>
         {shows.map((show, index) => (
           <Styles.ShowWrapper key={index}>
             <Styles.ImageWrapper>
               <img
                 src={show.show.image ? show.show.image.original : ""}
                 alt="Show Poster"
               />
             </Styles.ImageWrapper>

             <Styles.TextWrapper>
               <Styles.Title>{show.show.name}</Styles.Title>
               <Styles.Subtitle>
                 Score: {formatScore(show.score)}
               </Styles.Subtitle>
               <Styles.Subtitle>Status: {show.show.status}</Styles.Subtitle>
               <Styles.Subtitle>
                 Network: {show.show.network ? show.show.network.name : "N/A"}
               </Styles.Subtitle>
             </Styles.TextWrapper>
           </Styles.ShowWrapper>
         ))}
       </Styles.Container>
     )}
   </Styles.Root>
 );
};

export default Shows;

```

如上面的代码块所示，该组件有一个`useEffect`函数，用于从 TVMaze 的 API 获取数据，并将其存储在本地状态中。因为我们希望保持应用程序的交互性，不让用户看到一个空屏幕，所以有一个`isLoaded`状态，它的值是一个布尔值。`isLoaded`用于根据数据是否已成功提取，在屏幕上显示加载指示器。

这种方法通常被称为“渲染时提取”，因为提取直到组件被渲染后才开始。

现在让我们看看如何使用 React 悬念重写这个例子。您可以从这里开始[克隆现有的回购。](https://github.com/yomete/react-suspense)

首先，让我们确保安装了 React (alpha)的实验版本，因为悬念仍然是一个实验功能。您可以通过运行以下命令来实现这一点:

```
npm install [email protected] [email protected]
```

让我们从向 React 应用程序添加悬念组件开始。我们需要将所需的组件放在`<Suspense />`组件中，以便在所需的数据可用之前，悬念知道哪些组件需要成为`blocked`。

我们可以通过用下面的代码块编辑`components`目录中的`App.js`文件来做到这一点:

```
import React, { Suspense } from "react";
import "./App.css";

const Shows = React.lazy(() => import("./components/Shows"));

function App() {
 return (
   <div className="App">
     <header className="App-header">
       <h1 className="App-title">React Suspense Demo</h1>
     </header>
     <Suspense fallback={<p>loading...</p>}>
       <Shows />
     </Suspense>
   </div>
 );
}

export default App;

```

在上面的代码块中，`Shows`组件被一个`Suspense`组件包装，后者有一个`fallback`道具。这意味着当组件`Shows`等待一些异步操作时，比如从 TVMaze 的 API 中获取节目，React 将把`<p>loading...</p>`呈现给 DOM。然后，只有在承诺和 API 被解析之后，才会呈现`Shows`组件。

悬念本身不是一个数据获取库。相反，它是一种机制，让数据读取库进行通信，以对组件正在读取的数据尚未准备好做出反应。

目前，将悬念与数据获取库结合使用的方法是利用一种称为资源的机制。

资源是暂记异步数据的来源。资源只是一个返回`read`方法的对象。`read`方法要么返回异步数据的结果，要么返回一个错误。React Suspense 将根据内部工作需要调用该方法。

接下来，让我们为这个例子创建存放资源的文件。在`components`目录下创建一个新文件`fetchShows.js`。用下面的代码块编辑它:

```
import axios from "axios";

export const fetchShows = () => {
 let status = "pending";
 let result;
 let suspender = axios(`https://api.tvmaze.com/search/shows?q=heist`).then(
   (r) => {
     status = "success";
     result = r.data;
   },
   (e) => {
     status = "error";
     result = e;
   }
 );
 return {
   read() {
     if (status === "pending") {
       throw suspender;
     } else if (status === "error") {
       throw result;
     } else if (status === "success") {
       return result;
     }
   },
 };
};

```

上面的代码块中，`fetchShows`函数返回的是`read`对象，这是悬疑需要的资源。在函数的顶端有一些变量；`result`变量将保存来自 API 请求和`status`的响应，其中包含 API 请求的当前状态，可以是`pending`、`success`或`error`。

还有一个`suspender`变量存储 API 请求本身。在服务器响应之后，`status`和`result`变量被更新。下面是`read`对象的工作方式:

在 API 的`.then()`函数中将变量`status`和`result`设置为合适的值后，如果`status`为`pending`，则`read`对象自身抛出`suspender`承诺，该悬念将捕捉并显示`fallback`组件。

如果状态为完成，`read`对象返回结果，如果状态为`error`，`read`对象抛出`result`，在这种情况下，这是一个错误实例。

接下来，让我们看看如何在`Shows`组件中使用资源。用下面的代码块编辑`Shows/index.js`文件:

```
import { fetchShows } from "../fetchShows";
import * as Styles from "./styles";

const resource = fetchShows();

const formatScore = (number) => {
 return Math.round(number * 100);
};

const Shows = () => {
 const shows = resource.read();

 return (
   <Styles.Root>
     <Styles.Container>
       {shows.map((show, index) => (
         <Styles.ShowWrapper key={index}>
           <Styles.ImageWrapper>
             <img
               src={show.show.image ? show.show.image.original : ""}
               alt="Show Poster"
             />
           </Styles.ImageWrapper>

           <Styles.TextWrapper>
             <Styles.Title>{show.show.name}</Styles.Title>
             <Styles.Subtitle>
               Score: {formatScore(show.score)}
             </Styles.Subtitle>
             <Styles.Subtitle>Status: {show.show.status}</Styles.Subtitle>
             <Styles.Subtitle>
               Network: {show.show.network ? show.show.network.name : "N/A"}
             </Styles.Subtitle>
           </Styles.TextWrapper>
         </Styles.ShowWrapper>
       ))}
     </Styles.Container>
   </Styles.Root>
 );
};

export default Shows;

```

在上面的代码块中，资源从`fetchShows`导入并初始化为`resource`变量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们不需要检查是否为空，也不需要显示一个加载指示器，而是只调用`resource.read()`而不进行任何安全检查。尽管从 TVMaze 的 API 获取节目是一个异步过程，但资源是同步使用的，因为悬念负责获取和检查数据。

这种方法通过消除对安全检查、条件呈现、防止竞争条件的需要，使我们的应用程序更干净、更健壮。

## 反应悬念`ErrorBoundary`

悬疑要考虑的另一件事是它如何处理错误。我们在资源文件`fetchShows.js`中抛出一个错误，但是我们没有做任何事情来通知用户发生了一些错误。

React 提供了错误边界来帮助解决这个问题。[错误边界](https://blog.logrocket.com/handling-javascript-errors-react-error-boundaries/)是 React 组件，它捕捉子组件树中任何地方的 JavaScript 错误，记录这些错误，并显示一个回退 UI，而不是崩溃的组件树。因为如果资源文件中有错误，我们会抛出一个错误，这意味着我们可以创建一个`ErrorBoundary`组件，并将其嵌入到我们的组件树中。

因此，让我们创建错误边界组件，以便在我们的项目中使用。在`src/components`目录下创建一个文件`ErrorBoundary.js`:

```
import React from "react";

// Error boundaries currently have to be classes.
class ErrorBoundary extends React.Component {
 state = { hasError: false, error: null };
 static getDerivedStateFromError(error) {
   return {
     hasError: true,
     error,
   };
 }
 render() {
   if (this.state.hasError) {
     return this.props.fallback;
   }
   return this.props.children;
 }
}

export default ErrorBoundary;

```

然后可以在`App.js`文件中使用`ErrorBoundary`组件，如下所示:

```
import React, { Suspense } from "react";

import ErrorBoundary from "./components/ErrorBoundary";
import "./App.css";

const Shows = React.lazy(() => import("./components/Shows"));

function App() {
 return (
   <div className="App">
     <header className="App-header">
       <h1 className="App-title">React Suspense Demo</h1>
     </header>
     <ErrorBoundary fallback={<p>Could not fetch TV shows.</p>}>
       <Suspense fallback={<p>loading...</p>}>
         <Shows />
       </Suspense>
     </ErrorBoundary>
   </div>
 );
}

export default App;

```

您可以通过将`fetchShows`文件中的 API URL 编辑为无效并显示`ErrorBoundary`组件来测试这一点。

## **结论**

通过暂停，您可以在加载异步数据时暂停组件渲染。您可以暂停任何状态更新，直到数据准备就绪，并且您可以向树深处的任何组件添加异步加载，而无需通过您的应用程序检查所有属性和状态并提升逻辑。

这为快速网络带来了即时和流畅的用户界面，并为慢速网络带来了有意设计的加载状态，而不是一般的加载状态。

值得注意的是，这些 API 仍处于实验模式，不适合生产。最好始终与 React 团队保持一致，了解任何 API 变化和悬念功能的更新。

上述悬念演示的代码库可以在 [GitHub](https://github.com/yomete/react-suspense/tree/suspense) 上访问，现场演示可以在 [CodeSandbox](https://codesandbox.io/s/fragrant-architecture-9rp4c?file=/src/components/fetchShows.js) 上看到。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)