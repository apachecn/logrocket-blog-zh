# 如何使用 React 钩子自动对焦

> 原文：<https://blog.logrocket.com/how-to-autofocus-using-react-hooks/>

自动对焦可以让你的 app 更方便用户使用。例如，他们可以在表单加载后立即开始输入，而不是在输入前单击某个字段。如果他们正在处理一个大的表单，用户可以在之后的剩余字段中切换，一直到**提交**按钮，并点击**回车**而无需触摸鼠标。

在本文中，我们将看看如何添加自动对焦来反应输入字段。然后，我们将通过把我们写的内容变成一个 [React 钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)来使我们的代码可重用。

## 什么是 React 钩子？

我不得不承认，当 React Hooks 第一次出现时，我几乎没有做什么。当它们存在的时候，我使用了它们，看起来这是一个很好的解决方案，但是一开始我从来没有自己编写过钩子。对我来说，这是另一个需要学习的新事物，我只能问自己，“当我们已经有了可以工作的东西，谁还需要它？”

直到我有三个具有不同 JSX 的 React 组件，但其余代码基本上是相同的，所以它被复制和粘贴。这让我很困扰。在这里，如果我使用类组件，我通常会为这个功能创建一个自定义类，但是当我的所有组件都正常工作时，这感觉不太好。

所以，我研究了如何创建一个 React 钩子，我了解到钩子只是一个你创建的接受值并返回其他值的函数。换句话说，它是一个普通的函数，有一个特殊的名字，这样你就可以组织你的代码，把所有的“钩子”放在同一个文件夹中，减少重复的代码。“React Hooks”的概念和名字吓走了我一段时间，因为我认为它表示某种特殊类型的功能，如 thunk 或 saga。

你也可以使用 React 的官方定义:

> 挂钩是让您从功能组件“挂钩”React 状态和生命周期特性的功能。

但是我认为这个定义限制了你去思考你可以用它们做什么。因此，让我们介绍一下在 React 中实现自动对焦的方法，然后将自动对焦功能变成一个钩子。

## 使用 React 自动聚焦字段

有几种方法可以自动聚焦 React 输入字段。

### `autoFocus`道具

可以用`autoFocus`道具。

```
const Form = () => {
  return (
    <form>
      <label>
        Email
        <input name="email" type="email" autoFocus />
      </label>
      <label>
        Password
        <input name="email" type="email" />
      </label>
      <button type="submit">Login</button>
    </form>
  );
};
export default Form;

```

这是可行的，但是有一个警告，我们将会谈到。这种情况下，记得用`autoFocus`，不要用`autofocus`。如果你使用`autofocus`来代替，React 将永远不会真正设置自动对焦 DOM 属性。

![Input for Email](img/388bcbcc158353669fbed5da97bd851c.png)

