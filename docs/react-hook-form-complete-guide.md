# React Hook 表单:示例指南

> 原文：<https://blog.logrocket.com/react-hook-form-complete-guide/>

***编者按**:这篇文章于 2022 年 2 月 3 日更新，以反映 [React Hook Form v7](https://github.com/react-hook-form/react-hook-form/releases) 的变化。*

表单是用户与网站和 web 应用程序交互的重要部分。验证通过表单传递的用户数据是开发人员的一项重要职责。

React Hook Form 是一个帮助你在 React 中验证表单的库。它是一个最小的库，没有任何其他依赖项，同时性能良好，使用简单，与其他表单库相比，只需要开发人员编写较少的代码行。

在本指南中，您将学习如何使用 React Hook 表单库在 React 中构建优秀的表单，而无需使用任何复杂的`render`道具或高阶组件。请随意跳到本教程的任何部分:

## 什么是 React 钩子形式？

React 钩子表单采用了一种与 [React 生态系统](https://blog.logrocket.com/tag/react/)中的其他表单库略有不同的方法，通过使用`ref`来采用非受控输入，而不是依赖状态来控制输入。这种方法提高了表单的性能，减少了重新呈现的次数。

包的大小很小([只有 8.6 kB，压缩后为](https://bundlephobia.com/package/react-hook-form@7.25.1))，并且它没有依赖性。该 API 非常直观，为开发人员提供了无缝体验。React Hook Form 遵循 HTML 标准，使用基于约束的验证 API 来验证表单。

React Hook Form 提供的另一个伟大特性是它与 UI 库的无痛集成，因为大多数库都支持`ref`属性。

要安装 React Hook 表单，请运行以下命令:

```
npm install react-hook-form

```

## 如何在表单中使用 React 钩子

在本节中，您将通过创建一个非常基本的注册表单来了解`useForm`钩子的基础知识。

首先，从`react-hook-form`包中导入`useForm`钩子:

```
import { useForm } from "react-hook-form";

```

然后，在组件内部，按如下方式使用钩子:

```
const { register, handleSubmit } = useForm();

```

`useForm`钩子返回一个包含一些属性的对象。现在，你只需要`register`和`handleSubmit`。

`register`方法帮助您将输入字段注册到 React Hook 表单中，以便它可用于验证，并且可以跟踪它的值的变化。

为了注册输入，我们将把`register`方法传递到输入字段，如下所示:

```
<input type="text" name="firstName" {...register('firstName')} />

```

这个扩展运算符语法是该库的一个新实现，它允许在带有 TypeScript 的表单中进行严格的类型检查。你可以在这里了解更多关于 React Hook 表单中的[严格类型检查。](https://blog.logrocket.com/whats-new-in-react-hook-form-v7/#:~:text=Hook%20Form%20V7.-,Strictly%20typed%20forms%20in%20React%20Hook%20Form%20V7,-React%20Hook%20Form)

早于 v7 的版本将 register 方法附加到了`ref`属性上，如下所示:

```
<input type="text" name="firstName" ref={register} />

```

注意，输入组件必须有一个`name`属性，并且它的值应该是唯一的。

顾名思义，`handleSubmit`方法管理表单提交。它需要作为值传递给`form`组件的`onSubmit`属性。

`handleSubmit`方法可以处理两个函数作为参数。当表单验证成功时，将调用作为参数传递的第一个函数以及注册的字段值。当验证失败时，调用第二个函数时出错。

```
const onFormSubmit  = data => console.log(data);

const onErrors = errors => console.error(errors);

<form onSubmit={handleSubmit(onFormSubmit, onErrors)}>
 {/* ... */}
</form>

```

既然你已经对`useForm`钩子的基本用法有了一个相当好的想法，让我们来看一个更现实的例子:

```
import React from "react";
import { useForm } from "react-hook-form";

const RegisterForm = () => {
  const { register, handleSubmit } = useForm();
  const handleRegistration = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(handleRegistration)}>
      <div>
        <label>Name</label>
        <input name="name" {...register('name')} />
      </div>
      <div>
        <label>Email</label>
        <input type="email" name="email" {...register('email')} />
      </div>
      <div>
        <label>Password</label>
        <input type="password" name="password" {...register('password')} />
      </div>
      <button>Submit</button>
    </form>
  );
};
export default RegisterForm;

```

如您所见，没有导入其他组件来跟踪输入值。`useForm`钩子使得组件代码更干净，更容易维护，而且因为表单是不受控制的，所以你不必给每个输入传递像`onChange`和`value`这样的道具。

您可以使用自己选择的任何其他 UI 库来创建表单。但是首先要确保检查文档，并找到用于访问本机输入组件的引用属性的属性。

在下一节中，您将学习如何在我们刚刚构建的表单中处理表单验证。

## 如何用 React 钩子表单验证表单

要对字段应用验证，可以将验证参数传递给 register 方法。验证参数类似于现有的 HTML 表单验证标准。

这些验证参数包括以下属性:

*   `required`表示该字段是否为必填字段。如果该属性设置为`true`，则该字段不能为空
*   `minlength`和`maxlength`设置字符串输入值的最小和最大长度
*   `min`和`max`设置数值的最小值和最大值
*   `type`表示输入字段的类型；它可以是电子邮件、数字、文本或任何其他标准的 HTML 输入类型
*   `pattern`使用正则表达式定义输入值的模式

如果您想将一个字段标记为`required`，您的代码应该是这样的:

```
<input name="name" type="text" {...register('name', { required: true } )} />

```

现在尝试提交这个字段为空的表单。这将导致以下错误对象:

```
{
name: {
  type: "required",
  message: "",
  ref: <input name="name" type="text" />
  }
}

```

这里，`type`属性指的是失败的验证类型，`ref`属性包含原生 DOM 输入元素。

您也可以通过向验证属性传递字符串而不是布尔值来包含字段的自定义错误消息:

```
// ...
<form onSubmit={handleSubmit(handleRegistration, handleError)}>
  <div>
      <label>Name</label>
      <input name="name" {...register('name', { required: "Name is required" } )} />
  </div>
</form>

```

然后，使用`useForm`钩子访问错误对象:

```
const { register, handleSubmit, formState: { errors } } = useForm();

```

您可以像这样向用户显示错误:

```
const RegisterForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm();
  const handleRegistration = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(handleRegistration)}>
      <div>
        <label>Name</label>
        <input type="text" name="name" {...register('name')} />
        {errors?.name && errors.name.message}
      </div>
      {/* more input fields... */}
      <button>Submit</button>
    </form>
  );
};

```

下面是完整的例子:

```
import React from "react";
import { useForm } from "react-hook-form";

const RegisterForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm();
  const handleRegistration = (data) => console.log(data);
  const handleError = (errors) => {};

  const registerOptions = {
    name: { required: "Name is required" },
    email: { required: "Email is required" },
    password: {
      required: "Password is required",
      minLength: {
        value: 8,
        message: "Password must have at least 8 characters"
      }
    }
  };

  return (
    <form onSubmit={handleSubmit(handleRegistration, handleError)}>
      <div>
        <label>Name</label>
        <input name="name" type="text" {...register('name', registerOptions.name) }/>
        <small className="text-danger">
          {errors?.name && errors.name.message}
        </small>
      </div>
      <div>
        <label>Email</label>
        <input
          type="email"
          name="email"
          {...register('email', registerOptions.email)}
        />
        <small className="text-danger">
          {errors?.email && errors.email.message}
        </small>
      </div>
      <div>
        <label>Password</label>
        <input
          type="password"
          name="password"
          {...register('password', registerOptions.password)}
        />
        <small className="text-danger">
          {errors?.password && errors.password.message}
        </small>
      </div>
      <button>Submit</button>
    </form>
  );
};
export default RegisterForm;

```

如果您想在有`onChange`或`onBlur`事件时验证字段，您可以将一个`mode`属性传递给`useForm`钩子:

```
const { register, handleSubmit, errors } = useForm({
  mode: "onBlur"
});

```

在 [API 参考](https://react-hook-form.com/api/#useForm)中找到关于`useForm`钩子的更多细节。

## 与第三方组件一起使用

在某些情况下，您想要在表单中使用的外部 UI 组件可能不支持`ref`，并且只能由状态控制。

React Hook Form 对这种情况有规定，并且可以使用一个`Controller`组件轻松地与任何第三方控制的组件集成。

React Hook Form 提供了包装器`Controller`组件，允许您注册一个受控的外部组件，类似于`register`方法的工作方式。在这种情况下，您将使用来自`useForm`钩子的`control`对象，而不是`register`方法:

```
const { register, handleSubmit, control } = useForm();

```

假设您必须在表单中创建一个角色字段，该字段将接受来自选择输入的值。您可以使用`react-select`库创建选择输入。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`control`对象应该和字段的`name`一起传递给`Controller`组件的`control`属性。您可以使用`rules`属性指定验证规则。

应该使用`as`属性将受控组件传递给`Controller`组件。`Select`组件还需要一个`options`道具来呈现下拉选项:

```
<Controller
  name="role"
  control={control}
  defaultValue=""
  rules={registerOptions.role}
  render={({ field }) => (
    <Select options={selectOptions} {...field} label="Text field" />
  )}
/>

```

上面的`render`道具为子组件提供了`onChange`、`onBlur`、`name`、`ref`和`value`。通过将`field`扩展到`Select`组件，React Hook Form 注册输入字段。

您可以查看以下角色字段的完整示例:

```
import { useForm, Controller } from "react-hook-form";
import Select from "react-select";
// ...
const { register, handleSubmit, errors, control } = useForm({
  // use mode to specify the event that triggers each input field 
  mode: "onBlur"
});

const selectOptions = [
  { value: "student", label: "Student" },
  { value: "developer", label: "Developer" },
  { value: "manager", label: "Manager" }
];

const registerOptions = {
  // ...
  role: { required: "Role is required" }
};

// ...
<form>
  <div>
    <label>Your Role</label>
    <Controller
      name="role"
      control={control}
      defaultValue=""
      rules={registerOptions.role}
      render={({ field }) => (
        <Select options={selectOptions} {...field} label="Text field" />
      )}
    />
    <small className="text-danger">
      {errors?.role && errors.role.message}
    </small>
  </div>
</form>

```

你也可以在这里查看`Controller`组件[的 API 参考以获得详细的解释。](https://react-hook-form.com/api/#Controller)

## 结论

React Hook Form 是 React 开源生态系统的优秀补充。它使得开发人员创建和维护表单变得更加容易。这个库最大的优点是它更注重开发者的体验，并且使用起来非常灵活。React Hook Form 还可以很好地与状态管理库集成，并且在 React Native 中运行良好。

这就是本指南的内容。你可以在这里查看完整的代码和演示供你参考。直到下一次，保持安全，并继续建立更多的形式。干杯✌

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)