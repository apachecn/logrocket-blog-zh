# Vue 3 中的新特性以及如何使用它们

> 原文：<https://blog.logrocket.com/new-features-in-vue-3-and-how-to-use-them/>

Vue 3 尚未正式发布，但 Vue 团队已经发布了 Alpha 版本，供美国开发者使用 Vue 3 将附带的一些功能。

在写这篇文章的时候，我们有(Alpha-10)版本可以进行实验。

虽然这还没有准备好用于生产，但提前了解新功能总是好的，这样当稳定版本发布时，我们可以直接开始使用它或将现有的 Vue 2 应用程序迁移到版本 3.0 以使用最新的功能。

## 设置

我们将使用基于 WebPack 的设置。

为此，请克隆此存储库:

```
git clone https://github.com/vuejs/vue-next-webpack-preview.git vue-next
cd vue-next
```

现在安装软件包:

```
npm install
```

就是这样。我们现在有一个工作 Vue 3 项目设置。

要启动应用程序，只需运行以下命令:

```
npm run dev
```

在您的浏览器中打开`localhost:8080`，您可以看到一个简单的计数器应用程序。

打开`package.json`文件，这里可以看到 Vue 版本。写这篇文章的时候，版本是`3.0.0-alpha.8`。

打开`App.vue`，你会看到`setup()`方法，即这里已经使用的组合 API。我们可能会从 Vue 的官方插件`eslint-plugin-vue`中看到一些 lint 错误，因为 linters 还没有更新以理解新语法。

在我们开始编码之前，让我们回顾一下 Vue 3 中的新特性。

## Vue 3 中的新功能

Vue 3 速度更快，文件更小，并配备了更好的 TypeScript 支持。我们可以在本文中讨论并学习实现的一些新特性包括:

*   组合 API(现在是内置的)
*   多个根元素(模板语法)
*   焦虑
*   多个虚拟模型
*   更好的反应性
*   门户网站

### 组合 API

Composition API 是几个月前作为插件发布的，所以没有什么新的东西，但在 Vue 3 中，我们不必再像插件一样安装它了。现在，它内置在软件包中，无需任何额外设置即可开箱使用。

使用组合 API 有两个主要优点:

*   更好的组织
*   共享/重用代码

Vue 3 仍然支持 Options API，所以如果你认为你不需要 composition API，你可以一直使用 Vue 2 中使用的传统方法。

如果您不熟悉 Composition API，下面是我们如何使用它来实现一个组件:

```
<template>
  <div class="counter">
    <p>count: {{ count }}</p>
    <p>NewVal (count + 2): {{ countDouble }}</p>
    <button @click="inc">Increment</button>
    <button @click="dec">Decrement</button>
    <p> Message: {{ msg }} </p>
    <button @click="changeMessage()">Change Message</button>
  </div>
</template>
<script>
import { ref, computed, watch } from 'vue'
export default {
  setup() {
/* ---------------------------------------------------- */
    let count = ref(0)
    const countDouble = computed(() => count.value * 2)
    watch(count, newVal => {
      console.log('count changed', newVal)
    })
    const inc = () => {
      count.value += 1
    }
    const dec = () => {
      if (count.value !== 0) {
        count.value -= 1
      }
    }
/* ---------------------------------------------------- */
    let msg= ref('some text')
    watch(msg, newVal => {
      console.log('msg changed', newVal)
    })
    const changeMessage = () => {
      msg.value = "new Message"
    }
/* ---------------------------------------------------- */
    return {
      count,
      inc,
      dec,
      countDouble,
      msg,
      changeMessage
    }
  }
}
</script>
```

下面是 Options API 中的等效代码:

```
<template>
  <div class="counter">
    <p>count: {{ count }}</p>
    <p>NewVal (count + 2): {{ countDouble }}</p>
    <button @click="inc">Increment</button>
    <button @click="dec">Decrement</button>
    <p> Message: {{ msg }} </p>
    <button @click="changeMessage()">Change Message</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      count: 0,
      msg: 'some message'
    }
  },
  computed: {
    countDouble() {
      return this.count*2
    }
  },
  watch: {
    count(newVal) {
      console.log('count changed', newVal)
    },
    msg(newVal) {
      console.log('msg changed', newVal)
    }
  },
  methods: {
     inc() {
      this.count += 1
    },
    dec() {
      if (this.count !== 0) {
        this.count -= 1
      }
    },
    changeMessage() {
      msg = "new Message"
    }
  }

}
</script>
```

