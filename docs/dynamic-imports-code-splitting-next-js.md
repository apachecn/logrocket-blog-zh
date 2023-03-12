# 使用 Next.js 进行动态导入和代码拆分

> 原文：<https://blog.logrocket.com/dynamic-imports-code-splitting-next-js/>

## 介绍

优化生产环境中的性能有时是一项艰巨的任务。不能忽视对网站性能的微调，因为缺点会导致网页速度慢，UX 不好。这些网站往往加载缓慢，图像渲染缓慢，从长远来看，会导致网站访问者的跳出率增加，因为大多数用户不愿意等待内容弹出。

在本教程中，我们将介绍在 Next.js 应用程序中提高站点性能的不同模式。

### 目标和前提条件

在本文结束时，读者将清楚地了解如何在 Next.js web 应用程序中最大化性能。我们将讨论以下内容:

要阅读本文，需要先了解 Next.js 框架。

## 什么是动态导入和代码拆分？

动态导入，也称为代码分割，指的是将 JavaScript 代码束分割成更小的块，然后将它们拼凑在一起并加载到应用程序的运行时，作为大幅提升站点性能的一种方法。

它是作为 JavaScript 中静态导入的升级而开发的，静态导入是使用导入语法为 JavaScript 模块顶层的模块或组件添加导入的标准方式。

虽然这是一种常用的方法，但在性能优化方面存在一些缺点，尤其是在以下情况下:

*   大型代码库，它会创建更大的包，并导致更长的加载时间，因为构建过程会将所有需要的文件编译成一个包
*   需要某些用户操作的网页，例如单击导航菜单项以触发页面加载。这里，仅当满足导航标准时才呈现所需的页面，并且当静态导入组件时，可能会触发缓慢的初始页面加载

## 动态导入与静态导入有何不同？

与静态导入不同，动态导入通过应用一种称为代码拆分的方法来工作。代码分割是将代码分成不同的包，这些包使用树格式并行排列，其中模块是动态加载的——模块仅在需要时导入并包含在 JavaScript 包中。代码分割得越多，包的大小就越小，页面加载就越快。

该方法创建了在网页运行时动态加载的多个包。动态导入利用作为内联函数调用编写的导入语句。

我们来看一个对比。假设我们希望在应用程序中导入一个导航组件；导航组件的静态和动态导入示例如下所示:

静态导入:

```
import Nav from './components/Nav'
export default function Home() {
  return (
    <div>
      <Nav/>
    </div>
  )
}

```

动态导入:

```
import dynamic from "next/dynamic";
import { Suspense } from "react";
export default function Home() {
  const Navigation = dynamic(() => import("./components/Nav.js"), {
    suspense: true,
  });
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <Navigation />
      </Suspense>
    </div>
  );
}

```

这里，导航组件的相关部分在`import()`块中指定。注意`next/dynamic`不允许在`import()`参数中使用模板文字或变量。

另外，`react/suspense`有一个指定的 fallback 元素，它会一直显示，直到导入的组件可用。

## Next.js 中动态导入的好处

作为实施动态导入的结果，优化站点性能将反过来带来以下站点优势:

*   更快的页面加载速度:网站加载和显示内容的速度至关重要，因为你的受众希望快速完成工作，不会停留在慢速网页上
    *   动态导入对映像加载时间也有积极的影响
*   跳出率低:跳出率是指用户在没有与网站互动的情况下退出网页的比率，通常表明(并由)加载速度慢。较低的跳出率通常意味着更快的网站性能
*   改进的站点交互时间:这处理 TTI，或交互时间，即用户请求一个动作和用户得到结果之间的时间间隔。这些交互可以包括点击链接、滚动页面、在搜索字段中输入内容、向购物车添加商品等。
*   更高的网站转换率:随着越来越多的用户从使用优化良好的网站中获得满足感，他们将更有可能转化

有了这些好处，您可能会考虑如何在应用程序中使用动态导入。那么，最大的问题是，我们如何在 Next.js 应用程序中实现动态导入和代码拆分？下一节将展示如何实现这一点的详细步骤。

## 在 Next.js 中实现动态导入和代码拆分

