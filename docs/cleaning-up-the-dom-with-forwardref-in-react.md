# 在 React 中使用 forwardRef 清理 DOM - LogRocket 博客

> 原文：<https://blog.logrocket.com/cleaning-up-the-dom-with-forwardref-in-react/>

***编者按:**本帖更新于 2021 年 3 月 18 日。*

## 介绍

在本教程中，我们将回顾 React 中转发引用的概念，并理解它如何帮助我们管理与 DOM 的交互。为了获得更有吸引力的体验，我们将介绍如何创建引用、将创建的引用附加到 DOM 元素和类、使用 forwardRef 方法等等。

同样值得注意的是，我们将经常参考文档页面，以建立在已经存在的信息上，并通过相关的真实生活示例和片段来证明我们的概念，这些示例和片段将被托管在 [CodeSandbox](http://codesandbox.io) 上。

## React 中 forwardRef 是什么？

[React forwardRef](https://reactjs.org/docs/forwarding-refs.html) 是一种允许父组件将引用向下传递(即“转发”)给其子组件的方法。在 React 中使用 forwardRef 可以为子组件提供对由其父组件创建的 DOM 元素的引用。这样，孩子就可以在任何使用该元素的地方阅读和修改它。

## forwardRef 在 React 中是如何工作的？

要理解引用转发，我们必须首先理解什么是引用，它们是如何工作的，并涵盖一些用例。通常在 React 中，父组件通过 props 将数据传递给它们的子组件。

要改变一个子组件的行为，你可以用一组新的道具来渲染它。要修改子组件，使其表现出稍微不同的行为，我们需要一种方法来进行这种更改，而无需达到状态或重新呈现组件。

我们可以通过引用来实现这一点。通过引用，我们可以访问由元素表示的 DOM 节点。因此，我们可以修改它，而不接触它的状态或重新渲染它。

因为 refs 持有对 DOM 元素本身的引用，所以我们可以用 React 库中没有的原生 JavaScript 函数来操作它。例如，当点击一个按钮时，我们可以启动输入字段的焦点:

```
import * as React from "react";
import ReactDOM from "react-dom";

export default function App() {
 const ref = React.useRef();

 function focus() {
   ref.current.focus();
 }

 return (
   <div className="App">
     <input ref={ref} placeholder="my input" />
     <button onClick={focus}>Focus</button>
   </div>
 );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

你也可以在 [CodeSandbox](https://codesandbox.io/s/input-element-ref-fp220) 找到代码。

类似地，我们可以使用 JavaScript 来实现类似的效果，尽管不建议这样做，甚至认为在使用 React 时直接访问 DOM 是一种不好的做法。等同于获取引用的纯 JavaScript 代码是:

```
document.getElementById('myInput).focus()
```

通过 ref，我们操纵光标，每当单击按钮时，光标自动聚焦在输入元素上。如果没有 refs，我们必须使用 state 来检查输入字段是否应该聚焦——这是在做出决定之前，在这种情况下通常是不必要的。

这个信息很重要，因为`forwardRef`允许你在内部定义`ref`将指向什么元素。

### React 中何时使用引用

在 React 中有[个引用可以用`forwardRef`指向。正如在官方](https://blog.logrocket.com/a-guide-to-react-refs/) [React 文档](https://reactjs.org/docs/refs-and-the-dom.html)中看到的，我们可以使用 refs 来完成各种任务:

#### 管理焦点、文本选择或媒体播放

让我们假设你有一个输入组件。在应用程序的某些部分，当用户单击按钮时，您可能希望光标聚焦在它上面。更有意义的是只修改输入组件的特定实例而不改变状态(通过 refs)，而不是改变状态(通过 *props* )，这会导致组件每次都重新呈现。类似地，我们可以使用 refs 来控制音乐或视频播放器的状态(暂停、播放、停止)，而无需在我们单击按钮时重新呈现(更改状态)。

#### 递增值

想想一个中等的拍手按钮。实现类似特性的一个快速方法是，每当用户点击拍手时，递增存储在状态中的计数值。然而，这可能效率不是很高。每当用户单击 clap 按钮时，它将重新呈现，并且，如果我们发送一个网络请求来将值存储在服务器中，它将在按钮被单击时被发送。有了 refs，我们可以定位那个特定的节点，并在每次用户单击按钮时递增它，而不会导致重新呈现，最后，我们可以向服务器发送一个带有最终值的请求。

通过使用这种技术，我们可以提高应用程序的性能，因为我们可以防止重新渲染的发生，尽管它应该只在特定的情况下使用，并且要小心，因为如果使用不当，它可能会导致意想不到的行为。

#### 触发命令式动画

我们可以使用引用来触发元素之间的动画，这些元素依赖于它们自己的下一个状态，但存在于不同的组件中(这个概念称为引用转发)。Refs 还可以用来简化与第三方 DOM 库的集成以及管理多步表单值状态等。

## 在类组件中使用引用

### 在 React 中创建引用

为了创建 ref，React 提供了一个名为`React.createRef()`的函数。创建后，可以通过 ref 属性将它们附加到 React 元素。同样值得注意的是，refs 与 state 有些相似。构建构件时，会将参照指定给该构件的实例属性，以确保可以在构件中的任何位置参照这些参照:

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.newRef = React.createRef(); //newRef is now available for use throughout our component
  }
 ...
}
```

至此，我们已经创建了一个名为`newRef`的 Ref。要在我们的组件中使用这个 Ref，我们只需将它作为一个值传递给`ref`属性，如下所示:

```
class MyComponent extends React.Component {
 ...
  render() {
    return <div ref={this.myRef} />;
  }
}
```

我们在这里附加了 Ref，并传入了`newRef`作为它的值。因此，我们现在能够在不改变状态的情况下更新它。更多信息，[了解如何在 React](https://blog.logrocket.com/how-to-use-react-createref-ea014ad09dba/) 中使用 createRef。

### 附加参考

Refs 在组件渲染时创建，可在`componentDidMount()`或`constructor()`中定义。因此，它们可以附加到 DOM 元素或类组件，但不能附加到函数组件，因为它们没有实例。

您定义的每个 Ref 将代表 DOM 中的一个节点。因此，当您想要在一个`render()`函数中引用该节点时，React 提供了一个引用该节点的`current`属性。

```
const DOMNode = this.newRef.current; // refers to the node it represents
```

ref 的值根据它引用的节点类型(类组件或 DOM 元素)而不同。

为了更好地理解引用和它们引用的节点类型，以及与每个相关联的默认值，让我们考虑一下来自[文档](https://reactjs.org/docs/refs-and-the-dom.html)的这段内容:

*   当在 HTML 元素上使用 ref 属性时，在构造函数中用`React.createRef()`创建的 ref 接收底层 DOM 元素作为其`current`属性
*   当 ref 属性用于定制类组件时，ref 对象接收组件的挂载实例作为其`current`，即组件属性、状态和方法

让我们用一个小的视频播放器来演示这个概念。视频播放器将有一些暂停和播放功能。为了继续构建，创建一个新的 [CodeSandbox](https://codesandbox.io) 项目，并添加以下代码:

```
import ReactDOM from "react-dom";
import React, { Component } from "react";

export default class App extends Component {
  constructor(props) {
    super(props);
    this.myVideo = React.createRef();
  }
  render() {
    return (
      <div>
        <video ref={this.myVideo} width="320" height="176" controls>
          <source
            src="https://res.cloudinary.com/daintu6ky/video/upload/v1573070866/Screen_Recording_2019-11-06_at_4.14.52_PM.mp4"
            type="video/mp4"
          />
        </video>
        <div>
          <button
            onClick={() => {
              this.myVideo.current.play();
            }}
          >
            Play
          </button>
          <button
            onClick={() => {
              this.myVideo.current.pause();
            }}
          >
            Pause
          </button>
        </div>
      </div>
    );
  }
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

你也可以在这里找到代码[。](https://codesandbox.io/s/react-ref-sample-njkbc)

这里，我们使用 ref 通过调用视频上的 pause 和 play 方法来暂停和播放我们的视频播放器。当单击暂停或播放按钮时，将在视频播放器上调用该函数，无需重新渲染。

#### 对函数组件使用引用

引用不能附加到功能组件。但是，我们可以定义 refs 并将它们附加到 DOM 元素或类组件上。底线是——功能组件没有实例，所以您不能引用它们。

但是，如果您必须将一个 ref 附加到一个函数组件上，官方的 React 团队建议您将该组件转换成一个类，就像您需要生命周期方法或状态时所做的那样。

### 条件参考

除了传递默认的`ref`属性，我们还可以传递函数来设置 refs。这种方法的主要优点是，您可以更好地控制何时设置和取消设置 ref。这是可能的，因为它使我们能够在触发某些动作之前确定 ref 的状态。考虑下面来自[文档页面](https://reactjs.org/docs/refs-and-the-dom.html)的片段:

```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = null;
    this.setTextInputRef = element => {
      this.textInput = element;
    };
    this.focusTextInput = () => {
      // Focus the text input using the raw DOM API
      if (this.textInput) this.textInput.focus();
    };
  }
  componentDidMount() {
    this.focusTextInput();
  }
  render() {
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

我们没有在构造函数中定义 refs，而是将初始值设置为 null。这种方法的好处是`textInput`不会引用节点，直到组件被加载(当元素被创建时)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 在函数组件中使用引用

在函数组件中，我们不能简单地使用函数`createRef`，因为它每次被调用时都会创建一个新的引用。我们可以选择使用效果和状态来处理引用，但是 React 用`useRef`提供了一个更简单的方法。`useRef`负责每次返回与初始渲染时相同的引用。

使用`useRef`很简单，就像我们在本教程开始的第一个例子中展示的那样。

## 使用`forwardRef`转发 React 中的参考

当一个子组件需要引用其父组件的当前节点时，父组件需要一种方法将其 ref 向下发送到子组件。这种技术被称为引用转发。

[引用转发](https://reactjs.org/docs/forwarding-refs.html)是一种通过组件自动将引用传递给其子组件的技术。这在构建可重用组件库时非常有用。`forwardRef`是用于将引用传递给子组件的函数。

让我们以一个具有 InputText 组件的新库为例，它将提供许多功能，不过，现在，我们将保持它的简单性:

```
const InputText = (props) => (
<input {...props} />
));
```

在整个应用程序中，`InputText()`组件的使用方式类似于常规的 DOM 输入，因此为了管理焦点、选择或与之相关的动画，访问它的 DOM 节点可能是不可避免的。

在下面的例子中，应用程序中的其他组件无法访问由`InputText()`组件生成的 DOM input 元素，因此限制了我们已经预见到的满足应用程序需求所需的一些操作，比如以编程方式控制输入的焦点。

这里是当`React.forwardRef`进入以获得作为 props 传递的 ref，然后将它转发到它呈现的 DOM 输入:

```
const InputText = React.forwardRef((props, ref) => (
 <input ref={ref} {...props} />
));

```

既然我们的组件支持`forwardRef`，那么让我们在应用程序的上下文中使用它来构建一个按钮，当它被点击时会自动聚焦输入。代码如下所示:

```
import * as React from "react";
import ReactDOM from "react-dom";

const InputText = React.forwardRef((props, ref) => (
 <input ref={ref} {...props} />
));

export default function App() {
 const ref = React.useRef();

 function focus() {
   ref.current.focus();
 }

 return (
   <div className="App">
     <InputText ref={ref} placeholder="my input" />
     <button onClick={focus}>Focus</button>
   </div>
 );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

你也可以在[code sandbox](https://codesandbox.io/s/thirsty-kowalevski-pxyos?file=/src/index.js:68-156)上看到它的动作。

下面是对上面代码的澄清:

*   我们在需要引用的组件中定义一个引用，并将其传递给按钮组件
*   React 将传递 ref，并通过将其指定为 JSX 属性将其向下转发给`<input ref={ref}>`
*   当引用被附加时，`ref.current`将指向`<input>` DOM 节点
*   `InputRef` 组件中的第二个 ref 参数只存在于用 `React.forwardRef` 调用定义组件时
*   常规函数或类组件不接收 ref 参数，ref 在 props 中也不可用
*   Ref 转发不限于 DOM 组件。您也可以将引用转发给类组件实例

## 结论

React 中的 Refs 是一个强大的工具，它支持直接访问 DOM 节点，因此提供了一系列全新的方法和选项来构建性能更高、功能更丰富、更简洁的组件。

然而，在 React 中，直接访问 DOM 通常被认为是一种不好的做法，由于某种原因，如果使用不当，它会将所有的好处变成真正的问题。一般来说，应该避免使用它，并且只在非常特殊的情况下使用，并且要经过彻底的检查。

在本教程中，我们介绍了引用和引用转发的主题，我们查看了一些用例，并使用函数和类组件构建了代码。要阅读更多关于参考文献的信息，请点击此处查看相关文档。

感谢阅读！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)