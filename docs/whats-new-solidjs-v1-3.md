# SolidJS v1.3 有什么新功能？- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-solidjs-v1-3/>

## 介绍

2022 年 1 月，SolidJS 团队[宣布发布 Solid v1.3](https://twitter.com/solid_js/status/1479155108650700806) ，标题为“Spice must flow”。这个新版本是一个大版本，因为它带来了许多新特性，最显著的是对服务器端呈现(SSR)的全面改进。

根据作者的说法，这个版本中的一些特性是试验性的，其他一些特性已经被弃用，以方便将来的版本。这很重要，因为随着 Solid 及其社区的不断发展，随着时间的推移会有更多的改进，为将来的发布打下基础是关键。

在本文中，我们将探索 Solid v1.3 版本的细节，包括新的特性发布、改进和错误修复。

## 新功能发布

没有一些新功能的新版本是什么？Solid v1.3 提供了新的特性来增加其功能，并改善开发人员和最终用户的体验。我们将在本节中讨论其中的一些。

### HTML 流

Solid v1.3 增加了对 HTML 流的支持，这极大地缩短了 Solid 应用程序的启动加载时间。对于有缓存结果的应用程序，或者当用户遇到互联网连接缓慢时，他们不再需要在等待应用程序完成加载时看到占位符内容；一旦 HTML 可用，它就被流式传输并插入浏览器。

这个特性与`renderToStream` API 一起提供，它被设计用来处理 Node.js 和 web 可写流。节点和 web 平台也分别有`pipe`和`pipeTo`API，让用户选择何时在流中插入内容(立即、`onCompleteAll`或`onCompleteShell`)。

根据[实体文档](https://www.solidjs.com/docs/latest/api#rendertostream):

> `onCompleteShell`在将第一次刷新写入到浏览器的流之前，当同步呈现完成时触发。当所有服务器暂停边界都已确定时，调用`onCompleteAll`。

```
// node
const stream = renderToStream(() => <App />).pipe(res);

// web
const stream = renderToStream(() => <App />).pipeTo(writable);

```

### 服务器上的错误边界

Solid v1.3 支持同步渲染的错误处理和资源解析中出现的错误。此功能适用于所有渲染方法，`renderStream`、`renderToString`和`renderToStringAsync`。这个特性有助于开发人员体验的整体感觉，这是在开发人员社区中增加采用的最佳方式之一。

### `createReaction`活性基元

`createReaction`原语有助于将跟踪与重新执行分开。这个原语注册一个副作用，该副作用在表达式(由返回的跟踪函数包装)第一次被通知有变化时运行。必须再次调用`track`函数进行跟踪。

考虑这段代码:

```
const [s, set] = createSignal("start");

const track = createReaction(() => console.log("something"));

// next time s changes run the reaction
track(() => s());

set("end"); // "something"

set("final"); //no-op as reaction only runs on first update, need to call track again

```

### 外部来源

虽然这是实验性的，但这是一个非常有趣的特性，因为它允许您在 Solid 中使用第三方反应库。是的，你没听错:你可以在自己的应用程序中使用自己选择的第三方反应库，比如 [Vuex](https://blog.logrocket.com/do-you-really-need-vuex/) 、 [MobX](https://blog.logrocket.com/whats-new-in-mobx-6-0/) 或 Kairo。

考虑下面的代码块:

```
import { Reaction, makeAutoObservable } from "mobx";
import { enableExternalSource } from "solid-js";
import { render } from "solid-js/web";

let id = 0;
enableExternalSource((fn, trigger) => {
  const reaction = new Reaction(`[email protected]${++id}`, trigger);
  return {
    track: x => {
      let next;
      reaction.track(() => (next = fn(x)));
      return next;
    },
    dispose: () => {
      reaction.dispose();
    }
  };
});

class Timer {
  secondsPassed = 0;

  constructor() {
    makeAutoObservable(this);
  }

  increase() {
    this.secondsPassed += 1;
  }

  reset() {
    this.secondsPassed = 0;
  }
}

// component driven directly off MobX
function App() {
  const timer = new Timer();
  setInterval(() => {
    timer.increase();
  }, 1000);

  return <button onClick={() => timer.reset()}>Seconds passed: {timer.secondsPassed}</button>;
}

render(() => <App />, document.getElementById("app"));

```

首先，您必须从您选择使用的任何第三方库中导入您需要的特性。在这种情况下，我们使用的是 MobX 库，我们从其中导入了`action`和`makeAutoObservable`特性。

接下来，我们将从 Solid core 库中导入`enableExternalSource`函数。然后，我们用它的参数声明函数，并像平常一样使用 MobX 库。

## 功能改进

在最近的 Solid 版本中，现有的特性改进包括以下内容(都是为了改善已经很棒的开发人员体验):

### 更好的类型脚本支持

Solid v1.3 提供了更好的类型。

### 更好的源地图

当您为生产而构建时，在缩小和组合 JavaScript 文件的同时，您会生成一个源映射来保存关于原始文件的信息。源代码映射是一种保持客户端代码可读性和可调试性的方法，即使在缩小所有源代码和资源之后也是如此。更好的源地图意味着更好的开发者体验。

## 错误修复

除了新特性的增加和改进，Solid 的这个新版本没有遗漏错误修复。随着 1.3 版的发布，以下错误已被消除:

*   浏览器扩展修改头部元素，打破水合作用
*   从文件中重新插入关于水合的`<html>`
*   使用`createSelector`在多选上过度执行
*   事件委托与文档事件侦听器冲突
*   自有源无限递归
*   仅客户端渲染中用于水合的错误树分裂
*   惰性组件上的返回类型`preload`总是一个承诺
*   生成 SSR 时，在开始标记后出现前导空格的编译错误

## 结论

在本文中，我们回顾了 Solid v1.3 版本中实现的新特性、现有特性改进和错误修复。

Solid 通过频繁的更新和发布不断改进其特性、速度和开发人员体验。由于特性和功能与竞争对手不相上下，可以肯定地说，阻止 Solid 大规模采用的唯一因素是其社区的规模，它也在以稳定的速度增长。

如需进一步阅读，您可以浏览 GitHub 上的[发行说明。](https://github.com/solidjs/solid/releases/tag/v1.3.0?ck_subscriber_id=1285109397)

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。