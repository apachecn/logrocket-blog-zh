# 在 Vue 3 中使用 v-model 构建复杂表单

> 原文：<https://blog.logrocket.com/using-v-model-vue-3-build-complex-forms/>

在本文中，我们将介绍 Vue 3 中对`v-model`指令的修改。然后，我们将浏览一个教程，演示如何使用多个`v-model`绑定来简化在 Vue 中构建复杂表单的过程。

*向前跳转:*

## 什么是`v-model`指令？

Vue `v-model`指令支持表单输入元素上的双向数据绑定，比如任何 Vue 组件上的`input` 元素、`textarea`元素和`select`元素。

它以两种方式处理数据更新:

*   当输入的值改变时，`v-model`将该值反映到组件内部的状态上
*   当组件的状态改变时，`v-model`将改变反映到表单输入元素上

默认情况下，`v-model`指令使用不同的属性，并为不同的输入元素发出不同的事件:

*   `text`和`textarea`元素的`value`属性和`input`事件
*   复选框和单选按钮的`checked`属性和`change` 事件
*   值作为选择字段的属性和`change`事件

自定义组件中的一个简单输入元素如下所示:

```
<input
    type="text"
    :value="modelValue"
    @input="$emit(update:modelValue, $event.target.value)"
>
```

它的道具是这样定义的:

```
props: {
        modelValue: {
            type: String,
            default: '',
            required: true
        }
}
```

在父组件中，自定义组件的用法如下:

```
<CustomComponent v-model:modelValue="name" />

// or the shorthand

<CustomComponent v-model="name" />
```

在定制组件中，`v-model`指令假设一个内部属性已经用名称`modelValue`定义，并发出一个名为`update:modelValue`的事件。

您不局限于默认的命名约定；您可以使用自己选择的不同名称。为我们的`v-model bindings`起一个描述性的名字使我们能够在试图读取和定义什么属性被附加到父组件时使用更少的代码。

只是在选择命名属性时要确保一致性。下面是用于`modelValue`属性的自定义名称`fullName`的示例:

```
<input
    type="text"
    :value="fullName"
    @input="$emit(update:fullName, $event.target.value)"
>
```

```
props: {
        fullName: {
            type: String,
            default: '',
            required: true
        }
}
```

```
<CustomComponent v-model:fullName="fullName" />

// or the shorthand

<CustomComponent v-model="fullName" />
```

## `v-model`如何处理数据绑定？

v-model 指令有三个可用于数据绑定的修饰符:`.lazy`、。`number`和`.trim`。让我们仔细看看。

### `.lazy`

默认情况下，`v-model`在每个输入事件发出后与 Vue 实例的状态同步。但是使用`.lazy`修饰符，`v-model`允许同步在每次改变事件之后发生。

这里有一个显示使用`.lazy`修饰符的例子:

```
<input v-model.lazy="message" />
```

### `.number`

另一方面,`.number`修饰符允许我们自动将用户输入转换成数字。HTML 输入的默认值总是一个字符串，所以这个修饰符非常有用。如果该值不能被解析为数字，则返回原始值。

这里有一个显示使用`.number`修饰符的例子:

```
<input v-model.number="numPapayas" type="number" />
```

### `.trim`

`.trim`修饰符，顾名思义，自动从用户输入中删除空白。

这里有一个显示使用`.trim`修饰符的例子:

```
<input v-model.trim="message" />
```

## Vue.js 3 中的`v-model`与 Vue.js 2 有何不同？

