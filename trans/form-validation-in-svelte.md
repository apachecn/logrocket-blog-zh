# 苗条日志博客中的表单验证

> 原文：<https://blog.logrocket.com/form-validation-in-svelte/>

表单仍然是用户与 web 交互的不可或缺的一部分。在处理表单时，我们必须处理跟踪用户输入、验证和显示错误，以及处理表单提交。

在本文中，我们将了解输入绑定如何在 Svelte 中工作，如何用 Yup 验证表单，以及`svelte-forms-lib`如何使管理表单变得更容易。我们还将构建一个样本表单，并用这些工具来验证它，以展示在 Svelte 中构建和验证表单时的许多选项。

## 理解苗条的输入绑定

我们需要一种方法来跟踪和存储作为用户类型的输入字段的值。Svelte 提供了两个指令来实现这个目标:`on:input`和`bind`。

### `on:input`

每当发生输入事件时，都会调用此事件侦听器:

```
<script>
  let email = "";
  const handleInput = (e) => {
    email = e.target.value;
  };
</script>

<input type="email" name="email" on:input={handleInput} />
<p>{email}</p>

```

在上面的代码中，我们定义了一个`handleInput`并将其传递给电子邮件输入字段。每当用户输入时，`email`变量就会用字段的值进行更新。

### `bind:value`

`bind`指令是一种在 Svelte 中跟踪表单值的更干净的方法:

```
<input type="email" name="email" bind:value={email} />

```

不需要创建一个`handleInput`事件，也不需要为给定表单中的每个输入字段设置`event.target.value`,`bind`会为我们处理，每当我们填充输入时，`email`变量就会更新。

在本文中，我们将使用`bind`指令来跟踪和存储表单值，因为这是一种更简单的工作方式。

## 用“是”验证

是一个 JavaScript 对象模式验证器。Yup 确保对象中的数据符合我们的要求:

```
import * as yup from 'yup';

let values = {
    email: "",
    password: "",
    confirmPassword: "",
    hobby: "",
    checkbox: false,
};

const schema = yup.object().shape({
  email: yup.string().required("Email is required")
    .email("Email is invalid"),
  password: yup.string().required("Password is required"),
  confirmPassword: yup.string().required("Please confirm your password")
    .oneOf([yup.ref("password"), null], "Passwords do not match"),
  hobby: yup.string().required("Hobby is required"),
  checkbox: yup.bool().required("Checkbox must be accepted")
    .oneOf([true], "Checkbox must be accepted"),
});

const validationResult = schema
    .validate(values, { abortEarly: false })
    .then(() => {
      alert(JSON.stringify(values, null, 2));
    })
    .catch((err) => {
      console.log(err.errors);
    });

    //console.log message 
    //[ "Email is invalid", "Passwords do not match", "Hobby is required", "Che//ckbox must be accepted" ]

```

在`schema`中，我们定义了我们希望表单值的数据是什么样子。这确保了发送到服务器的数据是有效的。

我们使用其`validate`方法来验证 Yup 中的对象。我们可以在我们定义的任何模式上调用这个方法。

## 创建配置文件表单

现在我们已经了解了表单绑定如何在 Svelte 中工作，以及 Yup 如何验证对象值，让我们设置一个样本 profile 表单并验证它:

```
<script>
  import schema from './schema';
  let values = {
    //store form data that will then be validated
  };

  const handleSubmit = () => {
    //validate form and submit data
  };
</script>

<div class="container">
  <h1>Profile Form</h1>
  <form on:submit|preventDefault={handleSubmit}>
    <div>
      <input type="text" name="email" bind:value={values.email} 
        placeholder="Email"
      />
    </div>
    <div>
      <input type="password" name="password" bind:value={values.password}
        placeholder="Password"
      />
    </div>
    <div>
      <input type="password" name="confirmPassword"
        bind:value={values.confirmPassword}
        placeholder="Confirm password"
      />
    </div>
    <div>
      <select name="hobby" bind:value={values.hobby}>
        <option value="">Select a hobby</option>
        <option value="Eating">Eating</option>
        <option value="Reading">Reading</option>
        <option value="Sleeping">Sleeping</option>
      </select>
    </div>
    <div>
      <label for="checkbox">Check this box</label>
      <input name="checkbox" type="checkbox" bind:checked={values.checkbox} />
    </div>
  </form>
</div>

```

