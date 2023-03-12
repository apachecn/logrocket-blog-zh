# 什么是 React 纯功能组件？

> 原文：<https://blog.logrocket.com/what-are-react-pure-functional-components/>

***编者按:*** *本帖更新于 2022 年 3 月 23 日，以确保所有包都是最新的。*

React 是一个用于构建用户界面组件的开源 JavaScript 库。作为 2021 年最受欢迎的前端库之一，React 在撰写本文时拥有近 570 万用户和庞大的开发者社区。React 可以在从网络到移动设备的各种平台上运行。

在本教程中，我们将学习如何使用 [`React.PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent) 和 [`React.memo()` API](https://reactjs.org/docs/react-api.html#reactmemo) 来记忆 React 中的功能组件。首先，在深入一个示例之前，我们将介绍 React 组件的一些基础知识。我们开始吧！

## 目录

您可以在 [React RFCs 存储库](https://github.com/reactjs/rfcs)上跟上 React 框架的变化和建议。

## 什么是 React 组件？

像大多数现代 JavaScript 框架一样，React 是基于组件的。组件通常被定义为其状态和属性的函数。

### 对功能组件和类组件进行反应

React 支持两种类型的组件，类组件和功能组件。一个[功能组件是一个返回 JSX](https://blog.logrocket.com/fundamentals-functional-programming-react/) 的普通 JavaScript 函数。类组件是一个 JavaScript 类，它扩展了`React.Component`并在呈现方法中返回 JSX。

下面的代码片段显示了一个简单的`ReactHeader`组件，它被定义为类组件和功能组件:

```
// CLASS COMPONENT
class ReactHeader extends React.Component {
  render() {
    return (
      <h1>
        React {this.props.version || 17} Documentation
      </h1&gt;
    )
  }
}

// FUNCTIONAL COMPONENT
function ReactHeader(props) {
  return (
    <h1>
      React {props.version || 17} Documentation
    </h1>
  )
}

```

要复习 React 功能组件，请查看此视频教程:

[https://www.youtube.com/embed/Cla1WwguArA](https://www.youtube.com/embed/Cla1WwguArA)

视频

## React 中的纯成分是什么？

基于函数式编程范例中的纯概念，如果一个函数满足以下两个条件，则称它是纯的:

*   它的返回值仅由其输入值决定
*   对于相同的输入值，其返回值总是相同的

如果 React 组件为相同的状态和属性呈现相同的输出，则认为它是纯的。对于这种类型的类组件，React 提供了`PureComponent`基类。扩展`React.PureComponent`类的类组件被视为纯组件。

纯组件有一些性能改进和渲染优化，因为 React 为它们实现了 [`shouldComponentUpdate()`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 方法，并对道具和状态进行了简单的比较。

## 纯组件如何在 React 中工作？

实际上，React pure 组件看起来像下面的代码:

```
import React from 'react';

class PercentageStat extends React.PureComponent {

  render() {
    const { label, score = 0, total = Math.max(1, score) } = this.props;

    return (
      <div>
        <h6>{ label }</h6>
        <span>{ Math.round(score / total * 100) }%</span>
      </div>
    )
  }

}

export default PercentageStat;

```

## React 功能成分是否纯净？

功能组件在 React 中非常有用，尤其是当您想要将状态管理从组件中分离出来时。这就是为什么[它们经常被称为无状态组件](https://blog.logrocket.com/react-component-design-patterns-2022/)。

然而，功能组件不能利用`React.PureComponent`带来的性能改进和渲染优化，因为根据定义，它们不是类。

如果您希望 React 将一个功能组件作为一个纯组件对待，那么您必须将这个功能组件转换成一个扩展`React.PureComponent`的类组件。

看看下面这个简单的例子:

```
// FUNCTIONAL COMPONENT
function PercentageStat({ label, score = 0, total = Math.max(1, score) }) {
  return (
    <div>
      <h6>{ label }</h6>
      <span>{ Math.round(score / total * 100) }%</span>
    </div>
  )
}

// CONVERTED TO PURE COMPONENT
class PercentageStat extends React.PureComponent {

  render() {
    const { label, score = 0, total = Math.max(1, score) } = this.props;

    return (
      <div>
        <h6>{ label }</h6>
        <span>{ Math.round(score / total * 100) }%</span>
      </div>
    )
  }

}

```

## 使用重组中的`{ pure }` HOC

优化一个功能组件以便 React 可以把它当作一个纯组件来处理，这不一定要求你把组件转换成类组件。

[重组](https://github.com/acdlite/recompose)包提供了广泛的[高阶组件(hoc)](https://reactjs.org/docs/higher-order-components.html)集合，对于处理功能组件非常有用。重新组合包导出一个 [`[{ pure }]`](https://github.com/acdlite/recompose/blob/master/docs/API.md#pure) HOC，它试图通过阻止对组件的更新来优化 React 组件，除非一个属性发生了变化，并使用`shallowEqual()`来测试变化。

使用纯 HOC，我们的功能组件可以包装如下:

```
import React from 'react';
import { pure } from 'recompose';

function PercentageStat({ label, score = 0, total = Math.max(1, score) }) {
  return (
    <div>
      <h6>{ label }</h6>
      <span>{ Math.round(score / total * 100) }%</span>
    </div>
  )
}

// Wrap component using the `pure` HOC from recompose
export default pure(PercentageStat);

```

## 如何使用`React.memo()`

使用`[React.memo()](https://github.com/acdlite/recompose/blob/master/docs/API.md#pure)`，你可以创建记忆化的功能组件，避免使用简单的道具比较来渲染不必要的更新。

使用`React.memo()` API，前面的功能组件可以包装如下:

```
import React, { memo } from 'react';

function PercentageStat({ label, score = 0, total = Math.max(1, score) }) {
  return (
    <div>
      <h6>{ label }</h6>
      <span>{ Math.round(score / total * 100) }%</span>
    </div>
  )
}

// Wrap component using `React.memo()`
export default memo(PercentageStat);

```

### `React.memo()` API 实现细节

关于`React.memo()` API 的实现，有一些事情值得考虑。

例如，`React.memo()`是一个高阶分量。它将一个 React 组件作为其第一个参数，并返回一个特殊类型的 React 组件，该组件允许渲染器在记忆输出的同时渲染组件。因此，如果组件的道具是浅相等的，`React.memo()`组件将保释出更新。

`React.memo()`适用于所有 React 组件。传递给`React.memo()`的第一个参数可以是任何类型的 React 组件。但是，对于类组件，你应该使用`React.PureComponent`而不是`React.memo()`。`React.memo()`也使用`ReactDOMServer`处理服务器渲染的组件。

### 自定义紧急救助条件

`React.memo()` API 可以接受第二个参数，即`arePropsEqual()`函数。`React.memo()`的默认行为是肤浅地比较组件道具。但是，使用`arePropsEqual()`功能，您可以自定义组件更新的紧急情况。`arePropsEqual()`功能由两个参数`prevProps`和`nextProps`定义。

当道具被比较为相等时,`arePropsEqual()`函数返回`true`,从而防止组件被重新渲染，当道具不相等时，它返回`false`。

在类组件中，`arePropsEqual()`函数的行为非常类似于`shouldComponentUpdate()`生命周期方法，但方式相反。以下代码片段使用了自定义紧急救助条件:

```
import React, { memo } from 'react';

function PercentageStat({ label, score = 0, total = Math.max(1, score) }) {
  return (
    <div>
      <h6>{ label }</h6>
      <span>{ Math.round(score / total * 100) }%</span>
    </div>
  )
}

function arePropsEqual(prevProps, nextProps) {
  return prevProps.label === nextProps.label; 
}

// Wrap component using `React.memo()` and pass `arePropsEqual`
export default memo(PercentageStat, arePropsEqual);

```

我们使用严格的等号运算符`===`,因为我们想在不进行转换的情况下检查值和它们的类型之间的相等性。例如，`"1"`和`1`是不一样的。他们之间的松散平等将返回真实，`"1" == 1 // true`。但是，严格的平等将是虚假的，`"1"=== 1 // false`。所以，我们要进行严格的比较。

## 结论

有了`React.memo()` API，您可以享受使用功能组件和优化组件带来的性能优势。

在本文中，我们详细探讨了`React.memo()` API。首先，我们讨论了 React 中功能组件和类组件之间的区别，然后我们回顾了纯组件，学习了如何将功能组件转换为类组件。

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！