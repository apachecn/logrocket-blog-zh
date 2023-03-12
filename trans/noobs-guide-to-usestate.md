# noob 的使用指南

> 原文：<https://blog.logrocket.com/noobs-guide-to-usestate/>

我们可以使用功能组件作为我们的主要组件的原因之一是因为它现在可以使用像`useState`这样的钩子来包含自己的“状态”。因此，完全抛弃基于类的组件是可能的。

尽管钩子提供了这个优势，但是在我们的功能组件中使用`useState`时，仍然有可能培养出不好的习惯。在以函数形式构造组件时，我们仍然不安全，可能会引入潜在的陷阱。

你如何知道你是否用错了`useState`？请继续阅读。

## 突变状态，而不是使用由`useState`提供的`setState`

首先，变异状态在 React 生态系统中是一大禁忌，因为它大量实践了`immutability`的概念。为了演示如何在不知道的情况下不小心改变状态，请考虑下面的代码片段:

```
const [MyValue, setMyValue] = useState(0);

MyValue = 55;

```

这被认为是直接改变状态。我们严重违反了正确操作状态的经验法则，因为它应该被视为不可变的，除非我们调用数组的第二个元素，`setMyValue`。

因为状态值是“只读的”，所以不能这样修改它。它将抛出一个错误:

您可能会错误地变更状态的其他方式包括以下示例:

```
const [myValues, setMyValues] = useState([1,2,3,4,5]);

myValues[2] = 55;
const [myValues, setMyValues] = useState([1,2,3,4,5]);

//map creates new array. But it is still referencing the old array, so therefore in this case we are still mutating myValues array.
const newValues = myValues.map((item, idx) => {
        if(idx === 2) item = 55;

        return item;
});

```

在这个例子中，您试图改变一个状态值，它是一个数组。你也许可以改变它，但是这不会在你的组件中产生一个“重新渲染”,这意味着新的值不会显示在你的 UI 中。

