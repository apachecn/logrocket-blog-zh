# React 中的递归组件:一个真实的例子

> 原文：<https://blog.logrocket.com/recursive-components-react-real-world-example/>

有没有在 [React](https://blog.logrocket.com/tag/react/) 中使用过递归组件？我有。我和他们的第一次经历让我对前端项目有了全新的认识。因此，我认为写一篇关于 React 中使用递归组件的真实示例的文章是一个好主意，可以帮助人们更加熟悉使用递归组件。

在本文中，我们将探索 React 中递归组件的细节以及它们在实际应用程序中的用法。我们将在 React 中查看递归和非递归组件的示例，并评估它们的模块性、可读性和可维护性。之后，我们将构建一个真实世界的递归组件，一个类似于 VS 代码的嵌套文件浏览器。

*向前跳转:*

## React 中的递归组件是什么？

递归是在函数内部重复调用函数，直到满足基本条件(基本情况)。每次递归函数调用自己时，它都需要一个新的参数。例如，下面的`factorial`函数通过用不同的参数重复调用自身来工作，直到满足一个基本条件:

```
function factorial(num) {
    if (num <= 1) { // base condition
        return 1;
    }
    return num * factorial(num - 1); // function calling itself with new input value.
}
console.log(factorial(6)); // 720 

```

现在我们已经为理解递归组件打下了基础，让我们在 React 中看看它们。您可能知道，React 组件是函数。当一个 React 组件用不同的道具重复地在其内部渲染，直到满足一个基本条件，它被称为递归组件。

递归组件是独一无二的，因为它可以调用自身并帮助呈现深度嵌套的数据。这些组件不限于简单地在 UI 上呈现父数据；他们还呈现父母的[孩子的数据](https://blog.logrocket.com/deep-dive-iterating-context-children-react/)，直到达到深度限制。在这些组件中，孩子们可以通过[传递道具](https://blog.logrocket.com/the-beginners-guide-to-mastering-react-props-3f6f01fd7099/)来设定。

让我们看一个 React 中递归组件的简单例子:

```
import React from "react";

const RecursiveComponent = ({children}) => {
  return (
    <div>
      {children.length > 0 && <RecursiveComponent children={children} />}
    </div>
  );
};

export default RecursiveComponent;

```

如果你对这个概念在现实世界中的应用感到好奇，你可以在 [Reddit](https://www.reddit.com/) 和类似于 [VS Code](https://code.visualstudio.com/) 的文件浏览器中看到它们的作用。

现在您已经对递归组件有了基本的了解，我们将探索它们的用途并构建一个真实的例子。

## React 中的递归与循环有何不同？

你可能想知道循环和递归之间的关键区别。在这一节中，我们将看看这些差异。与循环相比，递归包括调用一个调用自身的函数，而循环需要不断调用相同的代码，直到满足特定的条件。

对于循环，我们必须首先定义控制变量，然后才能在循环的任何迭代中使用它。例如，当我们创建`for loop`来遍历一个数组时，我们必须声明一个计数器变量`let i=0`，来跟踪迭代的次数。这允许我们在遍历数组时使用计数器变量。否则，我们将无法在需要的时候使用它，我们将需要`increment`或`decrement`这些控制变量来避免无限循环。

另一方面，在使用递归时，我们不必声明任何变量来执行递归操作。这是因为递归运算不依赖于变量，只需要一个基条件就可以停止调用函数。

*   可返回性:对于循环，我们不能返回任何东西。然而，当我们使用递归时，我们可以从函数中返回值
*   可读性和模块化:通过递归，代码变得更加可读和模块化。然而，使用循环会使代码更长

## 为什么以及何时在 React 中使用递归组件

在 React 中，组件是构建用户界面的主要构件。它们很棒，因为它们帮助我们从整体上思考我们的应用程序，并从更容易推理的更小的代码块中构建它。

你可能会想，“为什么我们要制作递归组件？”在 React 中使用递归组件的主要原因是它们使代码[变得干燥](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，更具可读性，并且模块化。

解决了这个问题之后，让我们把重点放在理解 React 中何时使用递归组件上。最常见的情况是数据嵌套了几层。

假设我们有一个对象数组，每个对象都有一个`children`键对应另一个对象数组。该数组的每个对象部分都有一个对应于另一个对象数组的`children`键。同样，这些对象可以包含更多的子数组。

此类数据的一个示例如下:

```
export const data = [
  {
    isFolder: true,
    name: "public",
    children: [
      {
        isFolder: false,
        name: "index.html",
      },
    ],
  },
  {
    isFolder: true,
    name: "src",
    children: [
      {
        isFolder: true,
        name: "components",
        children: [
          {
            isFolder: true,
            name: "home",
            children: [
              {
                isFolder: false,
                name: "Home.js",
              },
            ],
          },
        ],
      },
      {
        isFolder: false,
        name: "App.js",
      },
    ],
  },
];

```

正如您所看到的，上面的数据有许多嵌套的子数组。这些嵌套的条目可以更深，但是为了解释清楚，我只保留了四层。

在这种情况下使用循环并不是一个好的选择，因为它需要编写大量的嵌套循环来遍历每一层数据。这使得你的代码更大，更难阅读。此外，如果我们不确定我们的数据有多深，使用循环遍历所有嵌套的数据是一个挑战。所以，在这种情况下，最好用递归。

## 使用标准 React 组件构建嵌套文件资源管理器

在本节中，我们将使用标准的 React 组件构建一个嵌套的文件资源管理器应用程序。在本教程的下一部分，我们将通过使用递归组件来构建相同的 React 应用程序。

首先，在`src`文件夹中新建一个 React app 和一个`data`文件夹。然后，在`data`中创建`data.js`，并将“[为什么以及何时在 React](#why-when-use-recursive-components-react) 中使用递归组件”一节中的数据复制并粘贴到后面的。

现在，用下面的代码替换`App.js`文件的代码:

```
import React from "react";
import { data } from "./data/data";

const NonRecursiveComponent = ({ data }) => {
  return <div>Non Recursive Component</div>;
};

const App = () => {
  return (
    <div style={{ margin: "8px" }}>
      <NonRecursiveComponent data={data} />
    </div>
  );
};
export default App;

```

我们在上面的代码中创建了一个`NonRecursiveComponent`,然后[在`App`组件中呈现它](https://blog.logrocket.com/react-conditional-rendering-9-methods/)。然后，我们将`data`道具从`App`传递到`NonRecursiveComponent`。

现在，让我们开始在 UI 上呈现数据。在`NonRecursiveComponent`中，用以下代码替换现有代码:

```
const NonRecursiveComponent = ({ data }) => {
  return (
    <div>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            <span>{parent.name}</span>
          </div>
        );
      })}
    </div>
  );
};

```

上面的代码将呈现所有的`parent`(第一级)数据，并在 UI 上显示出来:

![Rendered Recursive Component in React ](img/c7545da0bb7b274af5615d65bcd8d7df.png)

现在，让我们通过使用第二个`return`语句中的`map`方法来呈现`children`。您的代码应该如下所示:

```
const NonRecursiveComponent = ({ data }) => {
  return (
    <div>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            <span>{parent.name}</span>
            {parent.children.map((child) => { // rendering children of the parent
              return (
                <div key={child.name} style={{ paddingLeft: "20px" }}>
                  <span>{child.name}</span>
                </div>
              );
            })}
          </div>
        );
      })}
    </div>
  );
};

```

从那里，您应该可以在 UI 上看到`parent`的所有`children`:

![Children in React Recursive Component UI](img/158fc39a1ca0fa37ac53032499404ca2.png)

我们来渲染一下`grandchildren`。因此，要做到这一点，我们需要做我们为`children`做的同样的事情。您的代码应该如下所示:

```
const NonRecursiveComponent = ({ data }) => {
  return (
    <div>
      {data.map((parent) => { // rendering parent data
        return (
          <div key={parent.name}>
            <span>{parent.name}</span>
            {parent.children.map((child) => { // rendering children of the parent
              return (
                <div key={child.name} style={{ paddingLeft: "20px" }}>
                  <span>{child.name}</span>
                  {child.children && // rendering grandchildren of the parent
                    child.children.map((grandChild) => {
                      return ( 
                        <div key={grandChild.name} style={{ paddingLeft: "20px" }}>
                          <span>{grandChild.name}</span>
                        </div>
                      );
                    })}
                </div>
              );
            })}
          </div>
        );
      })}
    </div>
  );
};

```

您应该会在 UI 上看到类似这样的内容:

![Grandchildren in the Recursive Components in React](img/1bdbb8c5d1e57ad4af7f1a53907813fc.png)

现在，我们需要做的最后一件事是在 UI 上呈现`great-grandchildren`。我们需要做和渲染`children`和`grandchildren`一样的事情。完成此操作后，您的代码应该如下所示:

```
const NonRecursiveComponent = ({ data }) => {
  return (
    <div>
      {data.map((parent) => { // rendering parent data
        return (
          <div key={parent.name}>
            <span>{parent.name}</span>
            {parent.children.map((child) => { // rendering children of the parent
              return (
                <div key={child.name} style={{ paddingLeft: "20px" }}>
                  <span>{child.name}</span>
                  {child.children && // rendering grandchildren of the parent
                    child.children.map((grandChild) => {
                      return (
                        <div
                          key={grandChild.name}
                          style={{ paddingLeft: "20px" }}
                        >
                          <span>{grandChild.name}</span>
                          {grandChild.children && // rendering great-grandchildren
                            grandChild.children.map((greatGrandChild) => {
                              return (
                                <div
                                  key={greatGrandChild.name}
                                  style={{ paddingLeft: "20px" }}
                                >
                                  <span>{greatGrandChild.name}</span>
                                </div>
                              );
                            })}
                        </div>
                      );
                    })}
                </div>
              );
            })}
          </div>
        );
      })}
    </div>
  );
};

```

您的用户界面应该如下所示:

![React Recursive Components File](img/5e8d2f6f7d058cd67509de2cffe74b81.png)

到目前为止，我们已经使用常规的 React 组件构建了一个基本的嵌套文件浏览器组件。然而，如您所见，`NonRecursiveComponent`代码似乎在为每个嵌套层次的数据重复自己。随着数据嵌套层次的增加，这些代码变得越来越长，使得理解和维护变得更加困难。

那么，我们如何解决这个问题呢？在下一节中，我们将看看 React 中的递归组件如何提供帮助。

## 使用 React 中的递归组件构建嵌套文件资源管理器

在这一节中，我们将使用递归组件构建相同的嵌套文件浏览器，并为嵌套文件和文件夹实现一个`show/hide`功能。

让我们从在`App.js`文件中创建一个新的`RecursiveComponent`开始。然后，把`App`中渲染的`NonRecursiveComponent`换成`RecursiveComponent`。

您的代码应该如下所示:

```
const RecursiveComponent = ({ data }) => {
  return (
    <div style={{ paddingLeft: "20px" }}>
      Recursive Component
    </div>
  );
};

const App = () => {
  return (
    <div style={{ margin: "8px" }}>
      <RecursiveComponent data={data} />
    </div>
  );
};

```

现在，让我们在 UI 上呈现所有的`parent`数据:

```
const RecursiveComponent = ({ data }) => {
  return (
    <div style={{ paddingLeft: "20px" }}>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            <span>{parent.name}</span>
          </div>
        );
      })}
    </div>
  );
};

```

接下来，让我们使用递归来呈现所有嵌套的文件和文件夹数据。为此，我们需要做两件事:首先，用不同的`data`道具从内部渲染`RecursiveComponent`。第二，我们需要一个基本条件来停止渲染`RecursiveComponent`。

在我们的例子中，基本条件是当`children`的长度为零或者它们不存在时，此时我们不会调用`RecursiveComponent`。

您的代码应该如下所示:

```
const RecursiveComponent = ({ data }) => {
  return (
    <div style={{ paddingLeft: "20px" }}>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            <span>{parent.name}</span>
            {/* Base Condition and Rendering recursive component from inside itself */}
            <div>
              {parent.children && <RecursiveComponent data={parent.children} />}
            </div>
          </div>
        );
      })}
    </div>
  );
};

```

您的用户界面应该如下所示:

![Real-World Example of React Recursive Components](img/7896eb524a8bfa780e8b5eea2091e1ea.png)

😮惊喜！通过几行代码，我们获得了相同的输出。这就是 React 中递归的神奇之处。

现在，让我们对嵌套的文件和文件夹实现`show/hide`。在开始之前，我们需要对代码进行一些修改。首先，我们需要在数据中有条件地使用`isFolder`键，将`button`标签用于文件夹名称，将`span`标签用于文件名称。我们这样做是因为我们只将`onClick`事件添加到文件夹中，而不是添加到文件中，以显示或隐藏特定文件夹中的所有文件和文件夹。

更新后的代码应该如下所示:

```
const RecursiveComponent = ({ data }) => {
  return (
    <div style={{ paddingLeft: "20px" }}>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            {/* rendering folders */}
            {parent.isFolder && <button>{parent.name}</button>}
            {/* rendering files */}
            {!parent.isFolder && <span>{parent.name}</span>}
            <div>
              {parent.children && <RecursiveComponent data={parent.children} />}
            </div>
          </div>
        );
      })}
    </div>
  );
};

```

现在，让我们通过使用`RecursiveComponent`中的 [`useState`钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)创建一个状态来实现`show/hide`，如下所示:

```
import { useState } from "react";

const RecursiveComponent = ({ data }) => {
  const [showNested, setShowNested] = useState({});
  // ...
  // rest of the code
  // ...
};

```

这个`showNested`状态变量将像这样存储所有打开的文件夹:

```
{
  public: true, // if the folder is opened, set it equal to true
  src: false // if the folder is not opened, set it equal to false
}

```

现在，创建一个函数来处理`show/hide`:

```
const RecursiveComponent = ({ data }) => {
  const [showNested, setShowNested] = useState({});

  // handle show/hide functionality
  const toggleNested = (name) => {
    setShowNested({ ...showNested, [name]: !showNested[name] });
  };
  // ...
  // rest of the code
  // ...
};

```

`toggleNested`函数接受文件夹的名称作为参数，然后使用`setShowNested`函数更新`showNested`中该文件夹的值。这使它从`false`变为`true`，反之亦然。

现在，让我们给`button`添加一个`onClick`事件来调用`toggleNested`。然后，当用户单击任何一个**文件夹**时，将文件夹的名称作为参数传递，如下所示:

```
const RecursiveComponent = ({ data }) => {
  // ...
  // Rest of the code
  return (
    <div style={{ paddingLeft: "20px" }}>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            {parent.isFolder && (
              <button onClick={() => toggleNested(parent.name)}>
                {parent.name}
              </button>
            )}
            // ...
            // Rest of the code
            // ...
          </div>
        );
      })}
    </div>
  );
};

```

最后一步是更新`display` [CSS 属性](https://blog.logrocket.com/tag/css/)，我们将把它添加到`div`中，包装`RecursiveComponent`中的`RecursiveComponent`。当用户选择**文件夹**时，我们将更新`showNested`对象，将文件夹名作为`key`添加到该对象中。我们还将它的值设置为`true`，并添加一个检查来查看文件夹名称是否出现在`showNested`中。

然后，我们将把`display`设置为`block`。否则，我们将把它设置为`none`来隐藏嵌套的文件和文件夹:

```
const RecursiveComponent = ({ data }) => {
  // ...
  // Rest of the code
  return (
    <div style={{ paddingLeft: "20px" }}>
      {data.map((parent) => {
        return (
          <div key={parent.name}>
            // ...
            // Rest of the code
            // ...

            // Updating the display property using the showNested state
            <div style={{ display: !showNested[parent.name] && "none" }}>
              {parent.children && <RecursiveComponent data={parent.children} />}
            </div>
          </div>
        );
      })}
    </div>
  );
};

```

希望您能够切换所有的目录。至此，这个博客就完成了！

## 结论

在本文中，您了解了 React 中的递归组件，如何构建它们，以及为什么我们可能会使用它们。此外，我用一个真实的例子展示了递归与循环的不同之处。希望您学到了一些对您的下一个 React 项目有用的新东西。

我希望你喜欢这篇文章，感谢你花时间阅读它。如果你在阅读这篇文章时有任何问题，或者有进一步的疑问，请在评论区告诉我。如果你喜欢我在这里做的事情，并想帮助我继续做下去，别忘了点击分享按钮。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)