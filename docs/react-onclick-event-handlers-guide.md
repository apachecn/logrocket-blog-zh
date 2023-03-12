# React onClick 事件处理程序:完整指南

> 原文：<https://blog.logrocket.com/react-onclick-event-handlers-guide/>

***编者按:**这篇文章于 2022 年 7 月 8 日更新，以确保关于 React 18 的一切都是最新的，并在 React with TypeScript 中添加了一个关于[类型检查的部分。](#type-checking-react-typescript)*

在本教程中，我们将回顾 React 的`onClick`事件处理程序的基础，包括事件监听、处理自定义事件以及使用 TypeScript 对事件处理程序进行类型检查。

我们将涵盖的内容:

## React 中的事件处理程序是什么？

事件处理程序决定每当触发事件时要采取的操作。这可能是按钮点击或文本输入的改变。

本质上，事件处理程序使用户能够与 React 应用程序进行交互。用 React 元素处理事件类似于在 DOM 元素上处理事件，除了一些小的例外。

如果你熟悉标准 HTML 和 JavaScript 中事件的工作方式，那么学习如何在 React 中处理事件应该很容易。

## React 中的`onClick`处理程序是什么？

React `onClick`事件处理程序使您能够调用一个函数，并在用户单击应用程序中的一个元素(如按钮)时触发一个动作。

事件名称是在 camelCase 中编写的，所以在 React 应用程序中，`onclick`事件被写成`onClick`。另外， [React 事件处理程序](https://reactjs.org/docs/handling-events.html)出现在花括号内。

以下面这个用 HTML 编写的简单例子为例:

```
<button onclick="sayHello()">
  Say Hello
<button>

```

在 React 应用程序中，这个`button onClick`事件将被写成如下形式:

```
<button onClick={sayHello}>
  Say Hello
<button>

```

另一个关键区别是，在 React 中您必须显式调用`preventDefault`，而在 HTML 中，您可以简单地返回`false`以避免默认行为。

以下示例显示了如何在默认情况下阻止链接打开新页面:

```
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>

```

您可以在 React 中编写如下内容:

```
function ActionLink() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}

```

## React 中的合成事件是什么？

[React 实现了一个合成事件系统](https://blog.logrocket.com/getting-started-react-synthetic-event/)，为 React 应用和界面带来了一致性和高性能。它通过规范化事件来实现一致性，使它们在不同的浏览器和平台上具有相同的属性。

合成事件是围绕浏览器本机事件的跨浏览器包装。它与浏览器的本地事件具有相同的接口，包括`stopPropagation()`和`preventDefault()`，除了这些事件在所有浏览器中的工作方式相同。

它通过自动使用事件委托来实现高性能。实际上，React 不会将事件处理程序附加到节点本身。相反，单个事件监听程序附加到文档的根。触发事件时，“反应”会将其映射到适当的组件元素。

## 反应事件侦听器

为了让[监听 React](https://www.pluralsight.com/guides/event-listeners-in-react-components) 中的事件，向目标元素添加`onClick`属性——它是事件处理程序。这指定了单击该元素时要执行的功能，如下所示:

```
import React from "react";

const ShowAlertComponent = () => {
  const showAlert = () => {
    alert("I'm an alert");
  }

  return <button onClick={showAlert}>Show alert</button>;
}
export default ShowAlertComponent;

```

在上面的例子中，`onClick`属性被设置为作为事件目标的`showAlert`函数，当单击按钮时，它显示警告消息“我是一个警告”。

## 处理 React 组件中的事件

有几种方法可以处理功能性 React 组件中的事件。我们将在这里讨论其中的五个。

### 在`onClick`事件处理程序中调用内联函数

内联函数允许您直接在 JSX 中编写事件处理代码。请参见下面的示例:

```
import React from "react";

const App = () => {
  return (
    <button onClick={() => alert("Hello!")}>Say Hello</button>
  );
};

export default App;

```

这通常用于避免 JSX 之外的额外函数声明，尽管如果内联函数的内容太多，可读性会更差，也更难维护。

### 更新`onClick`事件处理程序中的状态

假设您的 React 应用程序要求您在一个`onClick`事件处理程序中更新本地状态。下面是如何做到这一点:

```
import React, { useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
    </div>
  );
};

export default App;

```

在上面的例子中，`useState`的值被`Increment`和`Decrement`按钮修改，这两个按钮在`onClick`事件处理程序中有一个更新函数`setCount`。

### 在一个`onClick`事件处理程序中调用多个函数

`onClick`事件处理程序也允许你调用多个函数。

```
import React, { useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  const sayHello = () => {
    alert("Hello!");
  };

  return (
    <div>
      <p>{count}</p>
      <button
        onClick={() => {
          sayHello();
          setCount(count + 1);
        }}
      >
        Say Hello and Increment
      </button>
    </div>
  );
};

export default App;

```

在上面的代码块中，单击按钮会增加本地状态并发出一条消息。这两个动作都由`onClick`事件处理程序中的独立函数执行。

### 将参数传递给`onClick`事件处理程序

事件处理程序的另一个常见用例是将参数传递给函数，以便以后使用。例如:

```
import React from "react";

const App = () => {
  const sayHello = (name) => {
    alert(`Hello, ${name}!`);
  };

  return (
    <button
      onClick={() => {
        sayHello("Yomi");
      }}
    >
      Say Hello
    </button>
  );
};

export default App;

```

这里，`sayHello`函数接受一个名称作为参数，然后用它来定制警告消息。在这种情况下，该函数接受“金尤美”这个名称，这样在单击时，按钮显示“你好，金尤美！”作为它的信息。

### 直接在`onClick`事件处理程序中使用合成事件

您也可以在`onClick`事件处理程序中直接使用合成事件。在下面的例子中，按钮的值是通过`e.target.value`获得的，然后用来警告一条消息。

```
import React from "react";

const App = () => {
  return (
    <button value="Hello!" onClick={(e) => alert(e.target.value)}>
      Say Hello
    </button>
  );
};

export default App;

```

## React 中的自定义组件和事件

当谈到 React 中的事件时，只允许 DOM 元素拥有事件处理程序。以带有`onClick`事件的组件`CustomButton`为例。由于上述原因，此按钮不会响应点击。

那么我们如何在 React 中为[定制组件处理事件呢？](https://blog.logrocket.com/build-react-custom-component-library-theme-ui/)

通过在`CustomButton`组件中呈现一个 DOM 元素并将`onClick` prop 传递给它。我们的`CustomButton`本质上是点击事件的一个传递。

```
import React from "react";

const CustomButton = ({ onPress }) => {
  return (
    <button type="button" onClick={onPress}>
      Click on me
    </button>
  );
};

const App = () => {
  const handleEvent = () => {
    alert("I was clicked");
  };
  return <CustomButton onPress={handleEvent} />;
};

export default App; 
```

在上面的例子中，`CustomButton`组件被传递了一个`onPress`的属性，然后这个属性被传递到`button`的`onClick`中。

## React with TypeScript 中的类型检查

这是一个关于如何在 React 中为事件处理添加类型检查的例子。`ChangeEvent`类型从 React 导入，并用于从`input`元素传递的事件。

```
import {useState, ChangeEvent} from "react";

const InputComponent = () => {
  const [email, setEmail] = useState<string>('');

  const onChange = (e: ChangeEvent<HTMLInputElement>): void => {
    setEmail(e.target.value);
  };

  return <input type="text" placeholder="Enter email address" onChange={onChange} value={email} />;
}
export default InputComponent;

```

让我们看看一个完整的表单，以及如何为输入和表单提交向事件处理程序添加类型。

```
import { useState, ChangeEvent, FormEvent } from 'react';

export default function App() {
  const [email, setEmail] = useState<string>('');
  const [password, setPassword] = useState<string>('');

  const handleSubmit = (event: FormEvent) => {
    console.log('Form was submitted!');
  };

  const handleEmailChange = (event: ChangeEvent<HTMLInputElement>) => {
    setEmail(event.target.value);
  };

  const handlePasswordChange = (event: ChangeEvent<HTMLInputElement>) => {
    setPassword(event.target.value);
  };

  return (
    <div className="App">
      <form onSubmit={handleSubmit}>
        <input type="text" placeholder="Enter email address" onChange={handleEmailChange} value={email} />
        <input type="password" placeholder="Enter password" onChange={handlePasswordChange} value={password} />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}

```

React 提供了一个您可以使用的`FormEvent`类型，并将其传递给`handleSubmit`函数。要了解 React 事件处理程序的更多信息，请查看 React 提供的事件处理程序类型的[详尽列表。](https://blog.logrocket.com/build-react-custom-component-library-theme-ui/)

## 结论

事件处理程序决定事件发生时应该采取什么行动。`onClick`事件用于监听 DOM 元素上的点击事件。

我们还回顾了功能组件中`onClick`事件处理程序的一些常见用例，比如更新状态、调用多个函数和使用合成事件。

我们讨论了`onClick`事件处理程序如何在定制组件中工作，最后我们看了如何给事件处理程序添加类型。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)