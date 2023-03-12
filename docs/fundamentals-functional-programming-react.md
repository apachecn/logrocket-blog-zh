# React - LogRocket 博客的函数式编程基础

> 原文：<https://blog.logrocket.com/fundamentals-functional-programming-react/>

理解函数式编程的概念对于 React 开发人员来说是一项有价值的技能。这是大多数 React 初学者经常忽略的一个重要话题，这使得他们在理解 React 如何做出一些决策时遇到了问题。

在本帖中，我们将介绍函数式编程概念，以及 React 如何采用它来编写更易于测试和维护的应用程序。

要学习本教程，请确保您对 React 有基本的了解。

## 函数式编程的快速概述

我们编写的每个程序或应用程序都遵循一种方法或写作风格，也称为[范式](https://en.wikipedia.org/wiki/Programming_paradigm)。因此，函数式编程是一种[声明式编程范式](https://en.wikipedia.org/wiki/Declarative_programming)，其中程序通过组合纯函数来构建。

让我们注意“组成”和“纯粹”这两个词，因为它们构成了函数式编程的构建块，我们将在下一节讨论它们。

## 数学中的函数

为了更好地理解函数式编程的概念，让我们快速地看一下数学中常用的函数。例如，我们有一个给定的函数:

```
y = f(x)

```

在此函数中，输出`y`只对输入`x`进行计算。这意味着每次我们用相同的输入调用函数`x`，我们总是得到相同的输出`y`。

该函数不会影响自身以外的任何东西，也不会修改传入的输入。因此，它被称为确定性函数或纯函数。

让我们看一个例子:

```
y = f(x) = 4x // if x = 2, y = 4(2) = 8

```

如上所述，对于每个输入`x = 2`，输出`y`将总是`8`。像这样的函数总是更容易理解和推理，因为我们确切地知道我们期望的是什么。

让我们更进一步，编写一个更复杂的函数，如下所示:

```
z = c(f(x))

```

这里，我们有两个函数，`c`和`f`，它们组合在一起形成一个更复杂的函数。在数学中，我们说`c`是`f(x)`的函数，意思是我们必须首先分别评估`f(x)`，就像这样:

```
y = f(x)

```

然后，我们将结果作为参数传递给`c`函数，如下所示:

```
 z = c(y)

```

这个功能概念叫做功能组合。它鼓励代码的可重用性和可维护性。

有了这个数学概念，理解计算机科学编程中的函数概念就是小菜一碟。

## React 中的函数式编程

接下来，我们将解释如何在 React 中应用函数式编程概念。我们还将看到 JavaScript 中的例子，因为它是 React 的底层语言。

让我们先来看看下面的 JavaScript 代码:

```
const arr = [2, 4, 6];

function addElement(arr, ele) {
  arr.push(ele);
}

addElement(arr, 8);

console.log("original data", arr); // Expected Output: [2, 4, 6, 8]

```

这里，我们定义了一个名为`addElement`的函数，它将一个元素添加到一个数组中。代码如输出所示工作，你不能在 CodeSandbox 上为自己 [尝试](https://codesandbox.io/s/unruffled-sutherland-z4ujd?file=/src/index.js)[。](https://codesandbox.io/s/unruffled-sutherland-z4ujd?file=/src/index.js)

这段代码看起来类似于前面解释的数学中的函数概念。也就是说，函数只对输入参数进行操作来创建输出。

但是仔细观察代码，我们会发现它违反了一个纯粹的函数概念，即函数不应该影响它之外的任何东西，也不应该修改传入的参数。

这样做的函数是不纯的函数，并且有副作用，比如操作输入参数，在本例中是全局`arr`数组。

在代码中，全局`arr`数组发生了变异，这意味着函数将全局变量从最初的`[2,4,6]`更改为`[2,4,6,8]`。

现在，假设我们想要重用全局变量来组成另一个函数。如果我们继续这样做，我们会得到一个意想不到的输出，这可能会导致程序中的错误。

这就把我们带到了函数式编程的第一个原则:纯函数。

## 使用纯函数

在函数式编程中，我们编写纯函数，这些函数只根据输入值返回输出，不会影响它们之外的任何东西。这有助于防止我们代码中的错误。

通过将这一功能概念应用于上面的代码，我们得到以下结果:

```
const arr = [2, 4, 6];

function addElement(arr, ele) {
  return [...arr, ele];
}

console.log("modified data", addElement(arr, 8)); // Expected Output: [2, 4, 6, 8]
console.log("original data", arr); // Expected Output: [2, 4, 6]

```

上面的函数是纯函数，它只计算输入参数的输出，从结果中可以看到，它从不改变全局`arr`数组。

[你可以在 CodeSandbox](https://codesandbox.io/s/kind-wozniak-liqhj?file=/src/index.js) 上亲自尝试一下。

请注意，代码还使用了不变性概念来使函数变得纯粹(我们稍后会谈到这一点)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这种类型的函数是可预测的，也更容易测试，因为我们总是会得到预期的输出。

### React 如何实现纯函数概念

React 应用程序组件最简单的形式如下所示:

```
const Counter = ({ count }) => {
  return <h3>{`Count: ${count}`}</h3>;
};

```

这类似于 JavaScript 中的纯函数，其中函数接收一个参数(在本例中是一个`count`道具)并使用该道具呈现输出。

然而，React 具有从父组件到子组件的单向数据流。当状态数据传递给子组件时，它就变成了不可变的属性，不能被接收组件修改。

因此，给定相同的道具，这种类型的组件总是渲染相同的 JSX。正因为如此，我们可以在任何页面部分重用组件，而不用担心不确定性。这种类型的组件是纯粹的功能组件。

### 提高应用性能

React 利用纯功能概念[来提高应用性能](https://blog.logrocket.com/5-react-performance-optimization-techniques/)。由于 React 的性质，无论何时组件的状态发生变化，React 都会重新呈现组件及其子组件，即使状态变化不会直接影响子组件。

在这种情况下，React 允许我们在`React.memo`中包装纯功能组件，以防止不必要的重新渲染，如果它收到的道具从未改变。

通过记忆上面的纯函数，我们只在`count`属性改变时才重新渲染该函数:

```
const CounterComponent = React.memo(function Counter({ count }) {
  return <h3>{`Count: ${count}`}</h3>;
});

```

### 状态更新中的纯函数概念

React 还在更新状态变量时实现了函数概念，尤其是当一个值基于先前的值时，比如在计数器或复选框的情况下。

看一下下面的代码:

```
import { useState } from "react";
const App = () => {
  const [count, setCount] = useState(0);

  const handleClick = () => setCount(count + 1);

  return (
    // ...
  );
};

const Counter = ({ count }) => {
  // ...
};

export default App;

```

这里，为了简洁起见，我们删除了代码的一些部分，但是扩展了我们之前的`Counter`组件为[显示一个按钮来增加计数](https://codesandbox.io/s/sad-dawn-erel6?file=/src/App.js)。

这是 React 初学者熟悉的代码。当代码工作时，我们可以通过遵循函数式编程概念来添加改进。

让我们关注代码的这一部分:

```
const handleClick = () => setCount(count + 1);

```

在`setCount` updater 函数中，我们使用了一个不作为参数传递的`count`变量。正如我们所了解的，这违背了函数式编程的概念。

React 提供的一个改进是向 updater 函数传递一个回调。在这个回调中，我们可以访问状态的先前版本，并从那里更新状态值:

```
const handleClick = () => setCount((prev) => prev + 1);

```

正如我们在`setCount`回调中看到的，输出只计算`prev`输入参数。这是纯粹的功能概念在起作用。

## 避免数据突变(不变性)

当一个函数变异或改变一个全局变量时，它会导致我们程序中的一个错误。

在函数式编程中，我们将数组和对象等可变数据结构视为不可变数据。这意味着我们从不修改它们，而是在传递给函数时制作一个副本，这样函数就可以根据这个副本计算它的输出。

让我们重温一下下面的代码:

```
const arr = [2, 4, 6];

function addElement(arr, ele) {
  return [...arr, ele];
}

console.log("modified data", addElement(arr, 8)); // Expected Output: [2, 4, 6, 8]
console.log("original data", arr); // Expected Output: [2, 4, 6]

```

我们在前面已经看到了这段代码，但是这一次我们将把注意力转移到不可变的函数方面。

这里，我们有一个只使用全局变量的副本来计算输出的函数。我们使用 ES6 spread 操作符(`…`)将现有数据复制到一个新数组中，然后添加新元素。通过这种方式，我们保持了原始数组输入数据的不可变，如结果所示。

### React 如何处理可变状态

因为 React 是一个反应式库，所以它必须对状态变化做出“反应”,以使 DOM 保持最新。显然，状态值也必须更新。

在 React 中，我们不直接修改状态。相反，我们使用类组件中的`setState()`方法或功能组件中的 updater 函数来更新状态。

看一下我们之前代码的摘录:

```
const handleClick = () => setCount((prev) => prev + 1);

```

这里，我们使用更新函数`setCount`来更新计数。当处理像数字和字符串这样的不可变数据时，只要下一个状态依赖于前一个状态，我们就必须只将更新后的值传递给 updater 函数或调用回调函数。

让我们看另一个更新字符串值的例子:

```
import { useState } from "react";

const App = () => {
  const [person, setPerson] = useState("");

  const handleChange = (e) => {
    setPerson(e.target.value);
  };

  return (
    // ...
  );
};

export default App;

```

这里，为了简洁起见，我们又删除了一些代码。

上面的代码更新了一个表单的文本字段，这涉及到使用不可变的字符串数据。因此，我们必须[通过将当前输入值传递给更新函数](https://codesandbox.io/s/proud-framework-dcnlr?file=/src/App.js)来更新输入字段。

然而，每当我们传递像数组和对象这样的可变数据时，我们必须制作状态数据的副本，并根据该副本计算输出。请注意，我们绝不能修改原始状态数据。

在下面的代码中，`handleChange`触发更新表单中每次击键的状态变量:

```
import { useState } from "react";

const App = () => {
  const [person, setPerson] = useState({
    fName: "",
    lName: ""
  });

  const handleChange = (e) => {
    setPerson({
      ...person,
      [e.target.name]: e.target.value
    });
  };

  return (
    // ...
  );
};

export default App;

```

如代码所示，我们[正在处理一个可变对象](https://codesandbox.io/s/vibrant-meninsky-9iwdk?file=/src/App.js)，因此，我们必须将状态视为不可变的。同样，我们通过使用 ES6 spread 操作符制作状态的副本并更新受影响的属性来实现这一点:

```
setPerson({
  ...person,
  [e.target.name]: e.target.value
});

```

另一个改进是确保更新函数`setPerson`使用一个状态变量作为回调函数的参数:

```
const handleChange = (e) => {
  setPerson((person) => ({
    ...person,
    [e.target.name]: e.target.value
  }));
};

```

现在，如果我们不遵循这个函数概念，直接改变状态，会发生什么？显然，我们会在应用程序中遇到一个错误。

为了看得更清楚，[再次访问这个代码沙箱](https://codesandbox.io/s/vibrant-meninsky-9iwdk?file=/src/App.js)，暂时从函数中注释掉`…person`，就像这样:

```
setPerson((person) => ({
  // ...person,
  [e.target.name]: e.target.value
}));

```

现在，通过尝试在表单域中写一些东西，文本将覆盖彼此。这是我们想要防止的错误，我们可以通过将状态视为不可变数据来做到这一点。

## 避免副作用

函数式编程代码应该是纯粹的。React 中的纯组件可以接收一个属性作为参数，并根据输入属性计算输出。

但有时，组件可以进行影响和修改其范围之外的某些状态的计算。这些计算被称为副作用。这些效果的例子包括数据获取和手动操作 DOM。

这些是我们在应用程序中经常执行的任务，因此副作用是不可避免的。

以下片段基于我们之前的`Counter`示例:

```
const Counter = ({ count }) => {
  document.title = `Number of click: ${count}`;
  return <h3>{`Count: ${count}`}</h3>;
};

```

在代码中，我们更新了文档标题以反映更新后的计数值。这是一个副作用，因为我们修改了不属于组件的 DOM 元素，从而使组件不纯。

不允许在组件体内直接执行副作用，以避免我们的应用程序不一致。相反，我们必须将这种效果从渲染逻辑中分离出来。React 为我们提供了一个名为
`[useEffect](https://blog.logrocket.com/guide-to-react-useeffect-hook/)` [的](https://blog.logrocket.com/guide-to-react-useeffect-hook/)[钩子来管理我们的副作用](https://blog.logrocket.com/guide-to-react-useeffect-hook/)。

下面的代码实现了这个挂钩:

```
const Counter = ({ count }) => {
  useEffect(() => {
    document.title = `Number of click: ${count}`;
  }, [count]);

  return <h3>{`Count: ${count}`}</h3>;
};

```

[通过在 React](https://codesandbox.io/s/admiring-hoover-s52eg?file=/src/App.js) `[useEffect](https://codesandbox.io/s/admiring-hoover-s52eg?file=/src/App.js)` [钩子](https://codesandbox.io/s/admiring-hoover-s52eg?file=/src/App.js)中放置副作用，意味着我们可以很容易地测试和维护渲染逻辑。

## 反应中的成分

在函数式编程中，组合是通过组合或链接多个较小的函数来构建复杂函数的行为。

如果我们回忆一下本文的开头，我们提到对于一个给定的函数，`c`和`f`，我们可以将它们组合成一个更复杂的函数，演示如下:

```
z = c(f(x))

```

但是现在，我们将在 React 的上下文中查看这个合成概念。

类似于上面的功能模式，我们可以通过使用 React 中的`children`属性注入其他组件来构建 React 中的复杂组件。这个特性还允许组件呈现不同数量的内容，而不需要提前知道内容。

这使我们能够灵活地决定组件内部的内容，并定制内容以获得所需的输出。

实现这个概念的组件的一个很好的例子包括`Hero`和`[Sidebar](https://blog.logrocket.com/create-sidebar-menu-react/)`。

### 构建可重用的`Hero`组件

假设我们想要创建一个包含不同内容的`Hero`组件，我们可以在应用程序的任何地方重用它。

我们可以这样开始编写组件:

```
function Hero({ children }) {
  return <section>{children}</section>;
}

```

这段代码中使用的`children` prop 允许我们在组件的开始和结束标记之间插入内容；在我们的例子中，是一个`Hero`组件。

所以，我们可以有这样的东西:

```
<Hero>
  <Banner>
    <h1>Home Page</h1>
    <p>This is the home page description</p>
  </Banner>
</Hero>

```

现在，`<Hero>`之间的所有东西都被认为是它的`children`道具，因此出现在`Hero`组件的`section`标签之间。

同样，`<Banner>` JSX 标签中的内容作为`children`道具传递到`Banner`组件中:

```
function Banner({ children }) {
  return (
    <div>
      {children}
      <button>Subscribe to newsletter</button>
    </div>
  );
}

```

标签`<Banner>`之间的内容(即`h1`和`p`之间的内容)替换 JSX 内的`children`。

在这段代码中，`Banner`组件只知道`button`元素，因为我们已经手动添加了该元素；它不知道什么将取代`children`道具。

这使得组件可以重用和灵活定制，因为我们可以控制作为`children`出现的内容。我们现在可以决定不在应用程序的另一个页面上呈现标题`h1`。

我们所要做的就是[将它从](https://codesandbox.io/s/angry-chebyshev-69hiw?file=/src/App.js) `[Banner](https://codesandbox.io/s/angry-chebyshev-69hiw?file=/src/App.js)` [标签](https://codesandbox.io/s/angry-chebyshev-69hiw?file=/src/App.js)之间的内容中排除。

通过将 React 组合与数学定义进行比较，我们可以说,`Banner`组件的输出变成了`Hero`组件的输入。换句话说，`Hero`与`Banner`组件组成一个整体组件。

这就是动作构图。

## 结论

我很高兴你在这里！在这篇文章中，我们通过实际例子了解了 React 如何将函数式编程概念应用到一些决策中。

我希望你喜欢阅读这篇文章。如果你有问题或贡献，请在评论区分享你的想法，我会很高兴地招待他们。最后，努力在网络上分享这个指南。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)