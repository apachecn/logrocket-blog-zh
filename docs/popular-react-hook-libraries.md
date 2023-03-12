# 流行的 React 钩子库

> 原文：<https://blog.logrocket.com/popular-react-hook-libraries/>

React 生态系统的旅程真的很有趣。自从 React 16.3 中的[时间切片和悬念等功能出现以来，我们已经有了一系列来自牛逼的 React 团队的有趣概念，但没有一个像 React Hooks 那样引人注目，React Hooks 在](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html) [React 16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) 中首次稳定发布。

提供一种更简洁的方式来编写代码，同时不必担心向后兼容性问题，这意味着可以肯定地说，钩子将会继续存在。在这篇博文中，我将描述钩子是如何成为救生员的。我将举例说明几个用例，这些用例以流行的 React Hook 库为特色——包括主流的和定制的(由像你我这样的爱好者创建的)。让我们开始吧。

## 什么是 React 钩子？

基本上，钩子提供了传递状态和属性的媒介，而不必创建类组件。采用基于功能的方法，通过钩子，我们可以将逻辑从 UI 中分离出来，这样它也可以在应用程序的其他部分重用。看看下面的两个代码示例:

```
import React, { Component } from "react";
class MovieButton extends Component {
  constructor() {
    super();
    this.state = { buttonText: "Click to purchase movie tickets" };
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    this.setState(() => {
      return { buttonText: "Enjoy your movie!" };
    });
  }
  render() {
    const { buttonText } = this.state;
    return <button onClick={this.handleClick}>{buttonText}</button>;
  }
}
export default MovieButton
```

上面的要点显示了当点击按钮时，`setState`如何改变`MovieButton`的内部状态。使用钩子，可以描述这种内部状态变化，而不必依赖于类、构造函数或 setState:

```
import React, { useState } from "react";
export default function MovieButton() {
  const [buttonText, setButtonText] = useState("Click to purchase movie tickets");
  function handleClick() {
    return setButtonText("Enjoy your movie!");
  }
  return <button onClick={handleClick}>{buttonText}</button>;
}
```

