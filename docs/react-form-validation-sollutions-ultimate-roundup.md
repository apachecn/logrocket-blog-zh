# React 表单验证解决方案:终极综述

> 原文：<https://blog.logrocket.com/react-form-validation-sollutions-ultimate-roundup/>

***编者按:**这篇文章于 2022 年 3 月 23 日更新，包含了关于下列 React 表单验证解决方案的更新信息。*

作为开发人员，通常最好不要重新发明轮子。这就是为什么实施解决方案的第一步是寻找现有的解决方案。这就是 React 所要做的——创建可重用的组件，这样我们就不会重复自己的工作。

在本文中，我们将了解 React 中一些最流行的表单管理和验证解决方案:

我们开始吧！

没有 Formik，关于 React form 解决方案的综述是不完整的。GitHub 上有 30k stars，它基本上是 React 中最流行的构建和管理表单的库。Formik 是一个可伸缩的、高性能的表单助手，具有最小的 API。它处理表单状态管理、验证和错误处理。

**安装**

```
yarn add formik

```

**基本用法**
下面是如何使用最基本形式的 Formik。钩子是封装所有优点的地方。要使用它，只需要传入一个包含每个表单字段默认值的对象`initialValues`。以及当表单验证通过时被调用的`onSubmit`处理程序。

当调用`useFormik`时，它返回事件处理程序和表单状态，我们可以用它们来指定何时触发表单验证。验证规则是通过`validate`属性添加的。回调函数作为该属性的值传递。也是验证单个表单字段的地方。每个字段的`onChange`事件都会触发验证。这意味着默认情况下，当用户更新第一个字段的值时，表单中的所有其他字段也会触发它们的验证码。

我们并不真的想要那样，这就是为什么我们需要从`useFormik`的返回对象中提取出`handleBlur`和`touched`。前者允许我们从字段中监听模糊事件，而后者允许我们检查用户是否已经访问了字段。从那里，我们可以通过`errors`检查错误:

```
import React from "react";
import { useFormik } from "formik";

function FormikComponent() {
  function validate(values) {
    const errors = {};
    if (!values.favoriteFood) {
      errors.favoriteFood = "Required";
    }

    if (!values.favoritePlace) {
      errors.favoritePlace = "Required";
    }
    return errors;
  }

  const {
    handleSubmit,
    handleChange,
    handleBlur,
    touched,
    values, // use this if you want controlled components
    errors,
  } = useFormik({
    initialValues: {
      favoriteFood: "",
      favoritePlace: "",
    },
    validate,
    onSubmit: (values) => {
      console.log(JSON.stringify(values));
      // values = {"favoriteFood":"ramen","favoritePlace":"mountains"}
    },
  });

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="favoriteFood">Favorite Food:</label>
      <input
        type="text"
        name="favoriteFood"
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {touched.favoriteFood && errors.favoriteFood
        ? <div>errors.favoriteFood</div>
        : null}

      <label htmlFor="favoritePlace">Favorite place:<label>
      <input
        type="text"
        name="favoritePlace"
        onChange={handleChange}
        onBlur={handleBlur}
      />

      {touched.favoritePlace && errors.favoritePlace
        ? <div>errors.favoritePlace</div>
        : null}

      <button type="submit">submit</button>
    </form>    
  );
}

export default App;

```

