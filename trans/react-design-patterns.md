# React 设计模式指南

> 原文：<https://blog.logrocket.com/react-design-patterns/>

***编者按:*** *本 React 组件指南最后一次更新是在 2022 年 12 月 21 日，添加了关于渲染道具模式和状态缩减器模式的信息。查看这篇文章* *了解更多关于 React 钩子的信息。*

设计模式是常见软件开发问题的解决方案模板。在 React 中，它们是解决 React 开发人员遇到的常见问题的成熟方法。

随着 React API 的发展，出现了新的模式，开发人员通常更喜欢新模式而不是旧模式。在本文中，我们将了解 2022 年一些有用的 React 设计模式。

以下是我们将要介绍的内容:

让我们开始吧。

## 反应组件设计模式

在这一节中，我们将看看顶级的 React 组件设计模式。这个列表包括一些最流行的 React 设计模式，这些模式对于横切关注点、全局数据共享(没有适当的钻取)、关注点(比如复杂的有状态逻辑)与其他组件部分的分离等等都是有效的。

### 高阶组件模式

高阶组件或 HOC 模式是一种高级的 React 模式，用于在我们的应用程序中重用组件逻辑。特设模式对于[横切关注点](https://reactjs.org/docs/higher-order-components.html#use-hocs-for-cross-cutting-concerns)很有用——这些特性要求在我们的应用程序中共享组件逻辑。这些功能的例子有授权、日志记录和数据检索。

hoc 不是核心 React API 的一部分，但是它们来自 React 功能组件的组合性质，这些组件是 JavaScript 函数。

高阶组件类似于 JavaScript 高阶函数；它们是零副作用的纯函数。像 JavaScript 中的高阶函数一样，hoc 的行为就像一个装饰函数。

在 React 中，高阶组件的结构如下所示:

```
import React, {Component} from 'react';

const higherOrderComponent = (DecoratedComponent) => {
  class HOC extends Component {
    render() {
      return <DecoratedComponent />;
    }
  }
  return HOC;
};

```

### 渲染道具图案

在上一节中，我们看到了`HOC`如何以一种方便的方式制作令人振奋的道具和共享的逻辑。在这一节中，我们将探索另一种方法，通过实现[渲染道具模式](https://reactjs.org/docs/render-props.html)，使 React 组件在我们的应用程序中可重用。

假设我们有一个`Paragraph`组件来呈现我们传递给它的任何东西。组件的主要目的是呈现我们传递给它的值。我们可以使用下面的代码来实现这一点:

```
<Paragraph render={() => <p>This is a rendered prop.</p>}>

```

要获得`render`属性的值，我们可以像这样调用它:

```
const Paragraph = props => props.render()

```

理想情况下，这意味着`Paragraph`是一个接收`render`属性并返回`JSX`组件的组件。看起来很简单对吗？

现在让我们来看一个更相关的场景。假设我们有一个`TextInput`，我们希望与两个组件共享它的值。我们可以用渲染道具来处理。

等等，`TextInput`的状态不是应该在父组件中吗？

是的，你是对的，但是在较大的应用程序中，执行状态提升通常很困难:

```
import { useState } from "react";
const TextInput = (props) => {
  const [value, setValue] = useState("");
  return (
    <>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="Type text..."
      />
      {props.children(value)}
    </>
  );
};
export default TextInput;

```

与其他 React 组件一样，input 组件也有子属性，所以我们在这里利用它来允许两个组件访问它的值。我们可以像这样创建两个组件:

```
const Comp1 = ({ value }) => <p>{value}</p>;
const Comp2 = ({ value }) => <p>{value}</p>;

```

然后像这样使用它们:

```
 <TextInput>
          {(value) => (
            <>
              <Comp1 value={value} />
              <Comp2 value={value} />
            </>
          )}
        </TextInput>

```

今后，`Comp1`和`Comp2`将保持与`TextInput`相同的值。

### 状态缩减模式

自从`react hooks`发布以来，状态缩减模式变得非常流行。它已经发展成为生产中各种代码库的传统，特别是因为它使用`useReducer`钩子对 Redux 工作流进行了抽象。

在这一节中，我们将探索如何使用状态缩减器模式来构建可重用的 React 应用程序。取消使用状态缩减器模式的最简单的方法是创建一个定制的助手钩子。在这一节中，我们将创建一个`useToggle`钩子来切换应用程序中的组件状态。

首先，让我们为我们的缩减器创建一个类型:

```
const toggleActionTypes = {
  toggle: "TOGGLE",
};

```

创建`toggleReducer`:

```
const toggleReducer = (state, action) => {
  switch (action.type) {
    case toggleActionTypes.toggle:
      return { on: !state.on };
    default:
      throw new Error(`Undefined type: ${action.type}`);
  }
};

```

然后，创建钩子(`useToggle`):

```
const useToggle = ({ reducer = toggleReducer } = {}) => {
  const [{ on }, dispatch] = useReducer(reducer, { on: false });
  const toggle = () => dispatch({ type: toggleActionTypes.toggle });
  return [on, toggle];
};

```

然后我们可以在如下所示的组件中使用它:

```
const Toggle = () => {
  const [on, toggle] = useToggle({
    reducer(currentState, action) {
      const updates = toggleReducer(currentState, action);
      return updates;
    },
  });
  return (
    <div>
      <button onClick={toggle}>{on ? "Off" : "On"}</button>
    </div>
  );
};
export default Toggle;

```

点击按钮将切换它的开和关状态。我们已经成功地让用户很容易地挂钩到我们的`useToggle` reducer 中发生的每一次更新。

### 提供者模式

React 中的 provider 模式用于跨 React 组件树中的多个组件共享全局数据。

提供者模式包含一个`Provider`组件，它保存全局数据，并使用一个`Consumer`组件或一个定制钩子在应用程序的组件树中共享这些数据。

提供者模式不是唯一的反应模式；像 [React Redux](https://react-redux.js.org/) 和 MobX 这样的库也实现了提供者模式。

下面的代码显示了 React Redux 的提供者模式的设置:

```
import React from 'react'
import ReactDOM from 'react-dom'

import { Provider } from 'react-redux'
import store from './store'

import App from './App'

const rootElement = document.getElementById('root')
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  rootElement
)

```

在 React 中，提供者模式是在 React 上下文 API 中实现的。

默认情况下，React 支持从父组件到其子组件的单向向下数据流。因此，为了将数据传递给位于组件树深处的子组件，我们必须通过组件树的每一层显式地传递属性——这个过程称为属性钻取。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

React 上下文 API 使用提供者模式来解决这个问题。因此，它使我们能够跨 React 组件树共享数据，而无需进行适当的钻取。

要使用上下文 API，我们首先需要使用`React.createContext`创建一个`context`对象。`context`对象带有一个接受一个值的`Provider`组件:全局数据。`context`对象还有一个`Consumer`组件，该组件订阅`Provider`组件进行上下文更改。然后，`Consumer`组件向孩子提供最新的上下文值属性。

下面展示了 React 上下文 API 的一个典型用例:

```
import { createContext } from "react";
const LanguageContext = createContext({});
function GreetUser() {
  return (
    <LanguageContext.Consumer>
      {({ lang }) => (
        <p>Hello, Kindly select your language. Default is {lang}</p>
      )}
    </LanguageContext.Consumer>
  );
}
export default function App() {
  return (
    <LanguageContext.Provider value={{ lang: "EN-US" }}>
      <h1>Welcome</h1>
      <GreetUser />
    </LanguageContext.Provider>
  );
}

```

React Context API 用于实现诸如当前认证用户、主题或首选语言等特性，其中全局数据在组件树中共享。

*注意，* *React 还提供了一个更直接的 API——*[`useContext`](https://reactjs.org/docs/hooks-reference.html#usecontext)*钩子**——用于订阅当前上下文值，而不是使用* `Consumer` *组件。*

### 复合组件模式

复合组件是一种高级的 React 容器模式，它为多个组件共享状态和处理逻辑(一起工作)提供了一种简单有效的方式。

复合组件模式为父组件和其子组件之间的通信提供了一个富有表现力和灵活性的 API。此外，复合组件模式使父组件能够隐式地与其子组件交互和共享状态，这使得它适合于构建声明性 UI。

两个很好的例子是`select`和`options` HTML 元素。`select`和`options` HTML 元素协同工作来提供一个下拉表单字段。

考虑下面的代码:

```
<select>
  <option value="javaScript">JavaScript</option>
  <option value="python">Python</option>
  <option value="java">Java</option>
</select>

```

在上面的代码中，`select`元素隐式地管理并与`options`元素共享它的状态。因此，尽管没有明确的状态声明，但是`select`元素知道用户选择了什么选项。

复合组件模式在构建复杂的 React 组件时非常有用，比如开关、选项卡切换器、折叠、下拉、标签列表等。它可以通过使用`context API`或 [`React.cloneElement`](https://blog.logrocket.com/using-react-cloneelement-function/) API 来实现。

在这一节中，我们将通过构建一个 accordion 来学习更多关于复合组件模式的知识。我们将用`context API`实现我们的复合组件模式。只需遵循以下步骤:

首先，搭建一个新的 React 应用程序:

```
yarn create react-app Accordion

cd Accordion

yarn start

```

然后，安装依赖项:

```
yarn add styled-components

```

接下来，添加虚拟数据:

在`src`目录中，创建一个`data`文件夹，并添加以下代码:

```
const faqData = [

{

id: 1,

header: "What is LogRocket?",

body:

"Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book."

},

{

id: 2,

header: "LogRocket pricing?",

body:

"Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book."

},

{

id: 3,

header: "Where can I Find the Doc?",

body:

"Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book."

},

{

id: 4,

header: "How do I cancel my subscription?",

body:

"Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book."

},

{

id: 5,

header: "What are LogRocket features?",

body:

"Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book."

}

];

export default faqData;

```

然后，创建组件并添加样式:在`src`目录中，创建一个`components`文件夹、`Accordion.js`文件和`Accordion.styles.js`文件。现在我们将使用样式组件创建我们的样式。将以下代码添加到`Accordion.styles.js`文件中:

```
import styled from "styled-components";

export const Container = styled.div
display: flex;
background: #6867ac;
border-bottom: 8px solid #ffbcd1;
font-family: "Inter", sans-serif;
; export const Wrapper = styled.div
margin-bottom: 40px;
; export const Inner = styled.div
display: flex;
padding: 70px 45px;
flex-direction: column;
max-width: 815px;
margin: auto;
; export const Title = styled.h1
font-size: 33px;
line-height: 1.1;
margin-top: 0;
margin-bottom: 8px;
color: white;
text-align: center;
; export const Item = styled.div
color: white;
margin: auto;
margin-bottom: 10px;
max-width: 728px;
width: 100%;
&:first-of-type {
margin-top: 3em;
}
&:last-of-type {
margin-bottom: 0;
}
; export const Header = styled.div
display: flex;
flex-direction: space-between;
cursor: pointer;
border: 1px solid #ce7bb0;
border-radius: 8px;
box-shadow: #ce7bb0;
margin-bottom: 1px;
font-size: 22px;
font-weight: normal;
background: #ce7bb0;
padding: 0.8em 1.2em 0.8em 1.2em;
user-select: none;
align-items: center;
; export const Body = styled.div
font-size: 18px;
font-weight: normal;
line-height: normal;
background: #ce7bb0;
margin: 0.5rem;
border-radius: 8px;
box-shadow: #ce7bb0;
white-space: pre-wrap;
user-select: none;
overflow: hidden;
&.open {
max-height: 0;
overflow: hidden;
}
span {
display: block;
padding: 0.8em 2.2em 0.8em 1.2em;
}
;

```

接下来，将以下代码添加到`Accordion.js`文件中:

```
import React, { useState, useContext, createContext } from "react";
import { Container, Inner, Item, Body, Wrapper, Title, Header
} from "./Accordion.styles";

const ToggleContext = createContext();
export default function Accordion({ children, ...restProps }) {
  return (
    <Container {...restProps}>
      <Inner>{children}</Inner>
    </Container>
  );
}

Accordion.Title = function AccordionTitle({ children, ...restProps }) {
  return <Title {...restProps}>{children}</Title>;
};

Accordion.Wrapper = function AccordionWrapper({ children, ...restProps }) {
  return <Wrapper {...restProps}>{children}</Wrapper>;
};

Accordion.Item = function AccordionItem({ children, ...restProps }) {
  const [toggleShow, setToggleShow] = useState(true);
  const toggleIsShown = (isShown) => setToggleShow(!isShown);
  return (
    <ToggleContext.Provider value={{ toggleShow, toggleIsShown }}>
      <Item {...restProps}>{children}</Item>
    </ToggleContext.Provider>
  );
};

Accordion.ItemHeader = function AccordionHeader({ children, ...restProps }) {
  const { toggleShow, toggleIsShown } = useContext(ToggleContext);
  return (
    <Header onClick={() => toggleIsShown(toggleShow)} {...restProps}>
      {children}
    </Header>
  );
};

Accordion.Body = function AccordionBody({ children, ...restProps }) {
  const { toggleShow } = useContext(ToggleContext);
  return (
    <Body className={toggleShow ? "open" : ""} {...restProps}>
      <span>{children}</span>
    </Body>
  );
};

```

在上面的代码中，`ToggleContext`上下文对象保存我们的`toggleShow`状态，并通过`ToggleContext.Provider`将该状态提供给所有的`Accordion` `children`。

此外，我们通过使用 [JSX 点符号](https://reactjs.org/docs/jsx-in-depth.html#using-dot-notation-for-jsx-type)来创建新组件并将其附加到`Accordion`组件上。

最后，用下面的代码更新`App.js`:

```
import React from "react";
import Accordion from "./components/Accordion";
import faqData from "./data";
export default function App() {
  return (
    <Accordion>
      <Accordion.Title>LogRocket FAQ</Accordion.Title>
      <Accordion.Wrapper>
        {faqData.map((item) => (
          <Accordion.Item key={item.id}
            <Accordion.ItemHeader>{item.header}</Accordion.ItemHeader>
            <Accordion.Body>{item.body}</Accordion.Body>
          </Accordion.Item>
        ))}
      </Accordion.Wrapper>
    </Accordion>
  );
}

```

你可以在这里看到手风琴的动作。

### 表示和容器组件模式

这些术语最初是由丹·阿布拉莫夫创造的。然而，他不再推广这些想法了。

表示模式和容器模式都很有用，因为它们帮助我们将关注点(例如，复杂的有状态逻辑)与组件的其他方面分开。

然而，因为 React Hooks 使我们能够在没有任何任意划分的情况下分离关注点，所以推荐使用 Hooks 模式，而不是表示和容器组件模式。但是根据您的用例，表示和容器模式可能仍然有用。

这些模式旨在分离关注点，并以一种易于理解的方式构建我们的代码。

表示组件是无状态的功能组件，只关心向视图呈现数据。并且它们不依赖于应用程序的其他部分。

在某些情况下，它们需要保存与视图相关的状态，可以用 React 类组件来实现。

呈现组件的一个例子是呈现列表的组件:

```
const usersList = ({users}) => {
  return (
  <ul>
      {users.map((user) => (
      <li key={user.id}>
          {user.username}
      </li>
      ))}
  </ul>
  );
};

```

容器组件是跟踪其内部状态和生命周期的有用的类组件。它们还包含表示组件和数据获取逻辑。

容器组件的示例如下所示:

```
class Users extends React.Component {
  state = {
    users: []
  };

  componentDidMount() {
    this.fetchUsers();
  }

  render() {
    return (); // ... jsx code with presentation component
  }
}

```

### 钩子图案

React Hooks APIs 是在 React 16.8 中引入的，它彻底改变了我们构建 React 组件的方式。

React Hooks API 为 React 功能组件提供了一种简单而直接的方式来访问常见的 React 特性，如 props、state、context、refs 和 lifecycle。

这样做的结果是，功能组件不再是哑组件，因为它们可以使用状态，挂钩到[组件生命周期](https://reactjs.org/docs/react-component.html#the-component-lifecycle)，执行副作用，以及来自功能组件的更多内容。这些特性最初仅由类组件支持。

尽管诸如表示和容器组件模式之类的模式使我们能够分离关注点，但是容器经常导致“巨大的组件”:具有跨越几个生命周期方法的巨大逻辑的组件。巨大的组件很难阅读和维护。

此外，因为容器是类，所以不容易组合。在使用容器时，我们还会面临其他与类相关的问题，比如自动绑定和使用 [`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) 。

通过增强功能组件跟踪内部状态、访问组件生命周期和其他与类相关的特性的能力，钩子模式解决了上面提到的与类相关的问题。作为纯 JavaScript 函数，React 功能组件是可组合的，消除了使用`this`关键字的麻烦。

考虑下面的代码:

```
import React, { Component } from "react";
class Profile extends Component {
  constructor(props) {
    super(props);
    this.state = {
      loading: false,
      user: {}
    };
  }
  componentDidMount() {
    this.subscribeToOnlineStatus(this.props.id);
    this.updateProfile(this.props.id);
  }
  componentDidUpdate(prevProps) {
    // compariation hell.
    if (prevProps.id !== this.props.id) {
      this.updateProfile(this.props.id);
    }
  }
  componentWillUnmount() {
    this.unSubscribeToOnlineStatus(this.props.id);
  }
  subscribeToOnlineStatus() {
    // subscribe logic
  }
  unSubscribeToOnlineStatus() {
    // unscubscribe logic
  }
  fetchUser(id) {
    // fetch users logic here
  }
  async updateProfile(id) {
    this.setState({ loading: true });
    // fetch users data
    await this.fetchUser(id);
    this.setState({ loading: false });
  }
  render() {
     // ... some jsx
  }
}
export default Profile;

```

从上面的容器中，我们可以指出三个挑战:

*   在我们可以设置状态之前，使用构造函数并调用`super()`。尽管这个问题已经通过在 JavaScript 中引入[类字段](https://github.com/tc39/proposal-class-fields)得到了解决，但是钩子仍然提供了一个更简单的 API
*   使用`this`
*   跨生命周期方法重复相关逻辑

Hooks 通过提供一个更干净、更精简的 API 解决了这些问题。现在我们可以重构我们的`Profile`组件，如下所示:

```
import React, { useState, useEffect } from "react";
function Profile({ id }) {
  const [loading, setLoading] = useState(false);
  const [user, setUser] = useState({});

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    updateProfile(id);
    subscribeToOnlineStatus(id);
    return () => {
      unSubscribeToOnlineStatus(id);
    };
  }, [id]);

  const subscribeToOnlineStatus = () => {
    // subscribe logic
  };

  const unSubscribeToOnlineStatus = () => {
    // unsubscribe logic
  };

  const fetchUser = (id) => {
    // fetch user logic here
  };

  const updateProfile = async (id) => {
    setLoading(true);
    // fetch user data
    await fetchUser(id);
    setLoading(false);
  };

  return; // ... jsx logic
}
export default Profile;

```

在高级情况下，钩子模式通过使我们能够创建定制的可重用钩子来提高代码的可重用性。你可以在我们的[上一篇文章](https://blog.logrocket.com/advanced-react-hooks-creating-custom-reusable-hooks/)中了解更多。

## 结论

在这篇文章中，我们了解了 2022 年一些有用的设计模式。设计模式很棒，因为它们使我们能够利用所有创建和审查这些模式的开发人员的专业知识。

因此，他们可以减少开发时间，因为我们在这个过程中提供解决方案并提高软件质量。

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