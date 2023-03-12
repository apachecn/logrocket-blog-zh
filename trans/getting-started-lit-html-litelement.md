# lit-html 和 LitElement - LogRocket 博客入门

> 原文：<https://blog.logrocket.com/getting-started-lit-html-litelement/>

根据其官方网站，lit-html 是一个高效的，富有表现力的，可扩展的 JavaScript HTML 模板库。虽然当前版本的 lit-html 是 [Lit 组件库](https://lit.dev)的一部分，但模板部分被分解为一个独立的库，我们将在本文中讨论。

这个库允许我们定义可以在 HTML 中包含不同值的占位符，从而使标记可重用。这为开发人员节省了宝贵的时间和精力。在本文中，我们将看看 lit-html 是如何工作的，并通过使用 html 和 CSS 的简单教程来应用它。

我们开始吧！

## 什么是 lit-html？

模板是以某种方式构建的静态和动态内容的组合。例如，注册表单的 HTML 模板可能如下所示:

```
<div>
<p>First Name: <span>_____</span></p>
      <p>Last Name: <span>_____</span></p>
      <p>Email: <span>_____</span></p>
      <p>Phone Number: <span>_____</span></p>
</div>

```

静态内容包括名、姓、电子邮件和电话号码字段。这些线条代表动态内容。

如果我们要用普通的 JavaScript 操作上面的 HTML，我们必须使用 DOM 选择 API，如`[querySelector()](https://www.w3schools.com/jsref/met_document_queryselector.asp)`、[、](https://www.w3schools.com/jsref/met_document_queryselectorall.asp)和`[getElementByTagName](https://www.w3schools.com/jsref/met_document_getelementsbytagname.asp)`来获取元素并对其执行操作；这很快会让应用程序看起来像是伪造的或者是糟糕的代码。

lit-html 允许我们用 JavaScript 呈现 html 模板，类似于上面的代码。可以呈现和操作模板的动态部分，使模板易于重用。

### 使用 lit-html 的好处

lit-html 有许多好处，可以改善用户和开发人员的体验。

*   在您的标记中提供 JavaScript 的全部功能
*   快速启动和更新
*   易于使用并具有可扩展的 API
*   小的库导致小的包大小和短的加载时间

如您所见，lit-html 是一个强大的模板库，对任何开发人员的项目都是一个很好的补充。

### 它是如何工作的

lit-html 依赖于 JavaScript 的 [ECMAScript 2015 (ES6)模板文字、](https://blog.logrocket.com/javascript-es6-5-new-abstractions-to-improve-your-code-54a369e82407/) HTML 的 [`<template>`标签](https://www.w3schools.com/tags/tag_template.asp)，以及其他一些未公开的技术来运行和提升性能。

下面是一个简单的 lit-html 模板示例:

```
html`
    <div>
        <h1>Hello ${recepient}</h1>
        <div>Hello ${content}</div>
    </div>
`

```

我们可以看到模板文字的一种高级形式的使用，称为[标记模板](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates)。标签，在这个例子中是 HTML，是一个处理模板文字的函数。有趣的是，模板字面量允许[将](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates_and_escape_sequences)[特定领域语言](https://en.wikipedia.org/wiki/Domain-specific_language)如 HTML(特定于 web 领域)的嵌入到 JavaScript 中。

lit-html 用占位符替换表达式，在创建模板的过程中创建一个字符串。创建模板后，lit-html 优化模板并记录占位符的位置；这些位置被称为`parts`。然后它删除占位符，用值填充`parts`，并在值改变时更新`parts`。

## 构建由 lit-html 支持的组件

尽管 lit-html 模板可以用来创建和操作 DOM 元素，但是这些模板并不依赖于任何组件模型。对于组件构建，我们可以使用 LitElement。

### 什么是 LitElement？

LitElement 是一个很棒的库，用于构建由 lit-html 模板支持的 web 组件。根据[官网](https://lit-element.polymer-project.org/)的说法，它是“一个用于创建快速、轻量级 web 组件的简单基类。”

它提供了一种简单的方法来创建本地和 web UI 组件，这些组件可以在项目中任何有 HTML 的地方使用，而不管使用的是前端库还是框架。

下面是一个 LitElement 组件的示例:

```
import {LitElement, html} from 'lit-element';
class TodoItem extends LitElement {
  static get properties() {
    return {
      item: {type: String},
    };
  }

  render() {
    return html`
      <div class="todo-item">
        <span>${this.item}</span>
      </div>
    `;
  }
}

customElements.define('todo-item', TodoItem);

```

LitElement 组件遵循[面向对象编程范例](https://en.wikipedia.org/wiki/Class_(computer_programming)#:~:text=In%20object%2Doriented%20programming%2C%20a,(member%20functions%20or%20methods)，这是一种创建可重用代码蓝图(类)和蓝图实例(对象)的编程范例。

我们在 LitElement 中将属性声明为[静态属性](https://javascript.info/static-properties-methods)，并创建一个 render 方法，该方法返回包含已定义模板的模板结果。接下来，我们定义组件，使其在整个项目中可用。

### 与现有前端框架的比较

用 LitElement 构建的组件是框架或库不可知的；它们遵循 [web 组件标准](https://developer.mozilla.org/en-US/docs/Web/Web_Components)，这意味着您可以创建可重用的定制元素，同时将它们的功能与代码的其余部分分离开来。shadow DOM 用于 LitElement 中的封装。

与许多前端库不同，当节点发生变化时，它们需要 diffing 来优化重新呈现 DOM，LitElement 利用 lit-html 来定义和呈现 html 模板。DOM 更新快如闪电，因为 lit-html 只重新呈现 UI 的动态部分。

## 用 LitElement 构建待办事项列表

让我们通过建立一个简单的待办事项列表来实践并巩固我们的 lit-html 和 LitElement 知识。我们将遵循[原子方法](https://bradfrost.com/blog/post/atomic-web-design/)来创建组件。首先，我们建造原子，然后将它们堆叠起来建造分子，最后创造有机体。

项目目录将如下所示:

![To Do List Project Directory](img/38e39b17d4f66f53afb957fb2257a6b1.png)

### listelement 项目中的 css 文件

LitElement 的样式存储在一个名为`todo-style.js`的 JavaScript 文件中。使用 lit-html 的 CSS 标签，我们可以用 JavaScript 写出样式:

```
import {css} from 'lit-element/lit-element.js';

export const style = css`
    *{
        font-family: monospace
    }
    ul{
        margin:0;
        padding-inline-start: 0
    }
    input{
        padding: 5px;
        font-size: 1rem;
        width:240px
    }
`

```

要使样式对 LitElement 可用，只需导入样式并在静态样式属性中定义它，如下所示:

```
import { style } from "./todo-style";

static get styles() {
    return [style];
}

```

### 在 LitElements 中使用属性

像大多数常见的前端框架一样，父组件可以通过属性和它们的子组件通信，反之亦然。您可以使用下面的方法定义属性，并查看代码片段中的示例:

*   属性:`<p id="${...}"></p>`
*   布尔属性:`?disabled="${...}"`
*   属性:`.value="${...}"`
*   事件处理程序:`@event="${...}"`

```
class TodoElement extends LitElement {
        static get properties(){
            return {
                list:{type:Array},
            }
        }
        static get styles() {
            return [style];
        }
        constructor() {
            super();
            this.list = JSON.parse(localStorage.getItem('litTodoList')) || [];
            this.todo = '';
        }
render() {
return html`
<div class="todo-body">
<h1>😎 Lit Todo 😎</h1>
<todo-list .list=${this.list}></todo-list>
</div>
`;
}
}
```

在这个元素中，我们使用一个属性列表，在属性名称前用一个点表示，来传递待办事项列表，我们将从 [`localStorage`](https://blog.logrocket.com/localstorage-javascript-complete-guide/) 中获取该列表。

我们可以轻松地映射到`TodoList`组件中的列表，并将每个`todo`传递给`TodoItem`组件:

```
class TodoList extends LitElement {
    static get properties(){
        return {
            list:{type:Array}
        }
    }
    static get styles() {
        return [style];
      }
  render() {
    return html`
        <ul>
            ${this.list.map((todoItem) => html`<li><todo-item item=${todoItem}></todo-item></li>`)}
        </ul>
    `;
  }
}

```

我们还可以向父组件发出事件，类似于在`TodoInput`组件中的做法:

```
class TodoInput extends LitElement {
  static get properties() {
    return {
      todo: {type: String},
      onInput: {type: Function},
      onKeyPress: {type: Function},
      createNewToDoItem: {type: Function},
    };
  }
  static get styles() {
      return [style];
  }
  render() {
    return html`
    <div class="todo-input">
        <input
            type="text"
            .value=${this.todo}
            @input=${this.onInput}
            @keypress=${this.onKeyPress}
        />
        <button
          class="todo-button"
          @click=${this.createNewToDoItem}
        >Add</button>
    </div>
    `;
  }
}

```

这里，要发出的函数在静态属性方法中声明，并绑定到其父组件中的函数:

```
    handleKeyPress(e) {
        if (e.target.value !== '' && e.key === 'Enter') {
            this.createNewToDoItem();
        }
    }

    handleInput(e) {
        this.todo = e.target.value;
    }

    createNewToDoItem(){
        const todoList = JSON.parse(
         localStorage.getItem('litTodoList')
        ) || []
        todoList.push(this.todo)
        this.list = todoList
        localStorage.setItem('litTodoList', JSON.stringify(todoList))
        this.todo = ""
    }

<todo-input
  todo=${this.todo}
  .onInput=${this.handleInput.bind(this)}
  .onKeyPress=${this.handleKeyPress.bind(this)}
  .createNewToDoItem=${this.createNewToDoItem.bind(this)}
></todo-input>

```

我给你留了一个小任务！向`todo`应用程序添加删除功能。这里是[源代码](https://github.com/Akhigbe-E/lit-todo)的链接。

## 结论

在本文中，我们研究了什么是 lit-html，它是如何工作的，以及在项目中使用它的好处。我们还通过构建一个简单的应用程序查看了 LitElement 的一些基本部分。利用本文中获得的知识，您现在应该能够向您的项目添加 LitElements 了。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。