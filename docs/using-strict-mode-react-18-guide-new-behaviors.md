# 在 React 18 中使用严格模式:新行为指南

> 原文：<https://blog.logrocket.com/using-strict-mode-react-18-guide-new-behaviors/>

React 已经存在很长时间了。每个主要版本都向我们介绍了新的技术、工具和处理 UI 问题的方式。

[React 于 2022 年 3 月](https://reactjs.org/blog/2022/03/29/react-v18.html)发布了 v18，包括了一些架构上的变化。这个版本主要关注于并发模式、新的 React 挂钩以及 React 的严格模式 API 的行为变化。虽然严格模式已经成为 React 的一项功能有一段时间了，但 v18 使它在捕捉早期错误方面更加有效，从而使代码库更加可预测。

在本文中，您将了解严格模式以及最初引入它的原因。您将看到它的各种特性，以及 v18 版本如何改进其 API 并提供与钩子更好的兼容性。

严格模式试图通过 React 基于悬念的架构为未来做好准备，使其在反思 UI 问题方面更具弹性。我们开始吧！

## React 的严格模式介绍

严格模式可以被认为是`["use strict"](https://www.w3schools.com/js/js_strict.asp)`符号。这是不久前在 ECMAScript v5 中引入的，确保了 JavaScript 的更严格版本。

```
"use strict";
x = 3.1415;

```

上面的例子会抛出一个错误，因为没有定义`x`。请注意在文件顶部添加`"use strict"`是如何确保这一点的。在没有添加`"use strict"`的情况下，您甚至可能不会得到这个错误，因为如果没有严格的类型定义(如`"use strict"`、TypeScript、 [flow](https://flow.org/) 等)，JavaScript 往往会执行奇怪的行为。)

类似地，React 中的严格模式是一个仅供开发使用的工具，它在您编写 React 代码时强制执行更严格的警告和检查。

您可以为任何组件启用`StrictMode`,只需将组件名称作为子属性包装在`StrictMode`中，如下所示:

```
<StrictMode>
    <Button />
</StrictMode>
<StrictMode>
    <Navbar />
</StrictMode>

```

更推荐的方法是用`StrictMode`包装根`App`组件。注意`App`一般是 create-react-app 和 Next.js 中的根组件

```
<StrictMode>
    <App />
</StrictMode/>

```

这将在整个 React 代码库中强制执行开发时间检查和警告。当然，确保像这样导入`StrictMode`:

```
import { StrictMode } from 'react'
 <StrictMode>
   .....
 </StrictMode>

```

或者像这样:

```
import React from 'react'
 <React.StrictMode>
  .....
 </React.StrictMode>

```

现在，我们将更深入地了解严格模式的各种含义，并帮助在开发的早期发现问题。

## 关于使用不安全生命周期方法的警告

[React 基于类的生命周期方法](https://blog.logrocket.com/react-lifecycle-methods-tutorial-examples/)经历了一系列的 API 变化。许多曾经被广泛使用的方法现在已经被官方否决，并且不鼓励支持更现代的 API。

React 的严格模式现在会警告开发者他们是否在使用这些被弃用的 API，比如`componentWillMount`、`componentWillReceiveProps`和`componentWillUpdate`。这些现在被认为是不安全的，以至于 React 在这些 API 名称前添加了一个`UNSAFE`前缀:

*   `UNSAFE_componentWillMount`
*   `UNSAFE_componentWillReceiveProps`
*   `UNSAFE_componentWillUpdate`

如果正在使用的任何第三方包包含这些被否决的 API，严格模式甚至足够智能地警告开发人员。您可以自己修改这些包，也可以选择其他包。

## 推荐使用`createRef` API 而不是传统的字符串引用

如果您在基于类的架构是创建组件的实际方式时使用过 React，您可能会使用字符串引用 API:

```
class Form extends Component {
  render() {
    return <input onClick={() => this.focus()} ref='input' />;
  }
  focus() {
    console.log(this.refs.input.value);
  }
}

```

尽管可读性强、使用方便，但由于以下几个原因，该 API 现在被认为是一种遗产，包括:

*   包装的组件不能确定它的子组件是否已经有了一个引用。使用回调引用模式可以解决这个问题
*   string ref API 可能很难阅读，也很难使用类型检查器进行静态分析

React 的严格模式警告开发者要么使用回调模式，要么使用更现代的`createRef` API。

## 关于不推荐使用的`findDOMNode`的警告

是一个基于类的 API，用于定位 DOM 树中任何组件的深层元素。

```
class Layout extends Component {

componentDidMount() {
  const nodeElement = ReactDOM.findDOMNode(this);
}

     render () {
     return <Navigation>{this.props.children}</Navigation>;
     }
    }

```

这可能看起来很好，但它实际上导致了 React 抽象原则的问题。

父元素必须确保其子元素向下延伸并呈现正确的 DOM 节点。一个很大的缺点是，`findDOMNode`只是一个一次性的调用 API，所以如果任何节点元素由于某种状态更新而改变，它将不会被`findDOMNode` API 反映和更新。

考虑到所有这些缺点，严格模式警告您不要使用该 API，并且它可能会在未来的 React 版本中被删除。

大多数时候，DOM 元素现在可以使用`ref`作为目标。您可以简单地将一个`ref`引用附加到您需要定位的元素上。

```
class Form extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();    
}

// handle textInput.current logic here //
  render() {
    return (
      <input
          type="text"
          ref={this.textInput} 
   />       
    );
  }
}

```

## 检测意外的副作用

React 的严格模式做了一些关于流行的内置钩子的有趣的事情，比如`useState`、`useMemo`和`useReducer`。具体来说，它在开发中调用这些函数两次，在生产模式中调用一次(如预期的那样)。

这可能会在调试代码时造成一些混乱，但是通过这样做，严格模式可以确保检查潜在的内存泄漏。这也有助于使代码在严格模式下更具确定性。

不仅仅局限于功能组件，同样的调用函数两次的行为也可以在基于类的架构中找到，比如`constructor`、`render`、`shouldComponentUpdate`等等。

如果您使用的是 create-react-app，则整个应用程序默认为严格模式。在类组件中使用这些钩子或状态更新函数时，您甚至会看到控制台消息被记录两次。

在 v18 之前，React 用于在函数被调用两次时立即使第二个`console.log`方法静默。但是，在 v18 中，React 没有抑制任何日志，从而为开发人员提供了更多的透明度。现在，所有这些日志在任何函数、钩子等的双重调用中都会被调用两次。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 旧上下文 API 上的警告

类似于 refs API，我们也有一个遗留的上下文 API。严格模式警告不要使用遗留上下文 API，因为它将从未来的版本中删除。相反，我们有一个使用提供者-消费者模式的更现代的上下文 API。

```
const ThemeContext = React.createContext('dark')

// consume it here
 <ThemeContext.Provider value={data}>
     {children}
</ThemeContext.Provider>

```

现在，这是使用新的上下文 API 处理应用程序状态上下文的推荐方式。

## React v18 卸载和重新安装架构

React v18 引入了关于卸载和重装的新的严格模式行为。现在，每个元素将被卸载并重新装载，其状态和效果与该元素第一次装载时相同。

典型的安装和重新安装周期如下所示:

*   该元素第一次安装
*   副作用产生了
*   严格模式现在模仿效果的破坏
*   副作用将应用于已安装的组件

这使得 React 代码更有弹性，并有助于保持 UI 的状态。例如，如果用户在第一个选项卡上，并立即在第一个和第二个选项卡之间来回切换，React 需要确保安装和销毁正确的元素块，同时保留正确的 UI 状态和副作用。

从 v18 开始，严格模式增加了这一开发专用行为。

## 结论

您现在已经涵盖了 React v18 的严格模式更新中的所有内容！

我们已经看到了严格模式如何影响开发模式工具。它有自己的一套规则和行为，确保对代码库进行严格的警告和检查。这不仅有助于开发人员使代码库为未来做好准备，也有助于重构。

React 官方团队建议在应用范围内强制执行严格模式，以充分利用它。对于未来的 React 版本，预计严格模式将获得更多的特性，以帮助像我们这样的开发人员获得更好的工具支持。

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