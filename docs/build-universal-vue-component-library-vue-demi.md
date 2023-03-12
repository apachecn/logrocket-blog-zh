# 用 Vue Demi 构建一个通用的 Vue 组件库

> 原文：<https://blog.logrocket.com/build-universal-vue-component-library-vue-demi/>

据创建者 Anthony Fu 介绍， [Vue Demi](https://github.com/vueuse/vue-demi?ref=madewithvuejs.com) 是一个正在开发的实用程序，它允许用户为 Vue 2 和 Vue 3 编写通用的 Vue 库，而不用担心用户安装的版本。

以前，为了创建支持两个目标版本的 Vue 库，我们会使用不同的分支来分离对每个版本的支持。对于现有的库来说，这是一个很好的方法，因为它们的代码库通常更稳定。

缺点是需要维护两个代码库，工作量翻倍。对于想要支持 Vue 的两个目标版本的新的 Vue 库，我不推荐这种方法。实现两次特性请求和错误修复是不理想的。

这就是 Vue Demi 的用武之地。Vue Demi 通过为两个目标版本提供通用支持来解决这个问题，这意味着您只需构建一次，就可以获得两个目标版本的所有好处，从而获得两个世界的最佳效果。

在本文中，我们将看看 Vue Demi 是做什么的，它是如何工作的，以及如何开始构建一个通用的 Vue 组件库。

## Demi 视图中的额外 API

除了 Vue 已经提供的 API 之外，Vue Demi 还引入了一些额外的 API 来帮助区分用户的环境和执行特定于版本的逻辑。让我们仔细看看他们！

> 注意，Vue Demi 还包括已经存在于 Vue 中的标准 API，比如`ref`、`onMounted`和`onUnmounted`等等。

### `isVue2`和`isVue3`

在 Vue Demi 中，`isvue2`和`isvue3`API 允许用户在创建 Vue 库时应用特定于版本的逻辑。

例如:

```
import { isVue2, isVue3 } from 'vue-demi' 
if (isVue2) { 
  // Vue 2 only 
} else { 
  // Vue 3 only 
}

```

### `vue2`

Vue Demi 提供了`vue2` API，允许用户像这样访问 Vue 2 的全局 API:

```
import { Vue2 } from 'vue-demi' 
// in Vue 3 `Vue2` will return undefined 
if (Vue2) { 
  Vue2.config.devtools = true 
}

```

### `install()`

在 Vue 2 中，复合 API 作为插件提供，需要在使用它之前安装在 Vue 实例上:

```
import Vue from 'vue' 
import VueCompositionAPI from '@vue/composition-api' 

Vue.use(VueCompositionAPI)

```

Vue Demi 试图自动安装它，但是在你想确保插件安装正确的情况下，`install()` API 可以帮助你。

被曝光为`Vue.use(VueCompositionAPI)`的安全版本:

```
import { install } from 'vue-demi' 

install()

```

## Vue Demi 入门

要开始使用 Vue Demi，您需要将其安装到您的应用程序中。对于本文，我们将创建一个集成 Paystack 支付网关的 Vue 组件库。

你可以这样安装 Vue Demi:

```
// Npm 
npm i vue-demi 

// Yarn 
yarn add vue-demi

```

您还需要添加`vue`和`@vue/composition-api`作为您的库的对等依赖项，以指定它应该支持的版本。

现在我们可以将 Vue Demi 导入到我们的应用程序中:

```
<script lang="ts"> 
import {defineComponent, PropType, h, isVue2} from "vue-demi" 

export default defineComponent({
  // ... 
}) 
</script>

```

正如这里看到的，我们可以使用已经存在的标准 Vue APIs，比如`defineComponent`、`PropType`和`h`。

既然导入了 Vue Demi，那就加入我们的道具吧。这些是用户在使用组件库时需要(或者不需要，取决于您的喜好)传递的属性:

```
<script lang="ts">
import {defineComponent, PropType, h, isVue2} from "vue-demi"
// Basically this tells the metadata prop what kind of data is should accept
interface MetaData {
  [key: string]: any
}

export default defineComponent({
  props: {
    paystackKey: {
      type: String as PropType<string>,
      required: true,
    },
    email: {
      type: String as PropType<string>,
      required: true,
    },
    firstname: {
      type: String as PropType<string>,
      required: true,
    },
    lastname: {
      type: String as PropType<string>,
      required: true,
    },
    amount: {
      type: Number as PropType<number>,
      required: true,
    },
    reference: {
      type: String as PropType<string>,
      required: true,
    },
    channels: {
      type: Array as PropType<string[]>,
      default: () => ["card", "bank"],
    },
    callback: {
      type: Function as PropType<(response: any) => void>,
      required: true,
    },
    close: {
      type: Function as PropType<() => void>,
      required: true,
    },
    metadata: {
      type: Object as PropType<MetaData>,
      default: () => {},
    },
    currency: {
      type: String as PropType<string>,
      default: "",
    },
    plan: {
      type: String as PropType<string>,
      default: "",
    },
    quantity: {
      type: String as PropType<string>,
      default: "",
    },
    subaccount: {
      type: String as PropType<string>,
      default: "",
    },
    splitCode: {
      type: String as PropType<string>,
      default: "",
    },
    transactionCharge: {
      type: Number as PropType<number>,
      default: 0,
    },
    bearer: {
      type: String as PropType<string>,
      default: "",
    },
  }
</script>

```

上面看到的属性是利用 Paystack 的弹出 JS 所必需的。

Popup JS 提供了一种将 Paystack 集成到我们的网站并开始接收付款的简单方法:

```
data() {
    return {
      scriptLoaded: false,
    }
  },
  created() {
    this.loadScript()
  },
  methods: {
    async loadScript(): Promise<void> {
      const scriptPromise = new Promise<boolean>((resolve) => {
        const script: any = document.createElement("script")
        script.defer = true
        script.src = "https://js.paystack.co/v1/inline.js"
        // Add script to document head
        document.getElementsByTagName("head")[0].appendChild(script)
        if (script.readyState) {
          // IE support
          script.onreadystatechange = () => {
            if (script.readyState === "complete") {
              script.onreadystatechange = null
              resolve(true)
            }
          }
        } else {
          // Others
          script.onload = () => {
            resolve(true)
          }
        }
      })
      this.scriptLoaded = await scriptPromise
    },
    payWithPaystack(): void {
      if (this.scriptLoaded) {
        const paystackOptions = {
          key: this.paystackKey,
          email: this.email,
          firstname: this.firstname,
          lastname: this.lastname,
          channels: this.channels,
          amount: this.amount,
          ref: this.reference,
          callback: (response: any) => {
            this.callback(response)
          },
          onClose: () => {
            this.close()
          },
          metadata: this.metadata,
          currency: this.currency,
          plan: this.plan,
          quantity: this.quantity,
          subaccount: this.subaccount,
          split_code: this.splitCode,
          transaction_charge: this.transactionCharge,
          bearer: this.bearer,
        }
        const windowEl: any = window
        const handler = windowEl.PaystackPop.setup(paystackOptions)
        handler.openIframe()
      }
    },
  },

```

`scriptLoaded`状态帮助我们知道是否已经添加了 Paystack Popup JS 脚本，而`loadScript`方法加载 Paystack Popup JS 脚本并将其添加到我们的文档头。

`payWithPaystack`方法用于在调用时用 Paystack Popup JS 初始化交易:

```
render() {
    if (isVue2) {
      return h(
        "button",
        {
          staticClass: ["paystack-button"],
          style: [{display: "block"}],
          attrs: {type: "button"},
          on: {click: this.payWithPaystack},
        },
        this.$slots.default ? this.$slots.default : "PROCEED TO PAYMENT"
      )
    }
    return h(
      "button",
      {
        class: ["paystack-button"],
        style: [{display: "block"}],
        type: "button",
        onClick: this.payWithPaystack,
      },
      this.$slots.default ? this.$slots.default() : "PROCEED TO PAYMENT"
    )
}

```

render 函数帮助我们创建没有`<template>`标签的组件，并返回一个虚拟 DOM 节点。

如果你注意到了，我们在条件语句中使用了 Vue Demi 的 API 之一`isVue2`，来有条件地呈现我们的按钮。如果没有这个，如果我们想在 Vue 2 应用程序中使用我们的组件库，我们可能会因为 Vue 2 不支持 Vue 3 的一些 API 而遇到错误。

现在，当我们建立我们的图书馆时，它将在 Vue 2 和 Vue 3 中都可以访问。

完整的源代码可以在[这里](https://github.com/ECJ222/vue-paystack2)找到。

## 结论

在本文中，我们通过考虑 Vue Demi 的特性、工作原理以及如何开始使用它来了解它。

Vue Demi 是一个神奇的包，有很多潜力和效用。我强烈推荐你在创建下一个 Vue 库时使用它。

我希望你喜欢这篇文章！如果你有任何问题，请留言。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。