# 为什么不应该在 React 应用程序中使用内联样式

> 原文：<https://blog.logrocket.com/why-you-shouldnt-use-inline-styling-in-production-react-apps/>

CSS 并不总是 web 开发的重要部分。在 CSS 流行之前，很多公司的网站都很难看，无法访问。在 web 发展的这些年里，新的框架被创建，JavaScript 被巩固为 web 的主要编程语言，开发人员也在提高他们的前端技能。现在，我们有很多设计网页的概念。

很多开发人员会记得我们曾经做了很多工作来处理 HTML 和 CSS。我们必须创建一个单独的。css 文件，通过使用 HTML 文档中的 link 标签链接我们的新 CSS 文件，之后，我们的 CSS 样式工作正常。

使用 CSS 设计我们的应用程序有时是一项具有挑战性的工作。我们没有很多新的 CSS 功能，浏览器也不支持很多新功能。你想象自己必须使用`float: left`和`float: right`来定位元素吗？

起初，这对于一些开发者来说很难理解，但是在过去的几年里，很多事情都发生了变化，CSS 样式就是其中之一。React 是现代应用程序中使用最多的 JavaScript 库，它带来了许多新的想法和概念来设计我们的应用程序。

## React 中的 CSS 样式

在 React 生态系统中，我们有许多不同的库用于不同的目的，在前端的样式部分也是一样，我们有许多库和概念来样式化我们的 React 应用程序。下面是对 React 应用程序进行样式化的最常用方法之一:

## CSS 模块

几年前，许多开发人员在 React 社区中使用并提倡使用 CSS 模块样式。使用 CSS 模块，默认情况下，所有的类名和动画都在本地范围内，这与 HTML 文档中的旧样式非常相似。这里有一个例子:

```
.container {
  width: 400px;
  height: 400px;
  background-color: blue;
}
```

模块只是一个简单的。css 文件，并使用动态 CSS 类名呈现到 HTML 中，这一概念的整体思想是避免名称冲突或影响应用程序中其他组件的样式:

```
.container_4xks92k2kl {
  width: 400px;
  height: 400px;
  background-color: blue;
}
```

## CSS-in-JS

