# 使用 Reakit - LogRocket 博客访问组件

> 原文：<https://blog.logrocket.com/accessible-components-with-reakit/>

React 应用程序中最常见的是组件的可重用性。我们一直在应用程序的不同部分使用和重用相同的组件，这是 React 应用程序中最棒的特性之一。

考虑到可重用性，我们可以构建令人惊叹的东西，我们可以创建一个完整的设计系统，供遵循相同标准和原则的不同项目中的不同团队使用。因此，我们可以注意到生产率将会提高，因为现在如果设计系统中有可用的组件，就不需要从头开始创建特定的组件。

自从 React 发布并被社区采用为创建 web 应用程序最常用的 JavaScript 库以来，我们可以注意到设计系统、组件库和 UI 库的数量增加了。我们有许多不同的选项来构建 React 应用程序，我们可以选择不同的设计系统或组件库来完成这项工作。

Reakit 是这些库中的一个，但是它有一个重要的概念——可访问性。现在，可访问性比以往任何时候都更是一个重要的话题，每个希望自己的应用程序对所有人都可用的开发人员都应该优先考虑这个问题。

在我们了解 Reakit 组件库以及它的特别之处之前，让我们先了解一下可访问性以及为什么它在现代 web 中如此重要。

## 网页内容可访问性指南(WCAG)

许多公司和开发人员都没有把可访问性作为头等大事来对待，但是为了给每个人创建更易访问的应用程序，可访问性在现代应用程序中有着重要的作用。

可访问性对于网络是如此重要，以至于 W3C 为它创建了一个标准指南，叫做网络内容可访问性指南( [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/) )。这是一套关于网页内容可访问性的标准和原则，为不同的人构建和提供更易访问的应用程序。

为了注意无障碍的重要性，根据世界银行的数据:

> 10 亿人(占世界人口的 15%)患有某种形式的残疾，发展中国家的残疾患病率更高。估计全球总数的五分之一，即 1 . 1 亿至 1 . 9 亿人，患有严重残疾。残疾人比非残疾人更有可能经历不利的社会经济后果，如受教育程度低、健康状况差、就业率低和贫困率高。

提供一个每个人都能毫无问题地访问的应用程序是非常重要的。这就是 Reakit 可以帮助我们在 React 中创建更易访问、可组合和快速的应用程序的地方。

## 现实

