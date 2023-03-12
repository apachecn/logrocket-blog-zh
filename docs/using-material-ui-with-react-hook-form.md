# 使用带有 React Hook 表单的材质 UI

> 原文：<https://blog.logrocket.com/using-material-ui-with-react-hook-form/>

React Hook Form 是 React 生态系统中最流行的处理表单输入的库之一。如果您正在使用一个组件库，比如 Material UI，让它正常工作可能会很棘手。

在本指南中，我们将演示如何使用 React 钩子表单的材质 UI。如果你想集成一些其他的 [React UI 库](https://blog.logrocket.com/top-7-ui-libraries-and-kits-for-react/)，比如 Ant Design 或者 Semantic UI，这个教程也是有帮助的。

为了跟上进度，你应该已经对[材质 UI](https://blog.logrocket.com/using-material-ui-in-react-native/) 和[反应钩子形式](https://blog.logrocket.com/the-complete-guide-to-react-hook-form/)有所了解。我们不会深入探究如何使用这些库。相反，我们将关注它们之间的集成。

为了展示如何在 React Hook 表单中使用 Material UI，我们将使用 Material UI 提供的最常用的输入组件构建一个完整的表单，包括:

*   文本输入
*   无线电信号输入
*   下拉式
*   日期
*   检验盒
*   滑块

该表单还将具有重置功能。它看起来会像这样:

![Material UI and React Hook Form Example](img/bf64c7ae8a4a287ce4879713eb80749f.png)

如果你更喜欢视觉学习，请查看附带的视频教程:

 [https://www.youtube.com/embed/aaj6YCil1p4?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/aaj6YCil1p4?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 文本输入组件

让我们从一个简单的表单组件开始。该组件中只有一个文本输入。

要在没有任何库的情况下通过传统方法构建该表单，我们需要单独处理输入的更改。我们还必须自己负责重置功能和验证。

它可能看起来像这样:

```
import TextField from "@material-ui/core/TextField";
import React, { useState } from "react";
import { Button, Paper } from "@material-ui/core";

export const FormWithoutHookForm = () => {
  const [textValue, setTextValue] = useState<string>("");

  const onTextChange = (e: any) => setTextValue(e.target.value);
  const handleSubmit = () => console.log(textValue);
  const handleReset = () => setTextValue("");

  return (
    <Paper>
      <h2>Form Demo</h2>

      <TextField
        onChange={onTextChange}
        value={textValue}
        label={"Text Value"} //optional
      />

      <Button onClick={handleSubmit}>Submit</Button>
      <Button onClick={handleReset}>Reset</Button>
    </Paper>
  );
};

```

输出将如下所示:

![Material UI and React Hook Form Text Input Component Example](img/069a98912e1eabb78ec290101988f3b3.png)

这里我们使用 React 本身提供的`useState`钩子来存储值:

```
const [textValue, setTextValue] = useState<string>("");

```

此外，我们在`onTextChange`函数中设置来自输入的值:

```
const onTextChange = (e: any) => setTextValue(e.target.value);

```

如果我们查看由`material-ui`提供的`TextInput`组件，我们可以看到有两个重要的道具传递给它:`value`和`onChange`。`value`负责输入的实际值，而`onChange`决定输入改变时会发生什么。无论我们如何使用这种形式，我们都需要注意这两点。

## 设置 React 挂钩表单

React Hook Form 从著名的`useForm`钩子中导出了一些实用程序，然后可以在输入组件中使用它们。

首先，导入`useForm`钩子:

```
import { useForm } from "react-hook-form";

```

然后，使用组件内的挂钩:

```
const { register } = useForm();

```

典型的输入可能如下所示:

```
<input type="text" ref={register} name="firstName" />

```

仔细看这里:我们将`register`作为一个值传递给实际输入组件的`ref`。所有的魔法都发生在幕后。

[reactstrap](https://reactstrap.github.io/) 提供了一个类似的名为`innerRef`的道具，可以通过我们的`register`与`react-hook-form`无缝集成。

不幸的是，当我们使用材质 UI 时，情况并非如此；该库还没有提供任何类似的属性来将`register`作为一个值传递给`ref`属性。

## `Controller`组件

React Hook Form 包含一个名为`Controller`的包装器组件，用于处理不能直接访问`ref`的组件库。

根据 [React 文档](https://reactjs.org/docs/render-props.html)，这是一个 render prop——一个返回 React 元素并提供将事件和值附加到组件中的功能。

这个特殊`Controller`组件的框架如下:

```
<Controller
  control={control}
  name="test"
  render={({
    field: { onChange, onBlur, value, name, ref },
    fieldState: { invalid, isTouched, isDirty, error },
    formState,
  }) => ( WHATEVER_INPUT_WE_WANT )}
/>

```

让我们来分析一下这里发生了什么:

*   `control`是一个道具，我们从`useForm`钩子得到它，并将其传递给输入
*   是 React Hook Form 如何在内部跟踪输入的值
*   `render`是最重要的道具；我们在这里传递一个渲染函数

## `render`道具

`Controller`的`render`属性是理解的最重要道具。该功能有三个按键:`field`、`fieldState`和`formState`。我们现在将重点放在`field`上。

对象导出两个东西(除了别的以外):`value`和`onChange`。我们已经看到，我们需要这两个东西来控制几乎任何输入。

## 重构我们的表单

所以让我们看看`Controller`组件是否真的解决了我们的问题。我们将使用`Controller`组件并在渲染函数中传递`TextInput`。

让我们首先从`useForm`钩子中提取出我们需要的东西:

```
const { handleSubmit, reset, control } = useForm();

```

然后，像这样使用表单中的`Controller`组件:

```
import TextField from "@material-ui/core/TextField";
import React, { useState } from "react";
import { Button, Paper } from "@material-ui/core";
import { Controller, useForm } from "react-hook-form";

export const FormWithHookForm = () => {
  const { handleSubmit, reset, control } = useForm();
  const onSubmit = (data: any) => console.log(data);

  return (
    <form>
      <Controller
        name={"textValue"}
        control={control}
        render={({ field: { onChange, value } }) => (
          <TextField onChange={onChange} value={value} label={"Text Value"} />
        )}
      />
      <Button onClick={handleSubmit(onSubmit)}>Submit</Button>
      <Button onClick={() => reset()} variant={"outlined"}>Reset</Button>
    </form>
  );
};

```

这个表单的工作方式和前面的一样。神奇的事情发生了，这要感谢由`Controller`提供的渲染函数的`field`属性。

所以我们现在知道如何使用 React Hook 表单的`Controller`组件让表单在没有任何`ref`的情况下工作。现在让我们将输入组件提取到一个单独的组件中，这样我们就可以在任何地方使用它。

这个通用组件需要来自其父组件的三个支持:

*   `name`，用于输入的键
*   用于访问 React 钩子表单的功能
*   `label`，输入的标签(可选)

```
import TextField from "@material-ui/core/TextField";
import { Controller } from "react-hook-form";
import React from "react";

export const FormInputText = ({ name, control, label }) => {
  return (
     (

      )}
    />
  );
};

```

我们将在表单中使用这个组件，如下所示:

```
import { FormInputText } from "./FormInputTextGood";

export const FormWithHookForm = () => {
  // rest are same as before
  return (
    <form>
      <FormInputText
        name={"textInput"}
        control={control}
        label={"Text Input"}
      />
    </form>
  );
};

```

现在组件更容易理解和重用。让我们也关注一些其他的输入。

## `Radio`输入组件

第二常见的输入元件是`Radio`。这里有一个重要的概念需要记住。

如果你已经使用过材质 UI 中的`Radio`，你就已经知道你需要`RadioGroup`组件作为父组件，以及里面的一堆选项，比如单个的`Radio`按钮，作为子组件:

```
import React from "react";
import {
  FormControl,
  FormControlLabel,
  FormLabel,
  Radio,
  RadioGroup,
} from "@material-ui/core";
import { Controller, useFormContext } from "react-hook-form";
import { FormInputProps } from "./FormInputProps";

const options = [
  {
    label: "Radio Option 1",
    value: "1",
  },
  {
    label: "Radio Option 2",
    value: "2",
  },
];

export const FormInputRadio: React.FC<FormInputProps> = ({ name,control,label }) => {
  const generateRadioOptions = () => {
    return options.map((singleOption) => (
      <FormControlLabel
        value={singleOption.value}
        label={singleOption.label}
        control={<Radio />}
      />
    ));
  };  

  return <Controller
      name={name}
      control={control}
      render={({field: { onChange, value }}) => (
        <RadioGroup value={value} onChange={onChange}>
          {generateRadioOptions()}
        </RadioGroup>
      )}
    />
};

```

这里的主要概念是相同的。我们只是使用来自渲染函数`field`对象的`onChange`和`value`，并将其传递给`RadioGroup`。

注意，我们在这里没有使用`label`。如果你想使用它，你需要添加材质 UI 的`FormControl`和`FormLabel`组件。

另请注意，使用了一个特殊功能`generateRadioOptions`来生成单独的无线电输入。我们在组件中添加了常量`options`。你可以把它们作为道具或者任何你认为合适的方式。

## 掉下

几乎任何形式都需要某种下拉菜单。`Dropdown`组件的代码如下:

```
import React from "react";
import { FormControl, InputLabel, MenuItem, Select } from "@material-ui/core";
import { useFormContext, Controller } from "react-hook-form";
import { FormInputProps } from "./FormInputProps";

const options = [
  {
    label: "Dropdown Option 1",
    value: "1",
  },
  {
    label: "Dropdown Option 2",
    value: "2",
  },
];

export const FormInputDropdown= ({name,control, label}) => {

  const generateSelectOptions = () => {
    return options.map((option) => {
      return (
        <MenuItem key={option.value} value={option.value}>
          {option.label}
        </MenuItem>
      );
    });
  };

  return <Controller
      control={control}
      name={name}
      render={({ field: { onChange, value } }) => (
        <Select onChange={onChange} value={value}>
          {generateSelectOptions()}
        </Select>
      )}
    />
};

```

## 日期输入

这是一个普通而特殊的组件。在 Material UI 中，我们没有任何开箱即用的组件。我们需要利用一些助手库。

首先，安装这些依赖项:

```
yarn add @date-io/[email protected] @material-ui/[email protected] [email protected]

```

小心版本。否则，您可能会遇到一些奇怪的问题。

我们还需要用一个特殊的包装器`MuiPickersUtilsProvider`包装我们的数据输入组件。这将为我们注入日期选择器功能。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

记住，这不是 React 钩子形式的要求；这是物料界面所要求的。因此，如果您使用任何其他设计库，如 Ant Design 或语义 UI，您不需要担心这一点。

```
import React from "react";
import DateFnsUtils from "@date-io/date-fns";
import { KeyboardDatePicker, MuiPickersUtilsProvider} from "@material-ui/pickers";
import { Controller } from "react-hook-form";
const DATE_FORMAT = "dd-MMM-yy";

export const FormInputDate = ({ name, control, label }) => {
  return (
    <MuiPickersUtilsProvider utils={DateFnsUtils}>
      <Controller
        name={name}
        control={control}
        render={({ field : {onChange , value } }) => (
          <KeyboardDatePicker
            onChange={onChange}
            value={value}
            label={label}
          />
        )}
      />
    </MuiPickersUtilsProvider>
  );
};

```

## 复选框组

如果你想使用一个简单的像开关一样工作的复选框，那么它很容易使用。你只需要像前面的组件一样使用它，所以我不会再次展示相同的东西。

但是，当您想要创建一组复选框并将选定的值设置为选定项的数组时，情况就变得复杂了。这里的主要挑战是 Material UI 不提供多选复选框组件。

没有明确的例子说明如何使用 React 钩子形式的这个组件。要启用此功能，我们必须维护选定项目的本地状态:

```
import React, { useEffect, useState } from "react";
import {Checkbox,FormControl,FormControlLabel,FormLabel} from "@material-ui/core";
import { Controller } from "react-hook-form";

const options = [
  {
    label: "Checkbox Option 1",
    value: "1",
  },
  {
    label: "Checkbox Option 2",
    value: "2",
  },
];

export const FormInputMultiCheckbox= ({name,control,setValue,label}) => {
  const [selectedItems, setSelectedItems] = useState<any>([]);

  // we are handling the selection manually here
  const handleSelect = (value: any) => {
    const isPresent = selectedItems.indexOf(value);
    if (isPresent !== -1) {
      const remaining = selectedItems.filter((item: any) => item !== value);
      setSelectedItems(remaining);
    } else {
      setSelectedItems((prevItems: any) => [...prevItems, value]);
    }
  };

  // we are setting form value manually here
  useEffect(() => {
    setValue(name, selectedItems); 
  }, [selectedItems]);

  return (
    <FormControl size={"small"} variant={"outlined"}>
      <FormLabel component="legend">{label}</FormLabel>

      <div>
        {options.map((option: any) => {
          return (
            <FormControlLabel
              control={
                <Controller
                  name={name}
                  render={({}) => {
                    return (
                      <Checkbox
                        checked={selectedItems.includes(option.value)}
                        onChange={() => handleSelect(option.value)}
                      />
                    );
                  }}
                  control={control}
                />
              }
              label={option.label}
              key={option.value}
            />
          );
        })}
      </div>
    </FormControl>
  );
};

```

所以在这个组件中，我们手动控制`value`和`onChange`。这就是为什么在`render`函数中我们不再使用`field`道具的原因。为了设定价值，我们在这里采用了另一个名为`setValue`的新道具。该功能是`react-hook-form`的特殊功能，用于手动设定值。

您可能会问，如果我们手动处理输入，为什么还要这样做？答案是当你使用`react-hook-form`时，你希望所有的输入都在一个地方。所以我们在这里给这个`MultiSelectCheckbox`组件一个特殊的处理，这样它可以很容易地与其他组件一起工作。

## 滑块

我们的最后一个组件是一个`Slider`组件，这是一个相当常见的组件。

代码很容易理解，但有一个问题:Material UI 提供的`onChange`函数不能与 React Hook 表单的`onChange`一起使用，因为签名不同。

因此，当我们试图在 React Hook 表单的`Controller`组件中使用`Slider`组件时，它会抛出错误。同样，我们必须维护一个本地状态来控制`onChange`并手动设置值。

该组件的完整代码如下:

```
import React, { useEffect } from "react";
import { Slider } from "@material-ui/core";
import { Controller } from "react-hook-form";

export const FormInputSlider = ({name,control,setValue,label}) => {
  const [sliderValue, setSliderValue] = React.useState(0);

  useEffect(() => {
    if (sliderValue) setValue(name, sliderValue);
  }, [sliderValue]);

  const handleChange = (event: any, newValue: number | number[]) => {
    setSliderValue(newValue as number);
  };

  return <Controller
      name={name}
      control={control}
      render={({ field, fieldState, formState }) => (
        <Slider
          value={sliderValue}
          onChange={handleChange}
        />
      )}
    />
};

```

## 把所有的放在一起

现在让我们在表单中使用所有这些组件。我们的表单将利用我们刚刚制作的所有可重用组件:

```
import { Button, Paper, Typography } from "@material-ui/core";
import { FormProvider, useForm } from "react-hook-form";
import { FormInputText } from "./form-components/FormInputText";
import { FormInputMultiCheckbox } from "./form-components/FormInputMultiCheckbox";
import { FormInputDropdown } from "./form-components/FormInputDropdown";
import { FormInputDate } from "./form-components/FormInputDate";
import { FormInputSlider } from "./form-components/FormInputSlider";
import { FormInputRadio } from "./form-components/FormInputRadio";

const defaultValues = {
  textValue: "",
  radioValue: "",
  checkboxValue: [],
  dateValue: new Date(),
  dropdownValue: "",
  sliderValue: 0,
};

export const FormDemo = () => {
  const methods = useForm({ defaultValues: defaultValues });
  const { handleSubmit, reset, control, setValue } = methods;
  const onSubmit = (data: IFormInput) => console.log(data);

  return (
    <Paper>
      <Typography variant="h6"> Form Demo </Typography>

      <FormInputText name="textValue" control={control} label="Text Input" />
      <FormInputRadio name={"radioValue"} control={control} label={"Radio Input"} />
      <FormInputDropdownname="dropdownValue"control={control}label="Dropdown Input"/>
      <FormInputDate name="dateValue" control={control} label="Date Input" />

      <FormInputMultiCheckbox
        control={control}
        setValue={setValue}
        name={"checkboxValue"}
        label={"Checkbox Input"}
      />

      <FormInputSlider
        name={"sliderValue"}
        control={control}
        setValue={setValue}
        label={"Slider Input"}
      />

      <Button onClick={handleSubmit(onSubmit)} variant={"contained"}>
        Submit
      </Button>
      <Button onClick={() => reset()} variant={"outlined"}>
        Reset
      </Button>
    </Paper>
  );
};

```

## 结论

现在我们的表单更干净，性能更好。从这里，我们可以非常容易地添加我们的[表单验证逻辑和错误处理](https://blog.logrocket.com/the-ultimate-roundup-of-react-form-validation-solutions/)。

要自己试验这个例子，可以查看一下 [GitHub](https://github.com/Mohammad-Faisal/react-hook-form-material-ui) 上的完整代码。

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