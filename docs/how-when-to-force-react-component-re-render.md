# 如何以及何时强制 React 组件重新呈现

> 原文：<https://blog.logrocket.com/how-when-to-force-react-component-re-render/>

React 通常会自动重新渲染组件，但是为了让我们真正理解如何以及何时强制 React 重新渲染组件，我们需要理解 React 的内部工作方式。

自从网络出现以来，我们就使用 HTML 和 CSS 来定义网站的视觉表现。这一点没有改变，但是随着 JavaScript 的引入，新概念使我们能够读取和更新页面的 HTML。我们称这个 API 为 DOM。

像 [jQuery](https://jquery.com/) 这样的库变得非常流行，因为它们在 DOM API 之上提供了一个抽象层，该抽象层提供了增强的功能和兼容性，使得开发人员可以轻松地构建 web 应用程序。

但是 jQuery 有其局限性。它没有解决开发人员在处理动态加载的内容时遇到的关键问题，比如保持 JavaScript 状态(变量)和 DOM (HTML)同步。在 JavaScript 中更改变量不会直接影响 DOM。

使用 React 这样的框架可以解决这个问题。React 依靠 JavaScript 来维护应用程序的状态。这个包含对页面上每个对象的 JavaScript 引用的主状态对象被称为[虚拟 DOM](https://blog.logrocket.com/managing-dom-components-reactdom/#:~:text=Virtual%20DOM%20(VDOM)%20vs%20DOM) 。虚拟 DOM 上的任何变化都会自动反映到 DOM 上，这是 React 最好的魔术。

但是我们如何更新虚拟 DOM 呢？我们通过构建组件并使用状态和道具来实现这一点。

您可能会有下一个问题，当组件没有按预期更新时，在边缘情况下会发生什么？这可能吗？可能吧，但是很复杂，大家讨论一下。

## 为什么 React 组件没有重新渲染？

通常，强制 React 组件重新渲染不是最佳实践，即使 React 无法自动更新组件。因此，在考虑强制重新渲染之前，我们应该分析我们的代码，因为 React magic 依赖于我们是好的宿主。

### React 中的状态更新不正确

让我们构建一个简单的组件来演示组件不呈现的一个常见原因。我们将构建一个简单的应用程序，它将显示一个用户名`Juan`，并且在按下一个按钮后，该名称将变为`Peter`。

[这里的](https://codesandbox.io/s/react-wrong-state-change-byx8f)是一个完整代码的应用程序演示。您可能注意到，单击按钮后，什么也没有发生，即使我们改变了按钮上的状态:

```
function changeUserName() {
   user.name = "Peter";
   setUser(user);
 }

```

组件没有改变，所以没有重新渲染触发器。原因如下。

React 通过检查其[浅相等性](https://reactjs.org/docs/shallow-compare.html)(或引用相等性)来评估状态变化，这将检查`state`的预览值和新值是否引用同一个对象。在我们的例子中，我们更新了`user`对象的一个属性，但是我们从技术上使`setUser`成为同一个对象引用，因此，React 没有察觉到其状态的任何变化。

如 [React 文档](https://reactjs.org/docs/react-component.html#state)中所述，状态应该被视为不可变的。

那么，我们该怎么解决呢？我们可以用正确的值创建一个新对象，如下所示:

```
function changeUserName() {
   setUser({
     ...user,
     name: "Peter"
   });
 }

```

注意，我们在 JavaScript 中使用了[扩展操作符](https://livecodestream.dev/post/how-to-use-the-spread-operator-in-javascript/)来保留原始对象的属性，同时在新对象下更新其`name`属性。最后的结果可以在这里观察[。](https://codesandbox.io/s/react-correct-state-change-forked-f1wc7)

### 没有状态变化的错误更新的道具

虽然看起来不可能，但是在没有状态改变的情况下错误地更新 props 是可能发生的，并且通常会导致错误。让我们看一个例子。

在这个[演示](https://codesandbox.io/s/react-props-not-state-ml872)中，我构建了一个时钟，它有一个主要问题:在我第一次加载屏幕后，时间没有改变。不是很有用的钟，对吧？

让我们来看看负责计算当前时间的代码:

```
let myTime;

 function setMyTime() {
   myTime = new Date();
   setTimeout(() => {
     setMyTime();
   }, 1000);
 }

 setMyTime();

```

这段代码看起来很难看，通常不是为 React 组件编写代码的好方法，但是它很有效。每一秒，运行时将调用函数`setMyTime`并更新变量`myTime`，然后将变量传递给我们的`Clock`组件进行渲染。

```
<Clock myTime={myTime} />

```

这个演示不起作用，因为道具是状态的反映，所以道具的独立变化不会触发重新渲染。为了修复它，我们需要一个全面的重写。

注意，我们引入了 state 来管理`myTime`和`useEffect`来启动和清除计时器，以避免组件重新渲染时出现错误。而且很管用！

```
const [myTime, setMyTime] = useState(new Date());

 useEffect(() => {
   var timerID = setInterval(() => tick(), 1000);

   return () => clearInterval(timerID);
 });

 function tick() {
   setMyTime(new Date());
 }

```

## 强制 React 组件重新渲染

强迫组件重新渲染通常是不可取的，React 中自动重新渲染的失败通常是由于我们代码库中的一个潜在错误。但是，如果您有必要强制 React 组件重新呈现，有几种方法可以实现。

### 强制更新 React 类组件

如果您在代码中使用类组件，那么您很幸运。React 提供了一个[官方 API 来强制重新渲染](https://reactjs.org/docs/react-component.html#forceupdate)，实现起来很简单:

```
someMethod() {
   // Force a render without state change...
   this.forceUpdate();
 }

```

在任何用户或系统事件中，您可以调用方法`this.forceUpdate()`，这将导致在组件上调用`render()`，跳过`shouldComponentUpdate()`，从而强制 React 重新评估虚拟 DOM 和 DOM 状态。

这种方法有一些注意事项:

*   React 将触发子组件的正常生命周期方法，包括`shouldComponentUpdate()`，因此我们只能强制重新呈现当前组件
*   VirtualDOM 仍然会用 DOM 验证它的状态，所以 React 只会在标记改变时更新 DOM

### 强制更新功能组件

没有官方的 API 来重新渲染一个函数组件，也没有 [React 钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)。然而，有一些聪明的技巧可以发出信号来对组件应该更新做出反应。

1.  用状态对象自身的新实例替换状态对象

假设我们想要强制刷新上面的更改用户示例。我们可以这样做:

```
someMethod() {
   // Force a render with a simulated state change
   setUser({ ...user });
 }

```

因为`user`是一个对象，我们可以将它复制到一个新的对象，并将其设置为新的状态。这同样适用于任何其他对象或数组。

2.有一个空的状态变量触发更新

这个方法很有趣，因为它在状态中创建了一个新对象。我们只关心它的更新功能如下:

```
const [, updateState] = React.useState();
const forceUpdate = React.useCallback(() => updateState({}), []);

```

在这里，我们使用`useCallback`来记忆我们的`forceUpdate`函数，从而在整个组件生命周期中保持它不变，并使它可以安全地作为`props`传递给子组件。

以下是如何使用它的示例:

```
import React from "react";

export default function App() {
 const [, updateState] = React.useState();
 const forceUpdate = React.useCallback(() => updateState({}), []);

 console.log("rendering...");

 return (
   <div className="App">
     <h1>Time to force some updates</h1>
     <button onClick={forceUpdate}>Force re-render</button>
   </div>
 );
}

```

现在，每次我们点击**强制重新渲染**按钮，组件就会重新渲染。你可以[在这里](https://codesandbox.io/s/forcing-functional-component-re-render-5uj12)观看现场演示。

## 结论

一般来说，我们应该防止强制 React 来重新渲染组件。如果 React 无法自动重新渲染组件，很可能是项目中的潜在问题阻止了组件的正确更新。

然而，我们讨论了一些常见的方法来强制 React 重新渲染组件，如果需要的话。编码快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)