上面的例子很好，但是在更复杂的表单上可能会很快失控。为此，我们可以使用[是的](https://github.com/jquense/yup)库。Yup 是一个模式构建器，用于解析值并验证它们。您可以使用以下命令安装它:

```
yarn add yup

```

要使用 Yup，你所要做的就是用`validationSchema`替换`validate`。这是专门为 Yup 集成而创建的:

```
import * as Yup from "yup";

function App() {
  const schema = Yup.object({
    favoriteFood: Yup.string().required("Required"),
    favoritePlace: Yup.string().required("Required"),
  });

  const { ... } = useFormik = ({
    // ...
    validationSchema: schema // replace validate with this
  });
}

```

**总结**

React Final Form 是一个框架无关的表单验证库，没有任何依赖性。[最终形式](https://github.com/final-form/final-form)是实际库的名称，而 React 最终形式是 React 包装器。它是基于订阅的，所以当表单状态更新时，只有特定的表单字段才会更新。

**安装**

```
yarn add final-form react-final-form

```

**基本用法**
用 React 最终表单验证表单有两种方式:[记录级](https://final-form.org/docs/react-final-form/examples/record-level-validation)和[字段级](https://final-form.org/docs/react-final-form/examples/field-level-validation)。记录级别与使用 Formik 的方式非常相似。就像 Formik 一样，您也可以轻松地使用 Yup 来实现验证规则。验证表单的方式取决于表单的复杂程度。如果您的表单相当简单，您可以坚持使用字段级验证。否则，使用记录级别。

下面是一个使用 Yup 进行记录级验证的例子。这要求我们使用最终表单库中的`setIn`实用函数将默认的错误对象转换成使用点括号语法的对象(例如`people[0].name.first`)。在下面的代码中，你可以看到它的工作方式类似于 Formik。尽管它依赖于使用定制组件(`<Form>`和`<Field>`)来包装原始的 HTML 元素，为其提供状态和输入回调:

```
import React from "react";
import { setIn } from "final-form";
import { Form, Field } from "react-final-form";
import * as Yup from "yup";

const schema = Yup.object({
  favoriteFood: Yup.string().required("Required"),
});

const validate = async (values) => {
  try {
    await schema.validate(values, { abortEarly: false });
  } catch (err) {
    const errors = err.inner.reduce((formError, innerError) => {
      return setIn(formError, innerError.path, innerError.message);
    }, {});

    return errors;
  }
};

const onSubmit = (values) => {
  console.log("values: ", values);
};

function FinalFormComponent() {
  return (
    <Form
      onSubmit={onSubmit}
      validate={validate}
      render={({ handleSubmit, form, submitting, pristine, values }) => (
        <form onSubmit={handleSubmit}>
          <Field name="favoriteFood">
            {({ input, meta }) => (
              <div>
                <label>Favorite Food</label>
                <input {...input} type="text" placeholder="favoriteFood" />
                {meta.error && meta.touched && <span>{meta.error}</span>}
              </div>
            )}
          </Field>
          <button type="submit">submit</button>
        </form>
      )}
    />
  );
}

```

下面是使用字段级验证的方法:

```
import React from "react";
import { Form, Field } from "react-final-form";

const onSubmit = (values) => {
  console.log("values: ", values);
};

const required = (value) => (value ? undefined : "Required");

function FinalFormComponent() {
  return (
    <Form
      onSubmit={onSubmit}
      render={({ handleSubmit, form, submitting, pristine, values }) => (
        <form onSubmit={handleSubmit}>
          <div>
            <Field name="favoriteFood" validate={required}>
              {({ input, meta }) => (
                <div>
                  <label>Favorite Food</label>
                  <input {...input} type="text" placeholder="Favorite food" />
                  {meta.error && meta.touched && <div>{meta.error}</div>}
                </div>
              )}
            </Field>
          </div>
          <button type="submit">submit</button>
        </form>
      )}
    />
  );
}

export default FinalFormComponent;

```

**总结**

*   React 最终形式不依赖于任何库
*   它允许您管理哪些表单元素收到表单状态更改的通知。这使得它非常有性能
*   支持反作用挂钩
*   通过 Yup 库支持基于模式的验证
*   依赖于充当 HTML 表单元素包装器的自定义组件
*   依赖 React 上下文 API 为每个表单字段提供表单状态数据
*   React 最终版本重 [3.2kB](https://bundlephobia.com/result?p=react-final-form@6.4.0) ，这意味着与最终版本的 [5.1kB](https://bundlephobia.com/result?p=final-form@4.19.1) 相比，它是非常轻量级的

Unform 是 React 的一个注重性能的表单库。它通过使用不受控制的表单组件来实现这一点。

**安装**

```
yarn add @unform/core @unform/web yup

```

**基本用法**
un forma 要求你创建自己的定制组件，在那里你使用`useField`钩子来访问字段数据和与字段交互的方法。在下面的代码中，我们只提取了`error`和`clearError`，这样我们就可以显示和清除验证错误消息:

```
import React, { useRef } from "react";
import { useField } from "@unform/core";
export default function Input({ name, ...inputProps }) {
  const inputRef = useRef(null);
  const { error, clearError } = useField(name);

  return (
    <>
      <input ref={inputRef} onFocus={clearError} {...inputProps} />
      {error && <span>{error}</span>}
    </>
  );
}

```

一旦有了自定义组件，现在就可以将它包含在表单中。un format 使用一个`Form`组件作为表单字段的容器。这是添加`ref`和`onSubmit`处理程序的地方。在下面的例子中，我们也使用了 Yup 进行验证。Unform 不包括自己的验证库，因此您可以使用任何想要的验证表单:

```
import React, { useRef } from "react";
import * as Yup from "yup";

import { Form } from "@unform/web";
import Input from "./components/Input";

function UnformComponent() {
  const formRef = useRef(null);

  async function handleSubmit(data) {
    try {
      formRef.current.setErrors({});
      const schema = Yup.object().shape({
        email: Yup.string().email().required()
      });
      await schema.validate(data, {
        abortEarly: false,
      });
      console.log(data);

    } catch (err) {
      const validationErrors = {};
      if (err instanceof Yup.ValidationError) {
        err.inner.forEach((error) => {
          validationErrors[error.path] = error.message;
        });
        formRef.current.setErrors(validationErrors);
      }
    }
  }

  return (
    <Form ref={formRef} onSubmit={handleSubmit}>
      <Input name="email" type="email" />
      <button type="submit">submit</button>
    </Form>
  );
}

export default UnformComponent;

```

> 注意:如果您计划进一步探索这个库，请确保只遵循使用版本 2 的教程。更好的是，只要坚持[官方文件](https://unform-rocketseat.vercel.app/)。这是因为有些教程使用版本 1。您可以很快确定它使用的是版本 1，因为它使用自定义表单组件。版本 2 包含了原始 HTML 元素的使用。查看[移民指南](https://unform-rocketseat.vercel.app/migration-guide)了解更多信息。

**总结**

*   支持 React 挂钩——这使得代码更容易阅读和维护
*   [通过使用`@unform/mobile`包支持 React Native](https://unform-rocketseat.vercel.app/guides/react-native)
*   使用 HTML 元素而不是自定义组件。集成是通过使用`useField` API 实现的
*   通过表单引用提供对表单数据、错误和方法的轻松访问
*   通过使用点符号作为输入名称，支持复杂的表单结构
*   需要创建自定义表单输入组件才能使用
*   统一内核重 [3.7kB](https://bundlephobia.com/result?p=@unform/core@2.1.0) 统一网页重 [606 字节](https://bundlephobia.com/result?p=@unform/web@2.1.0)

React Form 提供了在 React 中管理表单状态和验证表单的钩子。

**安装**

```
yarn add react-form

```

**基本用法**这为您提供了存储字段错误的属性。通常，您只想在用户已经“触及”特定字段时显示错误。这就是`isTouched`属性允许您检查的内容。

要验证字段，需要传递一个包含验证函数的对象:

```
import React from "react";
import { useField } from "react-form";

function Field({ name }) {
  const {
    value = "",
    meta: { error, isTouched },
    getInputProps,
  } = useField(name, {
    validate: (value) => {
      if (!value) {
        return `${name} is required.`;
      }
      return false;
    },
  });

  return (
    <>
      <input type="text" {...getInputProps()} value={value} />
      {isTouched && error ? <span>{error}</span> : null}
    </>
  );
}

export default Field;

```

一旦有了定制字段组件，现在就可以使用`useForm`钩子来验证表单。

注意`Form`组件是从`useForm`中提取的:

```
import React from "react";
import { useForm } from "react-form";
import Field from "./components/Field";

function ReactFormComponent() {
  const {
    Form,
    meta: { isSubmitting },
  } = useForm({
    onSubmit: async (values, instance) => {
      console.log("submit: ", values);
    },
  });

  return (
    <Form>
      <label>
        Favorite Food: <Field name={"favoriteFood"} />
      </label>

      <div>
        <button type="submit">Submit</button>
      </div>
    </Form>
  );
}

export default ReactFormComponent;

```

**总结**

*   通过使用`useForm`支持反作用钩
*   使用记忆进行快速重新渲染
*   缩小和压缩后，React Form 的重量为 4.5kB

一个简单的 React 组件，允许您使用 JSON schema 构建和验证 HTML 表单。默认情况下，它附带了简单的表单验证。诸如`required`和`minLength`之类的验证规则内置于其中。如果您想使用更高级的验证规则，您必须自己编写它们。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

目前支持 HTML 表单语义的 Bootstrap 和 Material UI。但是你也可以使用自己的 HTML 表单模板。

这个库在构建表单时非常灵活。唯一的缺点是完全使用它需要一点学习曲线，因为本质上您是在使用 API 来构建表单。

安装
这个库有两个内置的主题可以使用。默认情况下，有引导程序:

```
yarn add @rjsf/core

```

但是如果你想使用 Material UI，安装如下:

```
yarn add @rjsf/material-ui

```

**基本用法**
使用函数库有三个步骤:

1.  指定您的模式
2.  添加您的自定义格式和验证规则
3.  使用组件

当您指定模式时，可以添加数据类型、标签以及用户输入需要遵循的可选自定义格式。如果您的任何字段有自定义格式，您还需要向`Form`组件提供`customFormats`属性。默认情况下，只有当用户提交时，表单才会被验证。但是您也可以将`liveValidate`属性设置为`true`，这样它将在用户输入数据时验证字段。对于自定义验证，您可以提供`validate`道具。这是用于验证表单中特定字段的验证函数。最后，`onSubmit`函数只有在所有字段都通过验证后才会执行:

```
import React, { Component } from "react";
import { render } from "react-dom";
import Form from "@rjsf/core";

const schema = {
  type: "object",
  required: ["email", "age"],
  properties: {
    email: { type: "string", email: "Email Address", format: "email_address" },
    age: { type: "number" },
    done: { type: "boolean", title: "Done?", default: false },
  },
};

const customFormats = {
  email_address: /\[email protected]\S+\.\S+/,
};

function validate(formData, errors) {
  if (formData.age < 18) {
    errors.age.addError("You should be 18 years old or older.");
  }
  return errors;
}

const onSubmit = ({ formData }, e) => console.log("submit: ", formData);

function ReactJsonSchemaComponent() {
  return (
    <Form
      schema={schema}
      onSubmit={onSubmit}
      liveValidate={true}
      noHtml5Validate={true}
      showErrorList={false}
      customFormats={customFormats}
      validate={validate}
    />
  );
}

export default ReactJsonSchemaComponent;

```

**总结**

*   基于 JSON 模式自动生成表单并应用验证规则
*   支持引导和材料 UI HTML 语义
*   由于其广泛的 API，需要一点学习曲线
*   缩小和压缩后，React-json-schema-form 的重量为 1.3kB

React Hook Form 是一个轻量级的 React 表单验证库，主要使用钩子向 HTML 输入元素添加表单验证。如果您正在寻找一个高性能且易于使用的现代表单验证库，请选择这个库。

在这篇文章提到的所有库中。React 钩子形式需要编写最少的代码来使用它。这只是一个两步的过程:使用钩子，然后在你的字段中添加一个引用。

这个库的另一个伟大之处是它有非常好的文档和大量的例子。您可以找到几乎所有您能想到的用例的指南，比如与现有表单集成，或者如何将它与 UI 库(如 Material UI)一起使用。

**安装**

```
yarn add react-hook-form

```

**基本用法**
`useForm`钩子提供了实现表单验证所需的所有数据和方法。您需要将字段的名称作为强制参数传递给`register()`方法，这样字段就可以在表单中被唯一地标识。接下来，调用并析构输入组件上的`register()`方法。这就是将不受控制的组件注册到钩子中的原因。它要求你通过你需要的验证规则。或者，您也可以通过`message`属性传递您想要显示的错误消息。

在下面的例子中，我们使用了`required`和`pattern`规则来验证电子邮件字段:

```
import React from 'react';
import { useForm } from "react-hook-form";

function ReactHookFormComponent() {
  const { register, handleSubmit, formState: {errors} } = useForm();
  const onSubmit = data => console.log(data);
  // {"email":"[email protected]"}

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("email", { required: true, pattern: /\[email protected]\S+\.\S+/ })} />
      {errors.email?.type === 'required' && <span> This field is required</span>}
      {errors.email?.type === 'pattern' && <span> Invalid email</span>}

      <button type="submit">submit</button>
    </form>
  );
}

export default ReactHookFormComponent;

```

**总结**

*   使用不受控制的表单验证来获得最佳性能
*   通过使用验证规则，如`required`、`min`、`max`、`pattern`和[等](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation)，与现有的 HTML 表单验证标准保持一致
*   支持本机反应。只需将 HTML 表单元素替换为 React 本地等价元素(例如`TextInput`)
*   React 钩子形式不依赖于任何库
*   使您能够隔离组件并重新渲染以提高性能
*   有一个[表单生成器实用程序](https://react-hook-form.com/form-builder)，如果你想快速创建简单的表单
*   通过 Yup 库支持基于模式的验证
*   有很好的文档和示例用法
*   缩小和压缩后，React Hook Form 的重量为 9kB

Redux Form 允许你管理你的表单状态(表单输入值，表单错误等)。)通过 Redux 的方式。Redux Form 使用存储来跟踪表单状态，因此这个库的主要缺点是它的性能。即使您已经在应用程序中使用 Redux，也只在您需要现有 Redux 状态和表单数据之间的紧密耦合时才使用它。

**安装**

```
yarn add redux-form redux react-redux

```

**基本用法**
要使用 Redux 形式，需要从`redux-form`中提取`Field`分量和`reduxForm`高阶分量。在最低配置下，您只需要为表单传递一个惟一的名称(在本例中是`bio`)。但是在这里，我们也传入了一个验证函数。这在用户提交表单时执行。

要创建一个输入字段，可以使用`Field`组件。这里，我们传入用于渲染的`type`、`name`、`component`和`label`。`renderField()`函数负责返回输入组件。除了呈现标签和输入，它还呈现错误消息。这是通过 Redux 表单的`Field`组件实现的:

```
import React from 'react';
import { Field, reduxForm } from 'redux-form';

const validate = values => {
  const errors = {}
  if (!values.name) {
    errors.name = 'Required';
  }

  if (!values.email) {
    errors.email = 'Required';
  } else if (!/\[email protected]\S+\.\S+/i.test(values.email)) {
    errors.email = 'Invalid email address'
  }
  return errors;
}

const renderField = ({
  input,
  label,
  type,
  meta: { touched, error }
}) => (
  <div>
    <label>{label}</label>
    <div>
      <input {...input} placeholder={label} type={type} />
      {touched && (error && <span>{error}</span>)}
    </div>
  </div>
);

let BioForm = props => {
  const { handleSubmit } = props;
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <Field type="text" name="name" component={renderField} label="Name" />
      </div>
      <div>
        <Field type="email" name="email" component={renderField} label="Email" />
      </div>
      <button type="submit">Submit</button>
    </form>
  );
}

BioForm = reduxForm({
  form: 'bio',
  validate
})(BioForm);

export default BioForm;

```

要将所有这些放在一起，将`formReducer`包含到现有的 reducers 中，然后将一个`onSubmit`处理程序传递给表单组件:

```
import React, { Component } from 'react';
import { createStore, combineReducers } from 'redux';
import { reducer as formReducer } from 'redux-form';
import { Provider } from 'react-redux';
import BioForm from './components/BioForm';

const rootReducer = combineReducers({
  // your existing reducers..
  form: formReducer
});

const store = createStore(rootReducer);

class ReduxFormComponent extends Component {
  submit = values => {
    console.log('submit: ', values);
  }

  render() {
    return (
      <Provider store={store}>
        <BioForm onSubmit={this.submit} />
      </Provider>
    );
  }
}

export default ReduxFormComponent;

```

**总结**

*   Redux Form 通过 Redux 库管理表单状态
*   比这里提到的大多数库都要重。重量 [24.4kB](https://bundlephobia.com/result?p=redux-form@7.1.2) (缩小+gzip)
*   Redux 格式只能与 React 和 Redux 一起使用
*   Redux 表单只有在现有 Redux 状态和表单数据之间需要紧密耦合时才有用

Formsy 是 React 的一个表单输入生成器和验证器。它对表单验证规则使用简单的语法。它还包括不同表单状态的处理程序，比如`onSubmit`和`isValid`。如果您正在寻找构建和管理表单的一体化解决方案，Formsy 是一个不错的选择。这个库最大的缺点是不支持 React 钩子。

**安装**

```
yarn add formsy-react

```

**基本用法**
Formsy 类似于 Unform，它要求你创建自己的定制组件，用`withFormsy`高阶组件包装。这提供了操作输入组件和有条件地显示错误消息所需的所有属性和方法:

```
import React, { Component } from 'react';
import { withFormsy } from 'formsy-react';

class FormsyInput extends Component {
  constructor(props) {
    super(props);
    this.changeValue = this.changeValue.bind(this);
  }

  changeValue(event) {
    this.props.setValue(event.currentTarget.value);
  }

  render() {
    const errorMessage = this.props.errorMessage;

    return (
      <div>
        <input onChange={this.changeValue} type="text" value={this.props.value || ''} />
        <span>{!this.props.isPristine ? errorMessage : ''}</span>
      </div>
    );
  }
}

export default withFormsy(FormsyInput);

```

一旦创建了自定义组件，现在就可以在表单中使用它了。要创建一个表单，应该使用`Formsy`组件。这就需要`onValidSubmit`道具了。只有当所有表单字段都有有效输入时，才会执行此操作。在下面的例子中，我们使用了`isEmail`验证器，但是还有[许多其他的](https://github.com/formsy/formsy-react/blob/master/API.md#validators)可以使用:

```
import React, { Component } from 'react';
import Formsy from 'formsy-react';
import FormsyInput from './components/FormsyInput';

export default class App extends Component {

  submit(model) {
    console.log('valid form: ', model);
    // {email: "[email protected]"}
  }

  render() {
    return (
      <Formsy onValidSubmit={this.submit}>
        <FormsyInput name="email" validations="isEmail" validationError="This is not a valid email" required />
        <button type="submit">
          Submit
        </button>
      </Formsy>
    );
  }
}

```

**总结**

*   Formsy 是较早的 React 表单验证库之一
*   包括自己的验证规则，如`isEmail`和`isUrl`
*   需要创建自定义表单输入组件才能使用
*   没有对 React 钩子的官方支持，这使得代码看起来更加复杂
*   缩小和压缩后，Formsy 的重量为 9.9kB

简单的 React 验证器是一个受 Laravel 启发的 React 表单验证库。在本文讨论的所有库中，这个库拥有最多的[内置验证规则](https://github.com/dockwa/simple-react-validator#rules)。

**安装**

```
yarn add simple-react-validator

```

**基本用法**
GitHub repo 中的例子并没有真正使用钩子，所以你必须再深入一点，找出如何使用钩子。在下面的代码中，您可以看到它主要使用受控组件，因此您必须独立管理表单状态。这个库的另一个缺点是每次需要显示错误消息时都必须使用`forceUpdate`:

```
import React, { useState, useRef } from "react";
import SimpleReactValidator from "simple-react-validator";

function SimpleReactValidatorComponent() {
  const [email, setEmail] = useState("");
  const simpleValidator = useRef(new SimpleReactValidator());
  const [, forceUpdate] = useState();

  const form = React.createRef();

  const submitForm = () => {
    const formValid = simpleValidator.current.allValid();
    if (!formValid) {
      simpleValidator.current.showMessages(true);
      forceUpdate(1)
    } else {
      console.log('submit form.');
    }
  };

  return (
    <form ref={form}>
      <label>Email</label>
      <input
        name="email"
        type="text"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        onBlur={() => {
          simpleValidator.current.showMessageFor("email")
          forceUpdate(1);
        }}
      />

      {simpleValidator.current.message("email", email, "required|email")}
      <button type="button" onClick={submitForm}>submit</button>
    </form>
  );
}

export default SimpleReactValidatorComponent;

```

**总结**

*   许多基于 Laravel 的内置验证规则
*   将状态用于窗体状态管理
*   对错误消息提供本地化支持
*   没有官方的 React 钩子支持
*   当缩小和压缩时，简单的 React 验证程序重 [4kB](https://bundlephobia.com/result?p=simple-react-validator@1.4.6)

`rc-field-form`是一个高性能的表单组件库，具有强大的类型脚本支持。文档没有详细解释这个库的用法。你必须理解来自[例子](https://github.com/react-component/field-form/tree/master/docs/examples)的用例。

**安装**

```
yarn add rc-field-form

```

**基本** **u** *贤者
`<Form />`组件用于包装表单输入字段。当表单成功提交后，`onFinish` prop 用表单值触发回调函数。类似地，当表单中出现错误时，`onFinishFailed` prop 会调用一个带有错误值的回调函数。*

 *您可以用`<Field />`组件包装您的定制`<Input />`组件，并为输入字段传递`name`和验证规则。`Form.useForm()`钩子可以用来访问表单中的错误和值。

```
import Form, { Field } from "rc-field-form";
const Input = ({ value = "", ...props }) => <input value={value} {...props} />;

const Demo = () => {
  const [form] = Form.useForm();
  const onSubmit = (v) => {
    console.log(v);
  };

  const onError = (errors) => {
    console.log(errors);
  };

  const renderFieldErrors = (errors) =>
    errors.map((err) => <p key={err}>{err}</p>);

  return (
    <Form form={form} onFinish={onSubmit} onFinishFailed={onError}>
      {() => {
        const emailErrors = form.getFieldError("email");
        return (
          <>
            <Field name="email" rules={[{ required: true, type: "email" }]}>
              <Input placeholder="Email" />
            </Field>
            {renderFieldErrors(emailErrors)}
            <div>
              <button>Submit</button>
            </div>
          </>
        );
      }}
    </Form>
  );
};

```

**总结**

*   支持反应和反应原生
*   具有类型脚本支持
*   利用[异步验证器](https://github.com/yiminghe/async-validator/)库进行验证
*   内置[挂钩支架](https://github.com/yiminghe/async-validator/)
*   缩小和压缩后，rc-field-form 的重量高达 [16.6kB](https://bundlephobia.com/package/rc-field-form@1.24.0)

## 要避免的表单验证库

如果你正在开始一个新项目，或者只是在寻找一个现代的、积极维护的库，最好避免下面的库。这些库要么不再被主动维护，要么使用过时的 React API:

## 其他表单库

下面是其他表单库的列表。这些库的功能与我们已经介绍过的库基本相同，所以我将让您自己探索它们:

## 结论

在本文中，我们看了一些最流行和最有趣的 React 表单验证库。如您所见，它们中的大多数都有类似的直观 API 来管理表单和验证错误。根据您的需求，每种方法都有其优缺点。但是在大多数情况下，使用 Formik、React Final Form 或 React Hook Form 都不会出错。

本文只是对 React 可用表单解决方案的总结。如果需要更技术性的对比，一定要看下面: [React Hook Form vs. Formik:一个技术和性能的对比](https://blog.logrocket.com/react-hook-form-vs-formik-a-technical-and-performance-comparison/)。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)*