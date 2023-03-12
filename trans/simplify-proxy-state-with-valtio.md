# 使用 Valtio - LogRocket 博客简化代理状态

> 原文：<https://blog.logrocket.com/simplify-proxy-state-with-valtio/>

React 中的状态管理是开发人员有时会忽略的一个问题。总会有一些新的库，为您的应用程序选择正确的库可能是一项相当困难的工作。

对于每一个现代应用程序来说，拥有一个状态管理库是至关重要的，在选择库的时候有很多需要考虑的地方。

我们将使用一个名为 [Valtio](https://github.com/pmndrs/valtio) 的新状态管理库，这个库使得 JavaScript 和 React 应用程序的代理状态变得简单。

## 什么是代理？

代理是一种计算机软件模式。代理是一些可以拥有自定义行为的对象的包装。我们几乎可以代理任何东西，比如网络连接、对象、文件等等。代理的工作方式不同，但在结构上类似于[适配器](https://en.wikipedia.org/wiki/Adapter_pattern)和[装饰器](https://en.wikipedia.org/wiki/Decorator_pattern)。

> 代理是一个包装对象，客户端调用它来访问幕后的真实服务对象

代理是 [GoF 设计模式](https://en.wikipedia.org/wiki/Design_Patterns)的一部分，这本书是由[埃里希·伽马](https://en.wikipedia.org/wiki/Erich_Gamma)、[理查德·赫尔姆](https://en.wikipedia.org/?title=Richard_Helm&redirect=no)、[拉尔夫·约翰逊](https://en.wikipedia.org/wiki/Ralph_Johnson_%28computer_scientist%29)和[约翰·维里西德斯](https://en.wikipedia.org/wiki/John_Vlissides)撰写的。作者探索了面向对象编程和 23 种软件设计模式的能力。

代理可以帮助开发人员解决现代应用程序中反复出现的问题。代理更容易，因为它们帮助我们处理对象，它们对于验证、跟踪属性访问、web 服务、监控对象等情况非常有用。它们是更容易放置、更改、测试和重用的对象。

代理有两条规则:

*   应该控制对对象的访问
*   当访问一个对象时，应该提供额外的功能

从 ES6 版本开始，我们在 JavaScript 中提供了[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)。一个[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)接收两个参数:

*   `target`–您想要代理的原始对象
*   `handler`–将定义操作的对象

这就是我们如何使用 JavaScript 创建一个简单的[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy):

```
const target = {
  addition: () => 2 + 2,
  subtraction: () => 2 - 2,
};

const handler = {
  get: function(target, prop, receiver) {
    return target;
  },
};

const proxy = new Proxy(target, handler);

console.log(proxy.addition()); // 4
console.log(proxy.subtraction()); // 0

```

代理是一种非常强大的设计模式，用于观察对象并对其进行更改。它允许我们为对象设计自定义行为。

想象一下，如果我们可以在 React 应用程序中做到这一点。利用代理来处理我们的状态数据。我们不再需要建立庞大的状态管理库来处理我们所有的状态。有了瓦提奥，我们可以！

## 州政府

Valtio 是一个为 React 和 JavaScript 应用程序简化代理状态的库。

它是由一个名为 [Poimandres](https://github.com/pmndrs) 的开源团体创建的。这个开源集体负责 React 社区中的其他重要库，比如 [react-spring](https://github.com/pmndrs/react-spring) 、 [zustand](https://github.com/pmndrs/zustand) 、 [react-three-fiber](https://github.com/pmndrs/react-three-fiber) 、[React-use-手势](https://github.com/pmndrs/react-use-gesture)。

我们需要做的是包装我们的状态对象，然后我们可以在应用程序中的任何地方改变它:

```
import { proxy } from 'valtio'

const state = proxy({ count: 0 });
() => { ++state.count };

```

[Valtio](https://github.com/pmndrs/valtio) 有一个名为`useProxy`的钩子帮助我们从快照中读取数据。只有当组件正在访问的状态部分发生变化时，`useProxy`钩子才会重新呈现我们的组件:

```
const Counter = () => {
  const snapshot = useProxy(state);
  return (
    <div>
      {snapshot.count}
      <button onClick={() => ++state.count}>Add</button>
    </div>
  );
};

```

[Valtio](https://github.com/pmndrs/valtio) 有一个非常强大的功能叫做`subscribe`。我们可以从任何地方订阅我们的状态，并在组件中使用它。

想象一下，我们有一个非常复杂的代理状态，有一堆不同的状态。在我们的代理状态中，我们有一个身份验证状态，我们用它来知道用户何时通过身份验证:

```
import { proxy } from 'valtio'

const state = proxy({
  authenticated: false,
  settings: { ... },
  filters: { ... },
  ...
});

```

我们可以使用`subscribe`函数订阅我们状态的特定部分。`subscribe`函数接受两个参数，状态和回调。`state`是我们想要订阅的州的哪一部分。`callback`是一个回调函数，当状态改变时会被触发；

```
subscribe(state.authenticated, () => console.log('State changed to', state.authenticated));

```

[瓦尔蒂奥](https://github.com/pmndrs/valtio)也有一个叫做`subscribeKey`的函数，类似于`subscribe`函数。`subscribeKey`将订阅一个状态代理的原始属性。只有当指定的属性发生变化时，它才会被触发:

```
subscribeKey(state.authenticated, () => console.log('Authenticated state changed to', state.authenticated));

```

虽然现在普通 JavaScript 可能不太常见，但是 [Valtio](https://github.com/pmndrs/valtio) 的另一个特性是它可以用在普通 JavaScript 应用程序中:

```
import { proxy, subscribe, snapshot } from 'valtio/vanilla'

const state = proxy({ books: [...], isAuthenticated: false })

subscribe(state, () => {
  console.log('state:')
  const obj = snapshot(state);
})

```

现在我们对 [Valtio](https://github.com/pmndrs/valtio) 有了一点了解，让我们看看它在实践中是如何工作的。我们将使用 [Valtio](https://github.com/pmndrs/valtio) 为 React 中的状态代理创建一个简单的示例应用程序，并看看它的好处。

## 入门指南

我们将使用 [Create React App](https://create-react-app.dev/docs/getting-started/) 创建一个新的应用程序:

```
npx create-react-app simple-state-with-valtio

```

现在我们将安装 [Valtio](https://github.com/pmndrs/valtio) :

```
yarn add valtio

```

首先，我们将从 Valtio 导入函数`proxy`和`useProxy`。`proxy`函数用于创建新的代理状态。`useProxy`函数用于在我们的 React 组件上创建一个本地快照，以监视更改:

```
import { proxy, useProxy } from 'valtio'

```

现在，我们将创建我们的代理状态。我们的状态中有三个属性`firstName`、`lastName`和`users`:

```
const state = proxy({ firstName: "", lastName: "", users: [{}] })

```

在我们的组件中，我们将有一个表单、两个输入和一个按钮。我们将跟踪每个输入的变化，并将其存储在我们的状态代理中。该按钮将用于提交我们的表单:

```
const App = () => {
  return (
    <form>
      <input type="text" />
      <input type="text" />
      <button type="submit">Create</button>
    </form>
    );
}

```

在我们的组件内部，我们将使用`useProxy`钩子创建一个快照。该快照将用于从代理状态获取状态数据，并在状态发生变化时更新数据:

```
const App = () => {
  const snapshot = useProxy(state, { sync: true });
  return (
    <form>
      <input type="text" />
      <input type="text" />
      <button type="submit">Create</button>
    </form>
  );
}

```

注意，我们向`useProxy`函数传递了第二个参数。第二个参数是一个对象，我们告诉 Valtio 在触发 rerender 之前进行批处理。

现在，在我们的输入中，对于每个输入，我们将把值设置为我们的代理状态，并从我们的`snapshot`中读取值，就像这样:

```
const App = () => {
  const snapshot = useProxy(state, { sync: true });
return (
    <form>
      <input type="text" value={snapshot.firstName} onChange={(e) => (state.firstName = e.target.value)} />
      <input type="text" value={snapshot.lastName} onChange={(e) => (state.lastName = e.target.value)} />
      <button type="submit">Create</button>
    </form>
  );
}

```

现在，我们需要创建提交函数来提交表单。在我们的函数中，我们将创建一个新的用户分组`firstName`和`lastName`值，并将其推送到`users`数组。在新用户被推送到`users`数组后，我们希望将`firstName`和`lastName`的值都改为一个空字符串:

```
const App = () => {
  const snapshot = useProxy(state, { sync: true });
  const handleSubmit = (e: any) => {
    e.preventDefault();
    const newUser = { firstName: state.firstName, lastName: state.lastName };
    state.users.push(newUser);
    state.firstName = "";
    state.lastName = "";
  }
  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={snapshot.firstName} onChange={(e) => (state.firstName = e.target.value)} />
      <input type="text" value={snapshot.lastName} onChange={(e) => (state.lastName = e.target.value)} />
      <button type="submit">Create</button>
    </form>
  );
}

```

现在，我们的组件工作得非常好。我们能够使用 Valtio 管理我们的状态值，并向我们的代理状态提交新用户。现在唯一缺少的是显示用户数量的方法。

我们将再次使用`snapshot`并映射到我们的用户数组，对于每个用户，我们将显示一个`h1`元素:

```
const App = () => {
  const snapshot = useProxy(state, { sync: true });
const handleSubmit = (e: any) => {
    e.preventDefault();
    const newUser = { firstName: state.firstName, lastName: state.lastName };
    state.users.push(newUser);
    state.firstName = "";
    state.lastName = "";
  }
  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={snapshot.firstName} onChange={(e) => (state.firstName = e.target.value)} />
      <input type="text" value={snapshot.lastName} onChange={(e) => (state.lastName = e.target.value)} />
      <button type="submit">Create</button>

      <div>
        {snapshot.users.map((user: any) => (<h1>Hello {user.firstName} {user.lastName}</h1>))}
      </div>
    </form>
  );
}

```

Valtio 背后的[集体](https://github.com/pmndrs)在 React 社区中非常重要且受到尊重。他们也是重要项目的作者，如 [zustand](https://github.com/pmndrs/zustand) 、 [react-spring](https://github.com/pmndrs/react-spring) 、 [react-three-fiber](https://github.com/pmndrs/react-three-fiber) 等。

随着一群 T2 人和开发者愿意为这个项目做贡献，Valtio 图书馆的未来是非常光明的。

## 结论

状态数据对于现代应用程序来说是必不可少的。Valtio 简单、强大，结合了 React 和 JavaScript 中代理的力量，使得状态数据易于使用和更改。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)