我们可以看到，通过将特定特性的代码(状态、方法、计算属性、观察器等)放在一起，使用 Composition API 允许我们更好地组织，这在 Options API 中是不可能的。

在上面的例子中，`counter`的代码和改变一个`message`的代码在组合 API 中是明确分开的。

随着组件规模的增长，组织代码成为一个重要的因素。任何新开发人员都可以轻松理解代码，而无需花费太多时间分析所有代码行。

以前，我们可以使用 Mixins 来共享代码。然而，很难跟踪不同组件中的状态和方法，如果我们不小心，Mixins 有可能覆盖我们组件中现有的状态或方法。

使用组合 API 使得代码共享更加容易。我们可以提取出特定特性的代码，并在多个地方使用它，如下所示:

```
//message.js
import { ref, watch } from 'vue'
export function message() {
  let msg = ref(123)
  watch(msg, newVal => {
    console.log('msg changed', newVal)
  })
  const changeMessage = () => {
    msg.value = 'new Message'
  }
  return { msg, changeMessage }
}
```

在我们的组件中使用共享代码

```
<template>
  <div class="counter">
    <p>count: {{ count }}</p>
    <p>NewVal (count + 2): {{ countDouble }}</p>
    <button @click="inc">Increment</button>
    <button @click="dec">Decrement</button>
    <p>Message: {{ msg }}</p>
    <button @click="changeMessage()">change message</button>
  </div>
</template>
<script>
import { ref, computed, watch } from 'vue'
import { message } from './common/message'
export default {
  setup() {
    let count = ref(0)
    const countDouble = computed(() => count.value * 2)
    watch(count, newVal => {
      console.log('count changed', newVal)
    })
    const inc = () => {
      count.value += 1
    }
    const dec = () => {
      if (count.value !== 0) {
        count.value -= 1
      }
    }
    let { msg, changeMessage } = message()
    return {
      count,
      msg,
      changeMessage,
      inc,
      dec,
      countDouble
    }
  }
}
</script>
```

