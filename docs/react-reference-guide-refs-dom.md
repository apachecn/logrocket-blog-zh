# React 参考指南:Refs 和 DOM 

> 原文：<https://blog.logrocket.com/react-reference-guide-refs-dom/>

父组件与其子元素交互的标准方式是通过道具，例如，要修改一个子组件，你需要用新的道具重新渲染它。那不是新闻。

但是，当您需要访问在组件的 render 方法中创建的 DOM 节点或 React 元素时，会发生什么呢？道具在这里帮不了你什么。

这就是裁判派上用场的地方。

#### *向前跳转:*

* * *

## 何时使用参考文献

当您试图执行命令性动作时，引用的最佳用例出现，例如:

*   管理焦点、文本选择或媒体播放
*   使用命令式动画
*   与第三方 DOM 库集成

为了构建更加一致的 React 应用程序，您应该避免对任何可以以声明方式完成的事情使用 refs，例如，通过带有状态和属性的标准 React 数据流。例如，不是在一个`Modal`组件上公开`open()`和`close()`方法，而是传递一个`isOpen`属性，并通过 React 状态在内部管理它。

* * *

## 不要过度使用裁判

当你第一次遇到引用或者是第一次做出反应时，你可能会陷入一个陷阱，认为引用是一种在你的应用程序中“完成事情”的简单方法。它们当然符合您可能习惯使用的命令式模型。

如果您发现自己有这种想法，请花点时间重新评估如何通过典型的 React 数据流(即通过状态和道具)来解决这种数据流。

通常，手头的问题可以通过将状态提升到组件树中更高的父组件并将该状态值向下传递到所需的组件/元素来解决。

##### 查看我们关于如何使用 React 悬念提升组件状态的教程。

> **注意**，接下来的部分展示了如何使用 Refs 的例子。这些例子使用了 React 16.3 中引入的 API`React.createRef`。如果你使用的是 React 的早期版本，[参见](#callbackrefs)下面关于使用回调引用的部分。

* * *

## 创建参考

创建 ref 对象有两个步骤。

1.)使用`React.createRef`创建一个引用对象:

```
class App extends React.Component {
  // see this 
  this.myRef = React.createRef()
}
```

您可以通过调用`createRef`对象来创建一个 ref，并且通常将它分配给一个实例属性，例如上面例子中的`this.myRef`。这样做是为了在整个组件中引用 ref。

2.)在 render 方法中引用创建的`ref`:

```
class App extends React.Component {
   myRef = React.createRef()
   render() {
    //look here
    return <div ref={this.myRef}>I am a div</div>
  }
}
```

创建 ref 对象后，通过特殊的`ref`属性将它传递给所需的元素。

* * *

## 访问参考文献

在向 component `render`方法中的一个元素传递一个引用之后，对 DOM 节点的引用就变得可以访问了，如下所示:

```
const domNode = this.myRef.current
```

注意，`current`属性是在创建的 ref 对象上引用的，其中`this.myRef`表示通过`ref`属性传递给 DOM 节点的 ref。

看看上面的代码块。变量`domNode`包含什么值？嗯，这取决于`ref`属性被传递到的节点的类型——它总是不同的。

以下是不同的选项:

1.)当`ref`属性被传递给`HTML`元素时，`ref`对象接收底层 DOM 元素作为其`current`属性。

```
//render 
<div ref={this.myRef} />

//node contains HTMLElement (div) 
const node = this.myRef.current
```

2.)当`ref`属性被传递给一个定制类组件时，`ref`对象接收组件的挂载实例。

```
//render 
<MyClassComponent ref={this.myRef} />

//node contains MyClassComponent class instance
const node = this.myRef.current
```

3.)引用不能传递给函数组件，因为它们没有实例。

```
//render: don't do this. 
<MyFunctionalComponent ref={this.myRef}
```

下一节将演示上述节点类型的示例。

### 添加对 DOM 元素的引用