我们首先设置一个简单的 profile 表单来捕获用户数据。我们将表单字段绑定到一个`values`对象。这个对象是我们存储表单数据的地方。

## 验证配置文件表单

既然我们已经创建了概要文件表单，我们需要验证它。

与我们将错误记录到控制台时所做的不同，我们希望将它们显示给用户看:

```
<script>
  let errors = {};

  const handleSubmit = async () => {
    try {
      await schema.validate(values, { abortEarly: false });
      alert(JSON.stringify(values, null, 2));
      errors = {};
    } catch (err) {
      errors = err.inner.reduce((acc, err) => {
        return { ...acc, [err.path]: err.message };
      }, {});
    }
  };
</script>

```

在这个代码块中，我们创建了一个`errors`对象，用于存储从`validate`调用中返回的错误。然后，我们创建一个异步函数，`handleSubmit`。在这里，我们将处理表单验证和提交。

我们将想要验证的数据传递给这个方法。在这种情况下，我们将验证从表单收到的`values`。

`Validate`可以带第二个参数，一个 options 对象。默认情况下，验证在出现第一个错误时返回。为了获得所有返回的错误，我们必须将`abortEarly`设置为`false`。

如果没有错误，我们将显示表单值。如果有，我们显示错误。但是，在显示错误之前，我们必须访问它们:

```
errors = err.inner.reduce((acc, err) => {
  return { ...acc, [err.path]: err.message };
}, {});

```

为了访问错误，我们遍历 Yup 的验证`error.inner`数组，并返回一个由字段及其错误消息组成的新对象。然后，我们用每个相应输入字段的错误更新`errors`对象。

## 显示验证错误

现在我们有了保存每个输入字段错误的`errors`对象，我们需要显示它们:

```
 <div>
      <input type="email" />
      {#if errors.email}
        <span class="error">{errors.email}</span>
      {/if}
    </div>
    <div>
      <input type="password" />
      {#if errors.password}
        <span class="error">{errors.password}</span>
      {/if}
    </div>
    <div>
      <input type="password" />
      {#if errors.confirmPassword}
        <span class="error">{errors.confirmPassword}</span>
      {/if}
    </div>
    <div>
      <select name="hobby" bind:value={values.hobby}>
        <option value="">Select a hobby</option>
        <option value="Eating">Eating</option>
        <option value="Reading">Reading</option>
        <option value="Sleeping">Sleeping</option>
      </select>
      {#if errors.hobby}
        <span class="error">{errors.hobby}</span>
      {/if}
    </div>
    <div>
      <input name="checkbox" type="checkbox" bind:checked={values.checkbox} />
      {#if errors.checkbox}
        <span class="error">{errors.checkbox}</span>
      {/if}
    </div>

```

我们设置了一个`if`块来处理显示错误。如果特定字段存在错误，我们将显示该字段的错误。这个 CodeSandbox 链接保存了这个部分的代码。

## 用`svelte-forms-lib`确认

是一个受 Formik 启发的库，用于在一个苗条的项目中轻松构建表单。

您可以安装`svelte-forms-lib`及以下内容:

```
npm i svelte-forms-lib

```

首先，我们从`svelte-forms-lib`导入`createForm`函数:

```
import { createForm } from "svelte-forms-lib";

```

这个函数是将`svelte-forms-lib`集成到表单中的核心部分。

`CreateForm`让我们访问有用的表单帮助，比如`handleChange`和`handleSubmit`，等等。我们将需要这些助手函数来设置表单:

