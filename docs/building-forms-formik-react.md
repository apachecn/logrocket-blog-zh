# 在 React 中用 Formik 构建表单

> 原文：<https://blog.logrocket.com/building-forms-formik-react/>

***编者按:**本文更新于 2022 年 1 月 28 日，更新了任何过时的信息，并添加了使用 Formik 的 `[handleChange](https://blog.logrocket.com/building-better-react-forms-with-formik/#using-formiks-handlechange)`部分的[、使用 Formik 的](https://blog.logrocket.com/building-better-react-forms-with-formik/#using-formiks-handlechange) `[onSubmit](https://blog.logrocket.com/building-better-react-forms-with-formik/#using-formiks-onsubmit)`部分的[和使用 Formik 的](https://blog.logrocket.com/building-better-react-forms-with-formik/#using-formiks-onsubmit) `[setSubmitting](https://blog.logrocket.com/building-better-react-forms-with-formik/#using-formiks-setsubmitting)`部分的[。](https://blog.logrocket.com/building-better-react-forms-with-formik/#using-formiks-setsubmitting)*

使用 React 构建表单包括将状态设置为用户数据的容器，将 props 设置为控制状态如何使用用户输入更新的手段。可以在用户输入之间进行验证，并在表单提交时执行任意提交功能。

在本文中，我们将回顾如何在 React 中使用 Formik 来构建表单，包括:

## React 中为什么要用 Formik？

在我们学习如何使用 Formik 之前，让我们了解一下如何在没有库的情况下用最少的引导样式在 React 中编写表单。

在下面的例子中，我们首先在`constructor`方法中初始化所需的状态值。因为我们有两个必需的输入— `email`和`password` —我们为输入值、输入有效性和输入错误初始化状态:

```
constructor(props) {
  super(props);
  this.state = {
    formValues: {
      email: "",
      password: ""
    },
    formErrors: {
      email: "",
      password: ""
    },
    formValidity: {
      email: false,
      password: false
    },
    isSubmitting: false
  };
}

```

接下来，我们使用从 state 派生的输入值创建呈现表单的方法:

```
render() {
  const { formValues, formErrors, isSubmitting } = this.state;
  return (
    <div className="container">
      <div className="row mb-5">
        <div className="col-lg-12 text-center">
          <h1 className="mt-5">Login Form</h1>
        </div>
      </div>
      <div className="row">
        <div className="col-lg-12">
          <form onSubmit={this.handleSubmit}>
            <div className="form-group">
              <label>Email address</label>
              <input
                type="email"
                name="email"
                className={`form-control ${
                  formErrors.email ? "is-invalid" : ""
                }`}
                placeholder="Enter email"
                onChange={this.handleChange}
                value={formValues.email}
              />
              <div className="invalid-feedback">{formErrors.email}</div>
            </div>
            <div className="form-group">
              <label>Password</label>
              <input
                type="password"
                name="password"
                className={`form-control ${
                  formErrors.password ? "is-invalid" : ""
                }`}
                placeholder="Password"
                onChange={this.handleChange}
                value={formValues.password}
              />
              <div className="invalid-feedback">{formErrors.password}</div>
            </div>
            <button
              type="submit"
              className="btn btn-primary btn-block"
              disabled={isSubmitting}
            >
              {isSubmitting ? "Please wait..." : "Submit"}
            </button>
          </form>
        </div>
      </div>
    </div>
  );
}

```

现在我们必须编写`handleChange`方法来更新用户输入的状态:

```
handleChange = ({ target }) => {
  const { formValues } = this.state;
  formValues[target.name] = target.value;
  this.setState({ formValues });
  this.handleValidation(target);
};

```

每当状态值被更新时，我们将针对用户输入运行一个验证方法。这是我们的`handleValidation`方法:

```
handleValidation = target => {
  const { name, value } = target;
  const fieldValidationErrors = this.state.formErrors;
  const validity = this.state.formValidity;
  const isEmail = name === "email";
  const isPassword = name === "password";
  const emailTest = /^[^\[email protected]][email protected][^\[email protected]]+\.[^\[email protected]]{2,}$/i;
  validity[name] = value.length > 0;
  fieldValidationErrors[name] = validity[name]
    ? ""
    : `${name} is required and cannot be empty`;
  if (validity[name]) {
    if (isEmail) {
      validity[name] = emailTest.test(value);
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be a valid email address`;
    }
    if (isPassword) {
      validity[name] = value.length >= 3;
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be 3 characters minimum`;
    }
  }
  this.setState({
    formErrors: fieldValidationErrors,
    formValidity: validity
  });
};

```

这个基本表单的最后一部分是提交过程的`handleSubmit`方法。我们需要检查`formValidity`值，并且，如果有任何`false`值，再次运行验证方法而不提交表单:

```
handleSubmit = event => {
  event.preventDefault();
  this.setState({ isSubmitting: true });
  const { formValues, formValidity } = this.state;
  if (Object.values(formValidity).every(Boolean)) {
    alert("Form is validated! Submitting the form...");
    this.setState({ isSubmitting: false });
  } else {
    for (let key in formValues) {
      let target = {
        name: key,
        value: formValues[key]
      };
      this.handleValidation(target);
    }
    this.setState({ isSubmitting: false });
  }
};

```

现在表单已经可以使用了。React 只为您的应用程序提供“视图”层，这意味着它只提供制作表单组件的基本必需品。`component`、`state`和`props`就像拼图块，你必须把它们拼在一起才能构建一个工作表单。

正如您所看到的，对于只有两个文本框的表单来说，这是相当多的代码。想象一下，在一个有 10 个或更多输入的表单中，您需要记录多少状态值。呀！

是的，用 React 创建表单一点也不好玩；很啰嗦，很死板。构建表单和创建验证方法是很无聊的任务。在每个表单中，您至少需要执行以下操作:

1.  设置表单值、表单错误和表单有效性的状态
2.  处理用户输入和更新状态
3.  创建验证函数
4.  处理提交

以自然反应的方式构建表单要求您编写从设置状态到表单提交过程的每个部分。我做了无数的 React 表单，我总是发现构建表单的这一部分非常无聊和耗时。幸运的是，我不是唯一有这种感觉的人。

## 什么是福米克？

输入 [Formik](https://formik.org/) 。贾里德·帕尔默出于在构建 React 表单时的挫败感，编写了 Formik 库。他需要一种方法来标准化输入组件和表单提交流程。Formik 帮助您编写构建表单的三个最烦人的部分:

1.  获取表单状态内外的值
2.  验证和错误消息
3.  处理表单提交

这又是一个相同的表单，但是这次使用的是 Formik。这个新表单只使用了 Formik 库中的四个额外组件:`<Formik />`、`<Form />`、`<Field />`和`<ErrorMessage />`。

要释放 Formik 的能力，您可以将表单包装在`<Formik />`组件中:

```
<Formik>
  <Form>
    {/* the rest of the code here */}
  </Form>
</Formik>

```

让我们看看与 React 的自然方式相比，Formik 如何使构建表单更容易。

### 在 Formik 中获取表单状态内外的值

Formik 将通过它的`initialValues` prop 在内部设置状态来存储用户输入，因此您不再需要从构造函数初始化状态。

要获取 Formik 内部状态的值，可以使用`<Field />`组件替换常规的 HTML `<input />`组件。该组件将保持 Formik 状态和输入值同步，因此您不需要将`value`和`onChange`道具传递到`<Field />`组件中:

```
<Formik
  initialValues={{ email: "", password: "" }}
  onSubmit={({ setSubmitting }) => {
    alert("Form is validated! Submitting the form...");
    setSubmitting(false);
  }}
>
  {() => (
    <Form>
      <div className="form-group">
        <label htmlFor="email">Email</label>
        <Field
          type="email"
          name="email"
          className="form-control"
        />
      </div>
      <div className="form-group">
        <label htmlFor="password">Password</label>
        <Field
          type="password"
          name="password"
          className="form-control"
        />
      </div>
    </Form>
  )}
</Formik>

```

#### 使用 Formik 的`handleChange`

有了 Formik，就不再需要在`constructor`中初始化状态并创建自己的`handleChange`方法了。一切都搞定了。

Formik 有自己的`handleChange`方法，可以用它来更新用户输入的状态，不需要实现自己的`handleChange`方法。

`handleChange`方法根据输入的`name`属性更新表单值。

`handleChange`方法与`input`元素一起使用。`Field`组件在内部更新值，而不需要实现`handleChange`方法。

让我们用一个例子来说明:

```
const SignupForm = () => {
  const formik = useFormik({
    initialValues: {
      email: "",
    },
    onSubmit: (values) => {
      alert(JSON.stringify(values, null, 2));
    },
  });
  return (
    <form onSubmit={formik.handleSubmit}>
      <label htmlFor="email">Email Address</label>
      <input
        id="email"
        name="email"
        type="email"
        onChange={formik.handleChange}
        value={formik.values.email}
      />

      <button type="submit">Submit</button>
    </form>
  );
};

```

这里，我们有一个`input`，其中`name`被设置为`email`。请注意，我们为它设置了一个`onChange`事件处理程序。`onChange`事件处理程序调用`formik`对象的`handleChange`方法。

这将用新值更新`formik`对象的`values`对象，以便我们可以从`onSubmit`道具中的`values`对象中检索它。

### Formik 中的验证和错误消息

在构建表单时，验证是非常重要的。如果表单处理不当，会导致很多错误。

表单必须告诉用户哪些字段是必填的，某些字段中允许的值类型。这也有助于让用户清楚地知道他们的输入有什么问题。

Formik 提供了一种处理验证和显示错误消息的方法。在下面的章节中，我们将学习如何在 Formik 中显示验证信息，以及如何在 Formik 中显示错误信息。

#### 如何在 Formik 中显示验证消息？

Formik 中的验证会在特定事件期间自动执行。所有常见的事件，比如用户输入之后、焦点改变时和提交时，都包括在内，您不需要担心它们。你所需要做的就是将一个函数传递给 Formik 的`validate` prop。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

比较 Formik 验证和 vanilla React 验证之间的代码:

```
// Formik validation code. Take values from Formik
validate={values => {
  let errors = {};
  if (values.email === "") {
    errors.email = "Email is required";
  } else if (!emailTest.test(values.email)) {
    errors.email = "Invalid email address format";
  }
  if (values.password === "") {
    errors.password = "Password is required";
  } else if (values.password.length < 3) {
    errors.password = "Password must be 3 characters at minimum";
  }
  return errors;
}}

// Vanilla React validation code. Take values given by handleChange
handleValidation = target => {
  const { name, value } = target;
  const fieldValidationErrors = this.state.formErrors;
  const validity = this.state.formValidity;
  const isEmail = name === "email";
  const isPassword = name === "password";
  const emailTest = /^[^\[email protected]][email protected][^\[email protected]]+\.[^\[email protected]]{2,}$/i;
  validity[name] = value.length > 0;
  fieldValidationErrors[name] = validity[name]
    ? ""
    : `${name} is required and cannot be empty`;
  if (validity[name]) {
    if (isEmail) {
      validity[name] = emailTest.test(value);
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be a valid email address`;
    }
    if (isPassword) {
      validity[name] = value.length >= 3;
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be 3 characters minimum`;
    }
  }
  this.setState({
    formErrors: fieldValidationErrors,
    formValidity: validity
  });
};

```

#### 如何在 Formik 中显示错误信息？

验证就绪后，现在需要输出错误消息。Formik 的`<ErrorMessage />`组件将自动显示给定名称的`<Field />`组件的错误消息。

你可以通过`component`道具调整显示什么 HTML 标签。因为这个示例表单使用 Bootstrap 的样式，所以您还必须添加一个`className`道具:

```
// Formik error message output
<Field
  type="email"
  name="email"
  className={`form-control ${
    touched.email && errors.email ? "is-invalid" : ""
  }`}
/>
<ErrorMessage
  component="div"
  name="email"
  className="invalid-feedback"
/>

// Vanilla React error message output
<input
  type="email"
  name="email"
  className={`form-control ${
    formErrors.email ? "is-invalid" : ""
  }`}
  placeholder="Enter email"
  onChange={this.handleChange}
  value={formValues.email}
/>
<div className="invalid-feedback">{formErrors.email}</div>

```

错误消息的代码实际上是一样的，但是 Formik 验证中的代码比 vanilla React 中的少得多。好样的，福米克！

#### 我如何使用 Yup 进行验证？

虽然您已经感受到了在验证过程中使用 Formik 的好处，但是您可以通过使用对象模式验证器使它变得更加简单。

对象模式验证器是一个简单的库，它允许您定义 JavaScript 对象的蓝图，并通过验证过程确保对象值与蓝图相匹配。

这在验证表单数据时特别有用，因为它实际上是保存在 Formik 的`values` prop 中的一个对象。

现在有一个这样的库是 Yup，Formik 的作者非常喜欢 Yup，以至于他包括了一个特殊的道具来连接 Yup 和 Formik，名为`[validationSchema](https://jaredpalmer.com/formik/docs/api/formik#validationschema-schema-schema)`。

这个道具自动将 Yup 的验证错误转换成一个漂亮的对象，它的键匹配`[values](https://jaredpalmer.com/formik/docs/api/formik#values-field-string-any)`和`[touched](https://jaredpalmer.com/formik/docs/api/formik#values-field-string-any)`。

这里是一个 Formik 使用 Yup 作为验证模式的例子。注意 validate prop 是如何从`<Formik />`组件中移除的。

有了 Yup 的对象模式验证器，您不必再手动编写`if`条件。你可以通过[访问它的 GitHub repo](https://github.com/jquense/yup) 来了解更多关于 Yup 和它能做什么样的验证。

### Formik 中的表单提交流程

Formik 的`<Form />`组件将自动运行您的验证方法，如果有任何错误，将取消提交过程。

虽然您必须将`onSubmit`属性包含到常规的`<form />`元素中，但是 Formik 的`<Form />`包装器将运行您传递到`<Formik />`组件中的`onSubmit`属性函数:

```
// Formik's submit code. Won't be executed if there are any errors.
onSubmit={({ setSubmitting }) => {
  alert("Form is validated!");
  setSubmitting(false);
}}

// Vanilla React submit code. Check on validity state then run validation manually.
handleSubmit = event => {
  event.preventDefault();
  this.setState({ isSubmitting: true });
  const { formValues, formValidity } = this.state;
  if (Object.values(formValidity).every(Boolean)) {
    alert("Form is validated!");
    this.setState({ isSubmitting: false });
  } else {
    for (let key in formValues) {
      let target = {
        name: key,
        value: formValues[key]
      };
      this.handleValidation(target);
    }
    this.setState({ isSubmitting: false });
  }
};

```

Formik 至少只需要提交四行代码，并且不需要跟踪表单输入的有效性。这真是太棒了！

#### 使用 Formik 的`onSubmit`

Formik 的`onSubmit`道具是一个接受`values`道具并返回承诺的函数。在提交之前，你可以用这个对`values`道具做些什么:

```
    // Formik's submit code.
    onSubmit={(values, { setSubmitting }) => {
      alert("Form is validated!");
      setSubmitting(false);
    }}

```

这个函数属性与普通 JavaScript 中的`onsubmit`非常相似。`onSubmit`函数作为第一个参数和一个对象传递给表单的值，第二个参数是`FormikBag`的实例:

```
onSubmit: (values: Values, formikBag: FormikBag) => void | Promise<any>

```

`values`包含表格中所有输入的值。`formikBag`包含名称以`set`和`reset`开头的功能，例如`setSubmitting`和`resetForm`。

属性很有用，因为我们可以很容易地从传递给它的参数中操纵表单及其值。因此，它为我们使用 HTML 和表单 JavaScript APIs 提供了巨大的优势，因为它使表单操作变得简单。

让我们看看如何将`onSubmit`函数与`Formik`组件一起使用:

```
// js
<Formik
  initialValues={{ name: "jared" }}
  onSubmit={(values, actions) => {
    setTimeout(() => {
      alert(JSON.stringify(values, null, 2));
      actions.setSubmitting(false);
    }, 1000);
  }}
>
  {(props) => (
    <form onSubmit={props.handleSubmit}>
      <input
        type="text"
        onChange={props.handleChange}
        onBlur={props.handleBlur}
        value={props.values.name}
        name="name"
      />
      {props.errors.name && <div id="feedback">{props.errors.name}</div>}
      <button type="submit">Submit</button>
    </form>
  )}
</Formik>

```

这里，我们使用`initialValues`属性设置初始值。然后，在提交过程之前，我们使用`onSubmit`函数对`values`道具做一些事情。

我们有一个`form`元素，它在提交时调用`handleSubmit`函数。当点击`button type="submit"`时，这个提交事件被触发。然后，调用`Formik`组件中的`onSubmit`函数。

需要注意的一点是，提交表单时不会调用`onSubmit`函数。这是因为 Formik 的`<Form />`包装器会自动运行您的验证方法，如果有任何错误，就会取消提交过程。

#### 使用 Formik 的`isSubmitting`

这个`isSubmitting`是 Formik 在表单提交过程中设置的布尔属性。您可以使用它来显示加载微调器或禁用“提交”按钮:

```
isSubmitting: boolean;

```

我们可以使用这个属性来检测表单提交的状态或进度。让我们看一个如何使用这个`isSubmitting`属性的例子:

```
const Example = () => (
  <div>
    <h1>Sign Up</h1>
    <Formik
      initialValues={{
        email: "",
      }}
      onSubmit={async (values) => {
        await sleep(500);
      }}
    >
      {({ isSubmitting }) => (
        <Form>
          {isSubmitting && <div>Loading...</div>}

          <label htmlFor="email">Email</label>
          <Field name="email" placeholder="[email protected]" type="email" />

          <button type="submit" disabled={isSubmitting}>
            Submit
          </button>
        </Form>
      )}
    </Formik>
  </div>
);

```

我们看到我们从`Formik`组件的渲染道具中析构了`isSubmitting`属性。我们使用`isSubmitting`属性禁用提交按钮，并向用户显示一个`Loading…`指示器。

利用这个`isSubmitting`属性很棒，因为它允许我们为用户开发一个漂亮的 UI/UX，并告诉他们后台进程何时在后台运行。

#### 使用 Formik 的`setSubmitting`

`setSubmitting`函数设置 Formik 渲染道具的`isSubmitting`属性:

```
setSubmitting: (isSubmitting: boolean) =>
  void;

<Formik
  initialValues={{
    email: "",
  }}
  onSubmit={async (values, { setSubmitting }) => {
    // ...
  }}
></Formik>

```

当这个`setSubmitting`函数被布尔函数`true`调用时，那么`isSubmitting`被设置为`true`。如果用布尔函数`false`调用`setSubmitting`函数，那么`isSubmitting`被设置为`false`。

所以，我们可以看到`setSubmitting`函数设置了 Formik 的渲染道具的`isSubmitting`属性。

让我们看一个例子:

```
const Example = () => {
  return (
    <div>
      <h1>Sign Up</h1>
      <Formik
        initialValues={{
          email: "",
        }}
        onSubmit={async (values, { setSubmitting }) => {
          setSubmitting(true);
          await sleep(500);
          setSubmitting(false);
        }}
      >
        {({ isSubmitting }) => (
          <Form>
            {isSubmitting && <div>Loading...</div>}

            <label htmlFor="email">Email</label>
            <Field name="email" placeholder="[email protected]" type="email" />

            <button type="submit" disabled={isSubmitting}>
              Submit
            </button>
          </Form>
        )}
      </Formik>
    </div>
  );
};

```

这里，当提交表单时，我们用布尔函数`true`调用`setSubmitting`函数。这将`isSubmitting`设置为`true`，而`button`将为`disabled`，我们会看到一个`Loading...`指示器。

然后，线程休眠 5 秒，用布尔值`false`调用`setSubmitting`，将`isSubmitting`设置为`false`，按钮被禁用。`Loading...`指标终于消失了。

## 还原形式的问题

当然， [redux-form](https://github.com/erikras/redux-form) 很好用，但是你首先需要使用 redux。如果你用的是 [MobX](https://www.google.com/url?q=https://mobx.js.org/README.html&sa=D&source=editors&ust=1618946177801000&usg=AOvVaw0LeSOQvnr91y2z7ZXWSY8v) 呢？如果将来出现一个新的、更好的库，而您想用它取代 Redux，该怎么办？

除此之外，您的 React 表单实际上以某种方式影响了整个应用程序的流程吗？

考虑一下:用户名文本框的值对您的应用程序全局有影响吗？如果不是，那么真的没有必要使用 Redux 来跟踪它的值。甚至先知丹·阿布拉莫夫也说了同样的话。

redux-form 的另一个问题是将表单输入值存储到 Redux store 中。这意味着您的应用程序将在每次击键时调用 Redux 的 reducer 来更新一个文本框的值。这不是个好主意。

我喜欢用 Formik 的方式编写表单，但是如果你喜欢 redux-form，那也可以。

## 结论

构建表单是 React 不擅长的事情之一。幸运的是，React 有一个开发人员社区，他们互相帮助，使编写代码的过程更加容易。

如果您要在 React 应用程序中编写许多表单，Formik 无疑是必不可少的开源库之一。

它通过像`<Field />`和`<Form />`这样的组件抽象出表单的一部分，从而真正加速了您的开发过程并减少了样板代码。

虽然普通的 React 表单要求您指定自己的状态值和方法，但是您可以简单地将 props 传递给`<Formik />`组件来做同样的事情:处理用户输入、验证输入和表单提交。

如果您想了解更多关于 Formik 的信息，请前往文档或观看其创建者的演示。感谢阅读！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)