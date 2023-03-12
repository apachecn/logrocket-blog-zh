# Lit 与 React:比较指南

> 原文：<https://blog.logrocket.com/lit-vs-react-comparison-guide/>

对于开发人员来说，选择前端框架可能是一个困难的决定，因为有太多的选择。React 是最受欢迎的选择之一。这是一项完善的计划，截止到 [2021 年 JS 状态调查](https://2021.stateofjs.com/en-US/libraries/front-end-frameworks)，满意度达到 84%。尽管如此，还有其他几个具有有趣特性和功能的框架值得研究。

为下一个项目选择前端框架时，请考虑以下问题:

*   这个框架有我需要的特性吗？
*   与其他框架相比，这个框架有多快？
*   这个框架有多容易学习和使用？
*   什么规模的社区使用这个框架？

另一种反应是 [Lit](https://lit.dev) ，截至 [2021 年 JS 状态调查](https://2021.stateofjs.com/en-US/libraries/front-end-frameworks)，其满意度为 77%。 [Lit 易于学习和使用](https://blog.logrocket.com/getting-started-lit-html-litelement/)，它的小体积意味着快速的加载时间。

在本教程中，我们将比较反应和照明。我们还将在 Lit 中创建一个示例项目。

*向前跳转:*

我们开始吧！

## Lit 有什么新内容？

Lit 有几个区别于其他前端框架的特性:

*   LitElement 基类是本机 HTMLElement 的方便且通用的扩展。这个类可以被扩展来定义我们的组件
*   表达性和声明性模板使得定义组件应该如何呈现变得容易
*   反应属性是 Lit 组件的内部状态。当反应属性更改时，组件会自动重新渲染
*   作用域样式有助于保持 CSS 选择器的简单，确保组件样式不会影响其他上下文
*   支持普通 Javascript、类型脚本和人机工程学(装饰器和类型声明)

## 点燃与反应

Lit 的核心概念和特性与 React 相似，但也有一些显著的不同。比如 React 从 2013 年就有了，受欢迎程度远远超过 Lit。在撰写本文时，React 在 npm 上的周下载量约为[1590 万，相比之下，Lit 在 npm](https://www.npmjs.com/package/react) 上的周下载量为[12.7 万。](https://www.npmjs.com/package/lit)

但是，Lit 比 React 更快，占用的内存也更少。一项公开的基准比较显示 lit-html 比 React 的 VDOM 快 8-10%。与 React 的 40kB 相比，Lit 的内存大小只有 5kB。

这两个框架还提供了其他很酷的特性。让我们看看他们是如何比较的。

## JSX 和模板

JSX 是 JavaScript 的语法扩展，功能类似于模板语言，但具有 JavaScript 的全部功能。React 用户可以使用 JSX 轻松地用 JavaScript 代码编写模板。Lit 模板服务于类似的目的，但是将组件 UI 表达为其状态的函数。

下面是 React 中 JSX 模板的一个例子:

```
import 'react';
import ReactDOM from 'react-dom';

const name = 'World';
const el = (
  <>
    <h1>Hello, {name}</h1>
    <div>How are you? </div>
  </>
);
ReactDOM.render(
  el,
  mountNode
);

```

下面是一个在 Lit 中使用模板的例子:

```
import {html, render} from 'lit';

const name = 'World';
const el = html`
  <h1>Hello, ${name}</h1>
  <div>How are you?</div>`;

render(
  el,
  mountNode
);

```

正如我们在上面的例子中所看到的，Lit 不需要 React 片段来对其模板中的多个元素进行分组。相反，Lit 模板用 HTML [标记的模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates)包装。

## 组件和道具

组件是自包含的、可重用的代码片段。它们执行与 JavaScript 函数相同的操作，但是它们独立工作并返回 HTML。React 组件分为两种类型:类组件和功能组件。

### 类别组件

React 类组件的 Lit 等价物被称为 [LitElement](https://lit.dev/docs/api/LitElement/) 。

下面是 React 中基于类的组件的一个示例:

```
import React from 'react';
import ReactDOM from 'react-dom';

class Welcome extends React.Component {
  constructor(props) {
    super(props);
    this.state = {name: ''};
  }

  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

const el = <Welcome name="World"/>
ReactDOM.render(
  el,
  mountNode
);

```

下面是 Lit 中的同一个例子，使用`LitElement`:

```
import {LitElement, html} from 'lit';

class WelcomeBanner extends LitElement {
  static get properties() {
    return {
      name: {type: String}
    }
  }

  constructor() {
    super();
    this.name = '';
  }

  render() {
    return html`<h1>Hello, ${this.name}</h1>`
  }
}

customElements.define('welcome-banner', WelcomeBanner);

```

在为 LitElement 组件定义和呈现模板之后，我们将以下内容添加到我们的 HTML 文件中:

```
<!-- index.html -->
<head>
  <script type="module" src="./index.js"></script>
</head>
<body>
  <welcome-banner name="World"></welcome-banner>
</body>

```

现在，让我们看看功能组件是如何在这些框架中创建的。

### 功能组件

Lit 不使用 JSX，所以与 React 功能组件没有一一对应的关系。然而，更简单的方法是编写一个函数，它接收属性，然后根据这些属性呈现 DOM。

下面是 React 中一个功能组件的示例:

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const el = <Welcome name="World"/>
ReactDOM.render(
  el,
  mountNode
);

```

下面是 Lit 中的同一个例子:

```
import {html, render} from 'lit';

function Welcome(props) {
  return html`<h1>Hello, ${props.name}</h1>`;
}

render(
  Welcome({name: 'World}),
  document.body.querySelector('#root')
);

```

## 状态和生命周期方法

`state`是一个包含组件数据或信息的 React 对象。组件的`state`会随着时间而改变。每当它的`state`改变时，组件就会重新呈现。

Lit 的[反应属性](https://lit.dev/docs/components/properties/)是 React 的`state`和`props`的混合。更改后，反应属性可以触发组件生命周期，重新渲染组件，并可以选择读取或写入属性。反应属性有两种变体:

*   公共反应属性
*   内部反应状态

反应属性在 React 中实现，如下所示:

```
import React from 'react';

class MyEl extends React.Component {
  constructor(props) {
    super(props)
    this.state = {name: 'there'}
  }

  componentWillReceiveProps(nextProps) {
    if (this.props.name !== nextProps.name) {
      this.setState({name: nextProps.name})
    }
  }
}

```

反应属性在 Lit 中实现，如下所示:

```
import {LitElement} from 'lit';
import {property} from 'lit/decorators.js';

class MyEl extends LitElement {
  @property() name = 'there';
}

```

内部反应状态指的是不向组件的公共 API 公开的反应属性。这些状态属性缺少相应的属性，并且不适合在组件外部使用。组件的内部反应状态应由组件本身决定。

React 和 Lit 具有相似的生命周期，但有一些小而显著的差异。让我们仔细看看这些框架共有的一些方法。

### `constructor`

`constructor`方法在 React 和 Lit 中都可用。当从`class`创建一个对象时，它被自动调用。

下面是 React 中的`constructor`方法的一个例子:

```
import React from 'react';
import Chart from 'chart.js';

class MyEl extends React.Component {
  constructor(props) {
    super(props);
    this.state = { counter: 0 };
    this._privateProp = 'private';
  }

```

下面是 Lit 中的`constructor`方法的一个例子:

```
class MyEl extends LitElement {
  static get properties() {
    return { counter: {type: Number} }
  }
  constructor() {
    this.counter = 0;
    this._privateProp = 'private';
  }

```

### `render`

`render`方法在 React 和 Lit 中都可用。它显示指定元素内的代码。

下面是 React 中的`render`方法的一个例子:

```
render() {
    return <div>Hello World</div>
  }

```

下面是 Lit 中的`render`方法的一个例子:

```
render() {
    return html`<div>Hello World</div>`;
  }

```

### `componentDidMount`对`firstUpdated`和`connectedCallback`

React 中的`componentDidMount`函数类似于 Lit 的`firstUpdated`和`connectedCallback`生命周期回调的组合。该函数在组件安装后调用。

下面是 React 中的`componentDidMount`方法的一个例子:

```
componentDidMount() {
    this._chart = new Chart(this.chartElRef.current, {...});
  }

  componentDidMount() {
    this.window.addEventListener('resize', this.boundOnResize);
  }

```

以下是 Lit 中的`firstUpdated`和`connectedCallback`生命周期回调的示例:

```
firstUpdated() {
    this._chart = new Chart(this.chartEl, {...});
  }

  connectedCallback() {
    super.connectedCallback();
    this.window.addEventListener('resize', this.boundOnResize);
  }

```

### `componentDidUpdate`对`updated`

React 中的`componentDidUpdate`功能相当于 Lit 中的`updated`。它在组件的属性或状态改变后被调用。

下面是 React 中的`componentDidUpdate`方法的一个例子:

```
componentDidUpdate(prevProps) {
    if (this.props.title !== prevProps.title) {
      this._chart.setTitle(this.props.title);
    }
  }

```

下面是 Lit 中的`updated`方法的一个例子:

```
updated(prevProps: PropertyValues<this>) {
    if (prevProps.has('title')) {
      this._chart.setTitle(this.title);
    }
  }

```

### `componentWillUnmount`对`disconnectedCallback`

React 中的`componentWillUnmount`功能相当于 Lit 中的`disconnectedCallback`。该函数在组件被销毁或卸载后调用。

下面是 React 中的`componentWillUnmount`方法的一个例子:

```
componentWillUnmount() {
    this.window.removeEventListener('resize', this.boundOnResize);
  }
}

```

下面是 Lit 中的`disconnectedCallback`方法的一个例子:

```
disconnectedCallback() {
    super.disconnectedCallback();
    this.window.removeEventListener('resize', this.boundOnResize);
  }
}

```

## 钩住

挂钩是允许 React 功能组件“挂钩”React 状态和生命周期特性的功能。钩子在类内不起作用，但是它们允许我们在没有类的情况下使用 React。

与 React 不同，Lit 不提供从函数创建定制元素的方法，但是 LitElement 通过以下方式解决了 React 类组件的大多数主要问题:

*   在构造函数中不接受参数
*   自动绑定所有@event 绑定(通常是自定义元素的引用)
*   将类属性实例化为类成员

这里有一个 React 中钩子的例子(在制作钩子的时候):

```

import React from 'react';
import ReactDOM from 'react-dom';

class MyEl extends React.Component {
  constructor(props) {
    super(props); // Leaky implementation
    this.state = {count: 0};
    this._chart = null; // Deemed messy
  }

  render() {
    return (
      <>
        <div>Num times clicked {count}</div>
        <button onClick={this.clickCallback}>click me</button>
      </>
    );
  }

  clickCallback() {
    // Errors because `this` no longer refers to the component
    this.setState({count: this.count + 1});
  }
}

```

下面是同样的例子，使用 LitElement:

```
class MyEl extends LitElement {
  @property({type: Number}) count = 0; // No need for constructor to set state
  private _chart = null; // Public class fields introduced to JS in 2019

  render() {
    return html
        <div>Num times clicked ${count}</div>
        <button @click=${this.clickCallback}>click me</button>`;
  }

  private clickCallback() {
    // No error because `this` refers to component
    this.count++;
  }
}

```

## 参考文献

Refs 是 React 函数，允许我们访问 DOM 元素和我们创建的任何 React 元素。当我们想在不使用道具的情况下改变子组件的值时，就会用到它们。

在 Lit 中，ref 是使用`@query`和`@queryAll`装饰器创建的。这些装饰器分别相当于`querySelector`和`querySelectorAll`，直接呈现给 DOM。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面是 React 中 refs 函数的一个示例:

```
const RefsExample = (props) => {
 const inputRef = React.useRef(null);
 const onButtonClick = React.useCallback(() => {
   inputRef.current?.focus();
 }, [inputRef]);

 return (
   <div>
     <input type={"text"} ref={inputRef} />
     <br />
     <button onClick={onButtonClick}>
       Click to focus on the input above!
     </button>
   </div>
 );
};

```

下面是在 Lit 中使用`@query`装饰器的同一个例子:

```
@customElement("my-element")
export class MyElement extends LitElement {
  @query('input') // Define the query
  inputEl!: HTMLInputElement; // Declare the prop

  // Declare the click event listener
  onButtonClick() {
    // Use the query to focus
    this.inputEl.focus();
  }

  render() {
    return html
      <input type="text">
      <br />
      <!-- Bind the click listener -->
      <button @click=${this.onButtonClick}>
        Click to focus on the input above!
      </button>
   ;
  }
}

```

## 在 Lit 中创建基本的待办事项项目

让我们通过创建一个示例待办事项项目来看看 Lit 的实际应用。

首先，运行命令克隆 Lit starter JavaScript 项目:

```
git clone https://github.com/lit/lit-element-starter-js.git

```

然后，cd 到项目文件夹，并使用以下命令安装所需的包:

```
npm install

```

安装完成后，进入`lit-element-starter-js/my-element.js`文件。删除样板代码，并使用以下代码片段创建一个`Todo`组件:

```
import {LitElement, html, css} from 'lit';
class Todo extends LitElement {
  constructor() {
    super();
  }
  render() {
    return html
      <div class="todos-wrapper">
        <h4>My Todos List</h4>
        <input placeholder="Add task..."/>
        <button>Add</button>
        <div class="list">
            #Todo List
        </div>
      </div>
    ;
  }
}
customElements.define('my-element', Todo);

```

上面的代码用一个`constructor`方法和一个`render`方法创建了一个`Todo`组件，在这个组件中应用程序的所有反应属性都将被定义，这个组件呈现包含输入字段和按钮的 JSX。

接下来，让我们定义应用程序的属性。因为这是一个待办事项应用程序，我们需要一个`TodosList`来存储任务，需要一个`input`属性来获取用户输入。

现在，我们将下面的代码片段添加到`Todos`类中:

```
static properties = {
    TodosList: {type: Array},
    input: {type: String},
  };

```

然后，我们将使用下面的代码为`constructor`方法中的`TodosList`和`input`属性分配初始值:

```
 this.TodosList = [];
 this.input = null;

```

接下来，我们将创建一个添加和更新待办任务的方法:

```
setInput(event) {
    this.input = event.target.value;
  }

  addTodo() {
      this.TodosList.push({
      name: this.input,
      id: this.TodosList.length + 1,
      completed: false,
    });
    this.requestUpdate();
  }

 updateTodo(todo) {
    todo.completed = !todo.completed;
    this.requestUpdate();
  }

```

我们可以在上面的代码中看到，在修改状态后，`addTodo`和`updateTodo`方法中调用了`requestUpdate()`函数。这些方法改变了`TodosList`属性，所以我们调用了`requestUpdate()`函数来更新组件状态。

接下来，我们将修改`render`方法，将事件监听器添加到上面创建的方法中，并显示待办任务。

```
 render() {
    return html
      <div class="todos-wrapper">
        <h4>My Todos List</h4>
        <input placeholder="Add task..." @input=${this.setInput} />
        <button @click=${this.addTodo}>Add</button>
        <div class="list">
          ${this.TodosList.map(
            (todo) => html`
              <li
                @click=${() => this.updateTodo(todo)}
                class=${todo.completed && 'completed'}
              >
                ${todo.name}
              </li>
          )}
        </div>
      </div>
    ;
  }

```

最后，让我们添加一些样式，使应用程序看起来更有吸引力:

```
static styles = css
    .todos-wrapper {
      width: 35%;
      margin: 0px auto;
      background-color: rgb(236, 239, 241);
      padding: 20px;
    }
    .list {
      margin-top: 9px;
    }
    .list li {
      background-color: white;
      list-style: none;
      padding: 6px;
      margin-top: 3px;
    }
    .completed {
      text-decoration-line: line-through;
      color: #777;
    }
    input {
      padding: 5px;
      width: 70%;
    }
    button {
      padding: 5px;
    }
  ;

```

现在，让我们运行应用程序:

```
npm run serve

```

这是我们的待办事项项目示例！

![Sample Project Todos List](img/fbb033896e13f438f13466d517612018.png)

## 我应该从 React 切换到 Lit 吗？

每个框架都有独特的优点和缺点。React [为许多大公司](https://reactjs.org/docs/design-principles.html#stability)的网络应用提供支持，比如脸书、Twitter 和 Airbnb。它还有一个由[开发者和贡献者](https://reactjs.org/community/support.html)组成的庞大社区。

如果你目前正在使用 React，并且对这个选择感到满意，那么我认为你没有理由要换。但是，如果您正在处理一个需要真正快速性能的项目，那么您可以考虑使用 Lit。

要了解更多关于 Lit 的信息，请查看其官方文档。

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