# 用 FormKit 开发强大的 Vue.js 表单

> 原文：<https://blog.logrocket.com/powerful-vue-js-form-development-formkit/>

在现代 web 开发中，构建优秀的表单一直被认为是一项艰巨的任务。外部的表单只是一组选择框、文本字段、单选按钮和复选框。然而，对用户来说，主题化、验证和直观化会很快变得令人沮丧。

处理复杂的表单对每个开发人员来说都是一件麻烦事，这也是前端开发中的一个主要难点。对控制多个状态并使它们相互交互同时保持它们易于填充的能力的需求催生了像 [Formik](https://blog.logrocket.com/building-forms-formik-react/) 、 [React Hook Form](https://blog.logrocket.com/react-hook-form-complete-guide/) 、Veevalidate、Vuelidate、FormKit 等库。

在本帖中，我们将介绍 FormKit，以及它如何帮助我们在 Vue.js 中创建具有验证、样式和本地化功能的强大表单。

### 内容

## 什么是 FormKit？

FormKit 是一个强大的框架，它帮助在 Vue 中开发任何类型的表单，同时保持开发者体验一流。FormKit 背后的团队与开发流行的 [VueFormulate](https://vueformulate.com/) 库的团队是同一个团队。如果您熟悉这个库，您会记得他们在 Vue 中构建表单的创新方法。

VueFormulate 为开发人员提供了一个单一的组件，该组件大量利用 props 和 slots 来创建表单元素。这种方法简化了表单的编写、处理和验证。

FormKit 是 VueFormulate 的增强版本，它为开发人员提供了同样的好处，并带有微妙的魔力。今天，FormKit 以一个成熟的框架出现，旨在帮助开发人员编写优秀的表单。

## 使用 FormKit 创建表单

要在您的项目上安装 FormKit，您必须遵循 [FormKit 的安装文档](https://formkit.com/essentials/installation)中的第一步。

FormKit 提供了两种创建表单的简单方法:使用`<FormKit/>`组件或者从 JSON 模式生成表单。

### 用`<FormKit/>`组件创建表单

使用 FormKit，您可以使用单个 FormKit 组件创建高度可控的文本字段、选择框和其他表单元素。该组件附带了特定的属性，可以对您希望添加到表单中的输入元素进行高度定制:

```
<template>
  <FormKit
    label="Short word"
    type="text"
    help="Type any short word"
    validation="required|matches:/^[a-zA-Z]+$/|length:5"
    value="Hello"
  />
</template>

```

这个组件能够创建其他输入类型，类似于 HTML 的`<input>`标签如何使用各种`type`属性(即`<input type="text">`与`<input type="checkbox">`)。

这种方法非常强大，因为它不仅为开发人员提供了更少的 API 来学习，而且以最直观的方式学习。FormKit 组件提供了构建表单所需的[显式属性，并且能够将您需要它们展示的每个属性(属性)传递给呈现的 HTML 输入。](https://formkit.com/essentials/inputs#props--attributes)

在大多数情况下，您需要您的输入表现得像一个表单(或多个表单)一样，`<FormKit/>`组件能够成为一个包装器，主动收集来自 FormKit 子组件的所有值。它使用每个输入的`name`作为结果数据对象中的属性名。这个包装器还使您能够获取或编辑表单值，就像您在 Vue 中处理附加到任何输入的 v-model 属性一样。

为此，如果您需要一个全局表单，只需将您的输入包装在一个`<FormKit type="form">`中，如果您想要将表单的一部分分组，则包装在`<FormKit type="group">`中。这极大地有助于表单元素之间的交互，重置表单状态或提交给 API，这在 vanilla Vue 中对开发人员来说可能会变得不方便，因为他们可能必须监听来自多个来源的多个变量。

如果单个输入无效，包装器还会使整个表单无效，使表单无法提交:

```
<FormKit
  type="form"
  submit-label="Login"
  :errors="['No server.', 'More errors here']"
>
  <FormKit type="email" label="Email" />
  <FormKit type="password" label="Password" />
</FormKit>

```

### 从模式生成表单

动态表单是表单处理中的一个高需求特性。开发人员通常需要创建遵循自定义结构的表单，这种结构尚未预先指定，或者其答案和验证可以动态创建。

FormKit 使用`<FormKitSchema>`组件及其`:schema`道具提供了这一强大功能:

```
<script setup>
const schema = [
  {
    $formkit: 'email',
    label: 'Email address',
    validation: 'required'
  }
]
</script>

<template>
  <FormKit type="form">
    <FormKitSchema :schema="schema" />
  </FormKit>
</template>

```

FormKit 的 JSON 模式极其强大。它的范围从上面的简单形式到最复杂的形式。模式支持顶级功能，如条件逻辑、布尔运算符、循环、插槽和数据范围，使您能够根据需要定制生成的表单:

```
<FormKitSchema
  :schema="[
    {
      $formkit: 'email',
      label: 'Email address',
    }
  ]"
/>

```

上面的代码创建了一个电子邮件输入。这可以通过验证、密码、交互和[更多的](https://formkit.com/advanced/schema)来扩展。

## 使用 FormKit 验证表单

FormKit 通过使用和组合为您的表单预先编写的超过 [20 条验证规则](https://formkit.com/essentials/validation)，帮助您轻松验证您的表单。这些规则有多种使用方式，因此开发人员可以选择最适合他们项目的方式:

```
<!-- validation as string -->
<FormKit
  type="text"
  label="Number"
  validation="required|number|between:0,10"
  validation-visibility="live"
  help="Enter a number between 0 and 10."
/>

<!-- validation as array -->
<FormKit
  type="text"
  label="Phone"
  placeholder="xxx-xxx-xxxx"
  :validation="[['required'], ['matches', /^\d{3}-\d{3}-\d{4}$/]]"
  validation-visibility="live"
  :validation-messages="{
    matches: 'Phone number must be formatted: xxx-xxx-xxxx',
  }"
/>

```

在 FormKit 中，规则是可扩展的、可定制的，并且不需要第三方库来处理验证。为表单创建自定义规则就像编写一个函数一样简单，该函数获取一个值作为参数，并根据其中的条件返回一个布尔值:

```
// ./validation-custom-rules/yes-rule.js
// ensures that the value is always yes

export default function yes(node) {
  return node.value === 'yes' || node.value === 'YES'
}

// main.js ---------
import { createApp } from 'vue'
import App from './App.vue'
import { plugin, defaultConfig } from '@formkit/vue'
import yes from './my-custom-rules/yes-rule'

// prettier-ignore
createApp(App).use(plugin, defaultConfig({
  rules: { yes },
})).mount('#app')

```

FormKit 中的验证已经被简化，以帮助开发人员编写好的表单，但它是为了在保持我们所享受的简单性的同时进行适当的扩展而构建的。如果您的表单变得越来越复杂，需要更多的自定义规则和字段之间更大的交互，FormKit 结合 Vue 疯狂的组件模块化可以帮助我们创建复杂的表单和状态，同时保持代码简单易懂。

## 使用 FormKit 设计表单样式

设计原生表单并不容易。复选框、文本区域、文本字段和单选按钮都是`<input>`字段，但样式不同。FormKit 提供了一个名为 [Genesis](https://formkit.com/essentials/styling#outer-attributes) 的基础主题，它是开源的，可以适应你的项目。

FormKit 还提供了简单而健壮的样式工具，我们将在下面的小节中讨论。

### 自定义类别

大多数前端开发人员都有一个设计或主题系统要遵循，FormKit 通过提供多种方式来添加按优先级排序的类，从而实现了这一点。

如您所知，用单个组件来表示整个输入字段可能很麻烦，但 FormKit 通过提供非常具体的属性、选项和函数来为正确的元素提供定制类，使这变得很容易。可以使用以下任何方法(从最高到最低特异性)修改所有[部分](https://formkit.com/essentials/inputs#sections)的类别:

*   `{section-key}-class`道具(最具体)
*   `classes`道具
*   `classes`配置选项
*   `rootClasses`配置功能(最不具体)

### 外部属性

这些是开发人员可以用来设计输入样式的属性，有助于向用户提供更好的关于如何填充输入的反馈。`data-type`、`data-invalid`、`data-multiple`、`data-complete`和`data-errors`在 CSS 中被定义为简单的 HTML 属性:

```
<style>
[data-invalid] .formkit-inner {
  border-color: red;
  box-shadow: 0 0 0 1px red;
}

[data-complete] .formkit-inner {
  border-color: red;
  box-shadow: 0 0 0 1px green;
}
[data-complete] .formkit-inner::after {
  content: '✅';
  display: block;
  padding: 0.5em;
}
</style>

```

### 模式样式

模式可以像表单组件一样进行样式化。它们支持属性和属性绑定，使得在表单上添加类和属性变得容易。

除了以上所述，FormKit 还接受追加和重置类，通过使您能够动态地添加和删除类，帮助您将主题化带到下一个级别。

如上面的代码片段所示，FormKit 中的类可以像在 Vue 中一样绑定，在样式上允许更多的灵活性。

### 使用顺风 CSS 的样式

如果您使用的是 Tailwind CSS，FormKit 不仅支持您需要的多种内联样式，而且它还提供了一个包(`@formkit/tailwindcss`)来帮助您使用模板编写更简洁的代码，这些模板提供了在配置文件中对表单进行主题化的能力。带顺风导轨的[造型可在 FormKit 文档中找到。](https://formkit.com/guides/create-a-tailwind-theme#our-first-tailwind-input)

## 国际化(i18n)

国际化是使软件适应不同语言和地区特性的过程。

在撰写本文时，FormKit 附带了 23 种不同的语言环境，可以使用、互换、覆盖，甚至与像 [vue-i18n](https://blog.logrocket.com/advanced-localization-techniques-vue-js/) 这样的知名库结合使用。

在 FormKit 上设置附加和活动语言的工作方式如下:

```
import { createApp } from 'vue'
import App from 'App.vue'
import { plugin, defaultConfig } from '@formkit/vue'
import { fr, en } from '@formkit/i18n'

const app = createApp(App)
app.use(
  plugin,
  defaultConfig({
    // Define additional locales
    locales: { fr, en },
    // Define the active locale
    locale: 'en',
  })
)
app.mount('#app')

```

这些语言适用于预先编写的 FormKit 规则和消息。如果您有自己的定制规则消息，您可以将它们与 vue-i18n 库结合起来，在您的 Vue SFCs 中提供适当的语言环境。

## 更多酷炫的 FormKit 功能

### 插件

插件可以用来扩展 FormKit 的功能。它们是将 FormKit 元素(节点)作为参数并对其进行调整的函数。每次创建节点和添加插件时，都会调用这些函数。插件使用示例可在[示例页面](https://formkit.com/essentials/examples#plugins)上查看。

### 内置去抖功能

去抖是一种编程实践，用于确保一些繁重的任务不会过于频繁地触发，以免降低代码的性能。

FormKit 按需提供去抖输入，使开发人员能够通过添加一个单独的 prop `:delay="<time in milliseconds>"`来创建高性能的表单，其中验证可以延迟执行。

### 内置错误处理(包括服务器端)

FormKit 能够优雅地处理您的验证错误，并以一种非常全面的方式将它们传递给用户。现在，可能需要在服务器中处理全部或部分验证，并将它们返回到前端。这些错误可以使用`errors`或`input-errors`道具，或`node.setErrors()`或`$formkit.setErrors()`添加到表单中。

### 服务器端渲染

FormKit [与 Nuxt](https://formkit.com/essentials/installation)T2 无缝集成。js 并提供它所提供的一切以及 Nuxt 的所有特性。

### 强大的模式

FormKit 本身就是一个构建表单的框架，因为它提供了一个非常灵活的表单处理系统。架构是 FormKit 节点对象的数组，每个对象对应一个 HTML 元素、组件或文本节点。该模式能够引用现有的 Vue 变量，并使用可编辑的属性和属性呈现任何标记或组件。

### 低级功能(FormKit 核心)

[FormKit 核心](https://formkit.com/advanced/core)包含了 FormKit 中几乎所有的底层函数。这通过为开发人员提供表单内组件的良好定义的树结构，实现了较低级别的输入处理。FormKit 核心函数帮助编辑、遍历、监听和跟踪表单的状态。这被认为是更高级的，但是当你需要一个模块化的代码结构时，它会很有用。

### 全面的文档和表单工具包游乐场

FormKit 有很好的文档，大多数 API 都有很好的文档和结构。这些文档提供了完整的参考搜索功能，可以帮助您找到几乎所有需要的内容。

[form kit playground](https://formkit.com/playground)可以让你随心所欲地玩 API。您可以调整主题、编辑全局配置文件，并在需要时利用 FormKit 创建多个组件。

## 结论

像 FormKit 这样的库是游戏规则的改变者；它们为解决现代 web 开发中的主要表单问题提供了一个简单的解决方案，并让开发人员能够愉快地完成否则将是一项乏味的任务。

FormKit 既加速了你的开发过程，又向用户呈现了极其强大的表单，这种方式令人兴奋不已。

如果你需要更深入的比较，FormKit [在与 Vue 生态系统中的其他表单库](https://formkit.com/essentials/what-is-formkit#why-not)的比较中做得很好。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。