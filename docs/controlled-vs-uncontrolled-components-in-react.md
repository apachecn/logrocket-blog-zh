# React 中受控与非受控组件

> 原文：<https://blog.logrocket.com/controlled-vs-uncontrolled-components-in-react/>

`form`是 web 开发中最常用的 HTML 元素之一。自从引入 React 以来，处理表单的方式已经在许多方面发生了变化。

在 React 中，有两种方法可以处理组件中的表单数据。第一种方法是使用组件中的状态来处理表单数据。这就是所谓的受控组件。第二种方法是让 DOM 在组件中自己处理表单数据。这就是所谓的非受控组件。

在本教程中，我们将解释 React 中受控组件和非受控组件的区别。我们还将通过实际例子演示每种方法的工作原理。

## React 中的受控组件是什么？

[React 中的受控组件](https://itnext.io/controlled-vs-uncontrolled-components-in-react-5cd13b2075f9)是那些由组件状态处理表单数据的组件。

[表单](https://reactjs.org/docs/forms.html#controlled-components)用于在文档部分存储信息。来自该表单的信息通常被发送到服务器以执行操作。这些数据由表单输入元素和控制元素保存，如`input`、`select`、`textarea`等。，维护和控制它们的状态或值。

我这么说是什么意思？

每个表单元素都包含一个值。该值可以由用户或浏览器键入(`input`、`textarea`)或选择(`checkbox`、`select`、`radiobutton`等)。当元素的值发生变化时(由键入或选择动作触发)，它会相应地更新。

您可以在元素的`HTMLElement`实例中使用`.value`属性来获取元素的值。您还可以使用`.value`属性来设置表单元素中的值。

现在，我们可以在组件中使用 state 来保存或管理表单元素中元素的值。这里有一个例子:

```
function App() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");

  function onSubmit() {
    console.log("Name value: " + name);
    console.log("Email value: " + email);
  }
  return (
    <form onSubmit={onSubmit}>
      <input
        type="text"
        name="name"
        value={name}
        onChange={(e) => setName(e.target.value)}
        required
      />
      <input
        type="email"
        name="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        required
      />
      <input type="submit" value="Submit" />
    </form>
  );
}

```

这里我们有两种状态:`name`和`email`。这些状态被分配给`name`和`email`输入元素的`value`属性。

`name`状态保存`name`输入元素的值。当在`name`输入中输入一个值时，附属于它的`onChange`事件使用`setName`更新函数将输入值设置为`name`状态。

`email`状态保存`email`输入元素的值。附加到`email`输入的`onChange`事件通过`setEmail()`改变`email`状态，以保持输入到元素中的值。

如你所见，我们的输入元素`name`和`email`的值是由 React 状态控制的；状态成为输入元素的“唯一真实来源”。因此，上面显示的`App`组件是一个受控组件。

使用受控组件的缺点是，随着更多的控制元素添加到 form 元素中，组件中的状态数量会增加。

## React 中有哪些不受控制的组件？

[非受控组件](https://reactjs.org/docs/uncontrolled-components.html)是那些由 DOM 本身处理表单数据的组件。“不受控制”是指这些组件不受反应状态的控制。

表单元素的值传统上由 DOM 控制并存储在 DOM 中。我们必须引用表单元素的实例来从 DOM 中检索它们的值。

```
function App() {
  function onSubmit() {
    console.log("Name value: " + window.name.value);
    console.log("Email value: " + window.email.value);
  }
  return (
    <form onSubmit={onSubmit}>
      <input type="text" name="name" id="name" required />
      <input type="email" name="email" id="email" required />
      <input type="submit" value="Submit" />
    </form>
  );
}

```

在上面的代码中，我们给`name`和`email`输入元素分配了 ID 属性，分别为值`name`和`email`。当提交表单时，我们使用这些`id`属性来获取输入元素的值。

上面的组件是不受控制的组件，因为 React 对表单输入元素的值没有控制。

在这个例子中，我们直接使用了 DOM APIs。现在让我们以一种反应的方式重构代码:

```
function App() {
  const nameRef = useRef();
  const emailRef = useRef();

  function onSubmit() {
    console.log("Name value: " + nameRef.current.value);
    console.log("Email value: " + emailRef.current.value);
  }
  return (
    <form onSubmit={onSubmit}>
      <input type="text" name="name" ref={nameRef} required />
      <input type="email" name="email" ref={emailRef} required />
      <input type="submit" value="Submit" />
    </form>
  );
}

```

我们创建了两个 [React](https://reactjs.org/docs/refs-and-the-dom.html) [refs](https://reactjs.org/docs/refs-and-the-dom.html) 、`nameRef`和`emailRef`，并分别将它们赋给`name`和`email`输入的`ref`属性。这将导致`refs`在其`.current`属性中保存元素的`HTMLElement`实例。从`.current`中，我们可以引用`.value`属性来获取输入元素的值。

## 受控组件与非受控组件:主要区别

现在，我们已经了解了什么是反应受控组件和非受控组件，让我们回顾一下它们之间的一些主要区别:

*   受控组件是可预测的，因为表单元素的状态是由组件处理的
*   不受控制的组件是不可预测的，因为在组件的生命周期中，表单元素可能会丢失其参考，并且可能会被其他来源更改/影响
*   受控组件使您能够有效地对表单进行表单验证。什么改变了表单元素并不重要。他们的值在我们当地的州是安全的，所以我们在那里进行验证
*   使用受控组件，您可以很好地控制表单元素的值。你可以决定它们如何进行，什么可以插入，什么不可以插入

那么在 React 项目中应该使用哪一个呢？问题不在于受控组件和非受控组件是否更好，而是哪个更适合您的用例，更符合您的个人偏好。显然，受控组件可以让您更好地控制数据，但是如果您更喜欢在项目中使用不受控制的组件，那就更好了。

在 React 中，没有明确的规则来帮助您确定何时以及如何使用非受控组件和受控组件；这完全取决于你想在多大程度上控制你的输入。

## 结论

在本教程中，我们放大了表单元素和表单数据，包括一般情况和 React 框架内的情况。接下来，我们介绍了在 React 组件中处理表单数据的两种方法:受控和非受控。最后，我们深入研究了这两种类型的组件，并用实例演示了它们的行为。

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