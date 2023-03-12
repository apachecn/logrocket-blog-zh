# 如何在 Remix 中验证表单

> 原文：<https://blog.logrocket.com/how-to-validate-forms-remix/>

[Remix](https://blog.logrocket.com/remix-guide-newly-open-sourced-react-framework/) 消除了手动将表单连接到状态的需要，或者像在典型的 React 应用程序中一样，使用`submit`事件监听器在客户端处理表单提交。相反，Remix 把我们带回了 PHP 等语言处理表单的传统方式。

当用 React 处理表单时，我们必须设置状态表单数据和错误，决定我们是否要使用受控或不受控变量，并处理`onChange`、`onBlur`和`onSubmit`事件。

在本文中，我们将学习表单如何在 Remix 中工作，以及验证它们的不同方法。我们还将学习如何设置自定义验证，以及如何用[重新混合已验证的表单](https://www.remix-validated-form.io/)来验证表单。

最后，我们将看看 Remix 提供的定制`Form`组件，以及 Remix 在处理表单时如何采取与 React 不同的方法。

## 混音的形式方法

Remix 带回了处理表单的传统方法。

Remix 提供了一些函数(名为`action`和`loader`，我们可以用它们来执行服务器端操作和访问表单数据。有了这些函数，我们不再需要向前端提供 JavaScript 来提交表单，从而减轻了浏览器的负载。

在其他框架中，我们可能需要使用 JavaScript 进行`fetch`或`axios`调用，但是在 Remix 中我们不需要这样做。它有助于保持事情简单。

## `Form`组件

Remix 提供了一个定制的`Form`组件，其工作方式与原生 HTML `<form>`元素相同。与 React 表单不同，在 React 表单中，我们必须设置`onChange`、`onSubmit`或`onClick`事件处理程序，而在使用 Remix 时，我们不需要这样做。此外，我们不需要为表单设置状态，因为我们可以从 web 的`[formData()](https://developer.mozilla.org/en-US/docs/Web/API/FormData/FormData)` API 访问表单数据。

`Form`是一个支持 Remix 的增强型 HTML 表单组件。除了与服务器的交互是通过`fetch`而不是新的文档请求之外，它的行为与普通表单一样，允许组件在表单提交和返回数据时为页面添加更好的用户体验。

`Form`会自动对当前页面路由做一个`POST`请求。然而，我们也可以为`PUT`和`DELETE`请求配置它。需要一个动作方法来处理来自表单的请求。

## 在 Remix 中设置表单

让我们看看混音的基本形式是什么样的:

```
import { Form, useActionData } from "remix";

export async function action({ request }) {
  //here, do something with the form data and return a value  
}

export default function Sign() {
  const data = useActionData(); //we access the return value of the action here

  return (
    <Form
      method="post"
      style={{ fontFamily: "system-ui, sans-serif", lineHeight: "1.4" }}
    >
      <div>
        <label>
          Name: <input name="name" type="text" />
        </label>
      </div>
      <div>
        <label>
          Email: <input name="email" type="email" />
        </label>
      </div>
      <div>
        <label>
          Password: <input name="password" type="password" />
        </label>
      </div>
      <div>
        <label>
          Confirm Password: <input name="confirmPassword" type="password" />
        </label>
      </div>
      <button type="submit">Create Account</button>
    </Form>
  );
}

```

这里，我们使用 Remix 的`Form`组件和`useActionData`钩子，我们将在后面使用。当用户提交表单时，Remix 使用 fetch API 自动向服务器发出包含表单数据的 POST 请求。

`useActionData`从路由的动作返回 JSON 解析的数据。它最常用于处理表单验证错误。

动作是当我们提交表单时只在服务器上运行的功能。对`POST`、`PATCH`、`PUT`和`DELETE`方法调用动作，因为动作意味着修改或变更数据。

## 在 Remix 中验证表单

首先，让我们为表单字段设置验证逻辑:

```
const validateName = (name) => {
  if (!name) {
    return "Name is required";
  } else if (typeof name !== "string" || name.length < 3) {
    return `Name must be at least 3 characters long`;
  }
};

const validateEmail = (email) => {
  if (!email) {
    return "Email is Required";
  } else if (!/^[^\[email protected]][email protected][^\[email protected]]+\.[^\[email protected]]+$/.test(email)) {
    return "Invalid emaill address";
  }
};

const validatePassword = (password) => {
  if (!password) {
    return "Password is required";
  } else if (typeof password !== "string" || password.length < 6) {
    return `Passwords must be at least 6 characters long`;
  }
};

const validateComfirmPassword = (password, confirmPassword) => {
  if (!confirmPassword) {
    return "Confirm Password is required";
  } else if (password !== confirmPassword) {
    return "Password does not match";
  }

};

```

验证逻辑很简单。我们检查输入字段是否为空，或者它们是否满足某一组要求。如果他们没有通过这些检查，我们将返回错误消息。

接下来，我们为表单设置动作:

```
export const action = async ({ request }) => {
  const data = Object.fromEntries(await request.formData());

  console.log(data);
  // outputs { name: '', email: '', password: '', confirmPassword: '' }

  const formErrors = {
    name: validateName(data.name),
    email: validateEmail(data.email),
    password: validatePassword(data.password),
    confirmPassword: validateComfirmPassword(
      data.password,
      data.confirmPassword
    ),
  };

  //if there are errors, we return the form errors
  if (Object.values(formErrors).some(Boolean)) return { formErrors };

  //if there are no errors, we return the form data
  return { data };
};

```

这里，我们创建一个`formErrors`对象，并将验证函数的返回值传递给它们各自的键。

然后，检查是否有错误，返回`formErrors`，否则我们返回数据。在现实世界的项目中，我们会将用户重定向到另一条路线。

最后，让我们将操作与表单挂钩，并显示错误(如果有错误的话):

```
export default function Sign() {
  const actionData = useActionData();

  return (
    <Form
      method="post"
      style={{ fontFamily: "system-ui, sans-serif", lineHeight: "1.4" }}
    >
      <div>
        <label>
          Name: <input name="name" type="text" />
        </label>
        {actionData?.formErrors?.name ? (
          <p style={{ color: "red" }}>{actionData?.formErrors?.name}</p>
        ) : null}
      </div>

      <div>
        <label>
          Email: <input name="email" type="email" />
        </label>
        {actionData?.formErrors?.email ? (
          <p style={{ color: "red" }}>{actionData?.formErrors?.email}</p>
        ) : null}
      </div>

      <div>
        <label>
          Password: <input name="password" type="password" />
        </label>
        {actionData?.formErrors?.password ? (
          <p style={{ color: "red" }}>{actionData?.formErrors?.password}</p>
        ) : null}
      </div>

      <div>
        <label>
          Confirm Password: <input name="confirmPassword" type="password" />
        </label>
        {actionData?.formErrors?.confirmPassword ? (
          <p style={{ color: "red" }}>
            {actionData?.formErrors?.confirmPassword}
          </p>
        ) : null}
      </div>

      <button type="submit">Create Account</button>
    </Form>
  );
}

```

这里，我们从`actionData`访问`formErrors`对象，并有条件地为每个字段呈现适当的表单错误。

将所有这些放在一起，我们得到了下面的最终代码:

```
import { Form, useActionData } from "remix";

const validateName = (name) => {
//validation logic here
};

const validateEmail = (email) => {
//validation logic here
};

const validatePassword = (password) => {
//validation logic here
};

const validateComfirmPassword = (password, confirmPassword) => {
//validation logic here
};

export const action = async ({ request }) => {
  const data = Object.fromEntries(await request.formData());

  const formErrors = {
    name: validateName(data.name),
    email: validateEmail(data.email),
    password: validatePassword(data.password),
    confirmPassword: validateComfirmPassword(
      data.password,
      data.confirmPassword
    ),
  };

  if (Object.values(formErrors).some(Boolean)) return { formErrors };

  return { data };
};

export default function Sign() {
  const actionData = useActionData();

  return (
    <Form
      method="post"
      style={{ fontFamily: "system-ui, sans-serif", lineHeight: "1.4" }}
    >
      <div>
        <label>
          Name: <input name="name" type="text" />
        </label>
        {actionData?.formErrors?.name ? (
          <p style={{ color: "red" }}>{actionData?.formErrors?.name}</p>
        ) : null}
      </div>

      <div>
        <label>
          Email: <input name="email" type="" />
        </label>
        {actionData?.formErrors?.email ? (
          <p style={{ color: "red" }}>{actionData?.formErrors?.email}</p>
        ) : null}
      </div>

      <div>
        <label>
          Password: <input name="password" type="password" />
        </label>
        {actionData?.formErrors?.password ? (
          <p style={{ color: "red" }}>{actionData?.formErrors?.password}</p>
        ) : null}
      </div>

      <div>
        <label>
          Confirm Password: <input name="confirmPassword" type="password" />
        </label>
        {actionData?.formErrors?.confirmPassword ? (
          <p style={{ color: "red" }}>
            {actionData?.formErrors?.confirmPassword}
          </p>
        ) : null}
      </div>

      <button type="submit">Create Account</button>
    </Form>
  );
}

```

至此，我们已经成功地设置了一个 Remix 表单的自定义验证。虽然这种方法可行，但它不能完全满足所有可能的表单验证需求。

例如，验证逻辑仅在我们提交表单时运行。理想情况下，我们应该在用户输入或关注某个字段时验证表单。我们可以设置这个逻辑，但是编码起来会很繁琐，而且我们还需要解决几个易访问性问题。

幸运的是，我们可以使用一个库来正确处理 Remix 表单的验证。

## 使用重新混合验证表单验证重新混合表单

Remix Validated Form(简称 RVF)提供了一个用于验证 Remix 表单的`Form`组件和实用程序。

RVF 是验证库不可知的。它有针对 [Yup 和 Zod](https://blog.logrocket.com/comparing-schema-validation-libraries-zod-vs-yup/) 的官方适配器，但是我们可以[创建我们自己的适配器](https://www.remix-validated-form.io/validation-library-support)来支持我们选择的验证库。

让我们看看如何使用 RVF。

首先，我们设置一个定制的`Input`组件，如下所示:

```
import { useField } from "remix-validated-form";

export const Input = ({ name, label }) => {
  const { error, getInputProps } = useField(name);

  return (
    <div>
      <label htmlFor={name}>
        {label}: {""}
      </label>
      <input {...getInputProps({ id: name })} />
      {error && <p style={{ color: "red" }}>{error}</p>}
    </div>
  );
};

```

`useField`钩子返回`getInputProps`,它是一个 prop-getter，如果存在的话，还会返回一个验证错误消息。我们传入输入的名称和标签，并有条件地呈现错误消息。

接下来，我们设置一个定制的`SubmitBtn`组件:

```
import { useIsSubmitting } from "remix-validated-form";

export const SubmitBtn = () => {
  const isSubmitting = useIsSubmitting();

  return (
    <button type="submit" disabled={isSubmitting}>
      {isSubmitting ? "Submitting..." : "Submit"}
    </button>
  );
};

```

`useIsSubmitting`返回一个`isSubmitting`布尔值，通知我们提交事件何时发生(当用户提交表单时)。

现在，设置一个`validator`，RVF 将在后台使用它来验证表单字段。我们将使用 Zod 来创建验证模式:

```
export const validator = withZod(
  z
    .object({
      name: z
        .string()
        .nonempty("Name is required")
        .min(3, "Name must be at least 3 characters long"),
      email: z
        .string()
        .nonempty("Email is required")
        .email("Invalid emaill address"),
      password: z
        .string()
        .nonempty("Password is required")
        .min(6, "Password must be at least 6 characters long"),
      confirmPassword: z.string(),
    })
    .refine(({ password, confirmPassword }) => password === confirmPassword, {
      message: "Passwords must match",
      path: ["confirmPassword"],
    })
);

```

接下来，我们为表单创建一个动作:

```
export const action = async ({ request }) => {

  const result = await validator.validate(await request.formData());

  if (result.error) {
    // validationError comes from `remix-validated-form`
    return validationError(result.error);
  }

  return result;
};

```

如果存在错误，这将返回错误，否则返回表单数据。

现在，让我们将`Input`、`SubmitBtn`、`validator`和我们之前创建的动作放在一起，创建一个注册表单:

```
export default function Sign() {
  const actionData = useActionData();
  console.log(actionData);
  return (
    <ValidatedForm
      validator={validator}
      method="post"
      defaultValues={{ name: "Nefe", email: "[email protected]" }}
      style={{ fontFamily: "system-ui, sans-serif", lineHeight: "1.4" }}
    >
      <Input name="name" label="Name" />
      <Input name="email" label="Email" />
      <Input name="password" label="Password" />
      <Input name="confirmPassword" label="Confirm Password" />
      <SubmitBtn />
    </ValidatedForm>
  );
}

```

`ValidatedForm`是 RVF 的主要形体成分。以下是它接受的一些道具:

*   `defaultValues`，一个包含每个表单字段初始值的对象(这是一个可选字段)
*   `validator`，描述如何验证表单的对象
*   `resetAfterSubmit`，一个布尔值，在表单成功提交后将表单重置为默认值

将所有这些放在一起，我们得到了下面的最终代码:

```
import { useActionData } from "remix";
import { ValidatedForm } from "remix-validated-form";
import { withZod } from "@remix-validated-form/with-zod";
import { SubmitBtn } from "~/components/submitBtn";
import { Input } from "~/components/Input";
import { z } from "zod";

export const validator = withZod(
//validation logic here
);

export const action = async ({ request }) => {
  const result = await validator.validate(await request.formData());

  if (result.error) {
    return validationError(result.error);
  }

  return result;
};

export default function Sign() {
  const actionData = useActionData();

  return (
    <ValidatedForm
      validator={validator}
      method="post"
      defaultValues={{ name: "Nefe", email: "[email protected]" }}
      style={{ fontFamily: "system-ui, sans-serif", lineHeight: "1.4" }}
    >
      <Input name="name" label="Name" />
      <Input name="email" label="Email" />
      <Input name="password" label="Password" />
      <Input name="confirmPassword" label="Confirm Password" />
      <SubmitBtn />
    </ValidatedForm>
  );
}

```

## 结论

在本文中，我们学习了一种新的(但实际上是旧的)管理 web 表单的方法。我们已经看到了 Remix 的方法与 React 在处理表单方面的不同。

我们还学习了如何为我们的表单设置自定义验证，以及如何使用 Remix Validated 表单来验证它们。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)