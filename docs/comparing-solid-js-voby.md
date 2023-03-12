# 比较 SolidJS 和 Voby - LogRocket 博客

> 原文：<https://blog.logrocket.com/comparing-solid-js-voby/>

在前端 JavaScript 框架的世界中，我们不断看到新的创新，这些创新带来了更好的开发体验和更高性能的应用程序。

一方面，有像 Vue、React 和 Angular 这样的框架和库，允许你声明性地定义你的 UI，并通过虚拟 DOM 优化更新，确保只进行必要的更新。另一方面，有像 Svelte 和 SolidJS 这样的框架，它们不再提供框架和运行虚拟 DOM，而是将声明性 UI 编译成标准 JavaScript，从而产生更小的包、更快的速度和使用 observables 的更细粒度的反应。

自从 Vercel 雇佣了苗条的创造者 Rich Harris 全职开发苗条，Netlify 雇佣了 T2 的 SolidJS 创造者 Ryan Carniato 做 SolidJS 同样的事情以来，后一种框架已经获得了巨大的动力。

最近，另一个框架来了，Voby，它受 SolidJS 的许多想法的启发，但有一些不同。Voby 最初的目的是作为构建作者笔记应用程序的框架，[显著](https://github.com/notable/notable)。在本文中，我们将比较 Voby 和 SolidJS，看看 Voby 带来了什么。我们开始吧！

## 反应式用户界面语法

框架与框架之间最显著的区别之一是描述每个 UI 及其反应性的语法。

### SolidJS

SolidJS 使用 JSX 来表达 UI，使用钩子通过定制的 observables 实现来创建反应。相比之下，Svelte 使用 RXJS 进行观察。在 SolidJS 中，一个简单的计数器组件如下所示:

```
import { createSignal } from "solid-js";

function Counter(props) {
  const [count, setCount] = createSignal(0)
  return <div onClick={() => setCount(count() + 1)}>{count()}</div>;
}

```

使用 JSX 确实需要一个构建步骤，SolidJS 在这个构建步骤中进行了许多优化。然而，如果你真的想避免构建，你可以选择使用 [lit-html](https://lit.dev/docs/v1/lit-html/introduction/) 或者 [HyperScript](https://github.com/solidjs/solid/tree/main/packages/solid/h) 模板文字。

此外，您可以看到，在 SolidJS 中，反应性由[信号](https://www.solidjs.com/tutorial/introduction_signals)处理，这些信号是使用 Solid 的自定义可观察实现的可观察值。所有 JSX 表达式都被假定为 SolidJS 中的效果。这些信号可以在效果中使用，因此每当效果中使用的信号更新时，效果将重新运行。或者，在我们的例子中，效果将从 JSX 表达式重建 UI。用于信号的 API 与 React state 非常相似，在 React state 中，既有值，也有值的 setter 函数。你不能直接改变这个值。

### 沃比

Voby 还通过一个名为 [Oby](https://github.com/vobyjs/oby) 的库来使用 observables。Voby 也使用 JSX，但它也可以使用 [HTM](https://github.com/developit/htm) 作为替代，它在一种语法中混合了 JSX、HyperScript 和 lit-html。下面是一个使用 HTML 的简单 Voby 计数器组件的示例:

```
import {html} from 'voby';

const Counter = (): JSX.Element => {
  const value = $(0);
  const increment = () => value ( prev => prev + 1 );
  return html`
      <p onClick=${increment}>${value}</p>
  `;
};

```

Voby 处理反应的方式与 SolidJS 略有不同。使用`$()`功能定义无功值。您得到的不是值和 setter，而是一个既充当 getter 又充当 setter 的函数。当传递一个参数时，它将设置值。在`html`标记的模板文字中，如果在其中使用了一个可观察的值，那么每当值更新时，它就会更新。

## 控制流原语

SolidJS 和 Voby 都没有像 React 那样依赖于`array.map`和 JavaScript 来实现大量的控制流逻辑，而是内置了更易于使用的控制流组件，这意味着您不必担心关键的道具。

### 条件渲染

在 SolidJS 中，您可以使用`Show`组件进行条件渲染:

```
<Show when={state.count > 0} fallback={<div>Loading...</div>}>
  <div>My Content</div>
</Show>

```

如果`when`道具是`true`，`Show`组件会在子表达式中渲染 UI。如果没有，它将渲染`fallback`属性中的值。

另一方面，Voby 有一个`If`组件:

```
<If when={visible}>
   <p>Hello!</p>
</If>

```

`If`组件的工作方式非常类似于 SolidJS `Show`组件，如果`When`属性为`true`，则在子表达式中呈现 UI。

### 遍历列表

要在 React 中遍历数据数组，我们必须依赖于`array.map`方法，并确保传递一个惟一的 key prop 来允许虚拟 DOM 优化更新。在 SolidJS 和 Voby 中，我们不必担心关键道具或使用`map`。

SolidJS 有`For`组件，它将数组作为`each`道具:

```
<For each={state.list} fallback={<div>Loading...</div>}>
  {(item) => <div>{item}</div>}
</For>

```

如果数据还不可用，您可以传递一个`fallback`表达式。

Voby 还有一个`For`的成分。它基本上与 SolidJS 中的`For`组件工作相同，但是它使用`value`道具来定义要循环的数组，而不是`Each`道具:

```
<For values={numbers}>
      {( value ) => {
        return <p>Value: {value}</p>
      }}
</For>

```

### 开关

SolidJS `Switch`组件将遍历每个嵌套的`Match`组件，并使用`true`的`when`道具呈现第一个组件。如果没有`Match`被渲染，那么`Switch`上的`fallback`道具被渲染:

```
<Switch fallback={<div>Not Found</div>}>
  <Match when={state.route === "home"}>
    <Home />
  </Match>
  <Match when={state.route === "settings"}>
    <Settings />
  </Match>
</Switch>

```

Voby 使用`Switch`和`Switch.case`:

```
<Switch when={value}>
        <Switch.Case when={0}>
          <p>0, the boundary between positives and negatives! (?)</p>
        </Switch.Case>
        <Switch.Case when={1}>
          <p>1, the multiplicative identity!</p>
        </Switch.Case>
        <Switch.Default>
          <p>{value}, I don't have anything interesting to say about that :(</p>
        </Switch.Default>
</Switch>

```

Voby `Switch`的工作方式更像传统的 JavaScript switch 语句，即指定一个值，并针对一系列不同的情况进行测试，然后运行匹配情况下的代码。在这种情况下，值在`Switch`中的`when`属性中指定，案例在每个`Switch.Case`的`when`属性中。

## 结论

尽管 Voby 执行了 SolidJS 的许多思想和原则，但它仍处于早期阶段。因此，它不支持服务器端渲染或生产就绪 SolidJS 中可用的其他功能。然而，看到这个著名的应用程序运行，让我对未来感到乐观。

Voby 在创建高性能和反应式应用程序方面做得很好，所以它绝对是前端框架领域值得关注的东西。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。