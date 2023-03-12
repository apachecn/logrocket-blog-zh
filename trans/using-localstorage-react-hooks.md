# 使用带有 React 钩子的 localStorage

> 原文：<https://blog.logrocket.com/using-localstorage-react-hooks/>

`localStorage`是浏览器网页存储的两种机制之一。它允许用户在浏览器中将数据保存为键值对，以备后用。

与在当前浏览器标签运行时将数据保存在浏览器存储器中的`sessionStorage`机制不同，`localStorage`不会在浏览器关闭时清除数据。这使得它非常适合保存未绑定到当前浏览器选项卡的数据。

在向应用程序添加黑暗模式特性、持久化待办事项或持久化用户表单输入值等许多其他用例时，开发人员通常会实现`localStorage`。

在本指南中，我们将介绍如何使用 React 钩子在浏览器存储中使用`localStorage`来持久化用户的表单输入。我们还将介绍如何创建一个定制的 React 挂钩，以便在多个组件之间共享类似的逻辑。

## `localStorage`带有 React 钩子的先决条件

为了遵循这个指南，确保你对 React 和 [React 钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)有一个基本的[理解。另外，确保你的电脑上已经安装了](http://ibaslogic.com/react-tutorial-for-beginners/)的 [Node.js。](https://nodejs.org)

## 初始`localStorage`项目设置

使用一个新的 React 应用程序，让我们到计算机终端运行以下命令来创建一个新的 React 项目:

```
npx create-react-app localstorage-react-hook

```

项目文件夹生成后，用代码编辑器打开它，并通过运行`npm start`命令启动开发服务器。

项目应该在`[http://localhost:3000/](http://localhost:3000/)`时在浏览器中启动。

## 创建反应表单组件

如前所述，我们将使用`localStorage`在浏览器存储中保存用户的表单输入。

像每个 React 应用程序一样，我们的重点是`src`文件夹。因此，让我们删除`src`中的所有文件，并在`src`中创建一个`index.js`文件，以避免前端中断。

然后，将以下代码添加到`index.js`:

```
import React from "react";
import ReactDOM from "react-dom";

import App from "./components/App";
// styles
import "./app.css";

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);

```

请注意，我们导入了一个 CSS 文件来为应用程序添加样式。所以，让我们在`src`文件夹中创建一个`app.css`文件夹。[从 local storage-react-hook-project](https://github.com/Ibaslogic/localstorage-react-hook-project/blob/main/src/App.css)中复制样式，并将它们添加到`app.css`文件中。

接下来，在`src`文件夹中创建一个`components`文件夹来保存组件文件。然后，添加一个`App.js`文件和一个`Form1.js`文件。`App.js`文件是根和父组件，而`Form1.js`将保存表单输入。

在`components/App.js`文件中添加以下代码:

```
import Form1 from "./Form1";

const App = () => {
  return (
    <div className="container">
      <h1>localStorage with React hooks</h1>
      <Form1 />
    </div>
  );
};
export default App;

```

最后，将这段代码添加到`components/Form1.js`文件中:

```
import { useState } from "react";

const Form1 = () => {
  const [name, setName] = useState("");

  return (
    <form>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Full name"
        aria-label="fullname"
      />
      <input type="submit" value="Submit"></input>
    </form>
  );
};

export default Form1;

```

保存文件后，测试项目，您应该会看到这样的渲染:

![UI Shows Form and Button With Code Below Showing Component Rendering](img/060316437accf8f8d77acd97c6f3e5fe.png)

上面的代码是 React 中表单输入的最简单实现。通过使用`useState` React 钩子来控制组件，我们可以在每次击键时保持输入状态最新，如上所示。

但是，一旦我们触发页面刷新，输入数据就会清除，这是意料之中的。为了持久化输入数据，以便在页面重新加载或后续重新访问时可用，我们必须将数据保存在`localStorage`中。

## 将表单输入数据保存在`localStorage`中

[`localStorage`让我们访问浏览器的`Storage`对象](https://blog.logrocket.com/localstorage-javascript-complete-guide/)。`Storage`对象有保存、读取和删除数据的方法，以及许多其他操作。

要查看`Storage`方法的列表，请打开浏览器控制台并键入`localStorage`。按下回车键后，这些方法在`Storage`对象的`prototype`上变得可用。

### 使用`setItem()`方法

要在浏览器存储中存储表单输入数据，我们必须使用以下语法调用`setItem()`存储方法:

```
localStorage.setItem("key", "value")

```

浏览器存储只接受数据类型字符串。因此，对于不同数据类型的值，比如对象或数组，我们必须使用`JSON.stringify()`将其转换为 JSON 字符串。

### 使用`useEffect`挂钩执行副作用

我们还可以使用`useEffect` React 钩子来[执行副作用](https://reactjs.org/docs/hooks-effect.html)，比如将数据存储在浏览器存储器中。这使得这个钩子成为调用`setItem`方法的最佳位置。

打开`components/Form1.js`文件，在`return`语句上方添加以下代码:

```
useEffect(() => {
  // storing input name
  localStorage.setItem("name", JSON.stringify(name));
}, [name]);

```

确保从输入`useEffect`的反应是这样的:

```
import { useState, useEffect } from "react";

```

这里，我们分配了一个键`"name"`，和一个来自状态变量的动态值`name`。

`name`状态变量的初始值默认为空字符串:

```
const [name, setName] = useState("");

```

将字符串数据保存到存储器时，可以选择使用`setItem`中的`JSON.stringify`:

```
localStorage.setItem("name", JSON.stringify(name));
```

但是，如果值是不同的数据类型，如对象或数组，则需要使用`JSON.stringify`。

现在，保存文件并测试项目；我们应该会看到下面的渲染:

![useEffect Rendered In Browser With Code Below](img/5c1cdd002734a611f8da3a22f3e59855.png)

在每次击键时，输入值都保存在本地存储器中，因为持有`setItem`存储方法的`useEffect`钩子在第一次组件渲染时运行，并且在每次状态改变后运行。

但是，在页面重新加载时，存储中的值返回到空字符串。这是因为我们给状态变量`name`分配了一个默认的空字符串。因此，React 在初始渲染时使用空值。

现在，我们必须从存储中获取每个点的更新状态值，并将其作为默认状态值，而不是分配一个空字符串。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 从`localStorage`读取数据

在初始页面加载时，我们必须分配一个访问本地存储的函数，检索保存的值，并使用该值作为默认值，而不是将空字符串分配给`name`状态变量。

### 使用`getItem()`方法

更新`components/Form1.js`文件中的`useState`钩子:

```
const [name, setName] = useState(() => {
  // getting stored value
  const saved = localStorage.getItem("name");
  const initialValue = JSON.parse(saved);
  return initialValue || "";
});

```

这里，我们使用`getItem()`存储方法从本地存储中检索数据。代码中使用的`JSON.parse()`反序列化从存储中返回的 JSON 字符串。

在处理字符串值时，`JSON.stringify`和`JSON.parse`都是可选的，就像我们的例子中看到的那样。但是，其他数据类型，如对象和数组，需要它们。

保存文件并测试项目。在页面重新加载或以后的页面访问中，输入数据应该在表单字段中可用。

![Input Remains On Page Refresh](img/27fc9ae0776c08d95b31f7e5545dbaa4.png)

## 创建一个自定义的 React 钩子来持久化表单输入

有时我们可能希望在不同的组件中呈现和持久化更多的表单输入，比如文本输入和复选框输入。虽然我们可以很容易地从已经创建的逻辑中复制并在新的组件中使用它，但这并不总是可行的，尤其是当我们决定创建更多这样的输入时。

相反，React 允许我们使用定制钩子在组件之间提取和[共享相似的逻辑。在这一节中，我们将学习如何创建一个定制的钩子来保存多个组件中的表单输入。](https://blog.logrocket.com/advanced-react-hooks-creating-custom-reusable-hooks/)

让我们从创建另一个表单开始。在`src/components`文件夹中，创建一个名为`Form2.js`的新文件，并添加以下代码:

```
import { useState } from "react";

const Form2 = () => {
  const [name, setName] = useState("");
  const [checked, setChecked] = useState(false);

  return (
    <form>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Full name"
        aria-label="fullname"
      />
      <label>
        <input
          type="checkbox"
          checked={checked}
          onChange={(e) => setChecked(e.target.checked)}
        />{" "}
        Not a robot?
      </label>
      <input type="submit" value="Submit"></input>
    </form>
  );
};

export default Form2;

```

然后，导入并使用`components/App.js`文件中的组件:

```
// ...
import Form2 from "./Form2";

const App = () => {
  return (
    <div className="container">
      {/* ... */}
      <Form2 />
    </div>
  );
};
export default App;

```

保存文件并在前端查看表单。

![Input Field With Checkbox In Form](img/ec0a1234249aca284a4991b1063db59b.png)

与这个表单交互不会持久化`localStorage`中的状态值，因为我们还没有逻辑。因此，让我们定义一个逻辑来管理所有的表单输入。

### 提取`localStorage`逻辑

要开始提取`localStorage`逻辑，在`src`文件夹中创建一个名为`useLocalStorage.js`的文件，并添加以下代码:

```
import { useState, useEffect } from "react";

function getStorageValue(key, defaultValue) {
  // getting stored value
  const saved = localStorage.getItem(key);
  const initial = JSON.parse(saved);
  return initial || defaultValue;
}

export const useLocalStorage = (key, defaultValue) => {
  const [value, setValue] = useState(() => {
    return getStorageValue(key, defaultValue);
  });

  useEffect(() => {
    // storing input name
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
};

```

仔细看看上面的代码，我们只从`components/Form1.js`文件中提取了存储逻辑。我们没做什么特别的事。

通过创建一个名为`useLocalStorage`的定制钩子，我们维护了`Form1`组件中的所有存储逻辑。注意，你可以给你的定制钩子取任何名字，但是要确保以`use`开头。

`useLocalStorage`钩子需要两个参数:`key`和`defaultValue`。这意味着我们希望在不同的组件中调用钩子时传递这些值。

### 使用`useLocalStorage`定制挂钩

在`components/Form1.js`文件中，用自定义钩子替换`return`语句上面的逻辑，这样就有了以下内容:

```
import { useLocalStorage } from "../useLocalStorage";

const Form1 = () => {
  const [name, setName] = useLocalStorage("name", "");

  return (
    <form>
      {/* ... */}
    </form>
  );
};

export default Form1;

```

导入定制钩子后，我们可以使用它并传递惟一键和默认值，在本例中，默认值是一个空字符串。

如果我们对`components/Form2js`文件中的`Form2`组件进行同样的操作，我们应该得到如下结果:

```
import { useLocalStorage } from "../useLocalStorage";

const Form2 = () => {
  const [name, setName] = useLocalStorage("name2", "");
  const [checked, setChecked] = useLocalStorage("checked", false);

  return (
    <form>
      {/* ... */}
    </form>
  );
};

export default Form2;

```

保存所有文件并测试项目。我们应该能够在`localStorage`中持久化所有的表单输入。

![Side-By-Side Render and Code Showing Persisting Form Inputs With localStorage](img/ffc95e959a9dc0e16e2d0cc108f617a7.png)

干得好！

## 访问 SSR 应用程序的`localStorage`时出现问题

当使用像 Next.js 这样在服务器端执行代码的框架时，使用`localStorage`会产生一个错误，说明“没有定义窗口”

我们代码中使用的`localStorage`是`window`对象`window.localStorage`的内置属性。在我们的代码中，我们在访问`localStorage`时忽略了`window`，因为它是一个全局对象；我们可以选择包含`window`对象，因为它是可选的。

现在，这个`window`对象在服务器端不可用，而是在客户端/浏览器上可用，这提示了错误。要修复服务器端的错误，请检查是否定义了`window`对象。这样，我们的代码只能在有`window`的环境中运行。

打开`src/useLocalStorage.js`文件并更新`getStorageValue()`函数，这样您就有了以下内容:

```
function getStorageValue(key, defaultValue) {
  // getting stored value
  if (typeof window !== "undefined") {
    const saved = localStorage.getItem(key);
    const initial = saved !== null ? JSON.parse(saved) : defaultValue;
    return initial;
  }
}

```

别忘了我们还在`useLocalStorage.js`文件的`useEffect`钩子中使用了`localStorage`。但是在这种情况下，`localStorage`是安全的，因为`useEffect`钩子只在客户端运行，在那里我们可以访问`window`对象。

测试项目以确保一切仍按预期运行。

## 结论

我们已经介绍了如何使用 React 钩子在浏览器中使用`localStorage`来持久化数据。我们还学习了如何创建一个定制的钩子来将组件逻辑提取到可重用的函数中。

如果你喜欢这个指南，请在网上分享。如果你有任何问题或贡献，请在评论区分享。

在这里可以找到该项目的[完整源代码。](https://github.com/Ibaslogic/localstorage-react-hook-project)

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