考虑在点击按钮时聚焦文本输入的例子:

![Focusing a Text Input on Clicking a Button](img/e1e6156de7fc786dac78556fc2478c06.png)

为此，创建一个保存文本输入 DOM 节点的 ref 对象，并访问 DOM API 来聚焦输入，如下所示:

```
class App extends React.Component {
  // create a ref to hold the textInput DOM element
  textInput = React.createRef();

  focusTextInput = () => {
    // get the input dom node from the ref object
    const inputDOMNode = this.textInput.current;
    // Use the browser imperative api: call the focus method
    inputDOMNode.focus();
  };

  render() {
    return (
      <div>
        {/* pass a ref attribute to the input element */}
        <input type="text" ref={this.textInput} />
        <input
          type="button"
          value="Focus input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

注意，当组件挂载时，React 用输入 DOM 元素分配 ref 对象的当前属性。当组件被卸载时，它被分配给`null`。

ref 更新也保证发生在`componentDidMount`或`componentDidUpdate`生命周期方法之前。请看下面的演示。

### 添加对类组件的引用

如果我们决定重构前面的例子，将一个 ref 属性传递给一个类组件，例如`MyTextInput`，会怎么样呢？变化有多大？

请看下面:

```
class CustomTextInput extends React.Component {
  myInputRef = React.createRef();
  focusTextInput = () => {
    // get dom node from ref props
    const inputDOMNode = this.myInputRef.current;
    inputDOMNode.focus();
  };

  render() {
    // pass ref on from props
    return <input type="text" ref={this.myInputRef} />;
  }
}
```

我们有一个类组件`CustomTextInput`，它有一个`focusTextInput`函数。注意，`CustomTextInput`组件并不调用这个函数，而是创建和访问文本输入 DOM 节点。

因此，如果您想从另一个组件中触发这个函数处理程序，例如，在 mount 上或者当单击一个按钮时聚焦输入，那么下面的方法非常合适:

```
class App extends React.Component {
  // create a ref to hold the textInput DOM element
  textInputClassInstance = React.createRef();

  focus = () => {
    // call the child handler
    // this is possible because the ref object received the child class instance
    this.textInputClassInstance.current.focusTextInput();
  };

  componentDidMount() {
    this.focus();
  }

  render() {
    return (
      <div>
        {/* pass a ref attribute to the class component. Ref receives the class instance  */}
        <CustomTextInput ref={this.textInputClassInstance} />
        <input type="button" value="Focus input" onClick={this.focus} />
      </div>
    );
  }
}
```

请参见下面的演示:

参考和功能组件

### 请记住，您不应该在函数组件上使用 ref 属性，因为它们没有实例:

如果你想把一个引用传递给一个函数组件，可以考虑使用`forwardRef`，也许可以和`useImperativeHandle`一起使用。您还可以考虑将所述组件转换为类组件。

```
class Parent extends React.Component {
  textInput = React.createRef();

