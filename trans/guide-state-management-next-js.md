# 了解 Next.js - LogRocket 博客中的状态管理

> 原文：<https://blog.logrocket.com/guide-state-management-next-js/>

状态管理是任何现代 web 应用程序的核心，因为它决定了当用户与应用程序交互时，在应用程序使用会话期间屏幕上显示什么数据。

例如，勾选在线调查中的复选框，将产品添加到电子商务商店的购物车，或者从音乐播放器的播放列表中选择音频都是可能的，因为状态管理会跟踪用户的每个操作。

在本文中，我们将回顾许多状态管理方法，您可以使用它们来跟踪您的 [Next.js](https://nextjs.org) 应用程序中的状态。对于每种解决方案，我都将提供一个实际的例子，以便于理解每种方法是如何工作的。

我们将使用自顶向下的方法，首先回顾最简单的方法，然后进入更复杂用例的更高级的解决方案。

## 一个国家是如何运作的？

状态是保存数据当前状态的 JavaScript 对象。你可以把它想象成一个电灯开关，可以有“开”或“关”两种状态。现在，将相同的原理转移到 React 生态系统，并想象使用明暗模式切换。

每当用户点击开关时，相反的状态被激活。该状态随后在 JavaScript state 对象中被更新，因此您的应用程序知道哪个状态当前是活动的，以及在屏幕上显示什么主题。

不管应用程序如何管理它的数据，状态必须总是从父元素传递到子元素。

## 了解 Next.js 文件结构

因为 Next.js 是一个框架，所以它遵循特定的文件结构。为了回顾存储状态的不同方法，我们首先需要理解 Next.js 文件系统是如何构建的。

如果您在终端中运行`npx create-next-app project-name`，它将创建一个完整的 Next.js 应用程序，该应用程序由四个主要块组成:根层，然后是其中的`pages`、`public`和`styles`文件夹。

对于管理状态，我们将只使用`pages`和它里面的两个文件:`_app`和`index.js`。第一个是整个应用程序的根文件，其中配置了所有全局访问的组件。后者是`Home`组件的基本路径文件。

在`pages`文件夹中还有一个`api`文件夹，这是内置的，并且 Next.js 如何处理 API 端点的创建，允许它们接收请求和发送响应。我们将在教程的最后使用这个文件夹。

## 在 Next.js 中使用`useState`钩子进行状态管理

管理状态最常见的方法之一是使用`useState`钩子。我们将构建一个应用程序，让您通过单击按钮来增加分数。

导航到`pages`并在`index.js`中包含以下代码:

```
import { useState } from "react";

export default function Home() {
  const [score, setScore] = useState(0);
  const increaseScore = () => setScore(score + 1);

  return (
    <div>
      <p>Your score is {score}</p>
      <button onClick={increaseScore}>+</button>
    </div>
  );
}
```

我们首先导入了`useState`钩子本身，然后将初始状态设置为`0`。我们还提供了一个`setScore`功能，这样我们可以在以后更新分数。

然后我们创建了函数`increaseScore`，它访问当前的分数值，并使用`setState`将分数值增加`1`。我们为**加** **按钮**的`onClick`事件分配了函数，所以每次按下按钮，分数都会增加。

## `useReducer`钩子

`useReducer`钩子的工作方式类似于数组的 reduce 方法。我们传递一个缩减函数和一个初始值。缩减器接收当前状态和一个动作，并返回新状态。

我们将创建一个应用程序，让您将当前活动的结果乘以`2`。在`index.js`中包含以下代码:

```
import { useReducer } from "react";

export default function Home() {
  const [multiplication, dispatch] = useReducer((state, action) => {
    return state * action;
  }, 50);
  return (
    <div>
      <p>The result is {multiplication}</p>
      <button onClick={() => dispatch(2)}>Multiply by 2</button>
    </div>
  );
}
```

首先，我们导入了`useReducer`钩子本身。我们传入了缩减函数和初始状态。钩子然后返回当前状态和分派函数的数组。

我们将 dispatch 函数传递给了`onClick`事件，这样每次单击按钮时，当前状态值都会乘以`2`，并将其设置为以下值:`100`、`200`、`400`、`800`、`1600`等等。

## Next.js 中状态管理的道具钻取技术

在更高级的应用程序中，您不会直接在单个文件中处理状态。你最有可能将代码分成不同的组件，这样更容易扩展和维护应用程序。

只要有多个组件，状态就需要从父级传递到子级。这个技术叫[支柱钻](https://blog.logrocket.com/the-upsides-of-prop-drilling-in-react/)，可以多级深。

对于本教程，我们将创建一个两层深的基本示例，让您了解支柱钻孔的工作原理。将以下代码添加到`index.js`文件中:

```
import { useState } from "react";

const Message = ({ active }) => {
  return <h1>The switch is {active ? "active" : "disabled"}</h1>;
};

const Button = ({ onToggle }) => {
  return <button onClick={onToggle}>Change</button>;
};

const Switch = ({ active, onToggle }) => {
  return (
    <div>
      <Message active={active} />
      <Button onToggle={onToggle} />
    </div>
  );
};

export default function Home() {
  const [active, setActive] = useState(false);
  const toggle = () => setActive((active) => !active);

  return <Switch active={active} onToggle={toggle} />;
}
```

在上面截取的代码中，`Switch`组件本身不需要`active`和`toggle`值，但是我们必须“钻取”组件并将这些值传递给需要它们的子组件`Message`和`Button`。

## 在 Next.js 中使用上下文 API

`useState`和`useReducer`钩子，结合[道具钻孔技术](https://blog.logrocket.com/solving-prop-drilling-react-apps/)，将覆盖你构建的大多数基本应用的许多用例。

但是，如果你的应用程序非常复杂，道具需要通过多级传递，或者你有一些状态需要全局访问，那该怎么办呢？

这里，建议避免使用 prop drilling 并使用[上下文 API](https://reactjs.org/docs/context.html) ，它将允许您全局访问状态。

为不同的状态(如身份验证、用户数据等)创建单独的上下文总是一个很好的实践。我们将创建一个主题状态管理的例子。

首先，让我们在根目录中创建一个单独的文件夹，并将其命名为`context`。在内部，创建一个名为`theme.js`的新文件，并包含以下代码:

```
import { createContext, useContext, useState } from "react";

const Context = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");
  return (
    <Context.Provider value={[theme, setTheme]}>{children}</Context.Provider>
  );
}

export function useThemeContext() {
  return useContext(Context);
}
```

我们首先创建一个新的`Context`对象，创建一个`ThemeProvider`函数，并将`Context`的初始值设置为`light`。

然后，我们创建了一个定制的`useThemeContext`钩子，允许我们在将主题状态导入到应用程序的各个页面或组件中之后访问它。

接下来，我们需要将`ThemeProvider`包装在整个应用程序中，这样我们就可以访问整个应用程序中主题的状态。转到`_app.js`文件，并包含以下代码:

```
import { ThemeProvider } from "../context/theme";

export default function MyApp({ Component, pageProps }) {
  return (
    <ThemeProvider>
      <Component {...pageProps} />
    </ThemeProvider>
  );
}
```

要访问主题的状态，导航到`index.js`并包含以下代码:

```
import Link from "next/link";
import { useThemeContext } from "../context/theme";

export default function Home() {
  const [theme, setTheme] = useThemeContext();
  return (
    <div>
      <h1>Welcome to the Home page</h1>
      <Link href="/about">
        <a>About</a>
      </Link>
      <p>Current mode: {theme}</p>
      <button
        onClick={() => {
          theme == "light" ? setTheme("dark") : setTheme("light");
        }}
      >
        Toggle mode
      </button>
    </div>
  );
}
```

我们首先导入`useThemeContext`，然后访问`theme`状态和`setTheme`函数以在必要时更新它。

在切换按钮的`onClick`事件中，我们创建了一个更新函数，根据当前值在`light`和`dark`的相反值之间切换。

## 通过路线访问`Context`

Next.js 使用一个`pages`文件夹在你的应用程序中创建新的路线。例如，如果您创建一个新文件`route.js`，然后通过`Link`组件从某个地方引用它，那么它将可以通过 URL 中的`/route`访问。

在前面的代码片段中，我们创建了到`About`路线的路线。这将允许我们测试主题状态是全局可访问的。

这个路由目前不存在，所以让我们在`pages`文件夹中创建一个名为`about.js`的新文件，并包含以下代码:

```
import Link from "next/link";
import { useThemeContext } from "../context/theme";

export default function Home() {
  const [theme, setTheme] = useThemeContext();
  return (
    <div>
      <h1>Welcome to the About page</h1>
      <Link href="/">
        <a>Home</a>
      </Link>
      <p>Currently active theme: {theme}</p>
      <button
        onClick={() => {
          theme == "light" ? setTheme("dark") : setTheme("light");
        }}
      >
        Toggle mode
      </button>
    </div>
  );
}
```

我们创建了一个与之前在`Home`路线中使用的非常相似的代码结构。唯一的区别是页面标题和导航回`Home`的不同链接。

现在，尝试切换当前活动的主题并在路线之间切换。请注意，状态在两条路由中都得到了保留。您可以进一步创建不同的组件，主题状态将在它位于应用程序文件树中的任何时候都可以访问。

## 从 API 获取数据

当在应用程序内部管理状态时，前面的方法可以工作。然而，在现实生活中，您最有可能通过 API 从外部来源获取一些数据。

数据获取可以概括为向 API 端点发出请求，并在处理请求和发送响应后接收数据。

我们需要考虑到这个过程不是即时的，所以我们需要在准备响应时管理响应的状态，比如等待时间的状态。我们还将处理潜在错误的情况。

跟踪等待状态让我们可以显示一个加载动画来改善 UX，错误状态让我们知道响应不成功。它让我们显示错误消息，为我们提供有关原因的进一步信息。

## 获取 API 和`useEffect`钩子

处理数据获取的一个最常见的方法是结合使用本地的[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 和`useEffect`钩子。

钩子可以让我们在其他动作完成后产生副作用。有了它，我们可以跟踪应用程序何时被渲染，并且我们可以安全地进行获取调用。

要在 NextJS 中获取数据，将`index.js`转换为以下内容:

```
import { useState, useEffect } from "react";

export default function Home() {
  const [data, setData] = useState(null)
  const [isLoading, setLoading] = useState(false)

  useEffect(() => {
    setLoading(true)
    fetch('api/book')
      .then((res) => res.json())
      .then((data) => {
        setData(data)
        setLoading(false)
      })
  }, [])

  if (isLoading) return <p>Loading book data...</p>
  if (!data) return <p>No book found</p>

  return (
    <div>
      <h1>My favorite book:</h1>
      <h2>{data.title}</h2>
      <p>{data.author}</p>
    </div>
  )
}
```

我们首先导入了`useState`和`useEffect`钩子。然后，我们为`null`的接收数据和`false`的加载时间创建了单独的初始状态，表明没有进行 Fetch 调用。

一旦应用程序被渲染，我们将加载状态设置为`true`，并创建一个 Fetch 调用。一旦接收到响应，我们就将数据设置为接收到的响应，并将加载状态设置回`false`，表示提取完成。

接下来，我们需要创建一个有效的 API 端点，因此导航到`api`文件夹并在其中创建一个名为`book.js`的新文件，这样我们就获得了在前面的代码片段中包含在 fetch 调用中的 API 端点。包括以下代码:

```
export default function handler(req, res) {
  res
    .status(200)
    .json({ title: "The fault in our stars", author: "John Green" });
}
```

这段代码模拟了通常从一些外部 API 得到的关于书名和作者的响应，但是在本教程中不会有问题。

## 在 Next.js 中使用 SWR 进行状态管理

还有一种替代方法，由 Next.js 团队自己创建，以更方便的方式处理数据获取。

它被称为[SWR](https://swr.vercel.app/)——一个定制的钩子库，处理缓存、重新验证、焦点跟踪、间隔重新获取等等。要安装它，请在您的终端中运行`npm install swr`。

要查看它的运行情况，让我们转换一下`index.js`文件。

```
import useSWR from "swr";

export default function Home() {
  const fetcher = (...args) => fetch(...args).then((res) => res.json());
  const { data, error } = useSWR("api/user", fetcher);

  if (error) return <p>No person found</p>;
  if (!data) return <p>Loading...</p>;

  return (
    <div>
      <h1>The winner of the competition:</h1>
      <h2>
        {data.name} {data.surname}
      </h2>
    </div>
  );
}
```

使用 SWR 简化了许多事情:语法看起来更干净，更容易阅读，它非常适合可伸缩性，错误和响应状态在几行代码中得到处理。

现在，让我们创建 API 端点，以便获得响应。导航到`api`文件夹，创建一个名为`user.js,`的新文件，并包含以下代码:

```
export default function handler(req, res) {
  res.status(200).json({ name: "Jade", surname: "Summers" });
}
```

这个 API 端点模拟获取人的姓名，这通常是从包含公开可用姓名列表的数据库或 API 中获取的。

## 结论

在本教程中，我们构建了几个迷你应用程序来展示我们可以在 Next.js 应用程序中管理状态的许多方法。

每个用例使用不同的状态管理解决方案，但是选择最合适的状态管理解决方案的最大挑战是识别您需要跟踪什么状态的能力。

初学者经常会为管理简单的状态而纠结和选择过度的解决方案，但是随着时间的推移，它会变得更好，并且改进它的唯一方法是通过练习。希望这篇文章能帮助你朝着正确的方向迈出一步。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。