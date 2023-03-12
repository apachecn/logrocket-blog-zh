# React v17 的新特性，以及通往 v18 的道路

> 原文：<https://blog.logrocket.com/whats-new-in-react-v17-and-the-road-to-v18/>

几周前，React 团队发布了该库的最新版本 React v17.0 RC。在本帖中，我们将探讨这个新版本附带的新变化和更新。

React 是一个声明式的、高效的、灵活的 JavaScript 库，用于构建用户界面，它无疑是最受欢迎的 JavaScript 库之一。它在 GitHub 上有超过 [156，000 颗星，并且是最活跃的前端社区之一，构建伟大的应用程序。](https://github.com/facebook/react/)

## 升级版本的 UX 和通往 v18 的道路

这个新版本是独一无二的，因为它不附带任何可见的新功能。而是全面专注于 React 整体的升级体验。

自从 React 出现以来，总是有一个新的版本设置过程，其中您必须选择是升级您的版本以使用新功能，还是继续使用旧版本，没有任何中间选择。例如，上下文 API 已经被弃用，即使大多数 React 应用程序不使用它，React 仍然支持它。

围绕继续支持还是放弃使用旧版本的应用程序，一直存在争议。因此，随着时间的推移，这种升级策略被重新设想为更包容旧版本，因此，新版本 17 支持渐进式 React 升级。

React 团队基本上试图确保从一个 React 版本升级到另一个版本是简单无缝的。在版本 17 中，您得到了一个垫脚石来确保，例如，将您在一个版本中管理的树嵌入到由一个完全不同的版本管理的另一个树中会更加安全。

这只是为了提供更多的升级选项，因为在此之前，如果你从一个 React 版本升级到一个新的版本，它会升级整个应用程序。在某些情况下，在同一个项目中可以有两个版本的 React，这会导致许多事件问题。

例如，当 React 18 推出时，从 17 这样的版本升级会给你提供完全升级或渐进升级的选择。如果你选择渐进，你的应用会一点一点升级——比如把一个应用的大部分升级到 React 18，在 17 版本上保留一个对话框组件。

这可能不是理想的路线，但对于维护大型项目的开发人员来说会很方便，这个新版本将确保这些应用程序不再被遗忘。

## 对事件委托的更改

React 处理事件处理程序的方式是，如果您将一个事件处理程序内联到一个按钮上，如下所示:

```
<button onClick={handleClick}>
```

compile 上的普通 JS 等价物将如下所示:

```
myButton.addEventListener('click', handleClick);
```

React 然后将每个事件类型的一个处理程序直接附加到文档节点，而不是附加到声明它们的 DOM 节点。这就是所谓的事件委托，它对于大型应用程序和类似事件重放的过程非常有益。

考虑到我们在上一节刚刚谈到的逐步升级，用不同 React 版本构建的嵌套应用程序也必须重新设计。在 React 的这个新版本中，事件处理程序将不再附加在文档级；相反，它们将被附加到呈现树的 DOM 容器中。

```
const rootNode = document.getElementById('root');
ReactDOM.render(<App />, rootNode);
```

有了这个改变，现在嵌套用不同版本的 React 构建的应用程序是相当安全的，尽管这将从版本 17 和更高版本开始。

## 重大变化

这个新版本有一些突破性的变化。团队确保它们尽可能少；其中一些只是行为上的，但仍然值得注意。所以像以前版本中被否决的方法这样的东西仍然可用。

### 事件委托

关于事件委托的新变化，您可能会遇到一些问题。

例如，如果您使用`document.addEventListener()`添加手动监听器，您通常会期望它会捕获所有的 React 事件。在以前的 React 版本中，即使您在事件处理程序中调用了停止传播函数，您的自定义文档侦听器仍然会得到它们，因为本机事件已经在文档级别。

在这个新版本中，`e.stopPropagation()`实际上会阻止您的文档处理程序释放:

```
document.addEventListener('click', function() {
  // This custom handler will no longer receive clicks
  // from React components that called e.stopPropagation()
});
```

要解决这个问题，通过传递一个`{ capture: true }`选项作为第三个参数，将您的事件监听器转换为使用一个`capture`短语，如下所示:

```
document.addEventListener('click', function() {
  // Now this event handler uses the capture phase,
  // so it receives *all* click events below!
}, { capture: true });
```

这样，我们看到事件委托现在比以往任何时候都更接近正常的 DOM。

### 浏览器对齐

对 React 中的事件系统进行了一些更改，其中包括:

*   为了防止常见的混淆，比如滚动子元素时触发，`onScroll`事件不再冒泡
*   React `onBlur`和`onFocus`事件现在已经切换到在内部使用本机`focusin`和`focusout`事件，更好地匹配 React 的现有行为，甚至提供更多信息
*   像`onClickCapture`这样的捕获短语事件现在使用实际的浏览器捕获短语监听器

这几个变化更加符合浏览器的行为方式，甚至提高了互操作性。

### 没有事件池

从这个新版本开始，事件池优化已经从 React 中删除，原因是持续的混乱和不能提高现代浏览器性能的简单事实。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
function handleChange(e) {
  setData(data => ({
    ...data,
    // This crashes in React 16 and earlier:
    text: e.target.value
  }));
}
```

React 团队称这是一种行为变化，并将其标记为破坏，尽管他们在脸书没有看到它破坏任何东西，所以这种机会真的很低。另外，`e.persist()`在事件对象上仍然可用，尽管它不做任何事情。

### 效果清理时间

这个新版本也使得`useEffect`钩子清理函数计时更加一致。

```
useEffect(() => {
  // This is the effect itself.
  return () => {    // This is its cleanup.  };});
```

在 React 16 中，效果清理功能是同步运行的，与大多数效果相反，大多数效果不会延迟屏幕更新，React 默认异步运行。React 团队发现，这种同步过程并不理想，就像当用户切换标签时，大型应用程序的`componentWillMount`并不如此。

所以这个新版本带来了一些变化。效果清理功能现在将像其他功能一样异步运行，如果组件正在卸载，清理将在屏幕上显示更新后运行。

### 返回未定义的一致错误

React 开发人员知道，返回 undefined 的函数会标记一个错误:

```
function Button() {
  return; // Error: Nothing was returned from render
}
```

主要是因为无意中返回 undefined 非常容易:

```
function Button() {
  // We forgot to write return, so this component returns undefined.
  // React surfaces this as an error instead of ignoring it.
  <button />;
}
```

最初，这种行为只是类和函数组件独有的，但是在这个新版本中，添加了`forwardRef`和`memo`组件，使它们的行为与常规的类和函数组件一致。

```
let Button = forwardRef(() => {
  // We forgot to write return, so this component returns undefined.
  // React 17 surfaces this as an error instead of ignoring it.
  <button />;
});
let Button = memo(() => {
  // We forgot to write return, so this component returns undefined.
  // React 17 surfaces this as an error instead of ignoring it.
  <button />;
});
```

请注意，对于您不想呈现任何内容的情况，将返回`n``u``ll`。

### 取消私人出口

一些 React 内部构件已经暴露给其他不相关的项目，特别是 React Native for web，它过去依赖于事件系统的内部构件，而事件系统非常脆弱，很容易损坏。现在，在这个新的 React 版本中，这些私有导出被删除了，一个不同的解决方案已经为它们就位，使它们不再依赖这些内部机制。

这一变化仅仅意味着旧的 RN for web 版本将不能与 React v17 兼容，但新版本可以。

## 如何升级

我们建议您在测试项目中使用这个版本，因为在不久的将来会有更稳定的版本。您可以在 GitHub 上提出在使用或升级使用 React v17 RC [时可能遇到的任何问题。](https://github.com/facebook/react/issues)

要安装 npm，请运行:

```
npm install [email protected] [email protected]
```

要安装纱线，运行:

```
yarn add [email protected] [email protected]
```

该团队还通过 CDN 构建了 React 的 UMD 版本:

```
<script crossorigin src="https://unpkg.com/[email protected]/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/[email protected]/umd/react-dom.production.min.js"></script>
```

详细的安装说明可以阅读[的文档。](https://reactjs.org/docs/installation.html)

## 结论

至此，我们结束了我们的审查。尽管 React 17 没有提供任何前端新功能，但它通过直接解决升级体验问题为 18 版本开创了先例，并使 React 的行为与现代浏览器更加一致。黑客快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)