我选择先展示`useState`，因为它是引入 React 生态系统的第一个[。`useState`用于管理组件的本地状态，并在重新渲染之间保存它。令人着迷的是，该组件不必是 ES6 类组件——一个基本的 JavaScript 函数就可以了，我们完成了同样的事情，同时减少了十行代码。通过包含一对变量来实现`useState`——一个表示组件的实际开始状态，另一个表示您希望组件的状态更新到什么状态。](https://reactjs.org/docs/hooks-intro.html)

## 主流的 React 钩子库

### 状态和数据提取

假设我想只用钩子创建一个应用程序。最有可能的是，我必须在某个时候获取数据。一个好的方法是从需要定义状态的地方开始定义状态。首先，我将创建一个组件，并从 API 中获取数据，由该组件呈现:

```
import React, { useState, useEffect } from "react";

const URL = "https://api.punkapi.com/v2/beers";
export default function Landing() {
  const [beer, setBeer] = useState([]);
  useEffect(() => {
    fetch(URL)
      .then(response => response.json())
      .then(beer => setBeer(beer));
  });
}
```

这就把我们带到了`useEffect`钩。`useEffect`钩子允许您直接在功能组件内部处理生命周期事件。像设置订阅和获取数据这样的活动，我们可以使用生命周期方法如`componentDidMount()`来完成，现在可以通过`useEffect`来处理。根据 [React 的文档](https://reactjs.org/docs/hooks-effect.html):

> useEffect 与 React 类生命周期方法中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 的作用相同，但是统一到一个 API 中

所以在上面的例子中，我没有使用类组件，而是创建了一个函数，并在`useEffect`中调用了`fetch`方法。这里也不需要使用`this.setState`来更新状态，因为我创建了`setBeer`，一个从`useState`钩子中提取的随机函数。

如果您一直遵循这一点，并尝试使用上面的代码示例运行应用程序，您应该会遇到一个非常难看的无限循环:

 [https://www.youtube.com/embed/5w0snGY1w2Q?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/5w0snGY1w2Q?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

为什么？`useEffect`的作用与`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`相同。因为`setBeer()`在每次数据获取后更新`beer`的状态，所以组件被更新并且`useEffect`继续再次获取数据。

为了避免这个 bug，我们需要通过提供一个空数组作为`useEffect`的第二个参数来指定我们只想在组件挂载时获取数据:

```
import React, { useState, useEffect } from "react";

const URL = "https://api.punkapi.com/v2/beers";
export default function Landing() {
  const [beer, setBeer] = useState([]);
  useEffect(() => {
    fetch(URL)
      .then(response => response.json())
      .then(beer => setBeer(beer));
  }, {});
}
```

### 表单处理

通过定制钩子(目前在生态系统中有大量的钩子)，React 允许您重用和共享少量的逻辑。根据经验，当一个组件中有大量逻辑时，这是一个信号，表明您应该重构它并分发一些逻辑以避免组件膨胀。让我们依靠自定义挂钩来创建某种与我们的应用程序的交互性——比如一个用户可以提交数据的表单。react-hook-form 是一个完全用钩子构建的库，提供表单验证。我们会像安装 npm 包一样将它包含在我们的应用程序中:

```
npm i react-hook-form

```

然后导入我们需要的自定义钩子——`useForm`:

```
import React from "react";
import useForm from "react-hook-form";

const active = {
  fontSize: "15px"
};
export default function Purchase() {
  const { register, handleSubmit, errors } = useForm();
  const onSubmit = data => {  // upload the data retreived from the form to a database, return value to a user, etc
    console.log(data);
  };

  return (
    <div>
      <form onSubmit={handleSubmit(onSubmit)}>
        <label>Full Name</label>
        <input name="fullname" ref={register} />
        <label>Beer Name</label>
        <input
          name="beerName"
          ref={register({ required: true, maxLength: 10 })}
        />

        <select style={active} name="Title" ref={register({ required: true })}>
          <option value="">Select...</option>
          <option value="six-pack">Six Pack</option>
          <option value="twelve-pack">Twelve Pack</option>
        </select>
        <label>
          <input type="checkbox" placeholder="+18" name="+18" ref={register} />I
          am 18 and above
        </label>
        {errors.beerType && <p>This field is required</p>}
        <input type="submit" value="Pay Here" />
      </form>
    </div>
  );
}
```

工作原理概述:

 [https://www.youtube.com/embed/ojVqNiAJdGM?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/ojVqNiAJdGM?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 按指定路线发送

该应用程序正在逐步扩展，在这一点上，如果能包含每个具有多个组件的应用程序所需的内容(路线)就太好了。我们将利用`[hooksrouter](https://www.npmjs.com/package/hookrouter)`——一个很棒的库，它导出一个定制的钩子`useRoutes`:

```
npm i hookrouter
```

`useRoutes`评估预定义的路线对象，并在路线匹配时返回结果:

```
import React from "react";
import Purchase from "./components/Purchase";
import Landing from "./components/Landing";
import HomePage from "./components/HomePage";
const Routes = {
  "/": () => ,
  "/purchase": () => ,
  "/landing": () => 
};

export default Routes;
```

这减少了我们在使用传统 react 路由器时必须编写的过多代码，因为我们会在应用程序中为所有单独的路线渲染`<Route/>`组件，并在其中传递属性。现在，我们所要做的就是导入`Routes`组件并将其传递给`useRoutes`钩子:

```
// index.js or where you choose to render your entire app from
import { useRoutes } from "hookrouter";
import Routes from "./router";

function App() {
  const routeResult = useRoutes(Routes);
  return <div>{routeResult}</div>;
}
```

让我们看看在应用程序中导航是什么感觉:

 [https://www.youtube.com/embed/0shA2ukTvsY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/0shA2ukTvsY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 处理复杂的状态管理

当然`useState`是用来管理状态的，但是如果你的应用程序变得越来越复杂，你必须在一个状态对象中处理多个状态转换，那该怎么办呢？这正是`useReducer`钩子的用处。当您必须处理多个对象或数组中的数据，并且还要保持这些数据的可维护性和可预测性时，`useReducer`是首选。为了描述这个`useReducer`钩子，我将在应用程序中添加一个具有多状态架构的页面——也许是一个用户可以创建自己的啤酒配方的地方:

```
import React, { useReducer } from "react";

const myStyle = {
  color: "white",
  fontSize: "20px"
};

export default function Recipe() {
  const initialState = {
    RecipePrice: 0,
    recipe: {
      price: 100,
      name: "Oompa Loompa",
      image:
        "https://res.cloudinary.com/fullstackmafia/image/upload/v1568016744/20110111-132155-Homebrew-Grain_uihhas.jpg",
      ingredients: []
    },
    stockpile: [
      { id: "1", name: "Extra Pale Malt", price: 10 },
      { id: "2", name: "Ahtanum Hops", price: 6 },
      { id: "3", name: "Wyeast 1056", price: 8 },
      { id: "4", name: "Chinook", price: 5 }
    ]
  };
  const reducer = (state, action) => {
    switch (action.type) {
      case "REMOVE_ITEM":
        return {
          ...state,
          RecipePrice: state.RecipePrice - action.item.price,
          recipe: {
            ...state.recipe,
            ingredients: state.recipe.ingredients.filter(
              y => y.id !== action.item.id
            )
          },
          stockpile: [...state.stockpile, action.item]
        };
      case "ADD_ITEM":
        return {
          ...state,
          RecipePrice: state.RecipePrice + action.item.price,
          recipe: {
            ...state.recipe,
            ingredients: [...state.recipe.ingredients, action.item]
          },
          stockpile: state.stockpile.filter(x => x.id !== action.item.id)
        };
      default:
        return state;
    }
  };

  const [state, dispatch] = useReducer(reducer, initialState);

  const removeFeature = item => {
    dispatch({ type: "REMOVE_ITEM", item });
  };

  const addItem = item => {
    dispatch({ type: "ADD_ITEM", item });
  };

  return (
    <div className="boxes" style={myStyle}>
      <div className="box">
    <h4>Ingredients Stockpile</h4>
        <figure>
          <img width={"300px"} src={state.recipe.image} alt="my recipe" />
        </figure>
        <h2>{state.recipe.name}</h2>
        <pre>Amount: ${state.recipe.price}</pre>
        <div className="content">
          <h5>Added ingredients:</h5>
          {state.recipe.ingredients.length ? (
            <ol type="1">
              {state.recipe.ingredients.map(item => (
                <li key={item.id}>
                  <button
                    onClick={() => removeFeature(item)}
                    className="button"
                  >
                    REMOVE FROM LIST
                  </button>
                  {item.name}
                </li>
              ))}
            </ol>
          ) : (
            <pre>You can purchase items from the stockpile.</pre>
          )}
        </div>
      </div>
      <div className="box">
        <div className="content">
          {state.stockpile.length ? (
            <ol type="1">
              {state.stockpile.map(item => (
                <li key={item.id}>
                  <button onClick={() => addItem(item)} className="button">
                    ADD TO LIST
                  </button>
                  {item.name} (+{item.price})
                </li>
              ))}
            </ol>
          ) : (
            <pre>Nice looking recipe!</pre>
          )}
        </div>

        <div className="content">
          <h4>Total Amount: ${state.recipe.price + state.RecipePrice}</h4>
        </div>
      </div>
    </div>
  );
}
```

如果您熟悉 Redux，您会在上面的代码示例中认出`line 54`，其中`useReducer`接受一个带有组件初始状态和动作的 reducer 通常是一个用于根据需要更新组件状态的调度方法。因此，使用 reducers，我们可以将多个状态合并成一个状态，而不是创建一个以上的单个状态挂钩。让我们看看这个组件是如何工作的:

 [https://www.youtube.com/embed/yLiC3nEM7o0?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/yLiC3nEM7o0?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 挂钩收藏

自从 Hooks 发布以来，React 社区的热情令人惊叹。大量的定制钩子被创造出来，描绘出令人敬畏的功能。你一定要看看的自定义 React 钩子集合包括:

[React 钩子集合](https://github.com/nikgraf/react-hooks)，包含 300 多个定制钩子——其中最受欢迎的是`useArray`——一个为数组操作提供多种方法的钩子，数组操作是开发人员的日常工作。让我们更新我们的应用程序，加入`useArray`钩子:

```
import React from "react";
import { useArray } from "react-hanger";

const myStyle = {
  color: "white"
};
export default function App() {
  const todos = useArray(["35cl", "50cl", "60cl"]);
  return (
    <div style={myStyle}>
      <h3>Measures</h3>
      <button
        onClick={() =>
          todos.add(Math.floor(Math.random() * (60 - 35 + 1)) + 35 + "cl")
        }
      >
        CUSTOM
      </button>

      <ul>
        {todos.value.map((todo, i) => (
          <div>
            <li key={i}>{todo}</li>
            <button onClick={() => todos.removeIndex(i)}>
              Remove from list
            </button>
          </div>
        ))}
      </ul>
      <button onClick={todos.clear}>clear</button>
    </div>
  );
}
```

让我们看看它是如何工作的:

 [https://www.youtube.com/embed/dN3BSnw8NgU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/dN3BSnw8NgU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

我真正感兴趣的另一个集合是`[useHooks](https://github.com/gragland/usehooks)`，它包含了`useLockBodyScroll`，一个阻止用户滚动特定组件的钩子。我观察到这个钩子与 React 内置的`useLayoutEffect`钩子一起工作——它从 DOM 读取布局并同步重新渲染。要实现`useLockBodyScroll`，首先需要将其定义为一个函数:

```
import { useLayoutEffect } from "react";

export default function useLockBodyScroll() {
  useLayoutEffect(() => {
    // Get original value of body overflow
    const originalStyle = window.getComputedStyle(document.body).overflow;
    // Prevent scrolling on mount
    document.body.style.overflow = "hidden";
    // Re-enable scrolling when component unmounts
    return () => (document.body.style.overflow = originalStyle);
  }, []); // Empty array ensures effect is only run on mount and unmount
}
```

然后将其导入到所需的组件中:

```
import useLockBodyScroll from "./useLockBodyScroll";

export default function Landing() {
    useLockBodyScroll();
    const [data, setData] = useState([]);
    useEffect(() => {
        fetch(URL)
            .then(response => response.json())
            .then(data => setData(data));
    }, []);
    return ( <
        div >
        <
        button >
        <
        A style = {
            {
                textDecoration: "none"
            }
        }
        href = "/" >
        HOME <
        /A>{" "} <
        br / >
        <
        /button> {
            data.map(item => ( <
                Item.Group key = {
                    item.id
                }
                style = {
                    divStyle
                } >
                <
                Item >
                <
                Item.Image width = "80"
                size = "tiny"
                src = {
                    item.image_url
                }
                alt = "Beer Flask" /
                >
                <
                Item.Content >
                <
                Item.Header > {
                    item.name
                } < /Item.Header> <
                Item.Extra > {
                    item.tagline
                } < /Item.Extra> <
                Item.Meta style = {
                    {
                        lineHeight: 1.5
                    }
                } > {
                    item.description
                } <
                /Item.Meta> <
                /Item.Content> <
                /Item> <
                /Item.Group>
            ))
        } <
        /div>
    );
}
```

让我们看看它是如何工作的。浏览器中应该没有滚动条:

 [https://www.youtube.com/embed/-0SG_EYI-0g?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/-0SG_EYI-0g?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

现在，我们的应用程序已经完成了。我是不是忘了一件你觉得超级重要的事？欢迎您改进 CodeSandbox 中的演示。

摘要

## 我认为勾手是很长一段时间内最伟大的反应。尽管到目前为止已经取得了很大成就，但我们还有很多事情要做。在 React 爱好者中，某些论坛一直在争论 React 提供创建定制钩子的工具会导致生态系统中钩子的过载——类似于 jQuery 插件发生的情况。你对钩子有什么看法？你最近发现了哪些很棒的钩子？请在下面的评论中告诉我。干杯。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).