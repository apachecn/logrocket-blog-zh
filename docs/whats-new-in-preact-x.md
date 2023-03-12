# Preact X 有什么新功能？- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-preact-x/>

Preact 是一个 JavaScript 库，它将自己描述为一个[快速 3kB 的替代方案，以使用相同的现代 API 来反应](https://blog.logrocket.com/introduction-to-preact-a-smaller-faster-react-alternative-ad5532eb6d79/)。与类似的框架相比，它拥有最快的虚拟 DOM 库之一。您可以开始在 React/ReactDOM 代码中编写 Preact，而无需对工作流或代码库进行任何更改。

GitHub 上有超过 24，000 个️stars，并且有许多专门的社区成员不断提供支持，在 JS 中构建高效、小型、高性能、超快的前端应用程序从未如此简单。

自最初发布以来，Preact 的维护人员已经发布了几个版本来解决问题和增加功能。10 月，Preact X 推出了几个旨在解决常见问题和改进现有功能的更新。

让我们回顾一下最近的一些变化，并讨论它们如何帮助我们使用 PreactJS 开发更好的应用程序。

注意，本教程假设您对 PreactJS 或 reactJS 有基本的了解。要了解更多关于 Preact 的信息，请阅读图书馆的官方指南。

## Preact X 中的新功能和改进

Preact 的维护人员已经添加了重大改进，以支持许多最新的 react 特性。让我们回顾一些最有趣的新功能。

### 碎片

片段允许您分组子列表，而无需向 DOM 添加额外的节点，因为它们不会呈现在 DOM 中。您可以在通常使用包装器`div`的地方使用这个特性。它在处理列表、表格或 CSS flexbox 时最有用。

考虑以下标记:

```
class Table extends Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
class Columns extends Component {
  render() {
    return (
      <div>
        <td>One</td>
        <td>Two</td>
      </div>
    );
  }
} 

```

呈现的结果将是无效的 HTML，因为来自`Columns`组件的包装器`div`被呈现在`Table`组件的`<tr>`内。

使用片段，您可以在 DOM 上呈现输出，而无需添加任何额外的元素。

```
class Columns extends Component {
  render() {
    return (
      <>
        <td>One</td>
        <td>Two</td>
      </>
    );
  }
} 

```

现在，输出将是有效的 HTML，因为没有额外的`div`被添加到 DOM 中。片段可以用两种方式编写:

```
BY :
import { Fragment, render } from 'preact';

function TodoItems() {
  return (
    <Fragment>
        <li>A</li>
        <li>B</li>
        <li>C</li>
    </Fragment>
  )
}

or 

function TodoItems() {
  return (
    <>
        <li>A</li>
        <li>B</li>
        <li>C</li>
    </>
  )
}

```

要了解更多信息，请阅读 Preact X 官方指南中的[组件](https://preactjs.com/guide/v10/components/#fragments)文章。

### 钩住

钩子是基于类的组件 API 的替代物。钩子允许您组合状态和有状态逻辑，并在组件之间轻松地重用它们。Preact X 提供了许多现成的钩子以及创建定制钩子的能力。您可以从`preact/hooks`或`preact/compat`导入挂钩。

```
import {useState, useCallback} from 'preact/hooks';
or
import {useState, useCallback} from 'preact/compat';

function Counter() {
  const [value, setValue] = useState(0);
  const increment = useCallback(() => setValue(value + 1), [value]);

  return (
    <div>
      Counter: {value}
      <button onClick={increment}>Increment</button>
    </div>
  );
}

```

上面的代码是一个计数器组件，当单击它时，值会递增。它利用了 Preact X API 中提供的`useState`和`useCallback`钩子。如图所示，代码也与您在 React 中编写的代码相同。

注意，[钩子](https://preactjs.com/guide/v10/hooks/)是可选的，可以和类组件一起使用。

### componentDidCatch

Preact X 包含对`componentDidCatch`生命周期方法的更新，该方法在组件呈现后调用。这允许您处理在呈现期间发生的任何错误，包括在生命周期钩子中发生的错误，但不包括任何异步抛出的错误，比如在一个`fetch()`调用之后。当捕获到错误时，您可以使用这个生命周期来对任何错误做出反应，并显示一条漂亮的错误消息或任何其他后备内容。

```
class Catcher extends Component {
  state = { errored: false }

  componentDidCatch(error) {
    this.setState({ errored: true });
  }

  render(props, state) {
    if (state.errored) {
      return <p>Something went badly wrong</p>;
    }
    return props.children;
  }
}

```

在上面的代码中，我们调用了`componentDidCatch()`，一旦组件被渲染，它就会被调用。如果捕获到错误，您可以更新组件，让用户知道发生了错误，并将条目记录到日志服务中。

这确保了更干净的代码库和更容易的错误跟踪。[官方指南](https://preactjs.com/guide/v10/components/#componentdidcatch)有更多关于`componentDidCatch()`的信息。

### ccreatecontext

上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。尽管上下文对于 Preact 来说并不陌生，但是已知遗留 API `getChildContext()`在向虚拟 DOM 树的更深处交付更新时会出现问题。

通过`createContext(initialValue)`函数创建一个上下文对象。它返回一个用于设置上下文值的`Provider`组件和一个从上下文中检索值的`Consumer`组件。

```
import {useContext} from 'preact/compat';

const Theme = createContext('light');

function DisplayTheme() {
  const theme = useContext(Theme);
  return <p>Active theme: {theme}</p>;
}

// ...later
function App() {
  return (
    <Theme.Provider value="light">
      <OtherComponent>
        <DisplayTheme />
      </OtherComponent>
    </Theme.Provider>
  )
}

```

## 对 Preact 核心的更改

以前，`preact-compat`是作为一个单独的包包含在内的。现在它和 Preact 本身包含在同一个包中；使用 React 生态系统中的库不需要额外安装。

```
// Preact 8.x
import React from "preact-compat";

// Preact X
import React from "preact/compat";

```

Preact X 现在还直接支持 [CSS 定制属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)来设计 Preact 组件的样式。Preact 团队特别确保在测试过程中包含几个流行的包，以保证对它们的完全支持。

## 结论

在本教程中，我们探索了 Preact X 中引入的一些功能。要查看所有更改的具体列表并了解新版本的更多信息，请务必查看 GitHub 上的 [Preact 发布页面。](https://github.com/preactjs/preact/releases)

你最喜欢的新特性或 API 是什么？欢迎在评论中分享你的想法。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)