```
<script>
  import { createForm } from "svelte-forms-lib";
  const { form, handleChange, handleSubmit } = createForm({
    initialValues: {
      email: "",
      password: "",
      confirmPassword: "",
      hobby: "",
      checkbox: "",
    },
    onSubmit: (values) => {
      alert(JSON.stringify(values));
    },
  });
</script>
<div class="container">
  <h1>Registration Form</h1>
  <form on:submit|preventDefault={handleSubmit}>
    <div>
      <input
        type="text"
        name="email"
        bind:value={$form.email}
        placeholder="Email"
        on:change={handleChange}
      />
    </div>
    <div>
      <input
        type="password"
        name="password"
        bind:value={$form.password}
        placeholder="Password"
        on:change={handleChange}
      />
    </div>
    <div>
      <input
        type="password"
        name="confirmPassword"
        bind:value={$form.confirmPassword}
        placeholder="Confirm password"
        on:change={handleChange}
      />
    </div>
    <div>
      <select name="hobby" bind:value={$form.hobby} on:blur={handleChange}>
        <option value="">Select a hobby</option>
        <option value="Eating">Eating</option>
        <option value="Reading">Reading</option>
        <option value="Sleeping">Sleeping</option>
      </select>
    </div>
    <div>
      <label for="checkbox">Check this box</label>
      <input
        name="checkbox"
        type="checkbox"
        bind:checked={$form.checkbox}
        on:change={handleChange}
      />
    </div>
    <div>
      <button type="submit">Register</button>
    </div>
  </form>
</div>

```