对于这个属性如何工作，每个浏览器都有不同的规则。由于这些不一致， [React 在挂载元素](https://github.com/facebook/react/issues/11851#issuecomment-351672131)时调用 `[focus()](https://github.com/facebook/react/issues/11851#issuecomment-351672131)` [。](https://github.com/facebook/react/issues/11851#issuecomment-351672131)

但并不总是奏效。如果将 React 添加到一个现有的应用程序中，并将一个组件呈现到一个分离的元素中，React 将在浏览器准备好之前调用`focus()`，当输入被添加到 DOM 中时，它将不会被聚焦。因此，我们将自己调用`focus()`,而不是依赖 React 来调用输入。

### 使用`useCallback()`自动聚焦

我们可以对自动对焦进行更多控制的第一种方法是使用`useCallback()`挂钩。下面是我们新代码的相同表单:

```
import React, { useCallback } from "react";

const Form = () => {
  const emailInput = useCallback((inputElement) => {
    if (inputElement) {
      inputElement.focus();
    }
  }, []);

  return (
    <form>
      <label>
        Email
        <input name="email" type="email" ref={emailInput} />
      </label>
      <label>
        Password
        <input name="email" type="email" />
      </label>
      <button type="submit">Login</button>
    </form>
  );
};
export default Form;

```

`[useCallback()](https://reactjs.org/docs/hooks-reference.html#usecallback)`钩子返回一个内存化的回调。它接受两个参数。第一个是要运行的函数，第二个是运行函数所依赖的值的数组。这个参数有一个空数组，这意味着这个函数在组件渲染时只运行一次。

当表单组件呈现时，将设置电子邮件输入的引用。这将执行我们在`useCallback()`钩子中的函数，该函数在输入端调用`focus()`。

### 使用 React `useRef()`和`useEffect()`挂钩自动对焦

我们也可以用`useRef()`和`useEffect()` React 钩子获得相同的功能。下面是使用这些挂钩的同一个表单:

```
import React, { useRef, useEffect } from "react";

const Form = () => {
  const emailInput = useRef(null);

  useEffect(() => {
    if (emailInput.current) {
      emailInput.current.focus();
    }
  }, []);

  return (
    <form>
      <label>
        Email
        <input name="email" type="email" ref={emailInput} />
      </label>
      <label>
        Password
        <input name="email" type="email" />
      </label>
      <button type="submit">Login</button>
    </form>
  );
};
export default Form;

```

钩子将告诉 React 你需要你的组件在渲染后做一些事情。它接受两个参数。第一个是您想要运行的函数，第二个是一个依赖数组，其功能与在`useCallback()`中相同。

对于功能组件来说,`[useRef()](https://reactjs.org/docs/hooks-reference.html#useref)`钩子做的事情和`createRef()`对于基于类的组件做的事情一样。这个钩子创建了一个普通的 JavaScript 对象，您可以将它传递给一个元素来保存对它的引用。这个引用可以通过对象的`current`属性来访问。

因此，在上面的代码中，我们创建了对 email 字段的引用。然后，当组件呈现时，我们使用引用对象上的`current`属性调用 email 字段上的`focus()`。

这是非常简单的代码，但是如果您有很多使用 autofocus 的表单，那么将这段代码转换成一个钩子以便可以重用它会干净得多。

## 把我们的自动对焦功能变成一个反应挂钩

因为我们已经想出了两种在 React 中自动聚焦输入的方法，所以我们可以创建两个版本的`useAutoFocus()`钩子。第一个版本会用`useCallback()`。

```
import { useCallback } from "react";

const useAutoFocus = () => {
  const inputRef = useCallback((inputElement) => {
    if (inputElement) {
      inputElement.focus();
    }
  }, []);

  return inputRef;
};

export default useAutoFocus;

```

第二个版本使用`useRef()`和`useEffect()`。

```
import { useRef, useEffect } from "react";

const useAutoFocus = () => {
  const inputRef = useRef(null);

  useEffect(() => {
    if (inputRef.current) {
      inputRef.current.focus();
    }
  }, []);

  return inputRef;
};

export default useAutoFocus;

```

对于这个钩子的两个版本，我们所做的只是从表单本身移除了自动聚焦功能，并将它移到了自己的文件中。

代码本质上是一样的。在第一个块中，我们返回我们创建的回调函数。在第二个例子中，我们返回 ref。我们可以在表单组件中使用任何一种。

```
import React from "react";
import useAutoFocus from "../hooks/useAutoFocus";

const Form = () => {
  const emailInput = useAutoFocus();

  return (
    <form>
      <label>
        Email
        <input name="email" type="email" ref={emailInput} />
      </label>
      <label>
        Password
        <input name="email" type="email" />
      </label>
      <button type="submit">Login</button>
    </form>
  );
};
export default Form;

```

在这里，我们可以导入包含自动对焦功能的`useAutoFocus()`钩子的任一版本，并执行它以获得它创建的引用。然后，我们使用电子邮件字段上的引用。我们得到这样的结果:

![Autofocus in React Email and Password Fields](img/8086d42d568b6ae675c7e55ac05e032f.png)

## 结论

我们发现了三种在 React 中自动对焦一个区域的方法，我们把其中的两种方法变成了钩子。现在，我们有了一个可以重用的钩子，而不是到处复制和粘贴代码。您可以在这个 [CodeSandbox](https://codesandbox.io/s/autofocus-in-react-1ikmo7) 中找到本文使用的所有代码。希望您看到自定义 React 挂钩是多么简单和有用，并开始创建自己的挂钩。

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