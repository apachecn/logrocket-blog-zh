# 介绍 React 16.8，官方支持 Hooks - LogRocket 博客

> 原文：<https://blog.logrocket.com/introducing-react-16-8-featuring-official-support-for-hooks/>

React.js 是最受欢迎的 JavaScript 库之一，它发布了 16.8.0 版本，其中包括对钩子的官方支持。

[React 现在有超过 100 个版本](https://github.com/facebook/react)，超过 130，000 个 GitHub stars，200 多万个项目，以及许多专门的社区成员，让开发人员构建令人惊叹的用户界面变得更加容易。

## 挂钩的稳定支撑

挂钩是让您从功能组件“挂钩”React 状态和生命周期特性的功能。钩子让你不用写类就可以使用状态和其他 React 特性，因为它们在类内部不起作用。使用状态挂钩的一个简单示例如下:

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

在这篇文章中，钩子将不会被认真对待，相反，你将看到钩子背后的动机，解开钩子的神秘，以及它们的优点。

## 为什么是钩子？

React 的核心团队暗示了人们在用 React 构建应用程序时，随着时间的推移容易面临的一些问题。它们中的大多数乍一看似乎没有联系，但都是有效的:

### 重用有状态逻辑的困难

默认情况下，以前没有明确的方法将可重用行为附加到 React 组件。当然，你有像使用渲染道具和高阶组件这样的模式来尝试和破解它，但是你最终会在任何使用它们的时候改变组件的结构。

这几乎总是导致笨拙的代码表示，并且通过继承，导致代码变得难以辨认。一个典型的 React 应用程序将包含组件的包装器，这些组件包含提供者、消费者、hoc 和许多其他抽象的概念。

DevTools 和林挺可以帮助分解大部分内容，但是 React 需要一种根级别的概念来共享有状态逻辑。钩子非常适合这里，您可以从组件中提取有状态逻辑，这样它就可以被独立测试和重用。你可以在这里了解更多关于[打造自己的钩子的信息。](https://reactjs.org/docs/hooks-custom.html)

### 复杂的组件变得难以理解

一个很好的例子是生命周期方法的使用:你知道逻辑如何从一个方法传递到另一个方法，或者只有计算可以在一个方法中完成，而在另一个方法中使用。

在使用这些方法的过程中，会引入许多不相关的逻辑，从像`console.log`消息这样的小事到像事件监听器这样的大事。这使得下一个开发代码的开发人员——甚至是最初的作者——更有可能在某个时候感到困惑。

想象一下，维护组件开始时很简单，但后来却变成了一堆难以管理的有状态逻辑、副作用，当然还有 bug。在许多情况下，不可能将这些组件分解成更小的组件，因为有状态逻辑无处不在，使得单元测试变得困难。

Hooks 也有助于解决这个问题，因为您可以根据组件之间的关联将一个组件分解成更小的功能。

### 班级有时令人困惑

还记得当你第一次开始使用 React 时，理解它的类是多么的不直观吗？许多刚刚起步的开发人员仍然没有完全掌握`this`在 JavaScript 中是如何工作的；不同的语言有所不同，这并没有什么好处。

其他很好的例子是 React 中函数和类组件的参数之间的差异以及何时使用这些参数，这进一步显示了类有时会有多么令人困惑。

更糟糕的是:即使是机器(即编译器)也发现类在大多数时候令人困惑。以小型打印机为例。React 团队报告说，类不能很好地缩小，有时甚至使热重装变得脆弱和不可靠。Hooks 作为一个解决方案提供了一个平台，让您可以使用 React 特性，但是这次没有类。

## React v16.8 入门

要开始，你必须用你最喜欢的注册表安装新版本 16.8。要安装带纱线的 React 16，运行:

```
yarn add [email protected]^16.8.0 [email protected]^16.8.0
```

要使用 npm 安装 React 16，请运行:

```
npm install --save [email protected]^16.8.0 [email protected]^16.8.0
```

React 的 UMD 版本也可以通过 CDN 访问:

```
<script crossorigin src="https://unpkg.com/[email protected]/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/[email protected]/umd/react-dom.production.min.js"></script>
```

关于详细的安装说明，请参考文件。

既然您已经受到了尝试 React 挂钩的启发，这里有一些资源可以帮助您开始尝试使用它们:

### 需要注意的重要事项

*   钩子还没有覆盖类的所有用例，尽管 React 团队最终会朝着这个方向努力。还没有与`getSnapshotBeforeUpdate`和`componentDidCatch`生命周期方法等价的钩子。
*   像 redux-connect 和 react-router 这样的流行 API 将继续工作，但是对于像`useRedux()`或`useRouter()`这样不需要包装器组件的定制钩子已经有了计划。
*   静态类型与钩子一起工作，因为它们在设计时就考虑到了钩子。

### 林挺钩规则

React 现在有一个 [ESLint 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)，它强制执行挂钩的[规则以避免错误，并让你，开发者，遵循惯例。首先，该插件将任何以“use”开头的函数标记为一个钩子，后面跟着一个大写字母。这些规则是:](https://www.npmjs.com/package/eslint-plugin-react-hooks)

*   不能从常规函数中调用挂钩；仅来自功能组件。
*   钩子只能在函数组件的顶层使用，不能在嵌套函数甚至条件语句块中使用。

流行的 create-react-app 样板文件已经将这个插件集成到所有用它引导的 react 应用程序中。

### 我能马上使用钩子吗？

是啊！从 16.8.0 开始，React 包括 React DOM、React DOM 服务器、React 测试渲染器和 React 浅层渲染器的 React 挂钩的稳定实现。React Native 将在 [0.59 版本](https://github.com/react-native-community/react-native-releases/issues/79#issuecomment-457735214)中支持钩子。

### 测试挂钩

React 团队在这个版本中添加了一个名为`ReactTestUtils.act()`的新 API。它确保了测试中的行为与浏览器中的行为更加匹配。建议将任何代码呈现和触发组件更新打包到`act()`调用中。测试库也可以用它包装他们的 API。

例如，本页中[的反例可以这样测试:](https://reactjs.org/docs/hooks-effect.html)

```
import React from 'react';
import ReactDOM from 'react-dom';
import { act } from 'react-dom/test-utils';
import Counter from './Counter';
let container;
beforeEach(() => {
  container = document.createElement('div');
  document.body.appendChild(container);
});
afterEach(() => {
  document.body.removeChild(container);
  container = null;
});
it('can render and update a counter', () => {
  // Test first render and effect
  act(() => {
    ReactDOM.render(<Counter />, container);
  });
  const button = container.querySelector('button');
  const label = container.querySelector('p');
  expect(label.textContent).toBe('You clicked 0 times');
  expect(document.title).toBe('You clicked 0 times');
  // Test second render and effect
  act(() => {
    button.dispatchEvent(new MouseEvent('click', {bubbles: true}));
  });
  expect(label.textContent).toBe('You clicked 1 times');
  expect(document.title).toBe('You clicked 1 times');
});
```

对`act()`的调用也将刷新它们内部的效果。如果您需要测试一个定制的钩子，您可以通过在您的测试中创建一个组件并使用您的钩子来完成。

此外，为了减少样板代码，推荐使用 [`react-testing-library`](https://git.io/react-testing-library) ，这是为了鼓励编写像最终用户一样使用您的组件的测试。你可以在这里查看这个新版本的[完整变更日志。](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html#changelog)

## 结论

现在已经向您介绍了 React.js 的新版本，它附带了对 React 挂钩的稳定支持。导致采用这种编写 React 代码的新方法的动机也已经被阐明。今天试试 Hooks 吧——祝你黑客生涯愉快！

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

* * *