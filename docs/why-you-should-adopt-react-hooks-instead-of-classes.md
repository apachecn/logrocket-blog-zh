# 为什么你应该采用 React 钩子而不是类

> 原文：<https://blog.logrocket.com/why-you-should-adopt-react-hooks-instead-of-classes/>

在这篇文章中，我们将看看 React 钩子在 React 16 版本中引入的功能组件，以及为什么你应该在类组件上使用它们。这篇文章适合熟悉类的 React 开发人员和不知道使用哪一个的 React 新开发人员。

## 什么是 React 钩子？

根据[官方文档](https://reactjs.org/docs/hooks-intro.html)，Hooks 将以前在职业组件中可获得的所有能力带入了一个功能组件，并使它们在功能组件中可用。使用 React 挂钩，您现在可以在类的构造之外使用 React 的状态和其他特性:

```
import React, { useState } from 'react';
function Example() {
  // Declare a new state variable, which we'll call "count"
const [count, setCount] = useState(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

`useState`是 React 功能组件中可用的挂钩之一。虽然 Hooks 是去年发布的，但是它们附带了一些额外的优势，比如没有突破性的改变。这意味着它是完全向后兼容的，并且可以选择使用，这意味着如果你愿意，你可以选择坚持使用类，或者你可以尝试钩子。

## 为什么你应该考虑钩子而不是类

随着钩子的引入，你现在可以在一个功能组件中做任何你通常在类中做的事情。这是一个游戏规则的改变，因为你需要学习的概念更少，需要编写的代码也更少。在去年之前，React 开发人员已经使用了类，所以要把已经使用了一段时间的东西换成新的可能不太容易，但是因为钩子是可选的，所以你可以尝试一下，不会有任何负面影响。

## 功能组件中的生命周期方法

React 开发人员知道，如果你使用类组件，你无法真正避免使用生命周期方法，`componentDidMount`是最流行的方法之一。它在执行第一个组件渲染后运行。举例来说，让我们创建一个类组件来返回这个 LogRocket 博客的标题:

```
class NewComponent extends React.Component {
 componentDidMount() {
   console.log("The first render has executed!");
 }

 render() {
   return <div> <h3>You are reading a LogRocket article</h3> </div>;
 }
}
```

现在，在运行之后，您必须包含`componentWillUnmount`生命周期来销毁这个已经挂载的。现在多亏了 React 钩子，你可以在一个功能组件中获得相同的生命周期方法行为，我认为这是革命性的:

```
const FunComponent = () => {
 React.useEffect(() => {
   console.log("The first render has executed!");
 }, []);
 return <div> <h3>You are reading a LogRocket article</h3> </div>;
};

```

上面的代码块使用了一个名为`UseEffect`的 React 钩子，通过空数组的第二个参数，它返回与我们上面定义的类组件相同的输出。

## 功能组件中的状态管理

这可能是你在看到钩子可以像功能组件中的生命周期方法一样工作后问的下一个问题，是的，多亏了钩子，你现在可以在功能组件中处理状态。为了在一个简单的 clicker 类中实现 state，大多数人可能会这样做:

```
class NewComponent extends React.Component {
 constructor(props) {
   super(props);
   this.state = {
     counter: 100
   };
 }

 render() {
   return (
     <>
       <p>counter: {this.state.counter} counts left</p>
       <button onClick={() => this.setState({ counter: this.state.counter - 1 })}>
         Click
       </button>
     <>
   );
 }
}
```

现在你知道你必须有状态逻辑的超级道具，现在用一个功能组件来实现它，看起来是这样的:

```
const FunComponent = () => {
 const [counter, setCounter] = React.useState(100);

 return (
   <>
     <p>{counter} more counts to go!</p>
     <button onClick={() => setCounter(counter - 1)}>Click</button>
   <>
 );
};
```

`useState`钩子用于处理功能组件中的有状态逻辑，语法看起来很简单。以下是您可能希望编写功能组件而不是类的一些其他原因。

## 较少的代码行

因此，与类组件相比，使用功能组件平均只需编写更少的代码。下面是一个小例子，我们返回一个 div 标签，其中包含一个带有类组件的小标题:

```
import React, { Component } from "react";
class NewComponent extends Component {
 render() {
   return <div> <h3>You are reading a LogRocket article</h3> </div>;
 }
}
```

对于功能组件，它看起来像这样:

```
import React from "react";

const FunComponent = () => {
 return <div> <h3>You are reading a LogRocket article</h3> </div>;
};
```

## 更容易的学习曲线

在上面的例子中，你可以看到功能组件比类组件需要学习和理解的概念要少。功能组件是一个简单的函数，只返回 JSX，而在类组件方面，你必须理解 render 方法，以及如何使用它来扩展`React.component`。

## 不用再用“这个”了

这是一个有争议的观点，但是即使是 React 团队也确认 JavaScript 中的类的使用有时会让开发者甚至机器感到困惑。令人困惑的事情之一是使用`this`来传递属性以及它在不同语言中是如何工作的。举例来说，假设您有一个带有标题的标题组件:

```
<Heading title="blog" />
```

传递这个 title 属性对于函数组件和类组件都有非常不同的语法。对于类组件，代码块如下所示:

```
class NewComponent extends React.Component {
  render() {
    const {title} = this.props;
    return <h3>You are reading a LogRocket {title}</h3>;
 }
}
```

我们必须将它绑定到`this.props`以在 render 上输出“你正在阅读一个 LogRocket 博客”,同时，当使用一个功能组件来实现相同的结果时，你不必为此而烦恼:

```
const FunComponent = ({ title }) => {
 return <h3>You are reading a LogRocket {title}</h3>;
};
```

## 结论

这篇文章概述了 React 钩子以及为什么应该在函数组件中使用它们而不是类组件。如果你想了解更多关于 React 钩子的知识，请查阅官方文档；如果你只是想学习如何用钩子将你的应用从类重构为功能组件，那么[请查阅这篇伟大的文章](https://blog.logrocket.com/practical-react-hooks-how-to-refactor-your-app-to-use-hooks-b1867e7b0a53/)。我希望你发现这是有用的，快乐的黑客！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)