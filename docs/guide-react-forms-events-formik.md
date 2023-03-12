# 使用 Formik 反应表单和事件的指南

> 原文：<https://blog.logrocket.com/guide-react-forms-events-formik/>

那么，您已经开始构建 React 应用程序，现在您想知道如何在 React 中使用表单？不要担心。

在本教程中，我将解释 React 如何处理表单和事件。我还将带您了解如何编写代码来帮助从不同的表单组件获取输入，并在 React 应用程序中提交数据。

最后，我将向您展示如何使用 [Formik](https://formik.org/) 库构建各种表单，从简单到复杂。

## React 中的表单和事件

在我们开始编写代码之前，重要的是要注意 React 如何处理表单和事件。考虑下面这个简单的`input`标记:

```
<input type="text" placeholder="First Name"/>
```

要获取输入标记中的数据，您需要能够以某种方式获取内容。这对于 React 来说并不容易，因为没有像 Vue.js' `v-model`或 Angular 的`ng-model`这样的预置 API 来帮助这一点。

但这是可以做到的。怎么会？

通过确保视图(`input`、`select`或`textarea`字段)总是与状态同步。这意味着输入元素的值必须首先在状态中创建，然后在`render()`函数中设置为该状态值。

```
import React, { useState } from 'react'
​​
​​export default function App() {
​​  const [inputValue] = useState('')
​​
​​  return (
​​    <div className="App">
​​      <form>
​​        <label>Name</label>
​​        <input type="text" value={inputValue} />
​​        <input type="submit" value="Submit" />
​​      </form>
​​    </div>
​​  )
​​}
```

正如您在上面的代码块中看到的，`input`元素有一个值 `inputValue`，这意味着输入元素的值被设置为与状态中的`inputValue`同步。因此，在输入字段中输入的任何内容都将存储在状态中，但是这里有一个问题。

React 不会自动检测输入元素的变化。为此，我们使用一个`onChange`表单事件来检查更改。

```
​​import React, { useState } from 'react'
​​
​​export default function App() {
​​  const [inputValue, setInputValue] = useState('')
​​
​​  const handleChange = (e) => {
​​    setInputValue(e.currentTarget.value)
​​  }
​​
​​  return (
​​    <div className="App">
​​      <form>
​​        <label>Name</label>
​​        <input type="text" value={inputValue} onChange={handleChange} />
​​        <input type="submit" value="Submit" />
​​      </form>
​​    </div>
​​  )
​​}
```

这里，输入元素现在有了一个额外的`onChange`事件。每当`input`元素发生变化时，就会执行`onChange`事件，并设置为执行`handleChange()`函数。

`handleChange()`功能将始终自动将状态设置为输入的当前值。React 现在知道如何将输入元素的值存储到状态中，但是我们如何处理表单提交呢？看看下面的代码块:

```
import React, { useState } from 'react'
​​
​​export default function App() {
​​  const [inputValue, setInputValue] = useState('')
​​  
​​  const handleChange = (e) => {
​​    setInputValue(e.currentTarget.value)
​​  }
​​  
​​  const handleSubmit = (e) => {
​​    console.log(`Form value: ${inputValue}`)
​​    e.preventDefault()
​​  }
​​  
​​  return (
​​    <div className="App">
​​      <form onSubmit={handleSubmit}>
​​        <label>Name</label>
​​        <input type="text" value={inputValue} onChange={handleChange} />
​​        <input type="submit" value="Submit" />
​​      </form>
​​    </div>
​​  )
​​}

```

这基本上与上面看到的代码块相同，但是增加了一些内容。表单现在有一个执行`handleSubmit`函数的`onSubmit`事件。

`handleSubmit()`函数做两件事:每当提交表单时，它记录输入元素的当前值，最重要的是，它防止浏览到新页面的默认 HTML 表单行为。

将输入元素的值与状态同步的整个过程称为[受控组件](https://blog.logrocket.com/controlled-vs-uncontrolled-components-in-react/)。我们基本上使反应状态成为真理的唯一来源。负责呈现表单的 React 组件也负责每当用户向表单添加任何内容时发生的事情。

现在我们已经看到了 React 如何处理表单以及如何确保值与状态同步，让我们用不同类型的字段构建一个合适的表单，即`<input>`、`<textarea>`、`<select>`、`radio`等。

下面的代码块/演示包含了演示如何处理不同表单字段所需的所有代码。我们将逐一介绍它们，看看它们是如何工作的。

参见 [CodePen](https://codepen.io) 上 Simohamed ( [@smhmd](https://codepen.io/smhmd) )
的《笔 [LogRocket:反应](https://codepen.io/smhmd/pen/vYeJBoB)中表单的命令式指南》。

## 以反应形式

```
<div className="field">
​​  <label className="label">Full Name</label>
​​  <div className="control">
​​    <input
​​      className="input"
​​      type="text"
​​      name="fullName"
​​      value={fullName}
​​      onChange={handleChange}
​​    />
​​  </div>
​​</div>
```

`input`字段的代码实现很简单。该值被设置为与已经在状态中声明的`fullName,`同步。`onChange`事件用于在`input`字段发生变化时执行`handleChange`功能。

上面的`input`字段唯一的新内容是添加了`name`属性。因为我们将处理多个输入字段，所以重要的是要注意它们中的哪一个实际上被修改了，而`name`属性对此有所帮助。属性`name`的值必须与我们的状态对象的相应属性相同。

这也是`handleChange`函数发生变化的原因。

```
const handleChange = (event) => {
​​  const target = event.target
​​  const value = target.type === 'checkbox' ? target.checked : target.value
​​  const name = target.name
​​
​​  setState((state) => ({
​​    ...state,
​​    [name]: value
​​  }))
​​}
```

在上面的代码块中，`handleChange`函数使用分配给不同`input`元素的`name`属性，根据`event.target.value.`的值来决定要做什么

## `Textarea`

```
​​<div className="field">
​​  <label className="label">What do you like about React</label>
​​  <div className="control">
​​    <textarea
​​      className="textarea"
​​      name="message"
​​      value={state.message}
​​      onChange={handleChange}
​​    />
​​  </div>
​​</div>
```

`textarea`字段的工作方式与`input`字段类似。该值被设置为与已经在状态中声明的`message`同步。它也有`name`属性，并被设置为`message`。

## 使用`Select`

```
​​<div className="field">
​​  <label className="label">Pick your editor</label>
​​  <div className="control">
​​    <div className="select">
​​      <select
​​        value={state.editor}
​​        name="editor"
​​        onChange={handleChange}
​​      >
​​        <option value="vscode">VSCode</option>
​​        <option value="vim">Vim</option>
​​      </select>
​​    </div>
​​  </div>
​​</div>
```

`select`元素有一个`value`属性，该属性被设置为与已经在状态中声明的`editor`同步。因为这是一个选项下拉列表，所以知道选择了什么是很重要的，这就是为什么每个选项标签都有自己的`value`属性和独特的内容。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## `Checkbox`

```
<div className="field">
​​  <div className="control">
​​    <label className="checkbox">
​​      <input
​​        name="terms"
​​        type="checkbox"
​​        checked={state.terms}
​​        onChange={handleChange}
​​      />
​​      I agree to the{' '}
​​      <a href="https://google.com">terms and conditions</a>
​​    </label>
​​  </div>
​​</div>
```

React 表单中的`checkbox`元素的实现与上面的有些不同。不是将`state.terms`值设置为输入字段的`value`属性，而是将其设置为`checked`属性。状态值还必须是一个布尔值，这意味着要么是真值，要么是假值。

```
​​const [state, setState] = React.useState({
​​  fullName: '',
​​  emailAddress: '',
​​  password: '',
​​  editor: '',
​​  message: '',
​​  terms: false,
​​  test: ''
​​})
```

## `Radio`在作出反应

```
​​<div className="field">
​​  <div className="control">
​​    <label className="label">Do you test your React code?</label>
​​    <br />
​​    <label className="radio">
​​      <input
​​        type="radio"
​​        name="test"
​​        onChange={handleChange}
​​        value="Yes"
​​        checked={state.test === 'Yes'}
​​      />
​​      Yes
​​    </label>
​​    <label className="radio">
​​      <input
​​        type="radio"
​​        name="test"
​​        onChange={handleChange}
​​        value="No"
​​        checked={state.test === 'No'}
​​      />
​​      No
​​    </label>
​​  </div>
​​</div>

```

在 React 表单中实现`radio`元素的方式与上面的 checkbox 类似。所有的`radio`元素都有相同的 name 属性，但是有不同的`value`属性，如上所示，其中 Yes 单选按钮的值是`Yes`，No 单选按钮的值是`No`。

选中的属性用于将状态值设置为`Yes`或`No`中的任意一个。

## 使用 Formik

如果您认为以上述方式设置 React 表单有点紧张和令人担忧，那么我有好消息要告诉您。Formik 有助于[在 React](https://blog.logrocket.com/building-better-react-forms-with-formik/) 中制作出强大而轻便的形态。它使您能够获取和操作值、设置错误和警告、定制输入，以及使用更多使构建表单变得容易的功能。

> *Formik 跟踪你的表单的状态，然后通过 props 将它以及一些可重用的方法和事件处理程序(`handleChange`、`handleBlur​`和`handleSubmit`)暴露给你的表单。`handleChange`和`handleBlur`完全按照预期工作——它们使用名称或 id 属性来确定要更新哪个字段。*

Formik 组件结合 [Yup](https://github.com/jquense/yup) 可以用来编写非常棒的表单验证。Yup 用于对象模式验证，这意味着在用 Formik 构建 React 表单时，它可以用作验证器。

对于 Formik API，有三个重要的 API 需要考虑和理解:

1.  `withFormik`高阶分量
2.  `​​Field`组件
3.  `​​Form​`组件

## `withFormik`高阶分量

`withFormik`允许您创建更高阶的 React 组件。然后，您可以根据提供的选项在组件中传递一些道具和表单处理程序。让我们来看看`withFormik`中的一些可用选项。

*   顾名思义，它有助于 Formik 中的表单提交。它被自动传递表单值和包装在组件中的任何其他属性
*   `mapPropsToValues`:用于初始化表单状态的值。Formik 将 mapPropsToValues 的结果转换为可更新的表单状态，并使这些值作为`props.values`供新组件使用。
*   `validationSchema​`:返回一个 Yup 模式或实际的 Yup 模式本身的函数。这有助于表单内部的验证

现在，让我们看看一些例子，以利用更多的组件。

## Formik 中的`Field`组件

Formik 中的`Field`组件用于使用 Formik 自动设置反应表单。它能够通过使用`name`属性来获取值，它使用`name`属性来匹配 Formik 状态，并且它总是被设置为`input`元素。这可以很容易地通过指定组件属性来更改。

```
// default input element set to an email type
<Field type="email" name="email" placeholder="Email" />

// In this case, the Field acts as a select element
<Field component="select" name="color" >
  <option value="red">Red</option>
  <option value="green">Green</option>
  <option value="blue">Blue</option>
</Field>
```

## `<Form/>`组件

`<Form/>`是扩展原生表单元素的助手组件。它自动将`onSubmit`事件处理程序分配给`props.handleSubmit​`。我们需要做的就是将我们的`Field`组件包装在一个`Form`元素中，如下面的代码所示:

```
<Form>
  <Field type="email" name="email" placeholder="Email" />

  <Field component="select" name="color" >
    <option value="red">Red</option>
    <option value="green">Green</option>
    <option value="blue">Blue</option>
  </Field>
</Form>
```

了解了这些基础知识之后，让我们看看如何使用 Formik 来构建和验证 React 表单。

像受控组件一样，将构建一个完整的表单。然后我们将检查表单的不同部分以及它是如何工作的。完整的代码/演示可以在下面看到。

注意代码的第一行是导入。我们从`react-dom`进口`React`、`render`，从`Formik`进口一些有名称的零部件。Yup 也是进口的。

```
import React from 'react';
import { render } from 'react-dom';
import { withFormik, Form, Field } from 'formik'
import Yup from 'yup'
```

下一个代码块是带有一个`props`参数的`App`组件定义。`props`参数被设置为一个包含将在应用程序中使用的值的对象。

```
const {
  values,
  errors,
  touched,
  handleChange,
  isSubmitting
} = props;
```

表单中的所有细节都存储在`values`中，验证错误存储在`errors`中，`touched`是一个布尔值，用于检查输入字段是否处于焦点中，`handleChange`有助于在输入字段发生变化时执行特定的功能，`isSubmitting`也是一个布尔值，在单击提交时设置为`true`。`App`函数还返回一个`div`，其中包含表单的 JSX 标记。

在第 17 行，`<Form>`组件用于包含表单所需的全部代码。如上所述，它有助于防止打出`<form onSubmit={props.handleSubmit}/>`。

我将突出显示表单组件中不同的`Field`组件和`input`元素，以及它们如何与 Formik 一起工作。

## 文本输入

```
{touched.fullname && errors.fullname && <p>{errors.fullname}</p>}
<Field className="input" type="text" name="fullname" placeholder="Full Name" />
```

`Field`组件总是被设置为输入元素。剩下的工作就是定义 name 属性，以便 Formik 可以自动获取值。

在上面的代码行中，`Field`组件只是用于验证目的。它用`touched.fullname`检查输入元件是否对焦，然后用`errors.fullname`检查是否有任何错误。

如果有错误，它会在对象模式验证器中显示定制消息。稍后我将介绍验证的设置。

## `Select`

```
{touched.editor && errors.editor && <p>{errors.editor}</p>}
<div className="control">
  <Field component="select" name="editor">
    <option value="atom">Atom</option>
    <option value="sublime">Sublime Text</option>
  </Field>
</div>
```

如上所述，`Field`组件的默认状态被设置为 input，但这可以通过指定一个`component`属性很容易地改变，如上所示。name 属性被设置为`editor`，并且有两个值不同的`option`元素。如上所述，第一行代码也用于验证。

## `Radio`

```
{touched.test && errors.test && <p>{errors.test}</p>}
<div className="control">
  <label class="radio">
    <input name="test" type="radio" value="yes" className="radio" onChange={handleChange}
    />
    Yes
  </label>
  <label className="radio">
    <input
    name="test" type="radio" value="no" className="radio" onChange={handleChange}
    />
    No
  </label>
</div>
```

对于 radio 元素，我们不能使用`Field`组件，而是使用传统的方式，使用 input 和一种类型的`radio`。两个单选选项都设置为相同的名称属性，但每个单选选项都有不同的值。

## `Checkbox`

```
<label className="checkbox">
  {touched.newsletter && errors.newsletter && <p>{errors.newsletter}</p>}
  <Field type="checkbox" name="newsletter" checked={values.newsletter} />
  Join our newsletter?
</label>
```

这里的`checkbox`元素是带有复选框类型的`Field`组件。选中的事件被设置为将新闻稿的值更改为 true 或 falsy 值。

## `Submit`按钮

另一个需要注意的是按钮元素。Formik 自动检测到单击表单末尾的按钮元素表示用户打算提交所有的表单细节。

```
<button disabled={isSubmitting}>Submit</button>
```

`isSubmitting`是提交国。要么设置为`true`要么设置为`false`。当设置为`true`时，当福米克调用`handleSubmit`处理器时，该按钮将被禁用。

`DisplayFormikState`函数是一个无状态函数，它通过 props 在表单中显示原始数据和错误值。

```
const DisplayFormikState = props =>
  <div style={{ margin: '1rem 0', background: '#f6f8fa', padding: '.5rem' }}>
    <strong>Injected Formik props (the form's state)</strong>
    <div>
      <code>errors:</code> {JSON.stringify(props.errors, null, 2)}
    </div>
    <div>
      <code>values:</code> {JSON.stringify(props.values, null, 2)}
    </div>
    <div>
      <code>isSubmitting:</code> {JSON.stringify(props.isSubmitting, null, 2)}
    </div>
  </div>;
```

FormikApp 用于将整个表单包含在`withFormik`高阶组件中。

```
const FormikApp = withFormik({
  mapPropsToValues({ email, password, newsletter, editor, test }) {
    return {
      email: email || '',
      password: password || '',
      newsletter: newsletter || false,
      editor: editor || 'atom',
      test: test || ''
      }
  },
  validationSchema: Yup.object().shape({
    email: Yup.string().email('Email not valid').required('Email is required'),
    fullname: Yup.string().required('Full Name is required!'),
    password: Yup.string().min(9, 'Password must be 9 characters or longer').required('Password is required')
  }),
  handleSubmit(values, { resetForm, setErrors, setSubmitting }) {
    setTimeout(() => {
      if (values.email === '[email protected]') {
        setErrors({ email: 'That email is already taken' })
      } else {
        resetForm()
      }
      setSubmitting(false)
    }, 2000)
  }
})(App)
```

`mapsPropsToValues`函数有助于将我们之前在 App 函数中定义的外部属性转换成表单值。它返回从表单细节中获取的所有值。接下来要注意的是`validationSchema`。

`validationSchema`有助于表单验证。它使用[是的](https://github.com/jquense/yup)，这是一个对象模式验证器来实现。

正如您在上面看到的，值的名称被设置为 Yup，然后您决定该值应该是一个[字符串](https://github.com/jquense/yup#string)、[数字](https://github.com/jquense/yup#number)、[布尔型](https://github.com/jquense/yup#boolean)，还是一个[日期](https://github.com/jquense/yup#date)。您还可以通过链接`required()`并将错误消息放在括号内来确保该值是必需的。

Yup 还允许您使用`min()` API 设置字符串的最小长度。它接受两个值，即字符数，如果该值未达到该数量，则接受错误消息。

您可以查看 Yup [文档](https://github.com/jquense/yup)以获得更多 API 以及如何更好地验证表单。

`handleSubmit`函数是提交处理程序，它接受`values`(包含表单细节)`resetForm`(用于重置所有表单值)`setErrors`(设置有错误时的错误消息)`setSubmitting`(用于强制设置为 [`isSubmitting`](https://github.com/jaredpalmer/formik#issubmitting-boolean) )。

然后，我们的表单被包装在`withFormik` HoC 中，`FormikApp`被呈现在 React 应用程序上。

通过上面的所有代码，您可以看到 Formik 使构建 React 应用程序变得多么容易。

## 结论

在本教程中，我们学习了如何使用 React 构建表单。React 没有提供处理表单的默认方式，相反，您必须使用`handleChange`事件来检查更改，同时与状态同步。我还解释了 React 中受控组件的基础知识。

我们还使用了 Formik，它基本上是一个帮助构建表单的特设工具。我们介绍了 Formik 附带的不同 API 以及如何使用它们。

使用受控组件构建的表单的演示和代码可以在 [CodeSandbox](https://codepen.io/smhmd/pen/vYeJBoB?editors=0010) 上看到，Formik 的演示和代码也可以在[这里](https://codesandbox.io/s/8p25nojzk2?autoresize=1&view=editor)看到。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)