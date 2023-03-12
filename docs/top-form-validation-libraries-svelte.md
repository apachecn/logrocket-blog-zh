# Svelte 中的顶级表单验证库

> 原文：<https://blog.logrocket.com/top-form-validation-libraries-svelte/>

## 介绍

[表单验证难](https://blog.logrocket.com/form-validation-in-svelte/)。这就是为什么有几个库为我们处理表单验证，所以我们不必自己设置自定义验证解决方案。每个项目都有不同的表单验证需求，没有通用的解决方案。

因此，在本文中，我将带您浏览最流行和最活跃的表单验证库，以帮助您选择在下一个项目中使用哪一个。我根据 GitHub stars 的数量和它们拥有的 npm 下载量选择了这七个库，所以你可以放心，你选择的库在其他 dev 中很受欢迎。

这些库如下:

1.  [苗条形态库](#svelteformslib)
2.  [场](#felte)
3.  [苗条使用形式](#svelteuseform)
4.  [苗条的最终形态](#svelteff)
5.  [苗条尤物](#svelteyup)
6.  [扫描](#sveltik)
7.  [苗条身材](#sveltejsforms)

## 苗条的身材

是一个受 Formik 启发的库，用于在一个苗条的项目中轻松构建表单。虽然它没有很多特性，但是它很好地处理了验证表单和显示错误的基本场景。在设置基本的表单验证时，Svelte forms lib 是一个很好的轻量级库。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i svelte-forms-lib

```

将苗条表单库集成到表单的核心在于`createForm`函数，如下所示:

```
<script>
  import { createForm } from "svelte-forms-lib";
  import * as yup from "yup";

  const { form, errors, state, handleChange, handleSubmit } = createForm({
    initialValues: {
      name: "",
      email: ""
    },
    validationSchema: yup.object().shape({
      name: yup.string().required(),
      email: yup.string().email().required()
    }),
    onSubmit: values => {
      alert(JSON.stringify(values));
    }
  });
</script>

<form on:submit={handleSubmit}>
  <input
    id="name"
    name="name"
    on:change={handleChange}
    on:blur={handleChange}
    bind:value={$form.name}
    placeholder="name"
  />
  {#if $errors.name}
    <small>{$errors.name}</small>
  {/if}

  <input
    id="email"
    name="email"
    on:change={handleChange}
    on:blur={handleChange}
    bind:value={$form.email}
    placeholder="email"
  />
  {#if $errors.email}
    <small>{$errors.email}</small>
  {/if}

  <button type="submit">submit</button>
</form>

```

`createForm`让我们访问有用的表单帮助，比如`handleChange`和`handleSubmit`，等等。我们将需要这些助手函数来设置表单。

除了帮助函数之外，Svelte forms lib 公开了可观察的值，这些值为我们提供了关于表单当前状态的信息。你可以在这里查看[可观测到的](https://svelte-forms-lib-sapper-docs.vercel.app/observables)的列表。

我们将一个配置对象作为参数传递给`createForm`。这里，我们定义了表单的`initialValues`和一个将处理表单提交的`onSubmit`处理器。配置好`createForm`后，我们需要将 profile 表单挂接到 Svelte forms lib，这样它就可以跟踪表单值并处理提交。

为此，我们将`handleSubmit`助手传递给`form`元素。我们还将`handleChange`传递给输入字段，将`bind`的值传递给`form`可观察值。

### 确认

瘦表单库通过一个`validationSchema` prop 支持用 Yup 进行验证，该 prop 接受一个 schema 对象。苗条表单库也支持[自定义验证](https://svelte-forms-lib-sapper-docs.vercel.app/custom)。

### 开发者体验

作为一个受 [Formik](https://blog.logrocket.com/building-better-react-forms-with-formik/) 启发的库，Svelte forms lib 也有类似的 API。来自 React 背景的开发人员会发现这个库很容易使用。

另一个开发者体验的好处是，像 Formik 一样，Svelte forms lib 提供定制组件:

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
      <Field type="text" name="name" placeholder="Name" />
      <ErrorMessage name="name" />
    </div>
    <div>
      <Field type="email" name="email" placeholder="Email" />
      <ErrorMessage name="email" />
    </div>     

      <button type="submit">Submit</button>
  </Form>
</div>

```

它提供了`Form`、`Field`、`Select`和`ErrorMessage`组件。使用这些定制组件将减少样板文件，并使表单代码非常简洁。我们不再需要有条件地呈现每个字段的错误，因为`ErrorMessage`为我们处理了它。

苗条的形式图书馆有易于理解和初学者友好的文件。这些文档提供了几个例子，涵盖了不同的用例以及易于复制的代码片段。

## 条件

Felte 是一个简单的表单库，旨在帮助简化表单管理和验证。Felte 的特性涵盖了几个表单处理和验证用例。由于 Felte 提供了多种集成，因此使用起来会非常困难。因此，我建议你只在复杂的情况下使用它，比如多步表单。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i felte

```

Felte 提供了一个`createForm`函数，我们可以用它来建立这样的表单:

```
<script>
  import { createForm } from "felte";
  import { validator } from "@felte/validator-yup";
  import * as yup from "yup";
  const schema = yup.object({
    email: yup.string().email().required(),
    name: yup.string().required(),
  });
  const { form, errors } = createForm({
    extend: validator,
    validateSchema: schema,
    onSubmit: async (values) => {
      alert(JSON.stringify(values));
    },
  });
</script>

<form use:form on:submit|preventDefault>
  <input type="text" name="name" />
  {#if $errors.name}
    <span class="error">{$errors.name}</span>
  {/if}
  <input type="email" name="email" />
  {#if $errors.email}
    <span class="error">{$errors.email}</span>
  {/if}
  <button type="submit">Submit</button>
</form>

```

从`createForm`开始，我们拉出一个`form`动作，通过 Svelte 的`use`指令传递给 HTML 的`form`元素。这就是你所需要的将 Felte 整合到你的表单中，并使它们具有反应性。

`createForm`接受一个 config 对象，在这里我们用 Yup 设置验证，并定义一个处理表单提交的`onSubmit`函数。

### 确认

有了 Felte，你可以建立自己的自定义验证，或者使用像 Yup、Zod 或 Superstruct 这样的库。

在上面的代码片段中，我们使用了 Yup。我们从`createForm`访问`errors`对象，并有条件地显示与每个输入字段相关的错误。

### 开发者体验

Felte 是一个初学者友好的库，有很多特性和选项。我喜欢它支持用 Yup 验证的事实，尽管我们必须安装第三方库`@felte/validator-yup`来设置它。

Felte 拥有非常详细、有条理且易于理解的文档，其中充满了涵盖多个用例的演示和代码片段。

## 苗条的使用形式

[Svelte Use Form](https://github.com/noahsalvi/svelte-use-form) 是一个简单易用的表单库，处理每个输入字段的验证和状态，很好的处理了基本的表单验证和集成。但是，它不提供对高级情况(如多步表单)的现成处理。它支持输入、文本区、选择、单选按钮和复选框。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i -D svelte-use-form

```

Svelte 的使用表单提供了一个`useForm`函数，我们可以通过 Svelte 的`use`指令挂钩到 HTML 的`form`元素:

```
<script>
  import {useForm, Hint, HintGroup, validators, minLength,email,required} from "svelte-use-form";

  const form = useForm();

  let formData = {}; //we store the form values here

  const onSubmit = () => {
    $form.touched = true;
    if ($form.valid) {
      alert(JSON.stringify(formData));
    }
  };
</script>

<form use:form>
 <div class="form-row">
      <label for="name">Name</label>
      <input
        type="name"
        name="name"
        bind:value={formData.name}
        use:validators={[required, minLength(5)]}
      />
      <HintGroup for="name">
        <Hint on="required">Name is required</Hint>
        <Hint on="minLength" hideWhenRequired let:value>
          Name requires at least {value} characters.</Hint
        >
      </HintGroup>
    </div>

    <div class="form-row">
      <label for="email">Email</label>
      <input
        type="email"
        name="email"
        use:validators={[required, email]}
        bind:value={formData.email}
      />
      <HintGroup for="email">
        <Hint on="required">Email is required</Hint>
        <Hint on="email" hideWhenRequired>Email is not valid</Hint>
      </HintGroup>
    </div>

    <button type="submit" on:click|preventDefault={onSubmit}>Sign In</button>
</form>

```

我喜欢使用苗条使用表单的一点是它很容易与表单集成。我们可以导入`useForm`、`Hint`、`HintGroup`、`validators`、`minLength`、`email`、`required`。

`useForm`用于注册表单和苗条使用表单。`Hint`和`HintGroup`用于显示每个字段的验证错误。`minLength`、`email`和`required`是我们传递给`validators`的验证检查。

### 确认

细长的使用表单导出了一个`validators`函数，我们可以用它来为输入字段添加验证。我们使用`use`指令将`validators`链接到输入字段。

对于`name`输入，我们将其设置为`required`，其`minLength`为五个字符。对于`email`输入，我们将其设置为`required`，以检查电子邮件是否有效。

### 开发者体验

细长的使用表单没有提供可用于处理表单提交的`submit`处理程序。此外，没有存储表单值的`values`对象或类似的东西，所以我们必须将输入字段的值绑定到`formData`对象。

在我写这篇文章的时候，使用`Hint`或`HintGroup`时没有办法设计错误消息。如果我们想要设置错误消息的样式，我们必须这样做:

```
{#if $form.title?.touched && $form.title?.errors.minLength}
   <span class="error">The title requires at least {$form.title.errors.minLength} characters.</span>
{/if} 

```

苗条的使用形式有坚实、清晰的文件记录。提供了一些演示，展示了该库如何处理不同类型的输入字段，这使得苗条的使用表单更容易处理。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 苗条的最终形态

苗条的最终表单是 T2 最终表单的苗条包装器，这是一个与框架无关的表单库。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i --save final-form svelte-final-form

```

苗条最终形态有一个类似于[反应最终形态](https://github.com/final-form/react-final-form)的 API。如果您对此很熟悉，您会发现这个库很容易使用:

```
<script>
  import { Form, Field } from "svelte-final-form";
  const initialValues = {
    name: "Nefe",
    email: "[email protected]",
  };
  const onSubmit = (values) => {
    alert(JSON.stringify(values));
  };
  const validate = (values) => {
    const errors = {};
    if (!values.name) {
      errors.name = "Name is required";
    }
    if (!values.email) {
      errors.email = "Email is Required";
    } else if (!/^[^\[email protected]][email protected][^\[email protected]]+\.[^\[email protected]]+$/.test(values.email)) {
      errors.email = "Invalid emaill address";
    }
    return errors;
  };
</script>
<Form {onSubmit} {validate} {initialValues} let:form let:state>
  <form on:submit|preventDefault={form.submit}>
    <Field name="name" let:input let:meta>
      <label for="name">Name</label>
      <input
        name={input.name}
        on:blur={input.onBlur}
        on:focus={input.onFocus}
        on:input={(e) => input.onChange(e.target.value)}
        type="text"
        value={input.value}
      />
      {#if meta.touched && meta.error}
        <span class="error">{meta.error}</span>
      {/if}
    </Field>
    <Field name="email" let:input let:meta>
      <label for="email">Name</label>
      <input
        name={input.email}
        on:blur={input.onBlur}
        on:focus={input.onFocus}
        on:input={(e) => input.onChange(e.target.value)}
        type="text"
        value={input.value}
      />
      {#if meta.touched && meta.error}
        <span class="error">{meta.error}</span>
      {/if}
    </Field>
    <button type="submit" disabled={state.submitting}>Submit</button>
  </form>
</Form>

```

组件是一个由 Svelte Final Form 提供的特殊包装器，用于管理表单的状态。`Form`通过`let`指令将`form`和`state`组件传递给其子组件。

类似于`Form` , `Field`将`input`和`meta`组件传递给它的输入字段子节点。
`input`包含了我们需要传递给输入字段的必要方法，如`onBlur`、`onFocus`和`onChange`。
`meta`对象包含表单状态的信息。我们从`meta`确定输入是否被触摸或是否有错误。

### 确认

Svelte Final Form 不支持用 Yup 或其他对象验证模式进行验证，所以我们必须自己创建一个自定义验证。

`validate`函数处理表单的验证。当用户提交表单并通过所有验证时，将使用表单的值调用`onSubmit`函数。

### 开发者体验

苗条的最终形式使形式验证令人敬畏和有趣。它不仅“能正常工作”，还能让人们更容易理解代码是如何工作的。

它有一个初学者友好的 API，至于表单提交，`Form`有一个接受提交函数的`onSubmit` prop。我们不必跟踪表单数据，因为 Svelte Final Form 会为我们处理这些数据。

苗条的最终形式没有深入的文档库，因为它不需要；这很容易理解。然而，如果能有更多的演示就太棒了。

## 苗条——是的

是一个苗条的组件库，用来用 yup 验证表单。虽然 svelte-yup 支持使用 yup 进行表单验证，但是您必须创建验证集成，不像其他库那样为您处理它。有一个更深层次的知识可能是苗条的需要，是最大化的。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i svelte-yup yup

```

Svelte-yup 提供了`Form`和`Message`组件，我们可以使用它们将它集成到我们的表单中:

```
<script>
  import * as yup from 'yup';
  import {Form, Message} from 'svelte-yup';
  let schema = yup.object().shape({
    name: yup.string().required().max(30).label("Name"),
    email: yup.string().required().email().label("Email address"),
  });

  let fields = {name: "", email: ""};
  let submitted = false;
  let isValid;

  function formSubmit(){
    submitted = true;
    isValid = schema.isValidSync(fields);
    if(isValid){
      alert(JSON.stringify(fields));
    }
  }
</script>

<Form {schema} {fields} submitHandler={formSubmit} {submitted}>
  <input type="text" bind:value={fields.name} placeholder="Name">
  <Message name="name" />
  <input type="text" bind:value={fields.email} placeholder="Email address">
  <Message name="email" />
  <button type="submit">Submit</button>
</Form>

```

### 确认

svelite-yup 依赖于 Yup 来处理表单验证。当`formSubmit`被调用时，我们将`submitted`设置为`true`。然而，核心验证发生在`schema.isValidSync(fields)`。我们将验证检查的结果存储在`isValid`变量中，如果有效，我们就提交表单数据。

### 开发者体验

没有内置的方法来检查表单和验证状态，所以我们必须为它们设置`submitted`和`isValid`变量。

Svelte-yup 带有表单和`Message`组件，而`Message`负责为我们显示表单错误，这对开发者的体验是一个加分。

因为 svelte-yup 用 yup 处理验证，所以熟悉 Yup 的人不需要学习新的验证语法。这使得工作起来很容易。

svelte-yup 的文档结构良好且详细。文档提供了[演示](https://svelte-yup.netlify.app/)，甚至展示了如何将它与材料 UI 和引导定制表单域一起使用。

## 已被清除

类似于 Svelte forms lib，Sveltik 也是一个受 Formik 启发的库，用于处理 Svelte 中的表单。作为一个受 Formik 启发的库，您可以根据 Formik 的使用方式了解 Sveltik 可能的使用方式。您可以使用 [Formik 示例](https://formik.org/docs/examples/basic)来指导如何将 Sveltik 集成到您的应用程序中。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i sveltik

```

Sveltik 带有`Sveltik`、`Form`、`Field`和`ErrorMessage`自定义组件，我们可以使用它们来设置表单，如下所示:

```
<script>
  import { Sveltik, Form, Field, ErrorMessage } from 'sveltik'
  let initialValues = {
    name: '',
    email: '',
  }
  let validate = values => {
    const errors = {}
    if(!values.name){
      errors.name = "Name is required"
    }
    if (!values.email) {
        errors.email = 'Email is required'
    } else if (!/^[A-Z0-9._%+-][email protected][A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)) {
        errors.email = 'Invalid email address'
        }
      return errors
    }
    let onSubmit = (values) => {
      alert(JSON.stringify(values))
    }
</script>
<Sveltik {initialValues} {validate} {onSubmit}>
  <Form>
    <div class="form-row">
      <Field type="text" name="name" />
      <ErrorMessage name="name" as="span" />
    </div>
    <div class="form-row">
      <Field type="email" name="email" />
      <ErrorMessage name="email" as="span" />
    </div>

    <button type="submit">Submit</button>
  </Form>
</Sveltik>

```

在`Sveltik`组件中，我们传入了`initialValues`、`validate`和`onSubmit`道具。
`initialValues`是一个保存各个表单域初始值的对象，`validate`接受一个处理表单验证的函数，`onSumbit`接受一个处理表单提交的函数。只有当表单域中不存在错误时，才会运行此操作。

`Form`是一个围绕 HTML `<form>`元素的小包装器，它自动挂钩到 Sveltik 的`handleSubmit`和`handleReset`。`ErrorMessage`如果一个给定的字段被访问过并且有错误，则显示该字段的错误信息。

`Field`自动将输入的`onChange`、`onBlur`和`value`属性分别链接到 Formik 的`handleChange`、`handleBlur`和`values`对象。它使用`name`属性来匹配状态，并自动保持状态与输入值同步。默认情况下，它呈现一个 HTML 输入字段。

### 确认

Sveltik 不像 Svelte forms lib 那样提供开箱即用的集成。这意味着我们必须自己创建自定义验证，如上面的代码片段所示。

在`validate`函数中，我们对`name`和`email`输入字段进行验证检查，并根据错误相应地更新`errors`对象。

### 开发者体验

因为 Sveltik 是一个受 Formik 启发的库，任何有使用 Formik 经验的人都会发现它很容易使用。`Field`和`ErrorMessage`组件抽象出重复的样板代码，比如设置`onBlur`、`onChange`，并跟踪每个字段的值。

这不仅使 Sveltik 成为一个初学者友好的库，而且还提供了一个很好的开发者体验。

Sveltik 有一个易于理解的文档。然而，如果能看到更多涵盖不同场景的演示就更好了。

## 苗条身材

有了 sveltejs-forms，我们可以用 Svelte 制作声明性表单。它提供了`Input`、`Select`和`Choice`来简化集成。您不需要设置`onChange`、`onBlur`和其他表单事件处理程序，因为组件抽象了这些处理程序。

### 设置

在您的终端中运行以下命令来安装库:

```
npm i sveltejs-forms

```

我喜欢苗条身材的工作是如此简单。我可以说开发这个库花了很多心思；它提供了自定义的表单、输入、选择和复选框组件，我们可以使用它们来设置表单:

```
<script>
  import { Form, Input } from "sveltejs-forms";
  import * as yup from "yup";
  function handleSubmit({ detail: { values, setSubmitting, resetForm } }) {
    alert(JSON.stringify(values));
    setSubmitting(false);
    resetForm({ name: "", email: "" });
  }
  let schema = yup.object().shape({
    name: yup.string().required().label("Name"),
    email: yup.string().required().email().label("Email address"),
  });
</script>
<Form
  {schema}
  validateOnBlur={false}
  validateOnChange={false}
  on:submit={handleSubmit}
>
  <Input name="name" type="text" label="Name" placeholder="Name" />
  <Input name="email" label="Email Address" placeholder="Email" />
  <button type="submit">Submit</button> 
</Form>

```

这里，我们通过向`Input`组件传递必要的`name`和`type`属性来设置输入字段。`Form`组件接受`validateOnBlur`和`validateOnChange`道具，在这里我们定义验证应该何时运行。

我们设置了一个`handleSubmit`函数，并将其传递给`Form`的`on:submit`事件。这就是设置基本集成所需的全部内容！

### 确认

Sveltejs-forms 支持用 Yup 进行验证。`Form`有一个`schema`属性，我们在其中传递为输入字段定义的验证模式。我们定义的每个模式的名称必须对应于每个输入字段的 name 属性。这样，`Form`就可以跟踪每个字段的有效性和错误。

显示错误怎么办？`Form`和`Input`组件为我们处理这个问题。`Form`不仅仅跟踪每个字段的有效性和错误，它还创建一个存储`errors`对象的上下文。`Input`组件访问那个`errors`对象，并有条件地显示给定字段中存在的任何错误。

### 开发者体验

事实上，sveltejs-forms 提供了定制组件，这些组件抽象处理像`on:change`、`on:blur`、`on:input`这样的事情，并为我们设置输入值，这是一个巨大的开发人员体验加分。这使得设置表单不仅容易，而且快速。表单数据是为我们跟踪和存储的，所以我们不必自己绑定输入和存储数据。

然而，我们必须手动重置输入字段。如果我们有 15 个输入，那将是重复和乏味的。如果有一个`resetForm`方法或者类似的方法，可以被调用来为我们重置输入字段，那就太好了。

表单有一个简单明了的文档。它之所以简短，是因为它是一个简单明了的库，很容易上手。它还介绍了如何使用定制组件。

## 苗条表单验证库概述

| GitHub stars | 每周 npm 下载 | 缩小+g 压缩包尺寸 | 是集成 | 证明文件 | **苗条形态库** |
| --- | --- | --- | --- | --- | --- |
| 331 | 1,148 | 5.2kb | 是 | 伟大的 | **场** |
| 158 | 771 | 4kb | 是 | 伟大的 | **苗条使用形式** |
| 91 | 878 | 3.5kb | 不 | 好的 | **苗条的最终形态** |
| 32 | 131 | 5.6kb | 不 | 基础 | **苗条尤物** |
| 18 | 81 | 4.1 千字节 | 是 | 伟大的 | **扫描** |
| 165 | 50 | 14.4kb | 不 | 好的 | **苗条身材** |
| 179 | 351 | 8kb | 是 | 好的 | 结论 |

## 在本文中，我们介绍了 Svelte 中的顶级表单验证库。到目前为止，Svelte forms lib 满足了我的表单处理和验证需求，我总是在可能的时候使用它。

归根结底，每个图书馆都有自己的优点。当决定将来使用哪个库时，检查我们讨论过的指标，并根据应用的目标和要求进行权衡。

At the end of the day, each library is great in its own way. When deciding which library to use in the future, check the metrics we discussed and weigh them against the goals and requirements of your app.