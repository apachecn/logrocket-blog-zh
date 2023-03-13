# 利用信息构建强大的反应表单

> 原文：<https://blog.logrocket.com/build-powerful-react-forms-with-informed/>

表单是 web 开发的主要部分。如果您必须在 web 上收集用户输入并在您的服务器上处理这些输入，您需要一个表单。

虽然 React 中有许多处理表单的库，但在本文中，我将向您展示如何使用 [Informed 库](https://github.com/teslamotors/informed)构建强大的表单。Informed 是一个轻量级框架和实用程序，用于在 React 应用程序中构建强大的表单。

Informed library 与其竞争对手的不同之处在于，它拥有您所期望的所有框架工具:固执己见的表单 UI 组件和状态管理，以及用于格式化、屏蔽、调试等的内置实用程序。

让我们开始吧。

*向前跳转:*

## 设置通知

首先，通过 npm 在您的`react`项目中安装`informed`库:

```
npm install informed

```

表单的核心主要由一个父表单组件和几个子输入字段组成。输入字段可以采取几种形式，例如，基本文本字段、文本区域、单选输入、复选框输入、选择输入、数字输入等。不管`react`表单库如何，都是如此。

考虑以下基本形式:

![An example of a basic form](img/3660e0c9c8ff75bf12bce7d5dc6ca6de.png)

An [example of a basic form](https://stackblitz.com/edit/react-ts-jqjc8h?file=App.tsx)

其核心是一个有三个输入元素的表单:两个文本字段和一个**提交**按钮。

为了通过`informed`表示这个表单，我们可以使用它的内置组件，如下所示:

```
import * as React from 'react';
import { Form, Input, Debug } from 'informed'; {/** 👈 look here **/}

export default function App() {
  return (
    <Form
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
      <h1>Sign in </h1>
      <Input name="email" label="Email:" placeholder="[email protected]" />
      <Input name="password" type="password" label="Password:" placeholder="**********" />
      <button type="submit"> Submit </button>
    </Form>
  );
}

```

注意传递给`Input`字段的属性是如何类似于标准的 HTML 输入属性，即`name`和`placeholder`。

这个`Form`比较简单。提交后，表单值会记录在控制台中。如果您尝试在没有任何输入条目的情况下提交，将会记录一个空对象:

![Logging form state](img/6ab587d61b46cf0323e74c0796dc1ee1.png)

[Logging form state](https://stackblitz.com/edit/react-ts-jqjc8h?file=App.tsx)

```
{/** ... **/}
onSubmit={(formState) => {
  console.log(formState.values);
}}
{/** ... **/}

```

## 简单表单验证(带消息)

之所以有这么多表单库，并不是因为编写 HTML 输入字段很难——困难在于管理表单状态和输入验证。在上面的基本示例中，用户可以提交空输入。这离生产就绪还差得很远。

让我们解决这个问题。

### 简单的必需输入

验证的第一层是简单地创建必填字段。通过将`required`道具传递给`informed`输入来实现:

```
{/** ... **/}
<Input
  required {/** 👈 look here **/}
  name="email"
  label="Email:"
  placeholder="[email protected]"
/>
<Input
  required {/** 👈 look here **/}
  name="password"
  label="Password:"
  placeholder="**********"
/>
{/** ... **/}

```

现在，当您尝试提交表单时，您会看到默认的`This field is required`错误消息显示在必需的输入字段下面。

![The default required messages](img/e13b1b0cfbc6d74c0b694de5e8e8da2a.png)

[Default required messages](https://stackblitz.com/edit/react-ts-pzay2d?file=App.tsx)

### 更改所需的输入验证消息

在生产应用程序中，您希望完全控制向用户显示的消息。默认消息`This field is required`可能不够。您可能还希望传递基于用户语言的本地化消息。

为此，将一个`errorMessage` prop 传递给所需的输入，如下所示:

```
{/** ... **/}
<Input
  required
  name="email"
  label="Email:"
  placeholder="[email protected]"
  errorMessage="Your email is required" {/** 👈 look here **/}
/>
<Input
  required
  name="password"
  type="password"
  label="Password:"
  placeholder="**********"
  errorMessage="Your password is required" {/** 👈 look here **/}
/>
{/** ... **/}

```

上述代码产生以下内容:

![Custom error messages](img/9b317e09c166711eb50ec7f64e2da504.png)

[Custom error messages](https://stackblitz.com/edit/react-ts-pzsrhk?file=App.tsx)

## 超越基本的、必需的验证

添加`required`道具是很好的第一步。然而，在现实世界的项目中，你会想走得更远。

我们的登录表单可以使用更多的验证。例如，对于错误输入的电子邮件，如何验证实际的电子邮件输入？那么验证密码输入的长度呢？

为了获得更大的验证灵活性，您可以将一个`validate`函数属性传递给相关的输入，如下所示:

```
import * as React from 'react';
import { Form, Input, Debug } from 'informed';

const validateEmail = (v: string) => {
  if (!v.includes('@')) {
    return 'Please pass a valid email';
  }
};

const validatePassword = (v: string) => {
  if (v.length < 6) {
    return 'Your password must be at least 7 characters long';
  }
};

export default function App() {
  return (
    <Form
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
      <h1>Sign in </h1>
      <Input
        required
        name="email"
        label="Email:"
        validate={validateEmail} {/** 👈 look here **/}
        placeholder="[email protected]"
        errorMessage="Your email is required"
      />
      <Input
        required
        name="password"
                type="password"
        label="Password:"
        placeholder="**********"
        validate={validatePassword} {/** 👈 look here **/}
        errorMessage="Your password is required"
      />
      <button type="submit"> Submit </button>
    </Form>
  );
}

```

现在，当基本的`required`验证开始时，默认的`errorMessage`道具将一直显示。然而，一旦输入被填充并提交，函数`validate`就会启动，返回的字符串显示为输入错误消息:

![Custom validator functions](img/a5421a9f83d29358158ebff53859b4d4.png)

[Custom validator functions](https://stackblitz.com/edit/react-ts-bjokq2?file=App.tsx)

## 用 Yup 进行模式验证

实际上，大多数人使用健壮的模式验证库，如 [Yup](https://github.com/jquense/yup) 。知情的让你[也用好的](https://blog.logrocket.com/comparing-schema-validation-libraries-zod-vs-yup/)。

让我们[重写我们现有的验证](https://stackblitz.com/edit/react-ts-9lrju6?file=App.tsx)来使用 Yup，如下所示:

```
import * as Yup from 'yup';
import * as React from 'react';
import { Form, Input } from 'informed';

{/** 👇 look here **/}
const validationSchema = Yup.object().shape({
  email: Yup.string()
    .email('Please pass a valid email')
    .required('Your email is required'),
  password: Yup.string()
    .required('Your password is required')
    .min(7, 'Your password must be at least 7 characters long'),
});

export default function App() {
  return (
    <Form
      yupSchema={validationSchema} {/** 👈 look here **/}
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
      <h1>Sign in </h1>
      <Input name="email" label="Email:" placeholder="[email protected]" />
      <Input
        type="password"
        name="password"
        label="Password:"
        placeholder="**********"
      />
      <button type="submit"> Submit </button>
    </Form>
  );
}

```

上述内容的重要之处在于，我们将`yupSchema`属性传递给`Form`组件，然后像平常一样定义您的验证模式。

## 探索其他输入类型

Informed 如何处理其他表单输入类型，如文本区域、单选输入和选择输入？用法与常规输入非常相似:您导入关联的通知组件，并向其传递相关的属性。

考虑下面的例子:

![Other form inputs](img/64bcd5bd1f8a33867df0fa5e08b01314.png)

[Other form inputs](https://stackblitz.com/edit/react-ts-hrzu3i?file=App.tsx)

上面的表单利用了文本区域、单选按钮组、复选框和选择输入。有了 Informed，使用它们就像导入组件(大写名称)和传递与 HTML 元素相同的属性一样简单，只需要添加一个`label`属性就可以了。

方法如下:

```
import * as React from 'react';
import {
  Form,
  Input,
  TextArea,
  Checkbox,
  Select,
  Option,
  Radio,
  RadioGroup,
} from 'informed';

export default function App() {
  return (
    <Form
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
      <h1>Sign up </h1>
      <Input
        required
        name="name"
        label="Name:"
        placeholder="[email protected]"
      />
      <TextArea {/** 👈 look here **/}
        required
        name="bio"
        label="Bio:"
        placeholder="I am a teacher and a developer ..."
      />

      <RadioGroup name="relationship"> {/** 👈 look here **/}
        <Radio value="single" label="Single" />
        <Radio value="married" label="married" />
      </RadioGroup>

      {/** 👇 look here **/}
      <Checkbox name="newsletter" label="Subscribe to our newsletter?" />

      {/** 👇 look here **/}
      <Select name="status" label="Relationship status:">
        <Option value="" disabled>
          Select One...
        </Option>
        <Option value="single">Single</Option>
        <Option value="relationship">Relationship</Option>
        <Option value="complicated">Complicated</Option>
      </Select>

      <button type="submit"> Submit </button>
    </Form>
  );
}

```

## `Debug`组件

调试是构建真实软件的重要部分。使用`Informed`，您可以使用`Debug`组件查看当前表单状态，而不是手动呈现表单值或处理控制台日志。

考虑最后一个例子中`Debug`的使用:

```
import {Debug} from 'informed'

... 
<Debug /> 
...

```

您所要做的就是呈现`Debug`组件，您将获得整个表单当前同步状态的可视化表示。

![The debug component](img/0eefe83a533f01e22cbba34914a66209.png)

[The `debug` component](https://react-ts-r5ye9f.stackblitz.io)

要查看单个表单字段的状态，请将`DebugField`与相关字段的`name`一起使用，如下所示:

```
import {DebugField} from 'informed'

... 
<DebugField name="gender"/> 
...

```

## 控制验证发生的时间

控制何时触发表单验证是非常强大的。例如，为了验证从服务器接收的初始值，或者在初始化 SDK 时传递的默认值，您可能希望在装载表单时立即显示错误。

为了在表单安装后立即验证，将`validateOnMount` prop 传递给`Form`组件:

```
<Form validateOnMount />

```

如果您继续这样做，将在装载时进行验证，但不会向用户显示错误。

您可以使用`showErrorIfError`、`showErrorIfTouched`或`showErrorIfDirty`道具控制何时向用户显示验证错误。(`showErrorIfTouched`是这里的默认值。)

要验证表单并在装载时立即显示错误，请执行以下操作:

```
<Form validateOnMount showErrorIfError/>

```

现在，表单一安装(验证后)，就会显示错误[:](https://react-ts-mtfabt.stackblitz.io)

![Showing errors on mount](img/db0d6a1e7fb165550e71433c73f1a559.png)

[Showing errors on mount](https://stackblitz.com/edit/react-ts-mtfabt?file=App.tsx)

默认情况下，当字段模糊时将进行验证，但您可以更改这种行为。要进一步控制何时进行字段验证，请使用带有以下任意值的`validateOn`属性:

*   `change`
*   `blur`
*   `change-blur`
*   `change-submit`
*   `blur-submit`
*   `submit`

您还可以使用`change`值进行一些实时字段验证，如下所示:

```
{/** ... **/}

export default function App() {
  return (
    <Form
      showErrorIfError
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
      {/* ... */ }

      <TextArea
        name="bio"
        label="Bio:"
        validateOn="change" {/** 👈 look here **/}
        placeholder="I am a teacher and a developer ..."
      />

      {/* ... */ }
    </Form>
  );
}

```

注意`showErrorIfError`用于在验证后立即显示错误——而不是在输入字段模糊后。`validateOn`属性也被传递给`TextArea`组件，并提供一个`validate`函数。

这将模拟[实时验证，](https://stackblitz.com/edit/react-ts-79eqiu?file=App.tsx)遇到错误时向用户显示。

## 通过访问表单状态有条件地呈现表单字段

乍一看，有条件地呈现一个表单域并不是一件难事。你只需遵循你已经熟悉的`JSX`原则，例如:

```
true && <ConditionallyRenderThis />

```

但是，当条件呈现依赖于窗体的内部状态时，您需要访问窗体状态。

使用`Informed`有几种方法可以做到这一点。也许最灵活的选择是使用`useFormState`钩子获取整个表单的状态，然后像平常一样有条件地呈现:

```
const SpouseName = () => {
  const formState = useFormState();
  return (
    formState.values?.relationship === 'married' && (
      <Input name="spouse" label="🚨 Spouse Name:" />
    )
  );
};

export default function App() {
  return (
    <Form
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
     {/* ... */ }
      <SpouseName />
      {/* ... */ }
    </Form>
  );
}

```

现在，当用户选择**已婚**选项时，我们将继续请求他们配偶的名字。

![Conditionally display an input field](img/362498480b3c36f3d33594a308aafb3f.png)

[Conditionally display an input field](https://stackblitz.com/edit/react-ts-dd2iap?file=App.tsx)

需要注意的是，`useFormState`钩子是在`Form`中呈现的一个单独的子组件中使用的。如果您继续使用父组件中的`useFormState`钩子来渲染`Form`，这将不起作用。

`formState.values`在这里永远是`undefined`。您必须使用`Form`组件中的钩子从内部访问表单上下文:

```
export default function App() {
  const formState = useFormState()

  return (
    <Form
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
     {/* ... */ }
      {formState.values?.relationship === 'married' && (
         <Input name="spouse" label="🚨 Spouse Name:" />
      )
      {/* ... */ }
    </Form>
  );
}

```

## 简单的、有条件呈现的表单字段

Informed 还导出一个`Relevant`组件来简化条件渲染。例如，我们可以修改前面的例子来使用`Relevant`组件，如下所示:

```
<Form
    onSubmit={(formState) => {
      console.log(formState.values);
    }}
   >
   {/** ... */}

   <Relevant
        when={({ formState }) => formState.values?.gender === 'non_binary'}
      >
      <Input name="spouse" label="🚨 Spouse Name:" />
   </Relevant>

   {/** ... */}

   <button type="submit"> Submit </button>
 </Form>

```

呈现子组件的条件被传递给`when`函数 prop，它接收表单状态作为参数并返回一个`boolean`。

如果计算结果为`true`，则渲染子组件。这种情况:`<Input name="spouse" label="🚨 Spouse Name:" />`。

或者，您也可以通过向所需字段传递一个`relevant`属性，在字段级别应用条件呈现。考虑下面的例子:

```
{/** ... **/}
 <Select
    name="date"
    label="🚨 Shall we help you set up a date?"
    relevant={({ formState }) =>
      formState.values?.relationship === 'single'
    }
  >
    <Option value="" disabled>
      Select a date option
    </Option>
    <Option value="yes">Yes</Option>
    <Option value="no">No</Option>
 </Select>
{/** ... **/}

```

现在，每当用户选择他们的关系状态为`single`时，我们将继续呈现一个`select`输入，询问是否为他们安排约会。

![Conditionally rendering a field with the relevant prop](img/163c34140361db220395fd139a52ef38.png)

[Conditionally rendering a field with the `relevant` prop](https://stackblitz.com/edit/react-ts-p31ahp?file=App.tsx)

我们没有使用`Relevant`组件，而是在这里利用了`relevant`道具。它有一个类似于传递给`Relevant`的`when`道具的 API。prop 的值是一个接收表单状态的函数，预期返回值是一个`boolean`。

## 使用范围更轻松地组织数据

到目前为止，我们只使用了扁平形式的状态结构。例如，我们的表单在填写时将具有类似于以下的状态:

```
{
    "name": "Amadeus Mozart",
    "bio": "Senior software engineer",
    "relationship": "single",
    "date": "no",
    "newsletter": true,
    "status": "single"
}

```

但是，对于复杂的表单，您可能希望在表单状态中嵌套相关的值。例如，我们可以用以下状态表示:

```
{
    "name": "Amadeus Mozart",
    "bio": "Senior software engineer",
    "bond": {
      "relationship": "single",
      "status": "single",
      "date": "no"
    },
    "marketing": {
      "newsletter": true
    }
}

```

注意嵌套的`bond`和`marketing`字段。为了实现这一点，`Informed`公开了一个`Scope`组件，用于对相关表单字段进行分组。我们需要做的就是将子字段包装在一个`Scope`组件中，用一个`scope`道具指向最终嵌套的父字段的名称。

这里有一个例子:

```
export default function App() {
  return (
    <Form
      onSubmit={(formState) => {
        console.log(formState.values);
      }}
    >
      {/** ... **/}

     {/** Look here 👇 **/}
      <Scope scope="bond">
        <RadioGroup name="relationship">
          <Radio value="single" label="Single" />
          <Radio value="married" label="married" />
        </RadioGroup>

        <Relevant
          when={({ formState }) =>
            formState.values?.bond?.relationship === 'married'
          }
        >
          <Input name="spouse" label="🚨 Spouse Name:" />
        </Relevant>

        <Select
          name="date"
          label="🚨 Shall we help you set up a date?"
          relevant={({ formState }) =>
            formState.values?.bond?.relationship === 'single'
          }
        >
          <Option value="" disabled>
            Select a date option
          </Option>
          <Option value="yes">Yes</Option>
          <Option value="no">No</Option>
        </Select>

        <Select name="status" label="Relationship status:">
          <Option value="" disabled>
            Select One...
          </Option>
          <Option value="single">Single</Option>
          <Option value="relationship">Relationship</Option>
          <Option value="complicated">Complicated</Option>
        </Select>
      </Scope>

      {/** Look here 👇 **/}
      <Scope scope="marketing">
        <Checkbox name="newsletter" label="Subscribe to our newsletter?" />
      </Scope>

      <button type="submit"> Submit </button>
    </Form>
  );
}

```

需要注意的是，当您使用`Scope`组件时，您必须记住在访问表单状态时使用新的嵌套层次结构。

例如，考虑`relevant` prop 逻辑:

```
// before 
formState.values?.relationship === 'single'

// now: use the nested "bond" scope
formState.values?.bond?.relationship === 'single'

```

## 将输入格式化程序和掩码添加到 Informed

如果您尝试过在没有库的帮助下发布生产输入格式化程序或掩码，那么您知道它们会很快变得难以处理。有了`Informed`，你可以使用字符串或者旧的正则表达式来处理这个问题。

考虑我们的表单应用程序的以下新需求:如果用户是单身，并且希望安排一次约会，那么就请求他们的电话号码来安排。

假设该应用程序仅在美国可用，让我们继续为用户的电话号码提供一些输入格式:

```
...

<Input
  type="tel"
  name="tel"
  label="Phone number"
  relevant={({ formState }) =>
    formState.values?.bond?.relationship === 'single' &&
    formState.values?.bond?.date === 'yes'
  }
/>;

```

使用`relevant`道具，当用户的关系状态设置为`single`时，将显示输入的电话号码，他们想要一个日期。

![Conditionally displaying a telephone input](img/b88c8e2b3f7f9c269846d3dfec11d54a.png)

[Conditionally displaying a telephone input](https://stackblitz.com/edit/react-ts-telnoq?file=App.tsx)

现在，我们可以继续为这个电话号码输入提供一个`formatter` prop，如下所示:

```
<Input
  {/** ... **/}
  formatter="+1 ###-###-####"
  {/** ... **/}
/>;

```

这将把用户输入格式化为`+1 ###-###-####`，其中`#`代表有效数字。如果您检查表单状态，您会看到电话号码以相同的格式存储。

如果您更愿意在表单状态中存储原始的用户输入，那么就提供一个`parser`函数来去掉格式，如下所示:

```
<Input
  {/** ... **/}
  formatter="+1 ###-###-####"
  parser={(v) => v.replace('+1 ', '').replace(/-/g, '')}
  {/** ... **/}
/>;

```

所提供的`parser`函数取值并替换电话代码(`+1`)和破折号(`-`)。

显然，还有很多其他输入格式化和屏蔽的用例。我建议阅读一下官方文件。

## 为什么使用知情？

现在你已经对 Informed 的工作原理有了一个大致的了解，让我们来讨论一下为什么你应该考虑在你的下一个项目中使用`Informed`。

### 知情 vs. Formik

Formik 可以说是 React 和 React Native 最流行的开源表单库。那么，有见识的人如何对抗强大的力量呢？

Formik 的大多数受人喜爱的特性在 Informed 中同样适用。它们都有声明性 API，所以您可以专注于您的应用程序业务逻辑。它们也遵循你所习惯的相同的反应心理模型——即，没有花哨的观察者或引擎盖下的订阅，只有良好的反应状态和道具。它们也不会将您绑定到任何现成的特定状态管理库。因此，它们都适用于任何状态管理库，比如 Redux。

Informed 开始大放异彩的地方是它在 Formik 上提供的附加实用程序。知情提供:

*   格式化和屏蔽输入字段的明确规定
*   特殊的 [`Debug`组件](https://teslamotors.github.io/informed/?path=/story/debugging--debug)
*   使用 [`Relevant`组件](https://teslamotors.github.io/informed/?path=/story/relevance--relevant-comonent)和其他实用程序的动态表单的更多选项，例如 [KeepState](https://teslamotors.github.io/informed/?path=/story/keepstate--keepstate) ，它允许您保留一个字段的状态，即使它被卸载(不再显示在屏幕上)

Informed 超越了基本需求，并提供实用程序来解决您在现实世界中可能遇到的常见问题。

在我看来，与福米克相比，`Informed`相当不错。

### 知情与反应挂钩形式

React 钩子形态是 React 中建筑形态的绝佳替代品。它可以说是第二个最受欢迎的表单库。与 Formik 一样，提供表单实用程序是 Informed 与众不同的地方。

然而，值得一提的是，React Hook Form 的突出特点是它不依赖于受控的表单输入，这无疑使它的性能优于许多其他表单库(Informed inclusive)。

## 结论

尽管还是一个年轻的图书馆，但它充满了潜力。如果您发现自己开始了一个新项目，或者想要修补一个新的表单库，那么不妨试试 Informed。

我在使用 Informed 时遇到的一个警告是，对文档和导出类型有明显的更新，但这是一个有潜力的新项目的美妙之处。你可以为它的发展做出贡献。

干杯，快乐的建筑！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)