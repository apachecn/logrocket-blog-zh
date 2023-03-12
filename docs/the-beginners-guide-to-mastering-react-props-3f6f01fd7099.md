# React - LogRocket 博客中如何将道具传递给组件

> 原文：<https://blog.logrocket.com/the-beginners-guide-to-mastering-react-props-3f6f01fd7099/>

当您学习如何使用 React 开发 web 应用程序时，您将不可避免地遇到道具的概念。理解道具工作的方式对于掌握 React 是必不可少的，但是完全掌握这个概念并不是一件容易的事情。

[https://www.youtube.com/embed/0_6YYR3HRlw](https://www.youtube.com/embed/0_6YYR3HRlw)

视频

### React 中的道具:简介

Props 代表“属性”，它们在 React 应用程序中用于将数据从一个 React 组件发送到另一个 React 组件。让我们看看下面的示例代码。这里我们有一个呈现字符串的 React 组件:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  render(){
    return <div>Hello, World!</div>
  }
}

ReactDOM.render(<App />, document.getElementById("root"));
```

现在告诉你如何将道具添加到`App`组件中:就在`ReactDOM.render`上对 App 组件的调用旁边，键入一个随机属性并给它赋值。我将创建一个`name`属性，并将其指定为`“Nathan”`:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  render(){
    return <div>Hello, World!</div>
  }
}

ReactDOM.render(<App name="Nathan" />, document.getElementById("root"));
```

至此，`App`组件现在有了一个名为`name`的道具；你可以使用`this`从类中调用它。让我告诉你我是如何问候自己的:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  render(){
    return <div>Hello, {this.props.name}!</div>
  }
}