今天风格化 React 应用程序的流行方式是 CSS-in-JS，我们有很多著名的库可以这样做，比如[风格化组件](https://styled-components.com/)、[情感](https://emotion.sh/docs/introduction)、[镭](https://formidable.com/open-source/radium/)等等。关于 CSS-in-JS，你应该知道的一点是，CSS-in-JS 不是一个特定的库，它是一个试图解决 React 应用程序中样式问题的概念。

既然 React 是关于组件的，为什么不用它来设计我们的元素呢？这就是 CSS-in-JS 向我们介绍的思想，让我们创建特定样式的组件，并从 React 向我们介绍的组件化思想中受益，而不是向我们的元素传递大量的类名属性:

```
import styled from "styled-components";

const Button = styled.button`
  width: 200px;
  height: 40px;
  background-color: green;
  border-radius: 6px;
`;    

```

现在，让我们来谈谈最常用的 React 应用程序样式化方法，为什么这个概念对你的应用程序不是一个很好的主意，以及为什么你不应该使用它。

## React 中的内嵌样式

内联样式是 React 应用程序最常见的样式之一，许多开发人员在开始一个新的应用程序时开始使用这个概念，因为它在开始时非常容易理解，并且您可以获得与其他概念(如 CSS-in-JS 和 CSS 模块)相同的最终结果。内联样式非常著名并被广泛使用的原因之一是，当一个新的开发人员开始学习 React 时，他们可能会从内联样式概念开始。

我们也可以在普通的 HTML 文档中使用内联样式。如果您想要测试它，只需创建一个普通的 HTML 文档，并在您想要设置样式的元素中创建一个样式属性。在普通的 HTML 文档中，这个样式属性接收一个字符串，在这个字符串中，我们可以传递我们的 CSS 属性，就像这样:

```
<button style="width: 200px; height: 50px;">My Button</button>
```

在普通的 HTML 文档中，样式属性期望的类型是一个字符串，与 React 略有不同。

React 中的内联样式非常简单，您需要做的就是创建一个对象，并在该对象内部传递您想要的 CSS 属性:

```
const styles = {
  width: 200,
  height: 50,
  backgroundColor: 'red'
};
```

我们的样式对象中的属性必须是 camelCase 样式，这与 JavaScript 语言有关，我们不能使用 kebab-case，因为它不符合语法规则。

现在，我们可以将这个`styles`对象传递给我们的元素。React 中的每个元素都有一个样式属性，类似于每个 HTML 文档，但是在本例中，样式属性需要一个对象。这就是我们如何在 React 中使用内联样式。

```
import React from "react";

const styles = {
  width: 200,
  height: 50,
  backgroundColor: 'red'
};

const Button = () => (
  <button style={styles}>My Button</button
)
```

当您在 React 中使用内联样式时，您不需要向 CSS 中的属性传递一个单位，您可以只传递一个数字，React 会自动将 px 附加到您的数字内联样式属性。

但是，React 中的内联样式有一些问题，特别是如果您在一个大型应用程序中工作，并且有许多重用的组件。即使你没有在一个大的应用程序中工作，你最终也会在这篇文章中感受到内联样式的一些缺点。

## 内嵌造型有什么问题？

我们知道，当我们开始一个新的应用程序时，内联样式工作得非常好，我们可以在代码中的任何地方应用它。但是如果这个概念运作良好，我们为什么要停止使用它呢？

内联样式概念可能无法帮助您在应用程序中构建最好的 React 组件。如果你计划构建一个高性能、可伸缩、丰富的应用程序，内联样式并不适合你。

## CSS 功能

内联样式不是您的应用程序的好选择的主要原因之一是因为它不支持 CSS 特性(或者它的支持非常差)。

如今，每个应用程序都可能不得不使用一些选择器，比如`:hover`、`:active`、`:focused`等等。我们如何通过内联样式实现同样的行为呢？嗯，这比较棘手，有时你可能会为了在你的组件中实现一个简单的`:hover`而用很多不必要的代码结束。

使用 CSS-in-JS 库，您可以像这样非常容易地实现它:

```
const StyledButton = styled.button`
  width: 200px;
  height: 50px;
  background-color: red;
  &:hover {
    background-color: blue;
  }
`;

const App = () => {
  return (
    <StyledButton backgroundColor="green">
      My Button
    </StyledButton>
  )
};
```

如果你不打算使用 CSS-in-JS 来做这件事，你将不得不写很多代码，甚至可能使用状态来模拟元素中的悬停。这是内联样式最令人头疼的地方之一，也是您应该考虑在应用程序中放弃内联样式的主要原因。

## 小道具

有时，当我们构建 React 组件时，我们希望达到组件可重用性的良好水平，有时，我们会在某些情况下需要更改 CSS 元素的特定属性。假设我们有一个按钮，这个按钮的默认`backgroundColor`是红色的:

```
const Button = () => {
  return (
    <button
      style={{
        width: 200,
        height: 50,
        backgroundColor: "red"
      }}>
        My Button
    </button>
  );
};
```

但是，如果我想使用同一个按钮，但是在这种情况下，我想将`backgroundColor`改为绿色，该怎么办呢？每天，我们都会面临这些情况，这里的内联样式不能帮助我们创建一个好的组件。

我们可能不得不最终使用 JavaScript 三元语句，这不是一个好主意，因为这将使我们的代码更加沉重，难以理解，并增加组件的大小。

```
const Button = ({ green }) => {
  return (
    <button
      style={{
        width: 200,
        height: 50,
        backgroundColor: green ? "green" : "red"
      }}>
        My Button
    </button>
  );
};
```

我们一致认为这段代码看起来不太好。我们必须使用一个 JavaScript 三元语句来只改变按钮样式的一个属性，想象一下，如果我们有一个复杂的组件，有 20 个可改变的属性呢？很快就会变得一团糟。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

否则，在 CSS-in-JS 库中，我们所要做的就是将属性传递给我们的样式，它会根据属性的值而改变，就像这样:

```
const StyledButton = styled.button`
  width: 200px;
  height: 50px;
  background-color: ${({ backgroundColor }) => backgroundColor};
`;

const App = () => {
  return (
    <StyledButton backgroundColor="green">
      My Button
    </StyledButton>
  )
};
```

## 媒体查询

如今，应用程序支持移动设备是一种默认行为，所以你的应用程序中会有很多媒体查询。

但是，如果您正在使用内嵌样式，您不能使用媒体查询，因为它不支持它。记住，你使用一个普通的 JavaScript 对象来传递给你的元素，你怎么能使用媒体查询呢？

您可以通过创建一些自定义函数来使用它，以获得窗口的实际宽度大小，如下所示:

```
const minStyles = {
  width: 200,
  height: 50,
  backgroundColor: "red"
};

const maxStyles = {
  width: 300,
  height: 50,
  backgroundColor: "blue"
};

const App = () => {
  const renderStyles = () => {
    if (window.innerWidth <= 800) {
      return minStyles;
    }
    return maxStyles;
  };

  return <button style={renderStyles()}>My Button</button>;
};
```

但是从长远来看，想象一下如果你需要支持五种不同的媒体查询，你的代码会是什么样子？那会很乱，一点也不容易理解。此外，您还必须创建大量的`styles`对象来处理所有的媒体查询。

## 可量测性

想象一下，一个应用程序每天必须接触数百万用户。您最终将进入一个有许多组件的应用程序，内联样式可能会让您和您的同事在创建新组件或维护已有组件时慢一点。

如果您的应用程序中仍然有一些内联样式组件，并且它们工作得很好，那么现在，您可以暂时坚持这个概念。这里的目的是向您展示为什么您的应用程序从长远来看性能和可伸缩性不是很好。

开发人员的工作之一是试图找到最高效的解决方案，这种解决方案不需要花费太多时间来实现，并且从长远来看，有助于构建一个强大的、有弹性的应用程序。

## 结论

在本文中，我们了解了一些对 React 应用程序进行样式化的方法，了解了 React 中更多的内联样式化概念，以及如果我们计划开发一个可伸缩的、高效的应用程序，为什么不应该使用这个概念。

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