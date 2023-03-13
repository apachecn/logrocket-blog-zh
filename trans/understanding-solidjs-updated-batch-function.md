# 了解 SolidJS 更新的批处理功能

> 原文：<https://blog.logrocket.com/understanding-solidjs-updated-batch-function/>

所有框架旨在改进的最紧迫的挑战之一是状态和反应性管理，正如对框架和库所做的开发一样，如 [SolidJS](https://www.solidjs.com) 、React、Svelte、Angular 和 Vue。

批处理状态更新是框架试图改进其反应模型的一种方式。框架允许开发人员指定哪些更改是相关的，以便 UI 更新被延迟，直到所有相关的值都被更改，而不是重新呈现 UI 来响应每个单独的状态更改。

在 React 18 中，这个问题通过自动批处理特性得到了解决，该特性在批处理的基础上进行了改进，增加了事件、异步事件和钩子。这种行为是自动的，需要您使用`flushSync`方法来防止它。

虽然已经有一段时间了， [Solid 最近对其`batch`功能](https://www.solidjs.com/docs/latest#batch)进行了更新，该功能允许开发者根据他们特定应用的需求手动决定何时进行批处理。在本文中，我们将探索它是如何工作的。

## 固体术语复习

Solid 是一个前端框架，与 Angular、Vue 和 React 等替代框架相比，它提供了一些好处。

像 Svelte 一样，Solid 也是编译的，所以不需要将整个框架发送给每个最终用户，而是编译代码并转化成普通的 JS。因此，您只是向最终用户交付了小得多的包所必需的代码。

像 React 一样，Solid 使用 JSX 作为一种表达 UI 的方式。在 Solid 中，一个反应值被称为一个信号，一个应该在这些值改变时运行的操作被称为一个效果。

为了创建一个信号，我们使用`createSignal`，它类似于 React 中的`useState`。为了创造一种效果，我们使用`createEffect`。但是，组件返回的 JSX 会被自动视为一种效果。

这与 React 相反，在 React 中，每次状态改变时，组件功能都完整地运行。在 Solid 中，只有显式指定的内部效果代码才会在状态更新时重复。因此，与在 React 中使用`useEffect`来指定不需要在每次更新时运行的代码不同，在 Solid 中，如果您将代码封装在`createEffect`中，代码只在更新时运行。

## 没有`batch`会怎样？

为了测试这个例子，你可以从这个回购的主分支[中克隆代码。所有相关代码都在下面的`/src/App.jsx`中:](https://github.com/AlexMercedCoder/solidjs-batch-example)

```
import logo from './logo.svg';
import styles from './App.module.css';
import {createSignal, createEffect} from "solid-js"
function App() {
  const [p1, setP1] = createSignal("Alex Merced")
  const [p2, setP2] = createSignal("Tony Merced")
  let p1Input
  let p2Input
  createEffect(() => console.log(p1(), p2()))
  return (
    <div class={styles.App}>
      <header class={styles.header}>
        <h1>Player: {p1}</h1>
        <h1>Player: {p2}</h1>
      </header>
      <form onSubmit={(event) => {
        event.preventDefault()
        setP1(p1Input.value)
        setP2(p2Input.value)
      }}>
        <input type="text" placeholder="player 1" name="p1" ref={p1Input}/>
        <input type="text" placeholder="player 2" name="p2" ref={p2Input}/>
        <input type="submit"/>
      </form>
    </div>
  );
}
export default App;

```

我们有两个信号:电抗值`p1`和`p2`。这两个值在 UI 中都显示为`h1`。为了展示它们是如何变化的，我们创建了一个将它们都记录到终端的效果。

表单中有两个输入。当提交表单时，信号的值被分别设置为这些输入的值。在这里，我们看到一些独特的事情发生。假设您在第一个输入中输入`Bread`，在第二个输入中输入`Cheese`。提交表单时，您将看到以下控制台日志:

```
Bread Tony Merced
Bread Cheese

```

当我们在第 20 行更改`p1`时，它触发所有依赖于该值的效果，用两个控制台日志重新运行 UI 更新和效果。然后，当`p2`被更改时，整个事情再次发生，因此出现了第二个日志。

最终，最终的 UI 实际上取决于它们的值。为了提高效率，你应该等待两个值都被更新来重新运行所有的从属效果，这就是`batch`的用武之地。

## 在实体中使用`batch`

要查看示例代码，您可以[克隆存储库的这个分支](https://github.com/AlexMercedCoder/solidjs-batch-example/tree/batch)。假设我们的`src/App.jsx`文件中有下面的代码:

```
import logo from "./logo.svg";
import styles from "./App.module.css";
import { createSignal, createEffect, batch } from "solid-js";
function App() {
  const [p1, setP1] = createSignal("Alex Merced");
  const [p2, setP2] = createSignal("Tony Merced");
  let p1Input;
  let p2Input;
  createEffect(() => console.log(p1(), p2()));
  return (
    <div class={styles.App}>
      <header class={styles.header}>
        <h1>Player: {p1}</h1>
        <h1>Player: {p2}</h1>
      </header>
      <form
        onSubmit={(event) => {
          event.preventDefault();
          batch(() => {
            setP1(p1Input.value);
            setP2(p2Input.value);
          });
        }}
      >
        <input type="text" placeholder="player 1" name="p1" ref={p1Input} />
        <input type="text" placeholder="player 2" name="p2" ref={p2Input} />
        <input type="submit" />
      </form>
    </div>
  );
}
export default App;

```

在上面的代码片段中，我们在第 3 行添加了`batch`的导入，并在第 19 行将两个值更新封装在对`batch`的调用中。

如果您再次用`bread`和`cheese`填写输入，在点击**提交**后，您将只能看到一个控制台日志:

```
bread cheese

```

`batch`延迟运行效果，直到`batch`回调中的所有状态更新完成。这减少了我们代码中运行效果的次数，从而提高了代码的整体效率。

## 什么时候应该用`batch`？

在应用程序的早期版本中，您可能不会费心使用`batch`，而是仅仅关注于构建一个功能性的应用程序。但是，随着你的应用程序成熟，你需要优化性能，批处理状态更新，[记忆复杂的计算，以及其他技术](https://blog.logrocket.com/react-re-reselect-better-memoization-cache-management/)可以真正帮助你的应用程序尽可能平稳地运行。

底线是，如果你正在更新几个独特的信号，但你不想在它们都完成更新之前运行效果，使用`batch`。

## 结论

批处理是有用的，因为它可以防止更清晰的应用程序的冗余操作。React 采用的方法是假设您总是想要批处理，而 Solid 提供了工具，让您更容易地表达您想要的结果，并且只在您需要时进行批处理，而不是在您不需要时。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。