ReactDOM.render(<App name="Nathan" />, document.getElementById("root"));
```

这是 props 的基础:当你调用一个组件时，它允许你把你能想到的任何数据发送到这个组件中。当你有两个或更多的组件时，你可以传递数据。这是另一个包含两个组件的示例:

如上面的代码所示，您可以通过在调用组件时添加属性来在组件之间传递属性，就像在调用常规 JavaScript `function`时传递参数一样。说到函数，由于 React 也允许您使用`function`创建组件，接下来让我们看看 props 如何在函数组件中工作。

### 功能组件中的道具

在一个函数组件中，组件接收属性就像普通的函数参数一样。一个函数组件将接收具有您在组件调用中描述的属性的`props`对象:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return <Greeting name="Nathan" age={27} occupation="Software Developer" />;
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

除了一次传递多个属性之外，在这个例子中，您还可以看到`age`属性是一个数字数据类型。这表明您可以将 JavaScript 中可用的任何类型的数据传递到 props 中，比如`number`、`Boolean`或`object`。这就是 props 使您能够使用自顶向下的方法发送数据的方式，其中较高级别的组件可以向其下面的组件发送数据。

### 具有属性和状态的代码重用

道具的使用可以让你复用更多的 React 代码，避免重复自己。在我们的例子中，您可以为许多不同的人重用同一个`Greeting`组件:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return (
    <div>
      <Greeting name="Nathan" age={27} occupation="Software Developer" />
      <Greeting name="Jane" age={24} occupation="Frontend Developer" />
    </div>
  );
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

太好了！但是，由于 props 是只读的，并且在 React 应用程序的整个生命周期中不得手动更改，因此在 React 应用程序中只使用 props 并不能真正使它成为一个可以响应用户交互并相应渲染的动态应用程序。为了做到这一点，您需要使用`state`。

状态和道具一起构成了 React 应用程序的数据“模型”。虽然 props 应该是只读的，但是状态是用于可以基于用户动作而改变的数据的。让我们看看它们是如何一起创建动态应用程序的。

首先，让我们添加一个名为`textSwitch`的新状态，它将一个布尔值存储到`App`组件中，并将其传递给`Greeting`组件。`Greeting`组件将查看这个状态值来决定呈现什么:

这个代码示例展示了如何基于用户使用`state`和`props`的操作有条件地呈现应用程序的视图。在 React 中，状态作为道具从一个组件传递到另一个组件。因为属性名和值将作为常规的`props`对象属性被传递到组件中，所以它不关心数据来自哪里。

### 属性类型和默认属性

当您开发 React 应用程序时，有时您可能需要构造和定义一个 prop 来避免 bug 和错误。同样地，`function`可能需要强制参数，如果要正确呈现，React 组件可能需要定义一个属性。

您可能犯了一个错误，忘记将一个必需的属性传递给需要它的组件:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return <Greeting name="Nathan" />;
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

当`props.age`和`props.occupation`在`Greeting`组件中未定义时，React 将简单地忽略表达式来调用它们的值并呈现文本的其余部分。它不会引发任何错误，但你知道你不能让这种事情不了了之。

这就是`[propTypes](https://reactjs.org/docs/typechecking-with-proptypes.html)`来帮忙的地方。`PropTypes`是一个特殊的组件属性，可以用来验证组件中的属性。这是一个单独的可选 npm 软件包，因此您需要在使用它之前先安装它:

```
npm install --save prop-types
```

现在让我们在`Greeting`组件中制作所需的道具:

```
import React from "react";
import ReactDOM from "react-dom";
import PropTypes from "prop-types";

function App() {
  return <Greeting name="Nathan" />;
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

Greeting.propTypes = {
  name: PropTypes.string.isRequired, // must be a string and defined
  age: PropTypes.number.isRequired, // must be a number and defined
  occupation: PropTypes.string.isRequired  // must be a string and defined
};

ReactDOM.render(<App />, document.getElementById("root"));
```

声明了`propTypes`属性后，当`Greeting`组件的道具没有通过`propTypes`验证时，它会向控制台发出警告。

您还可以通过使用另一个名为`defaultProps`的特殊属性来定义 props 的默认值，以防 props 没有被传递到调用的组件中:

现在当调用`Greeting`时，将使用`defaultProps`中的默认值。

点击了解更多关于 React [中默认道具的信息。](https://blog.logrocket.com/a-complete-guide-to-default-props-in-react-984ea8e6972d/)

### 将数据从子组件传递到父组件

父组件是在其代码块中调用其他组件的任何组件，而子组件只是被父组件调用的组件。父组件使用 props 将数据向下传递给子组件。

您可能想知道，“如何将数据从子组件传递到父组件？”

答案是不可能的——至少直接不可能。但是在 React 中是这样的:你也可以传递一个`function`作为道具。这和问题有什么关系？让我们首先回到带有`state`的代码示例:

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

function App() {
  const [textSwitch, setTextSwitch] = useState(true);
  return (
    <div>
      <button onClick={() => setTextSwitch(!textSwitch)} type="button">
        Toggle Name
      </button>
      <Greeting text={textSwitch} />
    </div>
  );
}
function Greeting(props) {
  console.log(props.text);
  if (props.text) {
    return (
      <p>
        Hello! I'm Nathan and I'm a Software Developer. Pleased to meet you!
      </p>
    );
  }
  return (
    <p>Hello! I'm Jane and I'm a Frontend Developer. Pleased to meet you!</p>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

React 应用程序通常有三个组件层，顶层组件调用一个子组件，子组件又调用另一个子组件。为了说明这一点，我们需要稍微调整一下上面的例子。

让我们将`<button>`元素从`App`中移出，放入它自己的组件中。为了简单起见，我们称它为`ChangeGreeting`。然后，您将从`Greeting`组件而不是`App`组件调用这个组件:

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

function App() {
  const [textSwitch, setTextSwitch] = useState(true);
  return (
    <div>
      <Greeting
        text={textSwitch}
      />
    </div>
  );
}

function Greeting(props) {
  let element;
  if (props.text) {
    element = (
      <p>
        Hello! I'm Nathan and I'm a Software Developer. Pleased to meet you!
      </p>
    );
  } else {
    element = (
      <p>Hello! I'm Jane and I'm a Frontend Developer. Pleased to meet you!</p>
    );
  }
  return (
    <div>
      {element}
      <ChangeGreeting />
    </div>
  );
}

function ChangeGreeting(props) {
  return (
    <button type="button">
      Toggle Name
    </button>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

现在设置状态的按钮在`ChangeGreeting`组件中，它是状态所在位置的下两层(在`App`组件)。那么你怎么可能改变这个状态呢？答案是向下发送一个函数，直到它到达需要它的组件:

在上面的例子中，`App`组件正在发送`handleClick` prop，它具有将状态更改为`Greeting`组件的功能。`Greeting`组件实际上并不需要它，但是它的子组件`ChangeGreeting`需要，所以它将道具转发到那里。

在`ChangeGreeting`组件上，当按钮被点击时，它将调用`handleClick`函数，使`App`执行该函数。

当`App`中的状态更新后，React 视图被重新渲染，然后新的状态值通过 props 发送到`Greeting`。

所以，是的——React 不能将数据从子组件向上发送到父组件，但是父组件可以将函数发送到子组件。了解了这一点，您就可以将更新状态的函数发送到子组件中，一旦调用该函数，父组件就会更新状态。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您不能发送数据，但您可以使用函数发送改变信号。

### 支柱钻孔及其处理方法

传递数据的最后一个例子实际上代表了您在处理属性和状态时可能会遇到的另一个常见问题:属性钻取。

道具钻取是指沿着组件层向下传递道具，直到它们到达指定的子组件，而其他更高的组件实际上并不需要它们。

在上面的例子中，这看起来没问题，但是请记住，我们只有三个组件。当您有许多组件，并且所有组件都使用道具和状态相互交互时，道具钻取可能会成为一个令人头疼的维护问题。

为了避免这个问题，您可以做的事情之一是减少组件的数量，并且只在需要重用特定组件时创建新组件。

回到这个例子，完全不需要单独的`ChangeGreeting`组件，直到除了`Greeting`之外的另一个组件真正调用同一段代码。您只需要两个组件就可以做到这一点:

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

function App() {
  const [textSwitch, setTextSwitch] = useState(true);
  return (
    <div>
      <Greeting
        text={textSwitch}
        handleClick={() => setTextSwitch(!textSwitch)}
      />
    </div>
  );
}

function Greeting(props) {
  let element;
  if (props.text) {
    element = (
      <p>
        Hello! I'm Nathan and I'm a Software Developer. Pleased to meet you!
      </p>
    );
  } else {
    element = (
      <p>Hello! I'm Jane and I'm a Frontend Developer. Pleased to meet you!</p>
    );
  }
  return (
    <div>
      {element}
      <button onClick={props.handleClick} type="button">
        Toggle Name
      </button>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

这就对了——这样传递道具不需要道具钻孔。

### React 中的传递道具:结论

和所有学习 React 的事情一样，道具学起来很容易，但是很难掌握。现在你知道 props 是不可变的(只读的)数据，用于使 React 组件相互“对话”。它们非常类似于传递给函数的参数，可以是开发人员自己指定的任何内容。

状态和道具使您能够创建一个动态的 React 应用程序，该应用程序具有可重用、可维护和数据驱动的可靠代码库。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

* * *