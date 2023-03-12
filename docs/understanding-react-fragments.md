# 了解 React 片段- LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-react-fragments/>

***编者按:*** *这篇文章于 2022 年 1 月 25 日更新，以更新任何过时的信息，并添加了 [React 片段与](https://blog.logrocket.com/understanding-react-fragments/#react-fragment-vs-div) `[div](https://blog.logrocket.com/understanding-react-fragments/#what-react-fragment)`部分以及使用 `[key](https://blog.logrocket.com/understanding-react-fragments/#using-key-prop-react-fragments)` [道具的](https://blog.logrocket.com/understanding-react-fragments/#using-key-prop-react-fragments)[与 React 片段](https://blog.logrocket.com/understanding-react-fragments/#using-key-prop-react-fragments)部分。*

React 片段是 React v16.2.0 发布的一个简单而优雅的特性。理解它们的存在将使您能够编写更好的 React 组件，并在创建和设计布局时节省大量时间。

本教程旨在帮助您理解 React 片段以及它们派上用场的各种用例。

以下是我们将要介绍的内容:

## 什么是反应片段？

片段是一种语法，允许我们向 React 组件添加多个元素，而无需将它们包装在额外的 DOM 节点中。

让我们来看看下面的代码:

```
const App = () => {
  return (
    <h1>This is heading1 text</h1>
  );
}

export default App

```

这是一个简单的 React 组件。当我们在一个组件中只返回一个 JSX 时，我们可以避免用另一个包装元素包装 JSX，如上所示。然而，当我们像这样添加多个 JSX 元素时:

```
const App = () => {
  return (
    <h1>This is heading1 text</h1>
    <p>This is paragraph text</p>
  );
}

export default App

```

我们会遇到一个`SyntaxError`。从而使我们的应用程序崩溃。

在 React 中，当一个组件返回多个元素时，我们必须将它们包装在类似于`div`的容器元素中，这样代码才能工作:

```
const App = () => {
  return (
    <div>
      <h1>This is heading1 text</h1>
      <p>This is paragraph text</p>
    </div>
  );
};
export default App;

```

虽然这很好，但是它可能会在我们的组件中引起意想不到的问题。

## 反应片段与`div`

如果容器的用途是为 JSX 添加样式，那么它就没有问题。然而，他们并不总是需要包装我们的 JSX。在这种情况下，当我们这样做时，它们变成了额外的节点，使 DOM 树变得混乱。

有时当我们使用嵌套组件时，这些包装器会导致代码异常。例如，当使用 CSS Flexbox 和 Grid 时，`div`会导致布局中断。对于必须遵循特定结构(如`ul > li`和`table>tr>td`)的元素，我们也可能会遇到无效的 HTML。

说到这里，我们将看看实践中的一些问题，看看 React 片段是如何解决这些问题的。从 Flexbox 的 CSS 布局开始。

### 在 CSS 布局中使用`div`包装器

考虑下面的例子，使用 [Flexbox](https://blog.logrocket.com/flexbox-vs-css-grid/) 创建一个简单的行和列布局:

```
import "./styles.css";

const Row = ({ children }) => <div className="row">{children}</div>;

const Column = ({ children }) => <div className="col">{children}</div>;

const Box = ({ color }) => (
  <div className="box" style={{ backgroundColor: color }}></div>
);

export default function App() {
  return (
    <Row>
      <Column>
        <Box color="#007bff" />
      </Column>
      <Column>
        <Box color="#fc3" />
      </Column>
      <Column>
        <Box color="#ff3333" />
      </Column>
    </Row>
  );
}

```

每个`Row`呈现一个`div`，将内容排列成一行，一个`Column`以垂直方式呈现内容。在每个`Column`中，都有一个`Box`组件来呈现一个简单的`div`，它有一个固定宽度的容器和一个作为道具传递给它的背景色:

```
/* styles.css */
.row {
  display: flex;
}
.col {
  flex: 1;
}
.box {
  min-width: 100px;
  min-height: 100px;
}

```

上面的代码在一行中呈现三列，如下所示:

![The Code Renders Three Columns In Blue, Yellow, And Red From Left To Right](img/f278b5afaeaa835942c9a87f975580da.png)

在 CodeSandbox 上亲自查看[。](https://codesandbox.io/s/pedantic-dream-ec2w4?file=/src/App.js)

让我们重构上面的代码，将前两列分成不同的组件`ChildComponent`。设想这是一个您可能想要解耦的可重用组件:

```
export default function App() {
  return (
    <Row>
      <ChildComponent />
      <Column>
        <Box color="#ff3333" />
      </Column>
    </Row>
  );
}

const ChildComponent = () => (
  <div>
    <Column>
      <Box color="#007bff" />
    </Column>
    <Column>
      <Box color="#fc3" />
    </Column>
  </div>
);

```

预期的结果应该和以前一样，但事实并非如此。将前两列分离到一个单独的组件`ChildComponent`中，会破坏布局:

![With The Added Child Component, The Layout Breaks, Putting A Blue Box In The Left Corner, A Yellow Box Under The Blue, And A Red Rectangle To The Right Of The Blue](img/efab0e13dbd1d015256c5836a085202e.png)

在 CodeSandbox 上亲自查看[。](https://codesandbox.io/s/gracious-noyce-4tjze?file=/src/App.js)

`ChildCompoent`有一个`div`包装所有的 JSX 元素，将它们组合在一起。然而，额外的`div`会导致布局中断，因为浏览器认为它是布局的一部分。

你的浏览器不知道你已经添加了`div`来避免出错，它仅仅被用作你的封装 HTML 的包装器。

随着组件树嵌套得越来越深，调试和跟踪额外节点的来源可能会很困难。同样，如果我们使用 [CSS 网格](https://blog.logrocket.com/full-bleed-layout-css-grid/)来设计我们的布局，不必要的`div`会导致布局中断。

### 为什么我们在 React 中使用片段？

React 片段是在我们的代码中使用不必要的`divs`的一种更干净的替代方式。这些片段不会在 DOM 中产生任何额外的元素，这意味着片段的子组件将在没有任何包装 DOM 节点的情况下呈现。

React 片段使我们能够对多个兄弟组件进行分组，而不会在呈现的 HTML 中引入任何不必要的标记。

#### 在 CSS Flexbox 布局中使用片段

现在，回到我们的代码，我们可以通过将组件的 JSX 包装在 React 片段而不是`div`中来修复布局问题:

```
import React from 'react';

const ChildComponent = () => (
  <React.Fragment>
    <Column>
      <Box color="#007bff" />
    </Column>
    <Column>
      <Box color="#fc3" />
    </Column>
  </React.Fragment>
);

```

在 CodeSandbox 上亲自查看[。](https://codesandbox.io/s/ecstatic-pine-rdwl2?file=/src/App.js)

#### 在 React 中创建和渲染片段

有许多方法可以创建和渲染片段。您可以通过在导入的 React 对象上使用`Fragment`属性来创建片段，如上所示。您还可以从 React 导入一个片段作为 React 组件，并以类似的方式使用它:

```
import React, {Fragment} from 'react';

const ChildComponent = () => (
  <Fragment>
    <Column>
      <Box color="#007bff" />
    </Column>
    <Column>
      <Box color="#fc3" />
    </Column>
  </Fragment>
);

```

最后，您可以使用简写语法动态地创建一个 React 片段，使用一个空的 HTML 元素如语法`<></>`来包装组件。这是使用片段最干净、最简单的方法；感觉就像你在使用一个普通的 HTML 元素:

```
const ChildComponent = () => (
  <>
    <Column>
      <Box color="#007bff" />
    </Column>
    <Column>
      <Box color="#fc3" />
    </Column>
  </>
);

```

使用以上三种方法中的任何一种都可以恢复原始布局，因为它消除了 DOM 中无意义的`div`。

### 在一个`div`包装器中呈现列表

让我们看看片段的另一个常见用例。假设您想要在页面上呈现一个项目列表。这个列表可以是静态的，从本地 JSON 文件生成，或者从 API 检索。

为了简洁起见，我们将使用一个静态列表:

```
>import React from 'react';

const items = ["Item 1", "Item 2", "Item 3"];

const ItemRenderer = ({ item }) => (
  <div>
    <p>Rendering item:</p>
    <p>{item}</p>
  </div>
);

const renderItem = () => items.map((item, index) =>
  <ItemRenderer key={index} item={item} />
);

```

在这里，您只需遍历 items 数组并将每个项目作为道具传递给`ItemRenderer`组件，该组件呈现页面上的每个项目。如果您在浏览器上检查上面的代码，您将得到下面的 DOM 结构:

```
<div>
  <p>Rendering item:</p>
  <p>Item 1</p>
</div>
<div>
  <p>Rendering item:</p>
  <p>Item 2</p>
</div>
<div>
  <p>Rendering item:</p>
  <p>Item 3</p>
</div>

```

每个条目都在一个没有包装意义的父条目`div`中呈现。由于封闭的`div`没有附加样式或数据，因此可以安全地用 React 片段替换它。

### 使用 React 片段呈现列表

让我们用 React 片段替换`div`包装器，如下所示:

```
 import React from 'react';

const items = ["Item 1", "Item 2", "Item 3"];

const ItemRenderer = ({ item }) => (
  <>
    <p>Rendering item:</p>
    <p>{item}</p>
  </>
);

const renderItem = () => items.map((item, index) =>
  <ItemRenderer key={index} item={item} />
);

```

DOM 结构现在看起来干净多了:

```
  <p>Rendering item:</p>
  <p>Item 1</p>
  <p>Rendering item:</p>
  <p>Item 2</p>
  <p>Rendering item:</p>
  <p>Item 3</p>

```

这是一个非常简单的用例，您可能会在 DOM 上呈现一个额外的`div`。你的列表越大，影响就越大。

随着您的应用程序变得越来越大，架构越来越复杂，您可能会发现自己在应用程序中呈现大量不必要的`div`来呈现大型列表。这可能会使您的 HTML 膨胀，导致旧设备上的性能问题。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

它可能一点也不重要，但是呈现不必要的 HTML 元素总是不好的做法。如果您的应用程序有一个通用的列表组件，可以考虑使用片段作为包装器，以避免脱离干净的代码和语义。

## 对 React 片段使用`key`道具

有些场景需要在片段中使用`key`道具。让我们来看看下面的代码:

```
const items = [
  {name: "Ibas", title: "developer"},
  {name: "John", title: "teacher"},
];

const App = () => (
  <>
    <h1>List of all items:</h1>
    {items.map(({name, title}, index) => (
      <div key={index}>
        <p>{name}</p>
        <p>{title}</p>
      </div>
    ))}
  </>
);

export default App;

```

这里的重点是使用`map()`方法的迭代。正如我们所知，每当我们在 React 中映射条目以呈现列表时，React 使用`key`属性来标识哪些条目被更改、删除或添加。

此外，每当我们映射项目以呈现多个 JSX 时，我们必须将 JSX 包装在容器元素中。

在上面的代码中，我们用`div`包装了 JSX，并分配了所需的`key`道具。它工作得很好，但是让我们假设`div`在 DOM 树中是多余的，我们不想渲染它。你可以猜到，我们必须用一个碎片来替换它。

但是，使用简写符号`<></>`在这里不起作用，因为它不能接受属性。相反，我们可以使用`React.Fragment`或`Fragment`语法。请注意，该语法目前只接受`key`属性。

因此，通过对片段应用`key`属性，我们的代码现在看起来像这样:

```
import React from "react";

// ...
const App = () => (
  <>
    <h1>List of all items:</h1>
    {items.map(({ name, title }, index) => (
      <React.Fragment key={index}>
        <p>{name}</p>
        <p>{title}</p>
      </React.Fragment>
    ))}
  </>
);

export default App;

```

在 CodeSandbox 上亲自查看[。不会变得更简单。](https://codesandbox.io/s/aged-silence-gy0hy?file=/src/App.js)

## 结论

片段允许你写更干净，可读和可维护的代码。它们不是 HTML 中的`div`的替代品，但是如果您在代码中使用不必要的`div`，它们提供了一种更好的方法来组织和呈现您的标记。

您可以避免破坏布局的问题，或者使用片段优化标记呈现时间。但是，您应该只在需要的时候使用它们。如果你需要一个包装你的 JSX 的样式，使用`div`代替。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)