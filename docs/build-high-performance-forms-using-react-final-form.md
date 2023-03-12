# React Final Form:一个性能更好的表单库

> 原文：<https://blog.logrocket.com/build-high-performance-forms-using-react-final-form/>

有许多库可用于创建和管理表单。在 React 中，表单总是有点复杂。表单库旨在简化表单管理而不影响性能。

在本文中，我们将看看 [React Final Form](https://github.com/final-form/react-final-form) ，这是一个流行的表单管理库。我们将介绍 React Final Form 是如何工作的，将它与一些竞争对手进行比较，最后用一个相关的例子进行测试。我们开始吧！

## 什么是反应最终形式？

React Final Form 是一个用核心 JavaScript 编写的轻量级表单库，它作为一个表单状态管理库, [Final Form](https://final-form.org) 的包装器。

React 最终形式使用[观察者设计模式](https://blog.logrocket.com/how-typescript-design-patterns-help-you-write-better-code/#theobserverpattern:Iknowwhathappenedtoyou)，其中组件订阅特定事件。不是重新呈现整个表单，而是仅重新呈现已订阅的字段。

让我们看看 React Final Form 的一些主要特性。

### 最小束尺寸

React 最终表单是一个简单的最终表单库的包装器。它没有依赖性，并且是用纯 JavaScript 编写的，这使得它与框架无关。React 最终形式的 bundle 大小只有 [3.2kB minified](https://bundlephobia.com/package/react-final-form@6.5.3) 和 gzipped。

### 简单

由于其简单的表单状态管理，React Final Form 强调为所需功能编写代码，而不是为简单表单编写不必要的代码。React Final Form 的设计是高度模块化的，这使它成为许多用例的完美选择。

### 高性能

虽然在小表单中重新渲染没什么大不了的，但是随着表单大小的增加，我们会发现每次重新渲染的性能都会有明显的滞后。由于其基于订阅的模式，React Final Form 仅重新呈现必填字段，以防止延迟。

现在我们知道了 React Final Form 的基础知识，让我们来看看 Formik，一个类似的库，看看这两者是如何比较的。

## 与 Formik 的比较

Formik 是一个库，它在三个方面帮助开发人员编写 React 代码:获取表单状态的值、验证和错误消息，以及表单提交。

### 受欢迎程度和社区

让我们看看这两个图书馆的 [npm](https://www.npmtrends.com/) 趋势，以衡量受欢迎程度和社区规模。我们看到，在过去的六个月里， [Formik](https://formik.org/) 获得了比 React Final Form 更高的周下载量。

![Formik React Final Form Npm Downloads](img/94a9b44168a377a62749a578a85f643f.png)

在 GitHub 上， [React 最终形态](https://github.com/final-form/react-final-form)有 6.6K 颗星，而 [Formik](https://github.com/formium/formik) 有 27.7K 颗星。Formik 显然有一个更大的在线社区，但是，这两个库都有大量的线程和论坛，这意味着您应该能够获得社区支持。

正如下面的截图所示，这两个库都经常更新:

![React Final Form Formik Updates](img/19dcafd24db5ce6394c10d74f94f876b.png)

React Final Form 目前在 GitHub 上的公开问题比 Formik 少，但如果这个库越来越受欢迎，这种情况将来可能会改变。

### 大小和依赖性

Formik 的捆绑包大小为 [13kB](https://bundlephobia.com/package/formik@2.2.9) ，大于 React Final Form 的 3.2 kB 的捆绑包大小。

下面，我们可以看到两个库的包组合。React 最终形式具有更少的依赖性，减少了库在更新时崩溃的可能性。

![React Final Form Bundle Composition](img/3ae91113bd3f74209285cb2aff6043f9.png)

![Formik Bundle Composition](img/702209f7128eee9263024517ad330ae8.png)

## 设置 React 最终表单

让我们通过启动自己的项目来测试 React 最终表单的功能。通过运行以下命令，设置 React 项目并安装 React 最终表单库:

```
npm install --save final-form react-final-form

```

安装完库后，从库中导入主要组件，如下所示:

```
import { Form, Field } from 'react-final-form'

```

注意，在上面的代码片段中，我们导入了两个组件，`Form`和`Field`。`Form`是父组件，负责管理我们的表单，而`Field`包装 HTML 元素来创建一个独立的最终表单组件。由`Field`创建的组件有自己的状态，由`Form`标签管理。

让我们用 React Final Form 编写一个简单输入表单的代码。我们的代码包含用于`firstName`和`lastName`的输入字段。我们还添加了一个`submit`按钮:

```
/* eslint-disable jsx-a11y/accessible-emoji */
import React from 'react'
import { render } from 'react-dom'
import Styles from './Styles'
import { Form, Field } from 'react-final-form'

const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))

const onSubmit = async values => {
  await sleep(300)
  window.alert(JSON.stringify(values, 0, 2))
}

const App = () => (
  <Styles>
    <h1>React Final Form - Simple Example</h1>

    <Form
      onSubmit={onSubmit}
      initialValues={{ firstname: '', lastname :''}}
      render={({ handleSubmit, form, submitting, pristine, values }) => (
        <form onSubmit={handleSubmit}>
          <div>
            <label>First Name</label>
            <Field
              name="firstName"
              component="input"
              type="text"
              placeholder="First Name"
            />
          </div>
          <div>
            <label>Last Name</label>
            <Field
              name="lastName"
              component="input"
              type="text"
              placeholder="Last Name"
            />
          </div>

          <div className="buttons">
            <button type="submit" disabled={submitting || pristine}>
              Submit
            </button>
            <button
              type="button"
              onClick={form.reset}
              disabled={submitting || pristine}
            >
              Reset
            </button>
          </div>
        </form>
      )}
    />
  </Styles>
)

render(<App />, document.getElementById('root'))

```

启动服务器会给我们以下输出。

![Starting React Final Form Server Output](img/08587e4a089d33fba93a5af154e06721.png)

我们调用两个日志，一个来自`Form`，一个来自`Field`。让我们试试在`FirstName`中输入`sam`，看看会发生什么！

![Call Field Form Logs React Final Form](img/a81e7ab9866260bb70616a2f5919f97c.png)

![React Final Form Single Render](img/51194233b987af0a075c2c38b6cb49a7.png)

注意`Form`只渲染了一次。`Field`组件显示了独立的行为，因为它呈现的次数与输入的字符数相同。在 React 中，我们应该总是以更少的重渲染次数为目标，以避免表单大小增长时的延迟。

我们使用了一个[渲染道具](https://blog.logrocket.com/react-reference-guide-render-props/)，它让我们可以从`Form`组件访问不同的道具。请参见下面示例的最终输出:

![React Final Form Example Form](img/eb673be4eb1a62dc8f371140cdb5051f.png)

现在我们已经看到了 React Final Form 是如何工作的，让我们使用 Formik 运行相同的示例。

## 设置 Formik

和以前一样，我们将建立一个简单的表单，其中包含一个用于`firstName`的字段、一个用于`lastName`的字段和一个提交按钮。让我们称我们的形式为`Client Profile`:

```
import React from "react";
import ReactDOM from "react-dom";
import { Formik, Form, Field } from "formik";

// Messages
export default function App() {
  return (
    <Formik
      initialValues={{
        firstname: "",
        lastname: "",
      }}
      onSubmit={(values, { setSubmitting }) => {
        setTimeout(() => {
          console.log(JSON.stringify(values, null, 2));
          setSubmitting(false);
        }, 400);
      }}
    >
      {({ errors, touched, isValidating }) => (
        <div className="container">
          <div>
            <h3>Client Profile</h3>
          </div>
          <div>
            <Form>
              {console.log(“Render”)}
              <div>
                <Field
                  type="text"
                  placeholder="First Name"
                  name="firstname"
                />
              </div>
              <div>
                <Field

                  type="text"
                  placeholder="lastname"
                  name="name" 
                />
              </div>
            <button type="submit">Submit</button>
            </Form>
          </div>
        </div>
      )}
    </Formik>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

在第一次渲染时，我们的表单如下图所示:

![Formik First Render Output](img/075ec0fc668d6a14e113ff5579243701.png)

我们在`Form`标签中放置了一个日志来跟踪渲染。让我们在`input`字段中输入`Sam`。我们得到以下输出:

![Formik Log Form Tag](img/b18fce2feedb908bdff68bba66c6efe2.png)

请注意，当我们输入一个输入值时，表单总共重新呈现了九次，而 React Final Form 只呈现了一次。让我们深入考虑这些例子。

### `subscription`道具

在 React 最终形式中，`Form`组件采用了`subscription`道具，它实现了观察者设计模式并减少了渲染。`subscription`道具类似于`[useEffect](https://reactjs.org/docs/hooks-effect.html)`钩子，因为它会观察传递给它的值，并在它们改变时重新渲染。

在上面的 Formik 代码块中，我们没有在 prop 内部传递值。相反，`Form`在观察`{submitting || pristine}`的变化。

### 确认

React 最终表单提供了两种类型的验证:表单级验证和字段级验证。使用字段级验证，您可以在更改`Field`时运行验证。在表单级验证中，验证测试在提交`Form`时运行。

Formik 也有类似的机制，使用 [`validationSchema`](https://blog.logrocket.com/react-native-form-validations-with-formik-and-yup/) 进行验证。因此，两个库在这方面是平等的。

## 结论

React Final Form 处理表单的范例与其他库不同。它通过使用观察者设计模式有效地处理了不同库的重渲染问题。

React 最终形式不仅在尺寸上比 Formik 小，而且速度也更快。因此，如果您的目标是在不影响性能的情况下为 React 应用程序创建大型复杂表单，React Final Form 是最佳选择。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)