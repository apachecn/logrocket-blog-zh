# 2020 年在 React 中创建表单

> 原文：<https://blog.logrocket.com/forms-in-react-in-2020/>

输入字段。文本区域。单选按钮和复选框。作为开发人员，这些是我们与用户的一些主要交互点。我们把它们放在前面和中心，用户尽可能地填写它们，如果运气好的话，他们会把它们寄回给你，没有任何验证错误。

表单处理是许多 web 应用程序不可或缺的一部分，也是 React 做得最好的事情之一。你有很大的自由来实现和控制你想要的输入控件，并且有很多方法可以达到同样的目的。但是有最佳实践吗？做事有没有最好的方法？

本文将向您展示在 React 中处理表单值的几种不同方法。我们将看看使用状态，自定义挂钩，最后，根本没有状态！

请注意，在所有这些示例中，我们将创建一个带有电子邮件和密码字段的登录表单，但是这些技术可以用于大多数类型的表单。

## 请牢记可访问性

虽然这与当前的主题没有直接关系，但我想确保您记得让所有人都可以访问您的表单。为输入添加标签，在输入无效时设置正确的 aria 标签，并在语义上正确地构建内容。它使您的表单对每个人来说都更容易使用，并且使需要辅助技术的人也可以使用它。

## 用 useState 钩子处理表单

首先，让我们看看我通常是如何处理表单状态的。我将所有字段保存为独立的状态片段，并单独更新它们，如下所示:

```
function LoginForm() {
  const [email, setEmail] = React.useState("");
  const [password, setPassword] = React.useState("");

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    api.login(email, password);
  }
  return (
    <form onSubmit={handleSubmit}>
      <div>
      <label htmlFor="email">Email</label>
      <input
        type="email"
        id="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      </div>
      <div>
      <label htmlFor="password">Password</label>
      <input
        type="password"
        id="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      </div>
    </form>
  );
}

```

首先，我们创建两种不同的状态——电子邮件和密码。然后，这两个变量被传递给它们各自的输入字段，指示该字段的值。每当字段中的内容发生变化时，我们都会确保更新状态值，从而触发应用程序的重新呈现。

这对于大多数用例来说都很好，而且简单，容易理解，也不是很神奇。然而，每次都写出来是相当乏味的。

## 创建自定义挂钩

让我们做一个小的重构，创建一个定制的钩子来稍微改进我们的工作流:

```
const useFormField = (initialValue: string = "") => {
  const [value, setValue] = React.useState(initialValue);
  const onChange = React.useCallback(
    (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value),
    []
  );
  return { value, onChange };
};

export function LoginForm() {
  const emailField = useFormField();
  const passwordField = useFormField();

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    api.login(emailField.value, passwordField.value);
  };
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          type="email"
          id="email"
          {...emailField}
        />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input
          type="password"
          id="password"
          {...passwordField}
        />
      </div>
    </form>
  );
}
```

我们创建了一个定制钩子`useFormField`,它为我们创建了变更事件处理程序，并将值保存在 state 中。当我们使用它时，我们可以将钩子的结果传播到任何字段，事情将会像它所做的那样工作。

## 处理大量字段

这种方法的一个缺点是不能随着表单的增长而扩展。对于登录字段，这可能没问题，但是当您创建用户配置文件表单时，您可能想要询问大量信息！我们应该一遍又一遍地调用我们的自定义钩子吗？

每当我遇到这种挑战时，我都倾向于编写一个自定义钩子，将所有表单状态保存在一个大块中。它可能看起来像这样:

```
function useFormFields<T>(initialValues: T) {
  const [formFields, setFormFields] = React.useState<T>(initialValues);
  const createChangeHandler = (key: keyof T) => (
    e: React.ChangeEvent<HTMLInputElement>,
  ) => {
    const value = e.target.value;
    setFormFields((prev: T) => ({ ...prev, [key]: value }));
  };
  return { formFields, createChangeHandler };
}

export function LoginForm() {
  const { formFields, createChangeHandler } = useFormFields({
    email: "",
    password: "",
  });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    api.login(formFields.email, formFields.password);
  };
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          type="email"
          id="email"
          value={formFields.email}
          onChange={createChangeHandler("email")}
        />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input
          type="password"
          id="password"
          value={formFields.password}
          onChange={createChangeHandler("password")}
        />
      </div>
    </form>
  );
}
```

有了这个`useFormFields`钩子，我们可以继续添加字段，而不会增加组件的复杂性。我们可以在一个地方访问所有表单状态，看起来很整洁。当然，在某些情况下，您可能需要添加一个“出口”并直接暴露底层的`setState`,但是对于大多数表单来说，这已经足够了。

## 另一种方法

所以显式处理状态工作得很好，并且在大多数情况下是 React 的[推荐方法](https://reactjs.org/docs/uncontrolled-components.html)。但你知道还有另一种方法吗？事实证明，默认情况下，浏览器在内部处理表单状态，我们可以利用这一点来简化代码！

下面是相同的表单，但是让浏览器处理状态:

```
export function LoginForm() {
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    const formData = new FormData(e.target as HTMLFormElement);
    api.login(formData.get('email'), formData.get('password'));
  };
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          type="email"
          id="email"
          name="email"
        />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input
          type="password"
          id="password"
          name="password"
        />
      </div>
      <button>Log in</button>
    </form>
  );
}
```

现在，这看起来很简单！看不到一个挂钩，没有设置值，也没有更改侦听器。最棒的是它仍然像以前一样工作——但是怎么工作呢？

你可能已经注意到我们在`handleSubmit`函数中做了一些不同的事情。我们正在使用一个名为 [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 的内置浏览器 API。FormData 是从我们的输入字段中获取字段值的一种方便的(并且得到了[良好支持的](https://developer.mozilla.org/en-US/docs/Web/API/FormData#Browser_compatibility))方式！

我们通过 submit 事件的 target 属性获取对表单 DOM 元素的引用，并创建 FormData 类的一个新实例。现在，我们可以通过调用 formdata . get(' name-of-input-field ')按名称属性获取所有字段。

这样，您就永远不需要显式地处理状态。如果您想要默认值(比如从数据库或本地存储中填充初始字段值)，React 甚至为您提供了一个方便的`defaultValue`道具来完成这项工作！

我们经常听到“使用平台”作为一种轻蔑，但有时平台只是来包装一拳。

## 什么时候用什么

因为表单是大多数 web 应用程序不可或缺的一部分，所以知道如何处理它们很重要。React 为您提供了许多实现这一点的方法。

对于不需要大量验证的简单表单(或者可以依赖于 [HTML5 表单验证控件](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation))，我建议您只使用 DOM 默认提供的内置状态处理。有相当多的事情是您不能做的(比如以编程方式更改输入值或实时验证)，但是对于最简单的情况(比如上面的搜索字段或登录字段)，您可能会使用我们的替代方法。

当您正在进行自定义验证或需要在提交表单之前访问一些表单数据时，您需要用受控组件显式地处理状态。您可以使用常规的 useStateHooks，或者构建一个定制的钩子解决方案来稍微简化您的代码。

值得注意的是，React 本身建议您在大多数情况下使用受控组件(显式处理状态)——因为它更强大，并为您提供更大的灵活性。我认为你经常用简单来换取你不需要的灵活性。

无论您决定使用什么，在 React 中处理表单从来没有像今天这样简单。当情况需要时，您可以让浏览器在显式处理状态的同时处理简单的表单。无论哪种方式，你都可以用比以前更少的代码来完成工作。

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