如上所示，Next.js 使得通过`next/dynamic`模块在下一个应用程序中创建动态导入变得容易。`next/dynamic`模块实现 React 组件的惰性加载导入，并构建在 [React Lazy](https://reactjs.org/docs/code-splitting.html#reactlazy) 之上。

它还利用 [React 暂记库](https://blog.logrocket.com/async-rendering-react-suspense/)来允许应用程序推迟加载组件，直到需要它们，从而由于更轻的 JavaScript 构建而提高初始加载性能。

### 动态导入命名导出

在本文的前面，我们演示了使用`next/dynamic`导入组件。但是我们也可以对从另一个文件导出的函数或方法进行动态导入。这一点如下所示:

```
import React from 'react'

export function SayWelcome() {
  return (
    <div>Welcome to my application</div>
  )
}
const SayHello = () => {
  return (
    <div>SayHello</div>
  )
}
export default SayHello

```

在上面的代码中，我们有一个组件`SayHello`和一个命名的导入`SayWelcome`。我们可以只对`SayWelcome`方法进行动态显式导入，如下所示:

```
import dynamic from "next/dynamic";
import { Suspense } from "react";
export default function Home() {
  const SayWelcome = dynamic(
    () => import("./components/SayHello").then((res) => res.SayWelcome)
  );
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <SayWelcome />
      </Suspense>
    </div>
  );
}

```

上面的代码导入`SayHello`组件，然后从响应中返回`SayWelcome`导出。

### 动态导入多个组件

假设我们有`UserDetails`和`UserImage`组件。我们可以导入并显示这两个组件，如下所示:

```
import dynamic from 'next/dynamic'

const details = dynamic(() => import('./components/UserDetails'))
const image = dynamic(() => import('./components/UserImage'))

function UserAccount() {
  return (
    <div>
      <h1>Profile Page</h1>
      <details />
      <image />
    </div>
  )
}

const App = () => {
  return (
    <>
      <UserAccount />
  )
    </>
}

export default App

```

在上面的代码中，我们为`UserDetails`和`UserImage`组件添加了动态导入，然后我们将这些组件放入一个单独的组件`UserAccount`。最后，我们返回了应用程序中的`UserAccount`组件。

## 客户端渲染的动态导入

使用`next/dynamic`模块，我们还可以禁用导入组件的服务器端呈现，而是在客户端呈现这些组件。这特别适合于不需要太多用户交互或者具有外部依赖性的组件，比如 API。这可以通过在导入组件时将`ssr`属性设置为`false`来实现:

```
import dynamic from 'next/dynamic'

const HeroItem = dynamic(() => import('../components/HeroItem'), {
  ssr: false,
})

const App = () => {
  return (
    <>
      <HeroItem />
  )
    </>
}

```

这里，`HeroItem`组件的服务器端呈现设置为`false`，因此它在客户端呈现。

### 库的动态导入

除了导入本地组件，我们还可以为外部依赖项添加动态导入。

例如，假设我们希望[在用户请求时使用 Axios `fetch`](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/) 从 API 获取数据。我们可以为`Axios`定义一个动态导入并实现它，如下所示:

```
import styles from "../styles/Home.module.css";
import { React, useState } from "react";

export default function Home() {
  const [search, setSearch] = useState("");
  let [response, setResponse] = useState([]);
  const api_url = `https://api.github.com/search/users?q=${search}&per_page=5`;

  return (
    <div className={styles.main}>
      <input
        type="text"
        placeholder="Search Github Users"
        value={search}
        onChange={(e) => setSearch(e.target.value)}
      />

      <button
        onClick={async () => {
          // dynamically load the axios dependency
          const axios = (await import("axios")).default;
          const res = await axios.get(api_url).then((res) => {
            setResponse(res);
          });

        }}
      >
        Search for GitHub users
      </button>

      <div>
        <h1>{search} Results</h1>
        <ul>
          {response?.data ? (
            response && response?.data.items.map((item, index) => (
              <span key={index}>
                <p>{item.login}</p>
              </span>
            ))
          ) : (
            <p>No Results</p>
          )}
        </ul>
      </div>
    </div>
  );
}

```

在上面的代码中，我们有一个输入字段来搜索 GitHub 上的用户名。我们使用`useState()`钩子来管理和更新输入字段的状态，并且我们已经设置了当点击按钮`Search for GitHub users`时动态导入的`Axios`依赖项。

当返回响应时，我们对其进行映射并显示五个用户的`usernames`,这五个用户的名字与输入字段中输入的搜索查询相对应。
下面的 GIF 演示了上面的代码块:

![Dynamically importing libraries to a Next.js app](img/1da6041fd997d7dad3eeb36513c0219e.png)

## 结论

在本文中，我们学习了动态导入/代码分割，它的优点，以及如何在 Next.js 应用程序中使用它。

总的来说，如果你想缩短网站加载的时间，动态导入和代码拆分是一个必须的方法。如果你的网站有图片，或者显示的结果依赖于用户的交互，那么动态导入将会显著提高网站的性能和用户体验。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。