更多细节请参考官方组合 API [指南](https://vue-composition-api-rfc.netlify.com/)。

### 多个根元素(模板语法)

在 Vue 2 中，模板标签只能接受一个根元素。即使我们只有两个`<p>`标签，我们也必须将它们包含在一个`<div>`标签中才能让它工作。因此，我们必须在父组件中也更改 CSS 代码，以便它看起来像预期的那样。

在 Vue 3 中，这个限制被取消了。不再需要根元素了。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们可以在`<template></template>`部分直接使用任意数量的标签:

```
<template>
  <p> Count: {{ count }} </p>
  <button @click="increment"> Increment </button>
  <button @click="decrement"> Decrement</button>
</template>
```

Vue 2 中的等效代码:

```
<template>
  <div class="counter">
    <p> Count: {{ count }} </p>
    <button @click="increment"> Increment </button>
    <button @click="decrement"> Decrement</button>
  </div>
</template>
```

### 焦虑

悬念是一个新特性，它在主组件获取数据之前呈现一个默认/后备组件。

有时我们使用异步操作从服务器获取数据。不是用`v-if`处理模板，然后在我们返回数据时将它设置回来，而是悬念为我们做了这件事。

暂记可用于模板的两个部分或整个模板:

```
<template>
  <Suspense>
    <template #default>
      <div v-for="item in articleList" :key="item.id">
        <article>
          <h2>{{ item.title }}</h2>
          <p>{{ item.body }}</p>
        </article>
      </div>
    </template>
    <template #fallback>
      Articles loading...
    </template>
  </Suspense>
</template>
<script>
import axios from 'axios'
export default {
  async setup() {
    let articleList = await axios
      .get('https://jsonplaceholder.typicode.com/posts')
      .then(response => {
        console.log(response)
        return response.data
      })
    return {
      articleList
    }
  }
}
</script>
```

### 多个虚拟模型

我们都知道 v-model 是用于双向绑定的。我们通常在表单元素中使用它。有时，我们甚至用它来定制组件。

Vue-2 只允许在一个组件上使用一个 v-model。在 Vue-3 中，我们可以将任意数量的 v 模型绑定到我们的定制组件:

```
<template>
      <survey-form v-model:name="name" v-model:age="age"> </survey-form>
    </template>

    //SurveyForm.vue
    <template>
      <div>
        <label>Name: </label>
        <input :value="name" @input="updateName($event.target.value)" />
        <label>Age: </label>
        <input :value="age" @input="updateAge($event.target.value)" />
      </div>
    </template>
    <script>
    export default {
      props: {
        name: String,
        age: Number
      },
      setup(props, { emit }) {
        const updateName = value => {
          emit('update:name', value)
        }
        const updateAge = value => {
          emit('update:age', +value)
        }
        return { updateName, updateAge }
      }
    }
    </script>
```

### 更好的反应性

Vue 2 已经有了很好的反应性，你可能没有遇到过任何反应性缺乏的情况。然而，Vue 2 也有一些不足之处。

让我们重温 Vue 2，看看这些限制是什么。

为了演示反应性，我们将使用观察器来监听其中一个状态变量，然后修改它以查看`watchers`是否被触发:

```
<template>
  <div class="hello" @click="test">test {{list }} {{ myObj }}</div>
</template>
<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      list: [1, 2],
      myObj: { name: "Preetish" }
    };
  },
  watch: {
    list: {
      handler: () => {
        console.log("watcher triggered");
      },
      deep: true
    }
  },
  methods: {
    test() {
      this.list[2] = 4;
      this.myObj.last = "HS";
      delete this.myObj.name;
    }
  }
};
</script>
```

在 Vue-2 中，上述三种修改(例如基于索引向数组添加新项、向对象添加新项或从对象中删除项)都不是反应性的。因此`watchers`不会被触发，否则 DOM 会被更新。我们不得不使用`vue.set()`或`vue.delete()`方法。

在 Vue-3 中，这些功能直接工作，无需任何辅助功能:

```
export default {
  setup() {
    let list = ref([1, 2])
    let a = ref(0)
    let myObj = ref({ name: 'Preetish' })
    function myFun() {
      list.value[3] = 3
      myObj.value.last = 'HS'
      delete myObj.value.name
    }
    return { myFun, list, myObj }
  }
}
```

我们可以看到在 Vue 3 设置中`watcher`被触发了四次。

### 全球安装

当你在`about`项目中打开`main.js`时，你会注意到一些不同。我们不再使用全局 Vue 实例来安装插件和其他库。

相反，你可以看到`createApp`方法:

```
import { createApp } from 'vue'
import App from './App.vue'
const myApp = createApp(App)
myApp.use(/* plugin name */)
myApp.use(/* plugin name */)
myApp.use(/* plugin name */)
myApp.mount('#app')
```

这个特性的优点是，它保护 Vue 应用程序免受我们使用的第三方库/插件的影响，这些库/插件可能会覆盖或更改全局实例——主要是通过使用 Mixins。

现在使用`createApp`方法，我们将这些插件安装在一个特定的实例上，而不是全局对象上。

### 门户网站

Portal 是一个特性，我们可以将一个组件中的部分代码呈现到不同 DOM 树中的不同组件中。Vue 2 中有一个名为`portal-vue`的第三方插件实现了这一点。

在 Vue 3 中，将内置 portal，非常易于使用。

Vue 3 将有一个名为`<Teleport>`的特殊标签，这个标签内的任何代码都可以随时传送到任何地方。标签接受一个`to`参数。

让我们来看看实际情况:

```
<Teleport to="#modal-layer">
  <div class="modal">
      hello
  </div>
</Teleport>
```

`<Portal></Portal>`中的任何代码都将显示在提到的目标位置。

```
<div id="modal-target"></div>
```

在撰写本文时，`<Teleport>`在上面提到的 Alpha 版本中不起作用。

## 结论

如果你计划开始你的新项目，你仍然可以继续使用 Vue 2 和一个复合 API 插件，然后迁移到 Vue 3，因为除了移除过滤器之外，不会有任何突破性的改变。

Vue 3 将会有很多令人惊奇的新功能。集成的组合将对未来应用程序的开发流程产生重大影响，它提供了一种简单的方法来组织和共享代码，并提供了强大的类型脚本支持。

在即将到来的新更新中，性能将得到微调，包的大小将进一步减小。

其他功能，如悬念，多 v 模型等，将使开发比以前更容易。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。