如果你熟悉在 Vue 2 中使用 v-model 指令的[，你就会理解创建表单有多复杂。](https://blog.logrocket.com/an-imperative-guide-to-forms-in-vue-js-2/)

在 Vue 2 中，我们只允许每个组件使用一个`v-model`。为了支持复杂组件中的双向数据绑定，必须在`v-model`上使用成熟的有效载荷。

该组件将处理所有输入元素的状态，并且将生成一个有效负载对象来表示该组件的状态。然后，带有附加有效负载的事件将被发送到父组件。

这种方法在创建 Vue UI 库时产生了问题，因为它并不总是清楚有效载荷中包含了什么。开发人员别无选择，只能遍历 payload 对象来确定包含了哪些属性。

幸运的是，在构建支持双向数据绑定的定制组件时，Vue 3 为开发人员提供了更多的灵活性和能力。在 Vue 3 中，我们可以根据需要使用任意多的`v-model`指令。这非常方便，我们将在本文后面演示。

## 多个`v-model`指令绑定教程

让我们看看如何使用多个`v-model`指令绑定来简化复杂的 Vue 表单。

对于我们的示例，我们将使用一个结帐表单，其中列出了用户的名、姓和电子邮件地址，后面是一些与账单和交付相关的字段。

![Checkout Form](img/0385aaadbc381c7c3ead19c103056afc.png)

### 创建可重用组件

帐单和递送部分包括街道名称、街道号码、城市和邮政编码。

但是，由于用户的帐单和递送地址通常是相同的，所以让我们为表单创建一个可重用的地址组件。

首先，我们将使用以下命令设置 Vue 应用程序:

```
vue create <project-name>

```

然后，我们将在我们的`src`文件夹的`components`文件夹中创建一个可重用的组件`AddressFieldGroup.vue`。

这个可重用的组件将被导入到我们的`App.vue`文件中。使用`v-mode` l，这个可重用的组件将被绑定到`App.vue`文件中的一个定制组件。

让我们仔细看看可重用组件，`AddressFieldGroup.vue:`

```
AddressFieldGroup.vue
```

```
<template>
  <section class="address">
    <h2>{{ label }}</h2>
    <div class="address__field">
      <label for="streetName">Street name</label>
      <input
        type="text"
        id="streetName"
        :value="streetName"
        @input="$emit('update:streetName', $event.target.value)"
        required
      />
    </div>
    <div class="address__field">
      <label for="streetNumber">Street number</label>
      <input
        type="text"
        id="streetNumber"
        :value="streetNumber"
        @input="$emit('update:streetNumber', $event.target.value)"
        required
      />
    </div>
    <div class="address__field">
      <label for="city">City</label>
      <input
        type="text"
        id="city"
        :value="city"
        @input="$emit('update:city', $event.target.value)"
        required
      />
    </div>
    <div class="address__field">
      <label for="postcode">Postcode</label>
      <input
        type="text"
        id="postcode"
        :value="postcode"
        @input="$emit('update:postcode', $event.target.value)"
        required
      />
    </div>
  </section>
</template>

<script>
export default {
  name: "AddressFieldGroup",
  props: {
    label: {
      type: String,
      default: "",
    },
    streetName: {
      type: String,
      default: "",
    },
    streetNumber: {
      type: String,
      default: "",
    },
    city: {
      type: String,
      default: "",
    },
    postcode: {
      type: String,
      default: "",
    },
  },
};
</script>
```

在上面的代码中，类名为`address`的 section 元素被重用(我们将在本文稍后看到)以在父组件中创建`Billing Address`和`Delivery Address`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`label`属性为每个地址部分提供了相关的名称和四个输入字段:`streetName`、`streetNumber`、`city`和`postcode`。每个输入字段的属性以及`label`在`script`标签中定义。

`label`属性将从自定义组件`AddressFieldGroup`传递到其在`App.vue`文件中的父组件，以便为每个地址组提供唯一的标签或名称(例如，`Billing Address`或`Delivery Address`)。

### 创建`CheckoutForm`

现在，我们将在我们的`App.vue`文件中创建结帐表单，并将`AddressFieldGroup.vue` 导入到`App.vue`文件中:

```
App.vue
```

```
<template>
  <div class="app">
    <form @submit.prevent="handleSubmit" class="checkout-form">
      <h1>Checkout Form</h1>
      <div class="address__field">
        <label for="firstName">First name</label>
        <input type="text" id="firstName" v-model="form.firstName" required />
      </div>
      <div class="address__field">
        <label for="lastName">Last name</label>
        <input type="text" id="lastName" v-model="form.lastName" required />
      </div>
      <div class="address__field">
        <label for="email">Email</label>
        <input type="email" id="email" v-model="form.email" required />
      </div>
      <AddressFieldGroup
        label="Billing Address"
        v-model:streetName="form.billingAddress.streetName"
        v-model:streetNumber="form.billingAddress.streetNumber"
        v-model:city="form.billingAddress.city"
        v-model:postcode="form.billingAddress.postcode"
      />
      <AddressFieldGroup
        label="Delivery Address"
        v-model:streetName="form.deliveryAddress.streetName"
        v-model:streetNumber="form.deliveryAddress.streetNumber"
        v-model:city="form.deliveryAddress.city"
        v-model:postcode="form.deliveryAddress.postcode"
      />
      <div class="address__field">
        <button type="submit">Submit</button>
      </div>
    </form>
  </div>
</template>

<script>
import AddressFieldGroup from "./components/AddressFieldGroup";
import { reactive } from "vue";

export default {
  name: "CheckoutForm",
  components: {
    AddressFieldGroup: AddressFieldGroup,
  },
  methods: {
    handleSubmit() {
      alert("form submitted");
    },
  },
  setup() {
    const form = reactive({
      firstName: "",
      lastName: "",
      email: "",
      billingAddress: {
        streetName: "",
        streetNumber: "",
        city: "",
        postcode: "",
      },
      deliveryAddress: {
        streetName: "",
        streetNumber: "",
        city: "",
        postcode: "",
      },
    });

    return {
      form,
    };
  },
};
</script>

<style lang="scss">
.app {
  font-family: Arial, Helvetica, sans-serif;
  color: #434141;
  text-align: center;
}
.checkout-form {
  margin: 5px auto;
  padding: 10px;
  max-width: 500px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.address__field {
  padding-bottom: 10px;
  width: 250px;
  text-align: left;
}
label {
  display: block;
  font-weight: bold;
}

input {
  padding: 10px;
  width: 230px;
  border: 1px solid #fff;
  border-radius: 5px;
  outline: 0;
  background: #f8edcf;
}

button {
  margin-top: 30px;
  padding: 10px;
  width: 250px;
  color: #f8edcf;
  border: 1px solid #fff;
  border-radius: 5px;
  outline: 0;
  background: #434141;
}
</style>
```

在上面的代码中，我们创建了一个包含三个输入字段的`CheckoutForm`:`firstName`、`lastName`和`email`。我们还在表单中嵌入了两次可重用的`AddressFieldGroup`组件，并用它来表示用户的账单地址和送货地址。

我们使用了`v-model:{property-name}`格式来绑定两个定制`AddressFieldGroup`组件的每个属性。

除了`v-model`速记语法之外，这段代码也更短、更简单、更易读。这使我们能够快速解读和解码在父组件和定制组件(在本例中，是可重用的`AddressFieldGroup`组件)之间传递的属性。

我们还在`CheckoutForm`中定义了所有属性，包括两个地址的属性。我们将属性保存在一个名为`form**,**` 的反应对象中，将其值返回给组件，并使用它来设置`CheckoutForm`上的绑定。

## 结论

在本文中，我们研究了`v-model`指令，确定了哪些 Vue 修饰符可以与它一起使用，并演示了如何在 Vue 组件上使用多个`v-model`绑定来简化复杂 Vue 表单的创建。

`v-model`为我们提供了在单个组件实例上添加多个 v-model 指令的灵活性，`modelValue`也可以根据我们的喜好进行重命名。

要查看和使用本文中使用的示例，[请查看 CodeSandbox](https://codesandbox.io/s/v-model-multi-binding-bzp5gz) 上的源代码。祝你快乐！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。