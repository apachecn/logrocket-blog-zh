# React 中对迭代、上下文和子元素的深入探究

> 原文：<https://blog.logrocket.com/deep-dive-iterating-context-children-react/>

熟悉 React 最棘手的部分包括遍历信息集合、在应用程序中传递数据以及使用`props.children`。在本教程中，我们将详细回顾这三个概念，用几个相关的例子介绍它们的内部工作原理。

为了跟随本教程，您可以[派生这个 CodeSandBox](https://codesandbox.io/s/iterating-starter-code-4e64x?file=/src/data/data.js) 。你也可以[看看这个 GitHub 要点](https://gist.github.com/AlexMercedCoder/b4d86790176f2f5c7b374235cf3dc23c)。我们开始吧！

## 迭代反应子级

通常，我们会处理大量的数据，然后需要将这些数据呈现到 UI 中。通过使用循环或迭代，我们可以使这项任务变得容易得多，但是，开发人员在决定迭代什么时经常会感到困惑。

在 React 中，我们可以将 JSX 表达式注入 UI，但我们也可以注入 JSX 数组，这意味着当我们迭代数据时，我们希望最终产生一个数组。

在我们的开始代码中，你会注意到我们的`/src/data/data.js file`中有关于[最初的 150 个口袋妖怪](https://www.giantbomb.com/profile/wakka/lists/the-150-original-pokemon/59579/)的数据作为例子。用以下样板文件创建一个名为`src/components/Pokemon.js`的新文件:

```
function Pokemon(props) {
  return <div>Pokemon</div>;
}

export default Pokemon;

```

现在，让我们将组件导入到我们的应用程序中，以便它是可见的:

```
import Pokemon from "./components/Pokemon";
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <Pokemon />
    </div>
  );
}

```

让我们看看如何使用传统的`for`循环来循环我们的口袋妖怪:

*   创建一个空数组
*   对于数组中的每一项，我们将把一个 JSX 表达式放入数组中
*   我们将在组件返回的 JSX 中呈现数组

将以下代码添加到`Pokemon.js`:

```
import data from "../data/data";

function Pokemon(props) {
  //array to hold jsx for pokemon
  const pokemonJSX = [];

  //loop over data
  for (let index = 0; index < data.length; index++) {
    // variable with current pokemon
    const poke = data[index];
    // push jsx into array
    pokemonJSX.push(
      <section key={poke.name}>
        <h1>{poke.name}</h1>
        <img src={poke.img} alt={poke.name} />
      </section>
    );
  }

  // render data in jsx
  return <div>{pokemonJSX}</div>;
}

export default Pokemon;

```

注意 section 标签中的`key` prop。每当我们创建一个 JSX 数组时，顶级元素应该总是有一个具有唯一值的`key` prop，帮助 React 更高效地重新呈现这些数组。

虽然这个过程有效，但是有点冗长。我们可以用一个`for of`循环来清理我们的代码，就像这样:

```
import data from "../data/data";

function Pokemon(props) {
  //array to hold jsx for pokemon
  const pokemonJSX = [];

  //loop over data
  for (let poke of data) {
    // push jsx into array
    pokemonJSX.push(
      <section key={poke.name}>
        <h1>{poke.name}</h1>
        <img src={poke.img} alt={poke.name} />
      </section>
    );
  }

  // render data in jsx
  return <div>{pokemonJSX}</div>;
}

export default Pokemon;

```

我们仍然得到我们想要的结果，但是，UI 逻辑变得有点分散，而不是保持在组件的返回值中。我们可以使用数组方法`map`，作为一种更平滑的方法来遍历数组:

```
Array.map((item, index) => {})

```

`map`方法接受一个函数，数组中的每一项都传递给这个函数。在每次迭代中，都会为返回值创建一个新数组。如果我们传递`map`，一个返回期望的 JSX 的函数，它将返回我们的 JSX 数组，我们将不必担心声明一个数组或将值推入其中。

在我们的示例中，上面的过程类似于下面的代码:

```
import data from "../data/data";

function Pokemon(props) {
  // render data in jsx
  return (
    <div>
      {data.map((poke) => (
        <section key={poke.name}>
          <h1>{poke.name}</h1>
          <img src={poke.img} alt={poke.name} />
        </section>
      ))}
    </div>
  );
}

export default Pokemon;

```

现在，我们所有组件的视图逻辑都在一个地方。虽然，有时候很难理解这个`map`。在同样使用 JSX 的框架 [SolidJS](https://www.solidjs.com/) 中，有一个用于循环数据的`built`组件。我在我的 React 库中创建了一个类似的组件 [merced-react-hooks](https://www.npmjs.com/package/merced-react-hooks) ，它被加载到启动代码中。

使用`loop`组件，我们可以将地图抽象如下:

```
import data from "../data/data";
import { Loop } from "merced-react-hooks";

function Pokemon(props) {
  // render data in jsx
  return (
    <div>
      <Loop
        withthis={data}
        dothat={(poke) => (
          <section key={poke.name}>
            <h1>{poke.name}</h1>
            <img src={poke.img} alt={poke.name} />
          </section>
        )}
      />
    </div>
  );
}

export default Pokemon;

```

现在，我们看到了代码中的巨大差异，但一些额外的语义可能会使思考变得更容易。

### 在迭代中使用组件

我们可以通过创建一个负责渲染单个口袋妖怪的组件来使我们的代码更加简洁。用以下代码创建一个名为`src/components/OnePokemon.js`的新文件:

```
function OnePokemon(props) {

  const poke = props.poke;

  return (
    <section key={poke.name}>
      <h1>{poke.name}</h1>
      <img src={poke.img} alt={poke.name} />
    </section>
  );
}

export default OnePokemon

```

现在，我们可以清理口袋妖怪组件，让它更容易理解。将以下代码添加到`Pokemon.js`:

```
import data from "../data/data";
import { Loop } from "merced-react-hooks";
import OnePokemon from "./OnePokemon";

function Pokemon(props) {
  // render data in jsx
  return (
    <div>
      <Loop
        withthis={data}
        dothat={(poke) => <OnePokemon poke={poke} key={poke.name} />}
      />
    </div>
  );
}

export default Pokemon;

```

目前，`Pokemon`只处理口袋妖怪数据的迭代。`OnePokemon`处理一个口袋妖怪将如何出现。以这种方式编写代码有助于更清晰地分离关注点，因此更容易阅读组件文件。

## `Props.children`

`children`是唯一一个不需要以如下方式显式声明的属性:

```
<Component prop1="value1" prop2="value2">This text is passed as the children prop</Component>

```

相反，`children`属性由开始和结束组件标记之间的所有内容组成。创建一个新文件`src/components/Children.js`。将以下样板文件添加到`children.js`:

```
function Children(props) {

  console.log(props.children);

  return <h1>Children</h1>;
}

export default Children;

```

让我们使用 App.js 中的`children.js`组件。更新 App.js 后检查`console.log`:

```
import Pokemon from "./components/Pokemon";
import Children from "./components/Children";
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <Children>
        This text is the children prop
      </Children>
      <Pokemon />
    </div>
  );
}

```

开始和结束标记之间的代码作为`children`属性传递，因此被记录。这可以非常灵活地使用。尝试以下代码，然后检查`console.log`的结果:

```
<Children>
        {[1,2,3,4,5]}
</Children>

```

现在，`children`应该是本阵:

```
<Children>
        {() => {console.log("hello world")}}
</Children>

```

`children`现在应该是函数:

```
<Children>
        Some text before the function
        {() => {console.log("hello world")}}
</Children>

```

`children`现在应该是一个数组，文本作为第一个元素，函数作为第二个元素。本质上，您可以将任何类型的数据作为`children`属性传递，甚至可以将多个值作为`children`属性传递。

我们可以利用这一点。按如下方式再次更新代码:

```
      <Children>
        {(props) => <h1>{props.word}</h1>}
      </Children>

```

注意，这个函数看起来像一个组件，一个接收道具并返回 JSX 的函数。让我们重构`Children.js`来利用这一点:

```
function Children(props) {
  console.log(props.children);

  return <div><props.children word="it works"/></div>;
}

export default Children;
```

我们能够将`children` prop 用作组件，因为它是一个符合组件规则的函数。

`children`允许我们使用创建一些非常独特的实用组件。我创建了一个名为`createTransform`的函数来简化 merced-react-hooks 库中的过程。`createTransform`制造对其子组件执行转换的组件，如调整日期格式或大小写。

让我们使用`createTransform`使每个口袋妖怪的名字都是大写字母和粉红色的:

```
import { createTransform } from "merced-react-hooks";

// Create Transform Component that takes child and returns replacement
const AllCapsPink = createTransform((c) => (
  <span style={{ color: "pink" }}>{c.toUpperCase()}</span>
));

function OnePokemon(props) {

  const poke = props.poke;

  return (
    <section key={poke.name}>
      <h1>
        <AllCapsPink>{poke.name}</AllCapsPink>
      </h1>
      <img src={poke.img} alt={poke.name} />
    </section>
  );
}

export default OnePokemon;

```

## 在您的应用中交付数据

我们还可以在应用程序中传递数据作为道具，但是，随着组件树变得越来越大，这可能会变得非常乏味。如果您有在三个或更多组件之间共享的数据，您可能希望在 React 中使用一个名为 Context 的[特性，使它对您的整个应用程序可用。](https://blog.logrocket.com/how-to-use-react-context-with-typescript/)

我们可以有多个上下文来交付不同类型的数据。我们将创建一个上下文来传递一些主题数据。本质上，我们将遵循以下模式:

*   创建一个新的`context`对象
*   如果数据可能随着我们想要共享的数据而改变，则创建一个变量或状态
*   创建一个自定义组件来包装提供者。提供者是负责使上下文可用的组件
*   创建一个自定义挂钩，以便在我们的应用程序中轻松使用上下文

如下创建`src/context/Theme.js`:

```
import { createContext, useContext } from "react";

// The Data to be Shared
const theme = {
  backgroundColor: "navy",
  color: "white",
  border: "3px solid brown"
};

// create a new context object
const ThemeContext = createContext(theme);

// custom provider wrapper component
export const ThemeProvider = (props) => {
  // value prop determines what data is shared
  return (
    <ThemeContext.Provider value={theme}>
      {props.children}
    </ThemeContext.Provider>
  );
};

// custom hook to retreive data
export const useTheme = () => useContext(ThemeContext)

```

现在，我可以将包装组件导入到`Index.js`:

```
import { StrictMode } from "react";
import ReactDOM from "react-dom";
import { ThemeProvider } from "./context/Theme";

import App from "./App";

const rootElement = document.getElementById("root");
ReactDOM.render(
  <ThemeProvider>
    <StrictMode>
      <App />
    </StrictMode>
  </ThemeProvider>,
  rootElement
);

```

现在，我们可以使用自定义钩子轻松地将数据拉到我们需要的任何地方。让我们回到`OnePokemon.js`:

```
import { createTransform } from "merced-react-hooks";
import { useTheme } from "../context/Theme";

// Create Transform Component that takes child and returns replacement
const AllCapsPink = createTransform((c) => (
  <span style={{ color: "pink" }}>{c.toUpperCase()}</span>
));

function OnePokemon(props) {
  // bring in theme data from context
  const theme = useTheme();

  const poke = props.poke;

  return (
    <section style={theme} key={poke.name}>
      <h1>
        <AllCapsPink>{poke.name}</AllCapsPink>
      </h1>
      <img src={poke.img} alt={poke.name} />
    </section>
  );
}

export default OnePokemon;

```

## 使用 React 上下文交付状态

让我们创建另一个上下文，它将提供与计数器相关的状态。

*   创建新的上下文
*   创建一个新的包装组件，该组件声明状态并定义一个函数以所需的方式改变该状态
*   `Provider`被赋予一个具有状态和支持功能的对象
*   定制钩子将数据传送到需要的地方

将以下代码添加到`Counter.js`:

```
import { createContext, useContext, useState } from "react";

// create a new context object
const CounterContext = createContext(null);

// custom provider wrapper component
export const CounterProvider = (props) => {

  // state to share and functions to update state
  const [counter, setCounter] = useState(0)

  const addOne = () => setCounter(counter + 1)

  const minusOne = () => setCounter(counter - 1)

  //package state and functions to be shipped by provider
  const providedValue = {counter, addOne, minusOne}

  // value prop determines what data is shared
  return (
    <CounterContext.Provider value={providedValue}>
      {props.children}
    </CounterContext.Provider>
  );
};

// custom hook to retreive data
export const useCounter = () => useContext(CounterContext)

```

将`Provider`包裹在`index.js`中:

```
import { StrictMode } from "react";
import ReactDOM from "react-dom";
import { ThemeProvider } from "./context/Theme";
import { CounterProvider } from "./context/Counter";

import App from "./App";

const rootElement = document.getElementById("root");
ReactDOM.render(
  <CounterProvider>
    <ThemeProvider>
      <StrictMode>
        <App />
      </StrictMode>
    </ThemeProvider>
  </CounterProvider>,
  rootElement
);

```

现在，创建`src/components/Counter.js`并如下使用`CounterContext`:

```
import { useCounter } from "../context/Counter";

function Counter(props) {
  // get counter and supporting functions
  const { counter, addOne, minusOne } = useCounter();

  //return JSX
  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={addOne}>Add</button>
      <button onClick={minusOne}>Minus</button>
    </div>
  );
}

export default Counter

```

让我们在`App.js`中使用这个新组件:

```
import Pokemon from "./components/Pokemon";
import Children from "./components/Children";
import Counter from "./components/Counter"
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <Counter/>
      <Counter/>
      <Children>This text is the children prop</Children>
      <Pokemon />
    </div>
  );
}

```

请注意，两个计数器都会更新，因为它们没有任何内部状态。相反，计数器组件的每个实例都从我们通过上下文交付的相同共享状态中提取，这是一种帮助简化应用程序状态管理的强大方法。

## 结论

有效地迭代，使用`children` prop，并通过 React 上下文在应用程序中传递数据，将允许您更容易地利用 React 的强大功能。在本文中，我们深入讨论了这三个主题领域，同时查看了几个代码示例。我希望你喜欢这篇文章。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)