除了帮助函数，`svelte-forms-lib`还公开了可观察的值，这些值为我们提供了表单当前状态的信息。在本文中，我们将集中讨论如何使用`form`和`errors`可观测值。然而，你可以在这里查看[可观测到的](https://svelte-forms-lib-sapper-docs.vercel.app/observables)的完整列表。

我们将一个配置对象作为参数传递给`createForm`。这里，我们定义了表单的`initialValues`和一个将处理表单提交的`onSubmit`处理器。

配置完`createForm`后，我们需要将概要文件表单挂接到`svelte-forms-lib`，这样它就可以跟踪表单值并处理提交。

为此，我们将`handleSubmit`助手传递给`form`元素。我们还将`handleChange`传递给输入字段，将`bind`的值传递给`form`可观察值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## `svelte-forms-lib`中的自定义验证

现在我们知道了如何将`svelte-forms-lib`集成到表单中，我们需要处理表单验证:

```
<script>
  import { createForm } from "svelte-forms-lib";
  const { form, errors, handleChange, handleSubmit } = createForm({
    initialValues: {},
    validate: (values) => {
      let errors = {};
      if (!values.email) {
        errors.email = "Email is Required";
      } else if (!/^[^\[email protected]][email protected][^\[email protected]]+\.[^\[email protected]]+$/.test(values.email)) {
        errors.email = "Invalid emaill address";
      }
      if (!values.password) {
        errors["password"] = "password is required";
      }
      if (!values.confirmPassword) {
        errors["confirmPassword"] = "confirm password is required";
      } else if (values.confirmPassword !== values.password) {
        errors["confirmPassword"] = "password does not match";
      }
      if (!values.hobby) {
        errors["hobby"] = "hobby is required";
      }
      if (!values.checkbox) {
        errors.checkbox = "You must accept our terms";
      }
      return errors;
    },
    onSubmit: (values) => {
      alert(JSON.stringify(values));
    },
  });
</script>
<div class="container">
  <h1>Registration Form</h1>
  <form on:submit|preventDefault={handleSubmit}>
    <div>
      <input
        type="text"
        name="email"
        bind:value={$form.email}
        placeholder="Email"
        on:change={handleChange}
      />
      {#if $errors.email}
        <span class="error">{$errors.email}</span>
      {/if}
    </div>
    <div>
      <input
        type="password"
        name="password"
        bind:value={$form.password}
        placeholder="Password"
        on:change={handleChange}
      />
      {#if $errors.password}
        <span class="error">{$errors.password}</span>
      {/if}
    </div>
    <div>
      <input
        type="password"
        name="confirmPassword"
        bind:value={$form.confirmPassword}
        placeholder="Confirm password"
        on:change={handleChange}
      />
      {#if $errors.confirmPassword}
        <span class="error">{$errors.confirmPassword}</span>
      {/if}
    </div>
    <div>
      <select name="hobby" bind:value={$form.hobby} on:blur={handleChange}>
        <option value="">Select a hobby</option>
        <option value="Eating">Eating</option>
        <option value="Reading">Reading</option>
        <option value="Sleeping">Sleeping</option>
      </select>
      {#if $errors.hobby}
        <span class="error">{$errors.hobby}</span>
      {/if}
    </div>
    <div>
      <label for="checkbox">Check this box</label>
      <input
        name="checkbox"
        type="checkbox"
        bind:checked={$form.checkbox}
        on:change={handleChange}
      />
      {#if $errors.checkbox}
        <span class="error">{$errors.checkbox}</span>
      {/if}
    </div>
    <div>
      <button type="submit">Register</button>
    </div>
  </form>
</div>

```

除了用一个`initialValues`对象和一个`onSubmit`函数配置`createForm`之外，我们还可以添加一个`validate`回调来处理表单验证。

这里，我们检查每个输入字段的状态，并基于该状态更新`errors`对象。每当任何输入字段中有错误时，我们就在一个`if`块中显示它们。

## 是在`svelte-forms-lib`进行验证

虽然我们可以为表单创建一个自定义验证，但是我们也可以选择将这个责任交给 Yup。

我们将使用刚刚创建的同一个`schema`验证对象:

```
<script>
  import schema from "./schema";
  import { createForm } from "svelte-forms-lib";
  const { form, errors, handleChange, handleSubmit } = createForm({
    initialValues: {
      //initial values here
    },
    validationSchema: schema,
    onSubmit: (values) => {
      alert(JSON.stringify(values));
    },
  });
</script>

//profile form below

```

`Svelte-forms-lib`通过一个接受模式对象的`validationSchema` prop 为 Yup 验证提供支持。我们传入我们定义的模式。你可以在这里找到 [CodeSandbox 的链接。](https://codesandbox.io/s/svelte-forms-lib-yup-validation-4g9y0)

## `svelte-forms-lib`中的自定义表单组件

到目前为止，我们必须将`handleSubmit`传递给表单，将每个字段绑定到它们各自的值，并将`handleChange`传递给每个字段。

虽然这已经完成了工作，`svelte-forms-lib`提供了一种更好的、更少重复的处理表单的方式:定制组件。

这些组件将减少样板文件，使表单代码非常简洁:

```
<script>
  import { Form, Field, ErrorMessage, Select } from "svelte-forms-lib";
  import schema from "./schema";
  const formProps = {
    initialValues: {},
    validationSchema: schema,
    onSubmit: (values) => {
      alert(JSON.stringify(values));
    },
  };
</script>
<div class="container">
  <h1>Registration Form</h1>
  <Form {...formProps}>
    <div>
      <Field type="email" name="email" placeholder="Email" />
      <ErrorMessage name="email" />
    </div>
    <div>
      <Field type="password" name="password" placeholder="Password" />
      <ErrorMessage name="password" />
    </div>
    <div>
      <Field type="password" name="confirmPassword" placeholder="Password" />
      <ErrorMessage name="confirmPassword" />
    </div>
    <div>
      <Select name="hobby">
        <option value="">Select a hobby</option>
        <option value="Eating">Eating</option>
        <option value="Reading">Reading</option>
        <option value="Sleeping">Sleeping</option>
      </Select>
      <ErrorMessage name="hobby" />
    </div>
    <div>
      <label for="checkbox">Check this box</label>
      <Field type="checkbox" name="checkbox" />
      <ErrorMessage name="hobby" />
    </div>
    <div>
      <button type="submit">Register</button>
    </div>
  </Form>
</div>

//profile form below

```

这里，我们使用了`<Form/>`、`<Field/>`、`<Select/>`和`<ErrorMessage/>`组件。

我们通过我们定义的`formProps`变量将`initialValues`、`onSubmit`和`validationSchema`传递给`<Form/>`。`name`和`type`是`<Field/>`正常工作并呈现适当输入类型所必需的。

对于`<ErrorMessage/>`，我们传递我们想要跟踪的输入字段的名称，如果该输入有错误，`<ErrorMessage/>`将显示错误。我们不再需要自己有条件地呈现错误。

你可以在这里找到这个部分的链接。

## 结论

创建苗条的表单既简单又复杂。在本文中，我们学习了如何在 Svelte 中跟踪和存储输入值，如何用 Yup 处理验证，`svelte-forms-lib`是如何工作的，以及如何将这个强大的库集成到表单中。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)