Reakit 是一个底层组件库，帮助我们创建更易访问的 React 组件、库、设计系统和应用。由[迭戈·哈兹](https://twitter.com/diegohaz)创建的 Reakit 是在麻省理工学院许可下发布的，它获得了更多想要构建更易访问的 React 应用程序的采纳者。

Reakit 提供了各种可访问、可组合和可定制的组件，这些组件遵循 WAI-ARIA 标准。这意味着我们可以拥有许多在许多应用中经常使用的可访问组件，如**按钮**、**复选框**、**输入**等。

关于 Reakit 最好的事情之一是，它已经设法关注开箱即用的键盘集成，所以没有必要将它集成到您的组件中。它也没有默认的 CSS 样式，所以你可以使用你自己的 CSS 和任何你想使用的 CSS 解决方案。

## 易接近

我们知道可访问性非常重要，我们也知道使用具有完全可访问组件的组件库可以在应用程序中产生巨大的差异。

Reakit 严格遵循 [WAI-ARIA](https://www.w3.org/TR/wai-aria/) 标准，这意味着所有组件的设计和开发都考虑到了可访问性，提供真正的可访问组件并改善用户体验。

Reakit 还自带了焦点和键盘集成，例如:

*   当你按下`Enter`键盘时`Button`应该会有反应
*   您可以使用键盘上的箭头键在`Tab`组件中轻松导航

## (音乐)可设定的

React 是处理各种不同组件的一个非常好的解决方案，因为它允许我们轻松地在应用程序的不同部分重用组件。

Reakit 考虑到了组合，使我们的工作更容易构建不同的组件。我们可以使用`as` prop 来组合组件并改变 Reakit 组件的底层元素。

假设我们有一个`Radio`组件，我们想把这个组件组合成一个`Button`，我们可以很容易地把`as`属性作为`Button`来传递。

```
import { useRadioState, Radio, RadioGroup, Button } from "reakit";

const App = () => {
  const radio = useRadioState();
  return (
    <div>
      <h1>App</h1>
      <RadioGroup {...radio} aria-label="cars" as={Button}>
        <label>
          <Radio {...radio} value="tesla" /> Tesla
        </label>
      </RadioGroup>
    </div>
  );
}

export default App;
```

## 可定制的

Reakit 没有附带任何默认的 CSS，这使得组件非常容易定制和样式化。

从 Reakit 导入一个简单的`Button`,您会注意到其中没有默认的 CSS:

```
import { Button } from "reakit";

const MyButton = () => (
  <Button>Reakit Button</Button>
);

export default MyButton;
```

我们可以非常容易地将它与我们想要的任何 CSS 解决方案集成，例如，CSS-in-JS 库:

```
import styled from 'styled-components';
import { Button } from "reakit";

const StyledButton = styled(Button)`
  width: 100px;
  height: 30px;
  background: turquoise;
  border-radius: 5px;
  color: white;
`;

const MyButton = () => (
  <StyledButton>Reakit Button</StyledButton>
);

export default MyButton;
```

## 大小

当谈到包的大小时，Reakit 有一个非常好的包大小，与我们今天可用的其他 React 组件库相比，它不是一个沉重的库。

Reakit 大约有`31 kB`，每个组件的平均大小为`1 kB`。与其他组件库如 [Material UI](https://material-ui.com/) 和 [Ant](https://ant.design/) 相比，Reakit 是一个非常轻量级的解决方案。

## 入门指南

现在我们已经了解了 Reakit 的特性，让我们从头开始构建一些东西。我们将构建一个只有几个可访问组件的 todo 应用程序。

在开始之前，我们需要确保已经安装了`react`和`react-dom`:

```
yarn add react react-dom
```

现在，我们可以安装`reakit`:

```
yarn add reakit
```

我们将从 Reakit 导入两个组件，`Input`和`Button`。在这个例子中，我们只需要这两个组件，但是在现实世界的应用程序中，Reakit 为您提供了许多不同的组件来帮助您实现您的结果。

让我们创建一个新的`create-react-app`应用程序:

```
npx create-react-app reakit-example --template typescript
```

在我们的应用程序中，在我们的`App.ts`上，我们将使用 Reakit 创建我们的 todo 应用程序。让我们导入`Input`和`Button`并创建我们的状态逻辑:

```
import React, { useState } from 'react';
import { Input, Button } from "reakit";

const App = () => {
  const [tasks, setTasks] = useState([]);
  const [text, setText] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!text) return;
    addTask(text);
    setText("");
  };

  const addTask = (text) => {
    const newTasks = [...tasks, { text }];
    setTasks(newTasks);
  };

  const deleteTask = (index) => {
    const newTasks = [...tasks];
    newTasks.splice(index, 1);
    setTasks(newTasks);
  };
};

export default App;
```

Reakit 有一些组件处于实验模式，如`Form`、`FormLabel`和`FormInput`。我们不打算在这个例子中使用这些组件，因为它们可能会引入突破性的变化，甚至在未来的版本中被删除。

现在，我们将使用来自 Reakit 的`Input`和`Button`。这是我们的 todo 应用程序的外观:

```
import React, { useState } from 'react';
import { Input, Button } from "reakit";

const App = () => {
  const [tasks, setTasks] = useState([]);
  const [text, setText] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!text) return;
    addTask(text);
    setText("");
  };

  const addTask = (text) => {
    const newTasks = [...tasks, { text }];
    setTasks(newTasks);
  };

  const deleteTask = (index) => {
    const newTasks = [...tasks];
    newTasks.splice(index, 1);
    setTasks(newTasks);
  };

  return (
    <form onSubmit={handleSubmit}>
    <Input
      placeholder="Add a task"
      value={text}
      onChange={e => setText(e.target.value)}
    />

    <Button onClick={handleSubmit}>Add</Button>
    {tasks.map((task: any, index: number) => (
      <div key={index} onClick={() => deleteTask(index)}>
        <h1>{task.text}</h1>
      </div>
    ))}
   </form>
  )
};
export default App;
```

关于 Reakit 的一个很好的特性是，当你使用`Button`组件时，你想把它作为禁用的来传递，那么`aria-disabled`将已经被设置为`true`。

特别是对于那些想要创建一个新的设计系统，想要创建可访问的组件的人来说，Reakit 是一个非常好的选择。您可以使用 Reakit 为某些组件创建一个非常好且健壮的设计系统，并且仍然有不错的结果，尤其是在可访问性方面。

## 结论

构建可访问的应用程序不是一件容易的事情，它需要艰苦的工作，今天我们有一些组件库可以帮助我们实现一个好的结果，并为每个人提供一个可访问的应用程序。Reakit 是一个组件库，可以帮助我们做到这一点，它为我们提供了各种各样的具有可访问性的组件，并且没有任何默认的 CSS。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)