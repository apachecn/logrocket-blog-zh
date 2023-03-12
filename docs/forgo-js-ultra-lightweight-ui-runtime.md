# 介绍 ForgoJS，一个超轻量 UI 运行时日志博客

> 原文：<https://blog.logrocket.com/forgo-js-ultra-lightweight-ui-runtime/>

## 介绍

Forgo 是一个用于创建 web 应用程序的轻量级(4KB) JavaScript 库。尽管它被宣传为 React 的替代方案，甚至使用了 JSX 语法，但在使用 Forgo 时，有几个主要的区别需要记住。

在本文中，我们将看看如何使用 [Forgo 库](https://forgojs.org/)创建客户端 web 应用程序，同时，我们将看看 Forgo 和 React 之间的区别。

## 用 Forgo 创建演示组件

首先，我们应该通过运行以下命令来全局安装 webpack 和 webpack-cli:

```
npm i -g webpack webpack-cli

```

然后，我们使用以下命令将 JavaScript repo 克隆到我们的计算机中:

```
npx degit forgojs/forgo-template-javascript#main my-project

```

一旦我们完成了，我们应该跳转到我们的`my-project`文件夹并运行`npm i`来安装`package.json`中列出的包。`npm start`将运行我们刚刚克隆的项目。

默认情况下，项目在端口 8080 上运行。我们可以通过添加`-- --port=8888`选项来更改运行项目的端口，如下所示:

```
npm start -- --port=8888
```

我们克隆的项目带有 JSX 解析器和其他构建工具，所以我们不必担心项目的工具。

现在我们可以通过在`index.js`中编写以下代码来创建一个简单的组件:

```
import { mount, rerender } from "forgo";

function Timer() {
  let seconds = 0;

  return {
    render(props, args) {
      setTimeout(() => {
        seconds++;
        rerender(args.element);
      }, 1000);

      return <div>{seconds}</div>;
    },
  };
}

```

我们用`args.element`调用`rerender`来重新渲染一个组件。与 React 不同，状态存储在常规变量中，而不是状态中，我们在`return`语句中呈现`seconds`。

### 安装组件

要安装组件，我们需要添加:

```
window.addEventListener("load", () => {
  mount(<Timer />, document.getElementById("root"));
});

```

因此，加上我们刚刚写给`index.js`的样本组件，我们有:

```
import { mount, rerender } from "forgo";

function Timer() {
  let seconds = 0;

  return {
    render(props, args) {
      setTimeout(() => {
        seconds++;
        rerender(args.element);
      }, 1000);

      return <div>{seconds}</div>;
    },
  };
}

window.addEventListener("load", () => {
  mount(<Timer />, document.getElementById("root"));
});

```

我们可以用元素选择器替换`document.getElementById`，这样我们可以写:

```
window.addEventListener("load", () => {
  mount(<Timer />, "#root");
});

```

## 子组件和道具

与 React 一样，我们可以创建子组件并向它们传递道具。为此，我们写道:

```
import { mount } from "forgo";

function Parent() {
  return {
    render() {
      return (
        <div>
          <Greeter name="james" />
          <Greeter name="mary" />
        </div>
      );
    },
  };
}

function Greeter({ name }) {
  return {
    render(props, args) {
      return <div>hi {name}</div>;
    },
  };
}

window.addEventListener("load", () => {
  mount(<Parent />, document.getElementById("root"));
});

```

`Greeter`组件是`Parent`组件的子组件。`Greeter`接受`name`道具，我们在`return`语句中添加该道具来呈现其值。

我们也可以很容易地用 Forgo 添加输入。为此，我们应该写:

```
import { mount } from "forgo";

function Input() {
  const inputRef = {};

  return {
    render() {
      function onClick() {
        const inputElement = inputRef.value;
        alert(inputElement.value);
      }

      return (
        <div>
          <input type="text" ref={inputRef} />
          <button onclick={onClick}>get value</button>
        </div>
      );
    },
  };
}

window.addEventListener("load", () => {
  mount(<Input />, document.getElementById("root"));
});

```

我们创建一个名为`inputRef`的对象变量，并将其传递给 input 元素的`ref`属性。然后，我们获得具有`inputRef.value`属性的输入元素对象，就像我们在`onClick`方法中所做的那样。现在我们可以通过获取`value`属性来获取输入值。

## 列表和键

与 React 类似，我们可以通过调用数组上的`map`来呈现列表，然后返回我们想要呈现的组件。例如，我们可以写:

```
import { mount } from "forgo";

function App() {
  return {
    render() {
      const people = [
        { name: "james", id: 1 },
        { name: "mary", id: 2 },
      ];
      return (
        <div>
          {people.map(({ key, name }) => (
            <Child key={key} name={name} /&gt;
          ))}
        </div>
      );
    },
  };
}

function Child() {
  return {
    render({ name }) {
      return <div>hi {name}</div>;
    },
  };
}

window.addEventListener("load", () => {
  mount(<App />, document.getElementById("root"));
});

```

我们有一个带有数组`people`的`App`组件。我们可以通过调用`map`，然后返回带有`key`和`name`道具的`Child`组件，在浏览器中呈现数组。

属性允许 Forgo 通过分配一个唯一的 ID 来区分渲染的项目。`name`是我们在`Child`组件中得到并渲染的常规道具。

## 在 Forgo 中提取数据

我们可以通过承诺获取数据，就像我们对 React 组件所做的那样。然而，我们只能使用`then`方法来获取数据——没有 async/await。如果我们使用`async`和`await`，我们会得到一个`regeneratorRuntime not defined`错误。

因此，为了在组件挂载时获取数据，我们可以编写如下代码:

```
import { mount, rerender } from "forgo";

function App() {
  let data;

  return {
    render(_, args) {
      if (!data) {
        fetch('https://yesno.wtf/api')
          .then(res => res.json())
          .then(d => {
            data = d;
            rerender(args.element);
          })
        return <p>loading</p>
      }
      return <div>{JSON.stringify(data)}</div>
    },
  };
}

window.addEventListener("load", () => {
  mount(<App />, document.getElementById("root"));
});

```

`data`最初没有值，在这种情况下，我们调用`fetch`对一些数据发出 GET 请求。然后，我们将`d`参数赋给`data`，它有响应数据。

我们用`args.element`调用`rerender`在`data`更新后重新渲染组件，在下一个渲染周期，渲染字符串化的`data`值。现在我们应该能够看到从 API 中检索到的数据。

## 事件和错误

Forgo 组件在其呈现生命周期中发出各种事件。让我们以下面的组件为例:

```
import { mount } from "forgo";

function App() {
  return {
    render(props, args) {
      return <div id='hello'>Hello</div>;
    },
    mount(props, args) {
      console.log(`mounted on node with id ${args.element.node.id}`);
    },
    unmount(props, args) {
      console.log("unmounted");
    },
  };
}

window.addEventListener("load", () => {
  mount(<App />, document.getElementById("root"));
});

```

我们的`render`方法返回一个 ID 为`hello`的 div，当组件挂载时，我们调用`mount`方法。`args.element.node.id`接受根元素的`id`属性的值，所以它的值应该是`'hello'`。

因此，我们应该看到`'mounted on node with id hello'`被记录。`unmount`方法在组件卸载时运行，所以当组件从 DOM 中移除时，我们应该会看到`'unmounted'`。

例如，假设我们有:

```
import { mount, rerender } from "forgo";

function App() {
  let showChild = false

  return {
    render(props, args) {
      const onClick = () => {
        showChild = !showChild
        rerender(args.element)
      }

      return (
        <div>
          <button onclick={onClick}>toggle</button>
          {showChild ? <Child /> : undefined}
        </div>
      );
    },
  };
}

function Child() {
  return {
    render(props, args) {
      return <div>child</div>;
    },
    unmount(props, args) {
      console.log("unmounted");
    },
  };
}

window.addEventListener("load", () => {
  mount(<App />, document.getElementById("root"));
});

```

当我们点击**切换**按钮时，`Child`组件会消失，我们应该会看到`'unmounted'`。

我们还可以通过使用`shouldUpdate`函数手动调用`rerender`来选择何时重新渲染组件。`shouldUpdate`有参数`newProps`和`oldProps`，顾名思义，它们分别给出了道具的当前值和旧值。

考虑下面的例子:

```
import { mount, rerender } from "forgo";

function App() {
  let name = 'james'

  return {
    render(props, args) {
      const onClick = () => {
        name = name === 'james' ? 'jane' : 'james'
        rerender(args.element)
      }

      return (
        <div>
          <button onclick={onClick}>toggle</button>
          <Greeter name={name} />
        </div>
      );
    },
  };
}

function Greeter() {
  return {
    render({ name }, args) {
      return <div>hi {name}</div>;
    },
    shouldUpdate(newProps, oldProps) {
      console.log(newProps, oldProps)
      return newProps.name !== oldProps.name;
    },
  };
}

window.addEventListener("load", () => {
  mount(<App />, document.getElementById("root"));
});

```

**切换**按钮切换`name`的值，我们将它作为`Greeter`的`name`道具的值传入。因此，每当我们点击**切换**按钮，我们将看到控制台日志更新。

最后，我们可以用`error`方法处理返回的对象中的错误。子组件的错误会上升到父组件。

所以，如果我们有:

```
import { mount } from "forgo";

function App() {
  return {
    render() {
      return (
        <div>
          <BadComponent />
        </div>
      );
    },
    error(props, args) {
      return (
        <p>
          {args.error.message}
        </p>
      );
    },
  };
}

function BadComponent() {
  return {
    render() {
      throw new Error("error");
    },
  };
}
window.addEventListener("load", () => {
  mount(<App />, document.getElementById("root"));
});

```

`args.error.message`应该有我们传递给`Error`构造函数的字符串，因为错误会从子组件传播到父组件。

## 结论

Forgo 只有 4KB，是 React 的一个很小的替代品，它允许我们轻松地创建简单的组件。因为它使用 JSX 语法，所以 React 开发人员应该对它很熟悉。

然而，在行为上有一些主要的不同。必须用`rerender`方法手动重新渲染。[组件生命周期](https://blog.logrocket.com/react-lifecycle-methods-tutorial-examples/)也大不相同，状态存储为普通变量而不是状态。也没有挂钩；相反，我们使用生命周期方法在组件的生命周期中执行各种操作。

开始 Forgo yourself 的最简单方法是全局安装 webpack dev 依赖项，然后用`degit`克隆 starter 项目。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。