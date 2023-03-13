# 与 Vue 3 组合 API 的反应性:ref()和 reactive()

> 原文：<https://blog.logrocket.com/reactivity-vue-3-composition-api-ref-reactive/>

在单页应用程序(SPA)中，反应性是指应用程序更新其用户界面以响应底层数据变化的能力。反应性允许我们编写更干净的代码，防止我们必须手动更新 UI 来响应数据变化。相反，用户界面会自动更新。

然而，JavaScript 本身并不这样工作，所以我们可以使用 React、Vue 和 Angular 等库和框架来实现反应性。比如在 Vue 中，我们可以创建一个名为`state`的数据集；然后，Vue 使用一个观察器和观察者系统来跟踪状态并更新我们的 UI。

当 Vue 检测到状态变化时，它使用虚拟 DOM 来重新呈现 UI，并确保它总是最新的，通过使用记忆化和选择性更新等技术，使这个过程尽可能地快速和高效。

有几种不同的方法来定义我们希望 Vue 跟踪的应用程序状态。例如，在 Vue 3 中的[合成 API 之前，我们使用了一个数据函数，该函数返回我们想要跟踪的对象。然而，对于组合 API，我们使用两个函数来定义状态，`ref()`和`reactive()`。在本文中，我们将探索这两个函数，概述它们的独特之处，并了解何时应该使用它们。](https://blog.logrocket.com/vue-composition-api-vs-react-hooks/)

*向前跳转:*

## 视图〔t0〕

您可以使用 [`reactive( )`](/paper/ep/redirect/external-link?url=https%3A%2F%2Fvuejs.org%2Fapi%2Freactivity-core.html%23reactive&hmac=RHYc6xdJyliWCxc%2ButwPZTjkjP94Sn15HXM3sTTukWc%3D) 来声明 Vue 中使用组合 API 的对象或数组的状态:

```
import { reactive } from 'vue'

const state = reactive({
  first_name: "John",
  last_name: "Doe",
})

```

在上面的代码片段中，我们向 Vue 声明我们想要在`reactive( )`函数中跟踪对象。然后我们可以创建一个依赖于这个反应对象的 UI，Vue 将跟踪`state`对象并使用它来更新 UI:

```
<template>
  <div>{{state.first_name}} {{state.last_name}}</div>
</template>
<script>
import { reactive } from 'vue'
export default {
  setup() {
    const state = reactive({
      first_name: "John",
      last_name: "Doe",
    })

    return { state }
  }
}
</script>

```

在上面的代码中，我们显示了状态中的反应数据。现在，Vue 的反应系统可以跟踪这些数据，并在数据发生变化时更新用户界面。我们可以通过创建一个函数来测试这一点，当我们单击一个按钮时，这个函数会改变`first_name`和`last_name`:

```
<template>
  <div>{{state.first_name}} {{state.last_name}}</div>
  <button @click="swapNames">Swap names</button>
</template>
<script>
import { reactive } from 'vue'
export default {
  setup() {
    const state = reactive({
      first_name: "John",
      last_name: "Doe",
    })

    const swapNames = () => {
      state.first_name = "Naruto"
      state.last_name = "Uzumaki"
    }

    return { state, swapNames }
  }
}
</script>

```

点击按钮时，`swapNames`功能改变`first_name`和`last_name`，Vue 即时更新 UI 内容。

`reactive()`函数是在 Vue 组件中创建反应对象的强大工具，但是因为它在幕后工作，所以它有一个主要的限制；它只能处理对象。因此，您不能将它与字符串或数字之类的原语一起使用。为了处理这种限制，Vue 提供了第二个功能来声明应用程序中的反应状态，`ref()`。

## 视图〔t0〕

[`ref()`函数](https://vuejs.org/api/reactivity-core.html#ref)可以保存任何值类型，包括原语和对象。因此，我们以类似于`reactive()`函数的方式使用它:

```
import { ref } from 'vue'

const age = ref(0)

```

`ref()`函数返回一个特殊的反应对象。为了访问`ref()`正在跟踪的值，我们访问返回对象的`value`属性:

```
import { ref } from 'vue'

const age = ref(0)

if(age.value < 18) {
  console.log("You are too young to drink. Get lost!")
} else {
  console.log("Have some ale mate!")
}

```

下面的代码显示了我们将如何制作一个对`age` `ref()`的变化做出反应的 UI:

```
<template>
  <h1>{{age}}</h1>
  <button @click="increaseAge">+ Increase age</button>
  <button @click="decreaseAge">- Decrease age</button>
</template>
<script>
import { ref } from 'vue'
export default {
  setup() {
    const age = ref(0);

    const increaseAge = () => {
      age.value++
    }

    const decreaseAge = () => {
      age.value--
    }

    return { age, increaseAge, decreaseAge }
  }
}
</script>

```

您可能会注意到，在模板中使用`age` `ref()`时，我们不需要指定值。当你在模板中调用`age`时，Vue 会自动将 [`unref()`](https://vuejs.org/api/reactivity-utilities.html#unref) 应用到`age`中，所以你永远不需要使用模板中的值。

我们可以用一个`ref`重写第一个`reactive()`属性的例子；唯一的区别是，我们需要使用`.value`属性来访问 JavaScript 代码中的值。

## `ref()` vs `reactive()`:应该用哪个？

`ref()`和`reactive()`的显著区别在于`ref()`函数允许我们为原语和对象声明反应状态，而`reactive()`只为对象声明反应状态。

我们还必须通过使用返回对象的`.value`属性来访问`ref()`的值，但是我们不必为`reactive()`对象这样做。因此，在真实的场景中，您会发现使用`ref()`的 Vue 代码比使用`reactive()`的多，但是`reactive()`是完美的，因为它可以很容易地接受在组合 API 之前定义的状态。两者都有效地跟踪反应，所以使用哪一个是偏好和编码风格的问题。

## 反应性:Vue 2 与 Vue 3

Vue 3 并没有完全消除我们在 Vue 2 中定义反应状态的默认方式；相反，Vue 3 附带了两个 API，即[组合 API](https://blog.logrocket.com/new-features-in-vue-3-and-how-to-use-them/) 和选项 API。Options API 主要以 Vue 2 的方式工作:

```
<template>
  <h1>{{age}}</h1>
  <button @click="increaseAge">+ Increase age</button>
  <button @click="decreaseAge">- Decrease age</button>
</template>
<script>
import { ref } from 'vue'
export default {
  data: function() {
    return {
      age: 0
    }
  },
  methods: {
    increaseAge() {
      this.age++
    },
    decreaseAge() {
      this.age--
    }
  }
}
</script>

```

在上面的例子中，我们在状态中声明了`age`，并将其设置为`zero`。通过这些方法，我们可以增加或减少`age`，然后在视图上看到每次点击按钮的数据变化。上面的代码使用了选项 API，对 Vue 2 和 3 都有效。

从 Vue 2 返回的`data`属性与我们之前讨论的组合 API 中的`reactive()`函数非常相似。它只作为一个对象工作；不能从数据属性包含的函数中返回字符串这样的原始值。

## 迁移 Vue 2 应用程序以使用`ref()`和`reactive()`

将应用程序从 Vue 2 Options API 迁移到 Vue 3 Composition API 相当简单。我们可以很容易地将 Vue 2 `state`转换成`ref()`或`reactive()`。看看下面的 Vue 2 组件:

```
<template>
  <div>
    <button>Clicked {{xTimes}}</button>
  </div>
</template>
<script>
export default {
  data: function() {
    return {
      xTimes: 0  
    }
  },
  methods: {
    increase() {
      this.xTimes++
    }    
  }
}
</script> 

```

我们可以很容易地使用`ref()`重写这个组件来显示它。考虑下面的代码片段:

```
<template>
  <div>
    <button>Clicked {{xTimes}}</button>
  </div>
</template>
<script>
import {ref} from "vue"
export default {
  setup() {
    const xTimes = ref(0)
    return {xTimes}
  },
  methods: {
    increase() {
      this.xTimes++
    }    
  }
}
</script> 

```

我们也可以使用`reactive()`重写这个组件来显示它:

```
<template>
  <div>
    <button>Clicked {{xTimes}}</button>
  </div>
</template>
<script setup>
import {ref} from "vue"
const xTimes = ref(0)
const increase = () => {
  xTimes++
}   

</script>

```

我们可以通过使用`setup( )`语法使[的代码更加流畅，这看起来更像下面的代码:](https://vuejs.org/api/sfc-script-setup.html#basic-syntax)

```
<template>
  <div>
    <button>Clicked {{xTimes}}</button>
  </div>
</template>
<script>
import {reactive} from "vue"

  setup() {
    const state = reactive({
      xTimes: 0
    })
    return {state}
  },
  methods: {
    increase() {
      this.state.xTimes++
    }    
  }
}
</script>

```

## `ref()`和`reactive()`的缺点

使用`ref()`的缺点是总是不得不使用`.value`来访问您的状态会很不方便。如果您必须使用一个可能是`ref()`的值，您可能不知道它是否已经被初始化，并且在 null 上调用`.value`可能会抛出一个运行时错误。为了解决这个问题，我们可以`unref`T5，我们将在本文后面讨论。

另一方面，使用`reactive`的缺点是它不能用在原语上。您只能在对象和基于对象的类型上使用`reactive()`，比如`Map`和`Set`。您可以通过使用与在选项 API 中定义状态相同的方式使用`reactive()`来规避这个问题。

## 混合`ref()`和`reactive()`:这是个好主意吗？

没有规则或惯例反对混合使用`ref()`和`reactive()`。是否这样做是主观的，取决于相关开发人员的偏好和编码模式。

有一些库，像[Vuelidate](https://vuelidate.js.org/),[使用`reactive()`来设置验证状态](https://blog.logrocket.com/form-validation-in-vue-with-vuelidate/)。在这种情况下，组合其他州的多个`ref()`函数和验证规则的`reactive()`函数是有意义的。

在编写代码时，与团队达成一致以避免混淆是非常重要的。`ref()`和`reactive()`是非常高效的声明状态的工具，它们可以一起使用，没有任何技术上的缺陷。

## `ref()`打开包装

Vue 通过在特定情况下自动打开`ref()`功能来改善使用功能的人体工程学。在这些情况下使用`ref()`时，我们不需要使用`.value`，因为 Vue 会自动为我们解包。

Vue 为我们打开`ref()`函数的一个地方是在模板中。当从模板中访问一个`ref()`时，我们可以在不使用`.value`的情况下检索`ref()`的值，如下图和上例所示:

```
<template>
  <div>
    <button @click="increase">Clicked {{xTimes}}</button>
  </div>
</template>
<script>
import {ref} from "vue"
export default {
  setup() {
    const xTimes = ref(0)
    return {xTimes}
  },
  methods: {
    increase() {
      this.xTimes++
    }    
  }
}
</script>

```

当一个`ref()`被设置为一个`reactive()`对象的值时，Vue 也会在我们访问它的时候自动为我们解开`ref()`，如下图所示:

```
<template>
  <div>
    <button @click="increase">Clicked {{ state.count }}</button>
  </div>
</template>
<script setup>
import { ref, reactive } from "vue";

const state = reactive({
  count: ref(0),
});

function increase() {
  state.count++;
}
</script>

```

## 结论

应用反应性可以帮助你写出更简洁的代码。在本文中，我们学习了如何使用`reactive()`和`ref()`函数处理 Vue 中的反应性。此外，我们还了解了它们与 Vue 2 选项 API 以及 Vue 3 组合 API 的关系，讨论了何时使用它们。

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。谢谢！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。