为了实时展示它，[让我们给你一个变异数组](https://codepen.io/reciosonny/pen/gOLboEq)的工作示例:

```
let count = 0;
const App = () => {

  const [stateVal, setStateVal] = React.useState([1,2,3,4,5]);

  const onChangeArrayValues = () => {
    stateVal[count] = "Changed";

    count += 1;

    alert("updated array: " + stateVal);
  }

  return (
    <div>
      <h1>Changing array state values</h1>
      <h2>Array values: {stateVal}</h2>
      {/* <h2>Sum result: {multiplyByThree(5, 5)}</h2> */}

      <button onClick={() => onChangeArrayValues()}>Click to change</button>
    </div>
  );
}

```

因此，正如我们在这个例子中看到的，即使我们改变了状态数组，它也没有反映我们的 UI。React 足够聪明，能够知道状态是固定的，还是只是简单地“变异了”如果它发生了变异，它不会在其组件中发出“重新呈现”来反映我们 UI 中的新状态值。

基于对象的状态也是如此:

```
const App = () => {

  const [stateVal, setStateVal] = useState({ val1: "Hello world!" });

  return (
    <div>
      <h1 onClick={() => stateVal.val1 = "Mutated value..."}>
                                Test state: {stateVal.val1}
                        </h1>
    </div>
  )
}

```

[https://codepen.io/reciosonny/pen/ExNaagg](https://codepen.io/reciosonny/pen/ExNaagg)

我们也许可以让它变异而不会察觉到你已经变异了它。与上一个改变数组的例子相同的问题也会发生:新的值不会反映在我们的 UI 中。

在本例中，仍然需要使用由`useState`提供的`setState`函数来正确设置状态。

这并不是国家挂钩所独有的。事实上，在基于类的组件中管理状态也会犯同样的错误。

## 你应该如何设置状态？

解决这个问题的一个方法是确保我们使用不可变的方法，比如使用来自`useState`的第二个元素来设置状态值，就像这样:

```
const [myValues, setMyValues] = useState(0);

setMyValues(55);

```

这是以不可变的方式设置状态值的官方方法。我们使用第二个元素，它是一个设置状态的函数。

我们仍然可以将这种方法用于基于对象的状态。然而，在修改这种状态时，我们仍然必须遵守不变性的概念。这个示例代码片段将帮助您完成这个任务:

```
// Using Object.assign method:
const newState = Object.assign({}, state, {[item.id]: item});

// Or using ES6 spread syntax:
const newState = { ...oldState, prop1: "modified value" };

```

在为阵列设置状态时，最好的方法是重新创建要用其更改来修改的阵列。这是我所知道的修改阵列的最好方法之一:

```
const [myValues, setMyValues] = useState([1,2,3,4,5]);

// Copying new set of arrays using ES6 spread syntax
const newItems = [...myValues];
newItems[2] = 55; //modifying specific array element 

setMyValues(newItems); //set the new array with modified values

```

这是[在实时](https://codepen.io/reciosonny/pen/BaQyJdG)中的样子。

在这个示例代码片段中，我们有效地确保我们重新创建了该数组，然后在我们想要更改的特定元素中应用更改。通过这个方法，我们让 React 知道一个状态正在以一种不可变的方式被改变。这将触发组件“重新渲染”的引擎盖下。

## 在儿童组件道具中传递`useState`来使用它

在另一个组件中将`useState`作为道具传递是完全可能的。但是这样做没有任何好处，因为您可以通过在 JavaScript 代码顶部导入 React 来调用`useState`,并在所有组件中调用它。

下面是演示这一点的示例代码片段:

```
import React, { Component, useState } from 'react';
import { hot } from "react-hot-loader";

const NewComponent = ({ useStateFn }) => {

  const [val, setVal] = useStateFn(0); //we used useState from the props passed onto this component

  return (
                <div>

            <h2>Value: {val}</h2>
            <br/><br/>
            <button onClick={() => setVal(25)}>Change value</button>
          </div>
        );
}

const App = () => {

  return (
    <div>
      <h1>Hello world!</h1>

                        {/* We passed useState in child component for them to be consumed */}
      <NewComponent useStateFn={useState} />      
    </div>
  )
} 

```

这是一种不好的做法，你永远不应该这样使用`useState`。此外，这可能会引入大量代码，使应用程序更难修复。像躲避瘟疫一样躲避它。

## 不要将`useState`放在组件体或函数的顶部

根据官方 [React 文档](https://reactjs.org/docs/hooks-rules.html):

> 不要在循环、条件或嵌套函数中调用钩子。相反，总是在 React 函数的顶层使用钩子

因为`useState`是一个钩子，我们需要把它放在组件的顶层，所以把它放在顶层以外的地方可能会在组件结构中引入混乱。

不要这样做:

```
const App = () => {

  const onValueChanged = (input) => {
    setVal(input);
  }

  const [val, setVal] = useState(0);

  return (
    <div>
      <h1>Hello world!</h1>
    </div>
  )
}

```

执行以下操作:

```
const App = () => {

  const [val, setVal] = useState(0);

  const onValueChanged = (input) => {
    setVal(input);
  }

  return (
    <div>
      <h1>Hello world!</h1>
    </div>
  )
}

```

使用这个最佳实践将允许我们在应用程序变大时避免与调用状态相关的潜在错误。

## 在类组件或常规 JavaScript 函数中使用`useState`

如果你读过 React 官方文档中钩子的[规则，它们鼓励你不要在类或常规 JavaScript 函数中放置任何像`useState`这样的钩子。这是因为钩子不能很好地处理这些问题，尤其是在基于类的组件结构中。](https://reactjs.org/docs/hooks-rules.html)

假设您仍然坚持在基于类的组件上使用`useState`，如下例所示:

```
class App extends Component {

  render() {

    const [inputVal, setInputVal] = useState("");

    return (
      <div>
        <input type="text" onChange={(e) => setInputVal(e.target.value)} />

        <h1>Input value: {inputVal}</h1>
      </div>
    );
  }
}

```

这是你将会看到的:

对于这个问题，React 将立即通知您在基于类的组件上使用钩子是无效的用例。这意味着你不可能在它上面使用像`useState`这样的钩子。

还有其他一些微妙的用例，但是使用了错误的`useState`实现，比如在普通函数表达式中使用它。这里有一个例子。

```
const myFunction = (arg1, arg2, arg3) => {
  const [myStateValue, setMyStateValue] = useState("");

  //do logic here...
}

```

如果你还记得的话，钩子的规则是这样说的:

> 不要从常规的 JavaScript 函数中调用钩子

那么这就是对`useState`的无效使用，除非我们将该函数用作[自定义钩子](https://reactjs.org/docs/hooks-custom.html)。自定义钩子也只是一个 JavaScript 函数，只是这次它有自己的生命周期，比如添加`useEffect`来跟踪它的状态变化。

因此，您可以通过构造一个自定义钩子来更好地利用`useState`,而不是常规函数:

```
function useUpdateUserAccount(updatedUserAccount) {
  const [userState, setUserState] = useState(null);

  useEffect(() => {
    function handleStatusChange(user) {
                        setUserState(user);
    }

    UserAPI.updateAccount(updatedUserAccount, handleUserChange);
    return () => {

    };
  }, []);

  return userState;
}

```

在这个场景中，由于像`useEffect`这样的附加钩子，我们现在拥有了一个函数的完整生命周期。现在，它可以作为一个定制的钩子，跨您可能拥有的不同组件使用。这甚至可能是创建自己的商店的开始，而不是依赖 Redux 来获得更简单的用例。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

不要忘记在函数名前加上前缀`use`，这样你就遵循了钩子的规则！

## 将`setState`函数传递给子组件以设置父状态

这与在子组件中传递`useState`基本上是一样的糟糕做法。这一次，我们只传递 setState 函数来设置父组件的状态。

这是可能做到的。但是，这是一种不好的做法，并且在应用程序扩展的过程中可能会引入意想不到的副作用。

它也不容易阅读，可能会导致混乱，尤其是当组件与复杂的用例纠缠在一起的时候。

所以，与其这样做:

```
const NewComponent = ({ setValFn }) => {

  return (<div>
    <button onClick={() => setValFn(25)}>Change value</button>
  </div>);
}

const App = () => {
  const [val, setVal] = useState(0);

  return (
    <div>
      <h2>Value: {val}</h2>
      <br/><br/>

      <NewComponent setValFn={setVal} />      
    </div>
  )
}

```

执行以下操作:

```
const NewComponent = ({ onChangeValue }) => {

  return (<div>
    <button onClick={() => onChangeValue(25)}>Change value</button>
  </div>);
}

const App = () => {

  const [val, setVal] = useState(0);

  const onValueChanged = (input) => {
    setVal(input);
  }

  return (
    <div>
      <h2>Value: {val}</h2>
      <br/><br/>

      <NewComponent onChangeValue={onValueChanged} />      
    </div>
  )
}

```

你基本上是在做和以前一样的事情，我们打算设置一个父组件的状态。只是这一次，后一种方法是从子组件向父组件发出事件。然后，让父组件设置状态。

## 不使用数组析构来使用`useState`

你可能没有意识到，但是你可以这样使用`useState`:

```
const count = useState[0];
const setCount = useState[1];

```

这是因为像`useState`这样的钩子实际上是一个数组，它在每个元素中返回以下实现:

1.  初始化状态值:您在它的函数中传递的值。它可以是值、字符串、对象、数组等。)
2.  函数来设置您的状态

官方的 React 文档更喜欢你使用数组析构来代替，因为无论何时你声明一个状态钩子，它都更干净，更容易阅读。此外，他们使用数组析构，这适合他们优雅地声明状态的用例。

这并不意味着你用错了`useState`,但是不使用 ES6 的析构是去掉了如何声明`useState`的语法糖，更不用说你还额外添加了一行代码来声明它们。

我们可以看到官方的 [React 文档](https://reactjs.org/docs/hooks-overview.html)关于他们喜欢如何使用 ES6 数组析构来调用`useState`，就像这样:

```
const [count, setCount] = useState(0); //Array destructuring

```

要点:现在至少你知道可以用数组的方式使用 useState 了！

## 仅依靠`useState`来管理大型应用程序中的状态

在组件逻辑和简单用例中依靠`useState`应该没有问题。但是如果我们的整个应用程序只包含用于管理状态的`useState`,那么从长远来看，我们可能会有一个问题，这是由于复杂性和涉及不止两个组件的用例。

不仅仅需要使用`useState`的用例包括:

1.  如果在几个组件中需要一个状态
2.  如果一个应用程序扩展
3.  如果我们需要一个全球商店

如果我们仅仅依靠`useState`并且仅仅将状态传递给组件道具，我们可能会以“道具钻探”问题而告终。此外，如果我们要添加与身份验证和安全性相关的逻辑(这对于在某个时刻将用户会话保持在某个状态是必要的)，那么我们将需要更好的状态管理，以便在包含不同组件的不同页面上正确地存储和使用逻辑。

Redux 甚至 Context API 之类的状态管理库在大型应用程序上提供了显著的优势，因为它们可以跨不同的组件共享状态。它们通常伴随着浏览器工具来跟踪在几个组件中传递的是什么状态。

这使得共享和检查逻辑变得更加容易，因为通过使用状态管理解决方案(如 Redux)启用了复杂的工具。

那么，对于更大规模的 app 和 state，在多个组件中需要哪个呢？选择状态管理，比如 Redux。但是有几个状态管理解决方案可供选择，从 Flux 到 Context API。

用自定义挂钩怎么样？有可能。但是为了安全起见，对于更大的用例，最好依赖 Redux。

## 结论

得益于钩子，React 现在很灵活。最特别的是，多亏了`useState`,我们不再需要仅仅依靠基于类的组件来构建我们的 UI 组件。

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