  render() {
    // This will NOT work!
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```

请参阅 React Hooks 参考指南中的 useImperativeHandle。

##### ***See useImperativeHandle in action in our React Hooks reference guide.***

如前所述，您不应该将引用传递给功能组件，但是您可以在功能组件中创建和使用引用，如下所示:

向父组件公开 DOM 引用

```
function CustomTextInput(props) {
  // create ref object using useRef
  const textInput = useRef(null);

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```

* * *

## 有时，您可能希望从父组件访问子组件的 DOM 节点。这并不理想，因为它破坏了组件封装。然而，有一些合法的用例可以保证这种技术，例如，触发焦点或测量子 DOM 节点的大小或位置。

那么，你应该如何处理这个问题呢？

首先，您可以添加一个对子组件的引用，就像我们在前面的例子中所做的那样。这不是一个完美的解决方案，因为您得到的是一个组件实例，而不是子 DOM 节点。此外，这不适用于功能组件。

对于 React 16.3 或更高版本，请考虑对此类情况使用 ref 转发。引用转发允许您向父组件公开子组件的 DOM 节点。

***准备好深入了吗？阅读我们关于 forwardRef 的高级教程。***

##### ***Ready to go deeper? Read our advanced tutorial on forwardRef.***

React 16.2 或更低版本不支持引用转发。那你是做什么的？考虑将 ref 作为不同名称的属性显式传递。

注意，上面的方法需要向子组件添加一些代码。在更严格的用例中，您可能无法控制子组件的实现。你最好的选择是使用`[findDOMNode()](https://reactjs.org/docs/react-dom.html#finddomnode)`，但是注意这在`[StrictMode](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)`中是不被鼓励和反对的。

```
function CustomTextInput(props) {
  return (
    <div>
      {/* Pass ref to input node*/}
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  // create ref object 
  inputElement = React.createRef();

  render() {
    return (
       {/* pass ref object as a differently named prop: inputRef*/}
      <CustomTextInput inputRef={this.inputElement} />
    );
  }
}
```

如果不需要 do，强烈建议避免公开 DOM 节点。

回调参考

* * *

## 在我们讨论的所有例子中，ref 对象都是通过`ref`属性直接传递的。但是，React 支持另一种设置 refs 的方式；这些被称为回调引用。

不是通过`ref`属性传递一个`ref`对象:

传递函数:

```
myRefObject = React.createRef()
...
<div ref={myRefObject} />
```

React 将组件实例或 DOM 元素作为参数传递给函数。然后可以在其他地方存储和访问！

```
myRefCallback = (node) => {}
...
<div ref={myRefCallback} />
```

当设置和取消设置引用时，回调引用给你更多的控制来执行操作。记住，当组件挂载时，用 DOM 元素调用 ref 回调，当组件卸载时，用`null`调用它。

考虑下面的例子，它创建了一个 ref 回调并将一个 DOM 节点存储在一个实例属性中——这是一种常见的实现模式。

请参见下面的演示:

```
class App extends React.Component {
  //create instance variable
  textInput = null;

  setTextInputRef = (element) => {
    // save DOM element received in the instance variable: textInput
    this.textInput = element;
  };

  focusTextInput = () => {
    // Focus the text input using the raw DOM API
    if (this.textInput) this.textInput.focus();
  };

  componentDidMount() {
    // autofocus the input on mount
    this.focusTextInput();
  }

  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    return (
      <div>
        <input type="text" ref={this.setTextInputRef} />
        <input
          type="button"
          value="Focus  input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

与对象引用一样，可以在组件之间传递回调引用，例如父组件和子组件。请参见下面的示例:

在上面的例子中，`Parent`组件保存对应于实例变量`inputElement`中的子文本输入的 DOM 值。

请注意，ref 回调通过一个不同名称的属性传递给子组件`CustomTextInput`:`inputRef`。然后，`CustomTextInput`将回调传递给在`<input>`元素上设置的`ref`属性。

```
function CustomTextInput(props) {
  return (
    <div>
      {/*child component passes ref to callback*/}
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      {/* pass a callback ref to the child component*/}
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

回调引用的警告

如果你定义一个`ref`回调作为一个内联函数，那么每次渲染都会创建一个新的函数实例。然后回调被调用两次:第一次是 will `null`，第二次是 DOM 元素。本质上，React 必须清除旧的 ref 并建立一个新的 ref。

### 在大多数情况下，这并不重要，但是为了避免这种情况，将`ref`方法定义为类的绑定方法(对于类组件)。

传统 API:字符串引用

值得一提的是，早期的 ref API 支持将`ref`属性作为普通字符串，例如`myTextInput`和作为`this.refs.myTextInput`访问的 DOM 节点。

* * *

## 不要再这样了。字符串引用现在被认为是遗留的，它们有一些[问题](https://github.com/facebook/react/pull/8333#issuecomment-271648615)，并且它们可能会在 React 的未来版本中被移除。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

## .

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).