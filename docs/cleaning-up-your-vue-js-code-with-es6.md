# 用 ES6+日志火箭博客清理你的 Vue.js 代码

> 原文：<https://blog.logrocket.com/cleaning-up-your-vue-js-code-with-es6/>

ES6 是 web 向前迈出的一大步，它引入了许多新特性，解决了所有 JavaScript 开发人员面临的各种难题。但是它的一些特性特别适合于解决使用 Vue.js 开发时出现的问题。我们将了解每个功能的工作原理，以及它为您的应用程序和网站解决了什么问题。事不宜迟，我们开始吧！

### 特性#1:方法定义速记

我想说的第一个特性有一个纯粹的美学效果，但是它确实有助于使你的代码尽可能的可读。ES6 引入了这个简写来更简洁地为对象分配功能，我们在 Vue 中一直为方法、计算属性、观察器和生命周期方法这样做。下面是一个如何将其应用于 Vue 代码的示例:

```
// Without shorthand
{
    methods: {
        getValue: function() { // ... }
    },
    computed: {
        halfValue: function() { // ... }
    },
    created: function() { // ... }
}

// With ES6 shorthand
{
    methods: {
        getValue() { // ... }
    },
    computed: {
        halfValue() { // ... }
    },
    created() { // ... }
}
```

同样，这是一个很小的变化，但是对于可读性来说会有很大的不同。

### 特性#2:析构

析构是 ES6 中添加的一个特性，它使得从对象中提取属性并将其赋给变量变得更加容易。在我们开始研究这如何在我们的 Vue 代码中帮助我们之前，这里有一个关于对象析构如何工作的非常基本的例子:

```
const person = { name: 'Jake', email: '[email protected]', phone: '555-555-5555' }

// With destructuring
const { name, email, phone } = person

// Without destructuring
const name = person.name
const email = person.email
const phone = person.phone
```

上面的两个例子(有/没有析构)完全一样。使用析构的版本只是一个更干净的代码模式来达到相同的结果。

那么如何在你的 Vue 代码库中使用析构呢？在 Vue 中，析构主要表现在两个方面:从`this`析构属性，以及从作用域插槽接收属性。让我们浏览一下每一个用例。

#### 从`this`开始解构

在 Vue 中，要引用 Vue 或组件实例上的数据、方法或任何东西，可以使用`this`。但是有时不需要反复引用`this`就可以访问那些实例属性。让我向您展示一个将属性从`this`拉入本地函数范围的小技巧:

```
data() {
    return {
        endpoint: 'example.com/api',
    }
},
methods: {
    postForm() { // this is just an example method we can call in submitForm }
    submitForm() {
        // Without destructuring
        const endpoint = this.endpoint
        const postForm = this.postForm

        // With destructuring
        const { endpoint, postForm } = this
  }
}
```

这种模式不仅允许我们使用不带`this`前缀的变量，还让我们清楚地知道我们的函数所依赖的数据和/或方法。

#### 作用域插槽

插槽允许我们将模板传递到组件中，作用域插槽允许组件向这些模板提供一些组件数据。如果您不熟悉作用域插槽，这可能没有多大意义，但希望这个示例至少可以强化析构的工作方式，以及如何在许多不同的场景中使用它:

```
<!-- Without Destructuring -->
<User v-slot="slotProps">
    <div>Name: {{ slotProps.name }}</div>
    <div>Email: {{ slotProps.email }}</div>
</User>

<!-- With Destructuring -->
<User v-slot="{ name, email }">
    <div>Name: {{ name }}</div>
    <div>Email: {{ email }}</div>
</User>

```

与“从`this`析构”模式不同，析构我们的槽属性不仅允许我们在不使用`slotProps`前缀的情况下访问我们的变量，而且它准确地向我们显示了我们通过槽接受的属性。

### 特性#3:函数数组方法

ES6 引入了许多内置于阵列原型中的新方法。这些方法允许您以不同的方式与数组中的数据进行交互，比如转换每个项目(`map`)、对数组进行排序或过滤数组。我在 Vue 应用中常用的最喜欢的数组方法是`filter`、`map`、`forEach`和`includes`。这里有一个使用`filter`的例子:

```
computed: {
    // Without "filter" functional array method
    oldFilteredItems() {
        const filtered = []
        for (const item in this.items) {
            if(item.value > 10) {
                filtered.push(item)
            }
        }
        return filtered
    },
    // With "filter" functional array method
    filteredItems() {
        return this.items.filter((item) => item.value > 10)
    }
}
```

这减少了我们必须写(和读！)从七行变成只有一行！

### 功能#4:箭头功能

在我们学习箭头函数，它们是如何工作的，以及如何在你的 Vue 代码中使用它们之前，让我们看看它们解决的问题。查看以下代码:

```
data() {
    return {
        scrolled: false
    }
},
mounted() {
    window.addEventListener('scroll', function() {
        this.scrolled = true
    })
}
```

这个代码不起作用。为什么？因为当你创建一个新函数时，`this`的值被重新绑定为等于函数实例而不是 Vue 实例。如果您曾经遇到过这个问题，您可能已经尝试了以下方法来解决这个问题:

```
mounted() {
    var self = this
    window.addEventListener('scroll', function() {
        self.scrolled = true
    })
}
```

虽然这确实“修复”了问题，但是让`var self = this`散落在代码中绝对不是理想的，尤其是当这是一个可以用(drumroll please) … arrow 函数解决的问题时！

箭头函数与标准函数非常相似，但一个关键的区别是箭头函数不重新绑定`this`，这在 Vue 应用中非常有帮助！这是先前示例的更新版本，其中我们用箭头函数替换了标准函数，因此`this`不会被重新绑定:

```
mounted() {
    window.addEventListener('scroll', () => {
        this.scrolled = true
    })
}
```

我发现在编写 Vue 应用时，遵循以下规则很有帮助:在 Vue 组件中，`this`应该总是引用 Vue 实例。如果您使用箭头函数，这并不难实现，而且它使您的代码更容易理解。

如果你不熟悉箭头函数，它们绝对值得学习。虽然它们在这种情况下特别有用，但它们也允许您编写更简洁的函数，这适用于更多的情况。它们的另一个好处是与数组方法成对出现！如果你看看我在**特性#4** 中的`filteredItems`函数，你会发现我使用了一个箭头函数作为`filter`数组方法的第一个参数！

### 包扎

在我结束之前，我想谈谈我是如何着手确定这四个改进的，以及你如何学会发现你的代码库中可以改进的地方。这里有几个小技巧！

#### 寻找重复

并不是所有的重复都是不好的，但是在代码中看到任何重复的东西都会让你想知道是否有机会进行良好的抽象，或者学习一种新的模式或语言特性来解决你正在处理的问题。

#### 注意语言的变化

如果不跟上 JavaScript 的变化，就不可能知道通过使用数组方法可以简化代码中的许多循环。也就是说，你不必“深入”钻研每一件新事物，但要试着了解你所使用的语言有哪些可用之处。然后，当你遇到一个问题，希望你会想起一个语言功能，解决你所面临的问题。

#### 看别人的代码

如果你在一个团队中工作，要求和他们一起评审别人的代码，或者要求他们评审你的代码。看到其他人的代码，或者他们对你的代码的评论，会让你了解到其他人是如何以不同的方式做事的。当你看到一个你不认识的代码模式时，找出它是什么，如果它有意义，就把它应用到你的代码中。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

### 进一步阅读

* * *