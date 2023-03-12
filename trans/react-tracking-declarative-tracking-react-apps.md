# React-tracking:React 应用程序的声明式跟踪

> 原文：<https://blog.logrocket.com/react-tracking-declarative-tracking-react-apps/>

在你的网页上追踪用户有很多好处。例如，跟踪提供了对用户如何与您的网页交互的洞察，指示您的 UI 的哪些组件是最常用的和最不常用的。

声明性跟踪是一种跟踪用户在网页上的交互的方法。传统上，我们使用 DOM 来跟踪所有的用户交互，然而，基于组件的框架的发展使我们能够在应用程序中使用基于组件的跟踪。

React 中用于声明性跟踪的一个库是 [react-tracking](https://github.com/NYTimes/react-tracking) ，它实现了对页面每个组件的跟踪。您可以使用钩子和装饰器来声明对像点击事件、状态、功能组件、类组件等[事件的跟踪。此外，您可以将跟踪问题划分到单个组件，避免在整个应用程序中泄漏。](https://blog.logrocket.com/react-pure-components-functional/#functionalcomponentsvsclasscomponents)

在本文中，我们将使用 react-tracking 库来研究声明性跟踪。要阅读本文，您应该熟悉 React 和 React 挂钩。我们开始吧！

## 反应追踪是如何工作的？

我们可以把反应跟踪分解成三个主要部分。例如，`trigger`启动跟踪过程，并且可以是`click`事件、`loading`事件等。本质上，`trigger`是用户与应用中元素的交互。

`component`是我们正在跟踪的组件。通常情况下，`component`指表格、卡片等。您可以跟踪功能组件和基于类的组件。所有的跟踪数据都将被包装在`dispatch`组件中，并在被触发时显示在控制台中。如果不定义`dispatch`组件，跟踪数据将被推送到`window.dataLayer[]`，这对 Google Analytics 来说非常理想。

有了这三个组件，我们可以使用反应跟踪来跟踪应用程序中的任何组件。让我们通过一个例子来看看它是如何工作的。

## 反应跟踪示例:跟踪`click`按钮

作为一个例子，我们将使用一个`click`按钮来跟踪一个功能组件和一个类组件。

首先，让我们设置开始使用反应跟踪所需的环境。我们将使用 VS Code 作为我们的代码编辑器，它功能强大，包含帮助您编写更好代码的扩展。如果您熟悉不同的代码编辑器，请随意使用它。

### 设置 React

要安装 react-tracking，我们首先需要安装 react。我们将创建一个小程序来跟踪函数和类组件中的`button click`事件。`class`按钮还会跟踪点击次数。

首先，我们将按如下方式安装 React:

```
npm init react-app my-app

```

安装了 react 之后，我们可以安装 React 跟踪库。运行下面的命令:

```
npm install --save react-tracking

```

安装了 React 和 react-tracking 之后，我们将开始编写代码。

### react-跟踪进口

我们需要从 react-tracking 导入以下元素:

*   `React`
*   `useTracking`:用作钩子来跟踪任何事件
*   `track`:用`@`装饰器跟踪`class`和`member`功能，并将所有跟踪数据发送到控制台

使用下面的命令导入上面的项目:

```
import track, { useTracking } from "react-tracking";
import React from "react";

```

## 功能成分

该功能组件将包含`useTracking()`钩子，用于跟踪`click` `button`事件。`useTracking()`然后将按钮点击数据调度到控制台。

下面，我们有一个包含`trackEvent`的按钮的`onClick`事件函数。在这里，您可以传递希望在控制台中显示的消息，我们将在稍后添加:

```
const HookButton = () => {
  const {trackEvent} = useTracking()
  return (
    <div>
      <h2>Click for HookButton</h2>
      <button onClick={()=> trackEvent({funComponent:'HookButton', event: "HookButton-Clicked"})}>Click Me!</button>
    </div>
  );
}

```

## `class`组件

在`class`组件中，我们将跟踪`button click`事件以及点击次数。为了跟踪`class`组件，我们将使用`track`，后跟我们将设置的消息。

在`class`中，我们有一个统计点击次数的状态。作为回报，我们用链接到`handleClick`函数的`click`事件呈现了一个按钮。

我们还用`track`跟踪了这个类的成员函数。我们有一个以状态为道具的箭头函数来传递`numClicked`的值。在`handleClick`功能中，每次点击按钮时，我们都会将`numClicked`的状态值增加`1`:

```
@track({class:'ClassButton'})
class ClassButton extends React.Component{
  state = {
    numClicked : 1
  };

  @track((props, state) =>({event:'ClassButton-Clicked', clickCount:state.numClicked}))
  handleClick = () => {
    this.setState({
      numClicked: this.state.numClicked + 1
    })
  }
  render(){
    return(
      <div>
        <h2>Click for ClassButton</h2>
        <button onClick={this.handleClick}>Click Me!</button>
      </div>
    )
  }
}

```

## `App`组件

现在，我们将把`HookButton`功能组件和`ClassButton`类组件包装在`App`组件中:

```
const App = () =>{
  return(
    <div className="App">
      <h1>React-Tracking</h1>
      <HookButton />
      <ClassButton />
    </div>
  )
}

```

## `dispatch`组件

`dispatch`组件是一个功能性组件，当被触发时将在控制台中呈现。你可以像`Component: "Apps"`一样给`dispatch`添加自己的消息。`dispatch`使用`track`来显示我们在控制台中定义的内容。

我们还将使用`dispatch`将我们收集的所有数据发送到控制台，然后是您想要在页面上呈现的组件。接下来，我们只需要导出包含跟踪日志的功能组件:

```
const TrackedApp = track(
  { Component: "Apps" },
  {
    dispatch: data => {
      console.log(data);
    }
  }
)(App);

export default TrackedApp

```

上面代码的输出如下:

![React Tracking Hook Button Class Button Output](img/810ab30c582aa3ac1bf5e0f9e18f2e38.png)

当您单击按钮时，可以在控制台中找到跟踪详细信息。

`HookButton`点击输出:

```
{app: "my-app", funComponent: "HookButton", event: "HookButton-Clicked"}

```

`ClassButton`点击输出:

```
{app: "my-app", class: "ClassButton", event: "ClassButton-Clicked", clickCount: 1}

```

`clickCount`将随着您继续点击该按钮而增加。你可以找到代码并在代码沙箱中进行试验:

结论

## 在本教程中，我们探索了 react-tracking，它很好地跟踪了所有事件，并在控制台中提供了重要的细节。我们创建了一个程序来跟踪`click button`事件，并显示每次点击的跟踪数据。`@`装饰器和`track`可以跟踪 React 中的大部分数据，包括道具和状态中的数据。

您可以使用本教程中的步骤来跟踪应用程序中的不同组件和类。我希望你在下一个项目中考虑反应追踪。感谢您的阅读，如果您有任何问题，请务必留下评论。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).