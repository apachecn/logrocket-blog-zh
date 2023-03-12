# 探索 React 18 的三个新 API-log rocket 博客

> 原文：<https://blog.logrocket.com/exploring-react-18-three-new-apis/>

React 生态系统中目前最大的话题是 React 18 及其备受期待的并发渲染功能的全面发布。2021 年 6 月，React 团队宣布了 React 18 的计划以及即将到来的事情。几个月后，在 12 月， [React Conf 2021](https://reactjs.org/blog/2021/12/17/react-conf-2021-recap.html) 的主题是所有新宣布的并发渲染特性。

与 React 18 一起，发布了几个新的 API，允许用户充分利用 React 的并发渲染功能。这些挂钩是:

本文将介绍这三个新的 API，它们的用例，它们解决了什么问题，为什么添加它们，以及它们如何集成到并发呈现领域中。

### 开始前请注意

由于所有这些新 API 都与并发渲染相关，我建议您首先熟悉这个概念以及 React 团队如此关注它的原因。一个很好的起点是 React 发布的 [React 18 公告](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html#whats-coming-in-react-18)或[工作组公告](https://github.com/reactwg/react-18/discussions/4)。在那之后，接下来的部分会更有意义。

## `useSyncExternalStore`钩子

React v16.14.0 中为适应并发呈现而引入的 API 之一是`[useMutableSource](https://github.com/reactjs/rfcs/pull/147)`，它旨在允许 React 组件在并发呈现期间安全有效地与外部可变源集成。

挂钩将连接到数据源，等待更改，并相应地安排更新。所有这些都会以防止[撕裂](https://github.com/reactwg/react-18/discussions/69)的方式发生，当视觉不一致出现时，因为同一状态有多个值。

对于新的并发呈现特性来说，这是一个特别突出的问题，因为状态流会很快交织在一起。然而，采用`useMutableSource`被证明是困难的，原因如下:

### 1.钩子自然是异步的

钩子不知道它是否能重用`selector`函数的结果值，如果它改变的话。唯一的解决方案是重新订阅所提供的数据源并再次检索快照，这可能会导致性能问题，因为它会在每次渲染时发生。

对于用户和库(像 [Redux](https://blog.logrocket.com/tag/redux) )来说，这意味着他们必须记住项目中的每一个选择器，并且不能内联定义他们的`selector`函数，因为他们的引用不稳定。

### 2.它必须处理外部状态

最初的实现也有缺陷，因为它必须处理 React 之外的状态。这意味着由于其可变性，状态可能随时改变。

因为 React 试图异步解决这个问题，这有时会导致 UI 的可见部分被替换为回退，从而导致次优的用户体验。

所有这些都使得库维护人员的迁移非常痛苦，对于开发人员和用户来说都是次优体验。

### 用`useSyncExternalStore`解决这些问题

为了解决这些问题，React 团队改变了底层实现，并将钩子重命名为`useSyncExternalStore`，以正确反映它的行为。这些变化包括:

*   不要在每次选择器(用于快照)改变时都重新订阅外部源—相反，React 将比较选择器的结果值，而不是选择器函数，以决定是否再次检索快照，以便用户可以内联定义选择器，而不会对性能产生负面影响
*   每当外部存储改变时，产生的更新现在总是同步的，这防止了用回退替换 UI

唯一的要求是`getSnapshot`钩子参数的结果值需要是引用稳定的。React 在内部使用它来确定是否需要检索新的快照，因此它要么需要是不可变的值，要么需要是内存化/缓存的对象。

为了方便起见，React 将提供一个附加版本的钩子，自动支持对`getSnapshot`的结果值的记忆。

### 如何使用`useSyncExternalStore`

```
// Code illustrating the usage of `useSyncExternalStore`.
// Source: <https://github.com/reactwg/react-18/discussions/86>

import {useSyncExternalStore} from 'react';

// React will also publish a backwards compatible shim
// It will prefer the native API, when available
import {useSyncExternalStore} from 'use-sync-external-store/shim';

// Basic usage. getSnapshot must return a cached/memoized result
const state = useSyncExternalStore(store.subscribe, store.getSnapshot);

// Selecting a specific field using an inline getSnapshot
const selectedField = useSyncExternalStore(store.subscribe, () => store.getSnapshot().selectedField);

// Code illustrating the usage of the memoized version.
// Source: <https://github.com/reactwg/react-18/discussions/86>

// Name of API is not final
import {useSyncExternalStoreWithSelector} from 'use-sync-external-store/with-selector';

const selection = useSyncExternalStoreWithSelector(
  store.subscribe,
  store.getSnapshot,
  getServerSnapshot,
  selector,
  isEqual
);

```

## `useId`钩子

### 在服务器端运行 React

很长一段时间，一个 React 项目只在客户端运行。简而言之，这意味着所有的代码都被发送到用户的浏览器(客户端),然后浏览器负责向用户呈现和显示应用程序。

React 作为一个整体一直在向服务器端呈现(SSR)领域扩展。在 SSR 中，服务器负责根据 React 代码生成 HTML 结构。代替所有的 React 代码，只有 HTML 被发送到浏览器。

然后，浏览器只负责获取该结构，并通过呈现组件、在其上添加 CSS 和附加 JavaScript 来使其具有交互性。这个过程叫做水合作用。

水合最重要的要求是服务器和客户机生成的 HTML 结构必须匹配。如果不这样做，浏览器就不能决定应该如何处理结构的特定部分，这将导致不正确的呈现或者非交互式的用户界面。

这在依赖于标识符的特性中尤其突出，因为它们必须两边匹配，例如在生成唯一的样式类名和可访问性标识符时。

### `useID`钩子的进化

为了解决这个问题，React 最初引入了`useOpaqueIdentifier`钩子，但不幸的是，它也有一些问题:

*   在不同的环境中，挂钩会产生不同的输出(不透明):
    *   服务器端:它会产生一个字符串
    *   客户端:它会产生一个必须直接传递给 DOM 属性的特殊对象

这意味着钩子只能产生一个标识符，而且不可能动态生成新的 id，因为它必须遵守钩子的规则。因此，如果你的组件需要 X 个不同的标识符，它将不得不在不同的时间调用钩子 X，这显然在实践中没有很好的伸缩性。

```
// Code illustrating the way `useOpaqueIdentifier` handles the need for N identifiers in a single component, namely calling the hook N times. 
// Source: <https://github.com/facebook/react/pull/17322#issuecomment-613104823>

function App() {
  const tabIdOne = React.unstable_useOpaqueIdentifier();
  const panelIdOne = React.unstable_useOpaqueIdentifier();
  const tabIdTwo = React.unstable_useOpaqueIdentifier();
  const panelIdTwo = React.unstable_useOpaqueIdentifier();

  return (
    <React.Fragment>
      <Tabs defaultValue="one">
        <div role="tablist">
          <Tab id={tabIdOne} panelId={panelIdOne} value="one">
            One
          </Tab>
          <Tab id={tabIdTwo} panelId={panelIdTwo} value="one">
            One
          </Tab>
        </div>
        <TabPanel id={panelIdOne} tabId={tabIdOne} value="one">
          Content One
        </TabPanel>
        <TabPanel id={panelIdTwo} tabId={tabIdTwo} value="two">
          Content Two
        </TabPanel>
      </Tabs>
    </React.Fragment>
  );
}

```

像`[aria-labelledby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute)`这样的可访问性 API 可以通过空格分隔的列表接受多个标识符，但是因为钩子的输出被格式化为不透明的数据类型，所以它总是必须直接附加到 DOM 属性。这意味着不可能正确地使用前面提到的可访问性 API。

为了解决这个问题，实现被更改并重命名为`useId`。这个新的 Hook API 在 SSR 和水合过程中生成稳定的标识符，以避免不匹配。在服务器呈现的内容之外，它退回到一个全局计数器。

与使用`useOpaqueIdentifier`创建不透明数据类型(服务器中的特殊对象和客户机中的字符串)不同的是，`useId`钩子在两端都产生一个透明的字符串。

这意味着如果我们需要 X 个不同的 id，就没有必要再调用钩子 X 次了。相反，一个组件可以调用`useId`一次，并使用它作为整个组件所必需的标识符的基础(例如，使用后缀),因为它只是一个字符串。这解决了在`useOpaqueIdentifier`中出现的两个问题。

### 如何使用`useID`

下面的代码示例说明了如何根据我们上面讨论的内容使用`useId`。因为 React 生成的 id 是全局唯一的，后缀是局部唯一的，所以动态创建的 id 也是全局唯一的——因此不会导致任何水合不匹配。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
// Code illustrating the improved way in which `useId` handles the need for N identifiers in a single component, namely calling the hook once and creating them dynamically. 
// Source: <https://github.com/reactwg/react-18/discussions/111>

function NameFields() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id + '-firstName'}>First Name</label>
      <div>
        <input id={id + '-firstName'} type="text" />
      </div>
      <label htmlFor={id + '-lastName'}>Last Name</label>
      <div>
        <input id={id + '-lastName'} type="text" />
      </div>
    </div>
  );
}

```

## `useInsertionEffect`钩子

### CSS-in-JS 库的问题

React 18 中添加的最后一个钩子——我们将在这里讨论——是`useInsertionEffect`。这个与其他的略有不同，因为它唯一的目的是对 CSS-in-JS 库很重要，这些库动态地生成新的规则，并用`<style>`标签将它们插入到文档中。

在某些场景中，`<style>`标签需要在客户端生成或编辑，如果不小心的话，这会导致并发渲染的性能问题。这是因为当添加或删除 CSS 规则时，浏览器必须检查这些规则是否适用于现有的树。它必须重新计算所有的样式规则，并重新应用它们，而不仅仅是更改过的规则。如果 React 找到另一个也生成新规则的组件，同样的过程将再次发生。

这实际上意味着在 React 渲染时，必须针对每一帧的所有 DOM 节点重新计算 CSS 规则。虽然您很有可能不会遇到这个问题，但这不是一个可以很好扩展的问题。

从理论上讲，有很多方法可以绕过它，这些方法大多与时机有关。这个时间问题的最佳解决方案是在对 DOM 进行所有其他更改的同时生成这些标记，就像 React 库那样。最重要的是，它应该在任何东西试图访问布局之前发生，也应该在所有东西呈现给浏览器进行绘制之前发生。

这听起来像是`[useLayoutEffect](https://blog.logrocket.com/useeffect-vs-uselayouteffect-examples/)`可以解决的事情，但是问题是相同的钩子将会被用于读取布局和插入样式规则。这可能会导致不希望的行为，例如在一次操作中多次计算布局或读取不正确的布局。

### `useInsertionEffect`如何解决并发渲染问题

为了解决这个问题，React 团队引入了`useInsertionEffect`钩子。它非常类似于`useLayoutEffect`钩子，但是它不能访问 DOM 节点的 refs。

这意味着它只能是插入样式规则。它的主要用例是插入像`<style>`或 SVGs `<defs>`这样的全局 DOM 节点。由于这仅与在客户端生成标签相关，所以钩子不会在服务器上运行。

```
// Code illustrating the way `useInsertionEffect` is used.
// Source: <https://github.com/reactwg/react-18/discussions/110>

function useCSS(rule) {
  useInsertionEffect(() => {
    if (!isInserted.has(rule)) {
      isInserted.add(rule);
      document.head.appendChild(getStyleForRule(rule));
    }
  });
  return rule;
}

function Component() {
  let className = useCSS(rule);
  return <div className={className} />;
}

```

## 最后的想法

React 18 最令人期待的功能是它的并发渲染功能。随着该团队的发布，我们收到了新的 API，它将允许用户基于他们的用例采用并发渲染功能。虽然有些是全新的，但有些是基于社区反馈对以前 API 的改进版本。

在本文中，我们介绍了三个最新的 API，即`useSyncExternalStore`、`useId`和`useInsertionEffect`钩子。我们已经看了它们的用例，它们解决的问题，为什么与它们以前的版本相比某些改变是必要的，以及它们用于并发渲染的目的。

React 18 拥有众多新功能，绝对值得期待！

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