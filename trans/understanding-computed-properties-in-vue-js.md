# 了解 Vue.js - LogRocket 博客中的计算属性

> 原文：<https://blog.logrocket.com/understanding-computed-properties-in-vue-js/>

在 Vue.js 中，有几种方法可以设置在模板上显示的静态或动态值。这些方法包括使用文本插值、直接硬编码到 HTML 中，甚至使用简单的表达式来修改数据。

这就是计算属性派上用场的地方。在本教程中，我们将回顾在 Vue.js 中使用[计算属性的基础知识。然后，我们将通过构建一个实例应用程序，将所有新发现的技能付诸实践。](https://vuejs.org/v2/guide/computed.html)

以下是我们将要介绍的内容:

## Vue.js 中有哪些计算属性？

在 [Vue.js](https://vuejs.org/) 中，计算属性使您能够创建一个属性，该属性可用于以可读和高效的方式修改、操作和显示组件中的数据。

您可以使用计算属性根据数据模型中的一个值或一组值来计算和显示值。它还可以有一些基于其依赖关系缓存的自定义逻辑，这意味着它不会重新加载，但有一个变化的依赖关系，允许它在某种程度上侦听变化并相应地采取行动。

对于一个[基本示例](https://codesandbox.io/s/basic-computed-property-example-dlleo)，考虑计算属性`count`:

```
<template>
  <div class="hello">
    <h1>{{ count }}</h1>
  </div>
</template>

<script>
export default {
  name: "HelloWorld", 
  data() {
    return {
      shopNumber: 2
    }
  },
  computed: {
    count: function() {
      return 'The shop number is ' + this.shopNumber 
    }
  }
};
</script>

```

`count`属性从`data`函数接收`this.shopNumber`作为依赖项，然后返回一个包含`this.shopNumber`的句子，该句子可直接用于模板。

## Vue.js 中如何使用计算属性？

您可以使用计算属性来解决更复杂的问题。让我们详细介绍几个 Vue.js 计算属性派上用场的用例。

### 过滤数据

计算属性对于过滤数据非常有用。例如，假设您正在从输入搜索栏中过滤一个数组。

在下面的`data`函数中，我们有一个数组`userData`，包含我们想要在组件中显示的信息，但是我们还需要允许用户通过使用输入标签(与`data`属性`searchQuery`绑定)进行搜索来过滤显示的内容。所有这些都是在计算属性`resultQuery`内完成的:

```
export default {
  name: "HelloWorld",
  data() {
    return {
      userData: [
        {
          image:
            "https://pbs.twimg.com/profile_images/1347769407892811786/fJyOAatX_400x400.jpg",
          name: "Tunde",
          uid: "LfhxERlvyfh2auIY0HnpidjJg3L2",
        },
        {
          name: "bola",
          image:
            "https://pbs.twimg.com/profile_images/1355220122512863234/0NZI8bzI_400x400.jpg",
          uid: "R6lyXuNwZfc9ztLDfIZBSZLg2QD2",
        },
        {
          uid: " k8ZVBdA9wfetiB8vJV3Qc07NZty1",
          image:
            "https://pbs.twimg.com/profile_images/1321720900274868224/w5iM_Ads_400x400.jpg",
          name: "Supreme",
        },
      ],
      searchQuery: null
    };
  },
  computed: {
    resultQuery() {
      if (this.searchQuery) {
        return this.userData.filter((item) => {
          return this.searchQuery
            .toLowerCase()
            .split(" ")
            .every((v) => item.name.toLowerCase().includes(v));
        });
      } else {
        console.log(this.userData);
        return this.userData;
      }
    },
  },
};

```

在我们的模板中，`searchQuery`被绑定到搜索输入标签，从 th `resultQuery`返回的数据被循环显示在组件中。

```
<template>
  <div>
    <input v-model="searchQuery" type="text" placeholder="Search the name">
    <div
      class="Chatroom__list__messages"
      v-for="result in resultQuery"
      :key="result.image"
    >
      <div class="Chatroom__list__messages__img">
        <img :src="result.image" />
      </div>
      <div class="Chatroom__list__messages__message">
        <div class="Chatroom__list__messages__message__name">
          <div class="Chatroom__list__messages__message__name__indi">
            {{result.name}}
          </div>
          <div class="Chatroom__list__messages__message__name__time">
            yesterday
          </div>
        </div>
        <div class="Chatroom__list__messages__message__details">
          message from supreme
        </div>
      </div>
    </div>
  </div>
</template>

```

基本上，`resultQuery`检查用户是否在绑定到`searchQuery`的`input`标签中输入了任何东西。然后，用`userData.name`过滤。如果用户没有搜索到任何东西，它将返回整个`userData`数组。

我在 [CodeSandbox](https://codesandbox.io/s/computed-properties-filtering-6rqfj?file=/src/components/HelloWorld.vue) 上创建了一个实例。

### 计算

同样，我们可以使用计算属性来处理计算。我们需要做的就是将依赖项传递到属性中，编写我们需要它来执行的计算，然后返回答案:

```
<script>
export default {
  name: "ComputedCalculation",

data() {
  return {
    number: 2
  }
},
computed: {
  totalMarks() {
    return this.number * 2;
  }
}
};
</script>

```

我们有一个计算属性`totalMarks`，我们从`data`函数传入一个依赖项(`number`)并将其乘以 2:

```
<template>
  <div class="hello">
    <h1>{{ totalMarks }}</h1>

  </div>
</template>

```

这里有一个[活生生的例子](https://codesandbox.io/s/computed-property-calculation-24pc3)。

在我们的模板中，我们只需在其中调用计算出的属性名，值就会显示在组件中。这可能看起来是一个相对简单的例子，但是想象一下，您正在构建一个金融计算器，并处理多个依赖项和更复杂的计算，例如复利:

```
Compound Interest = P [(1 + i)n – 1]

```

其中:

*   `P`是本金
*   `i`是以百分比表示的名义年利率
*   `n`为复利周期数

将这个公式直接写入您的模板不利于代码的可读性，而且它肯定是不可伸缩的。

### `v-if`内的布尔条件句

您可以使用计算属性来检查条件是否为真，然后在我们的模板中使用`v-if`来决定是否显示代码块。

这里，`displayBoolean`被默认设置为`true`，并被设置为对计算属性`display`的依赖。在`display`内，我们可以检查显示属性是否为`true`。如果是，那么我们返回`true`；否则，我们返回`false`:

```
<script>
export default {
  name: "HelloWorld",

data() {
  return {
    displayBoolean: true
  }
},
computed: {
  display() {
    if(this.displayBoolean) {
      return true
    }
    else return false
  }
}
};
</script>

```

来自`display`的结果被输入到模板中的`v-if`中，以决定代码块是显示还是隐藏。然后，出于演示目的，我们创建一个按钮来切换`displayBoolean`的值:

```
  <template>
  <div class="hello">
    <h2 v-if="display">Display conditionally</h2>
    <button @click="displayBoolean = !displayBoolean">Toogle</button>
  </div>
</template>

```

### 通过`mapGetters`使用计算属性

考虑到我们正在使用`mapGetters`，我们可以使用计算属性来访问从 getters 中检索的数据。

在下面的例子中，我们将 getter `getChatInfo`重命名为`chatData`，并通过我们的`chatInfo`计算属性返回 getter 中的数据:

```
<script>
import { createNamespacedHelpers } from "vuex";
const { mapGetters } = createNamespacedHelpers("chat");
export default {
  name: "Chat",
  computed: {
    ...mapGetters({
      chatData: "getChatInfo",
    }),
    chatInfo() {
      console.log(this.chatData);
      return this.chatData;
    },
  },
};
</script>
```

```
<template>
  <div class="hello">
    <h1>{{ chatInfo.name }}</h1>
    <h1>{{ chatInfo.message }}</h1>
  </div>
</template>

```

### 使用计算的属性作为 setters

虽然有些不合常规，但您也可以使用计算属性来设置数据，而不仅仅是作为计算机的获取器来检索数据。

我们可以这样定义一个计算 setter:

```
<script>
export default {
  name: "HelloWorld",
  data () {
    return {
      firstName: ""
    }
  }, 
  computed: {
    theFullName: {
      get () {
        return `${this.firstName}`
      },
      set (fullName) {
        this.firstName = fullName
      }
    }
  }
};
</script>

```

`theFullName`是一个计算属性，它能够通过使用`v-model`将计算属性绑定到`input`标签的内容来设置数据。在这里，我们通过输入并实时查看数据属性`firstName`的值来操作它:

```
<template>
  <div class="hello">
    <h1>{{ theFullName }} </h1>
    <input v-model="theFullName" type="text">
  </div>
</template>

```

来看看一个[活生生的例子](https://codesandbox.io/s/computed-properties-setters-prr56)。

为了将我们学到的知识付诸实践，让我们在 Vue.js 中创建一个购物车，允许用户选择他们想要的商品数量，计算多个商品的成本，并计算包括运费在内的总额。

注意:为了演示计算的属性，这个示例应用程序的[代码可能看起来并不完全枯燥。](https://codesandbox.io/s/computed-property-checkout-page-h6iq4?file=%2Fsrc%2Fcomponents%2FHelloWorld.vue%3A336-391)

每个项目都有一个 5.00 美元的固定价格。用户选择他们需要的数量，然后将其绑定到计算属性`quantity`，该属性充当 getter 和 setter。每件商品的总价(数量*单价)在一个名为`priceCalc`的计算属性中进行计算。使用`quantity2`和`priceCalc2`对第二个项目重复该过程:

```
<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      qty: 1,
      price: 5,
      qty2: 1,
      price2: 5,
      showTotal: false
    }
  },
  computed: {
    quantity: {
      get() {
        return `${this.qty}`
      },
      set(theQuantity) {
        this.qty = theQuantity
      }
    },
    priceCalc () {
      return this.qty * this.price
    },
    quantity2: {
      get() {
        return `${this.qty2}`
      },
      set(theQuantity) {
        this.qty2 = theQuantity
      }
    },
    priceCalc2 () {
      return this.qty2 * this.price2
    },
    subtotal () {
      return this.priceCalc + this.priceCalc2
    },
    shipping () {
      return (2 * 10)/ this.subtotal || ''
    }
  },
  methods: {
    switchView() {
      this.showTotal = !this.showTotal
    }
  }
};
</script>

```

在计算属性`subtotal`中添加各个项目的总成本，并使用相同的模式计算`shipping`。`total`是包括运费在内的所有费用的总和。

我们有一个隐藏和显示我们的`subtotal`、`shipping`和`total`的`button`。这是通过将数据属性`showTotal`设置为`false`并在`method` `switchView`内切换来实现的。点击`button`触发`switchView`功能。

### 计算属性与方法

Vue.js 中的计算属性和方法似乎可以互换，但从技术上讲，它们对 Vue 应用程序的影响是不同的。

计算属性缓存结果，这意味着计算属性在组件安装后只执行一次，除了它的任何依赖关系更改。同时，每次在组件中调用方法时，该方法都会执行。

计算属性和方法有不同的用例。这通常取决于您是否希望函数每次都执行，而不考虑依赖关系。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

例如，假设您在一个函数中有一个`Math.random()`,并且您希望它每次都重新加载。在这种情况下，您需要使用方法而不是计算属性。

缓存通过减少应用程序需要重新加载和请求的次数来帮助提高性能。

## 结论

计算属性在 Vue.js 中是非常必要的，因为它们允许您读写更干净、可伸缩和更可读的代码。

现在你已经理解了计算属性的一些基本用例，我鼓励你阅读官方的 Vue.js 文档，并自己进一步探索，看看你能在 Vue.js 中用计算属性做些什么。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。