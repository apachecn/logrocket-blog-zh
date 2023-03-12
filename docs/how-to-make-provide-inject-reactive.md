# 如何制作提供/注入反应式博客

> 原文：<https://blog.logrocket.com/how-to-make-provide-inject-reactive/>

按照设计，Vue 的`provide`和`inject`特性是[非反应式](https://vuejs.org/v2/api/#provide-inject)，即使很多人(包括我自己！)希望他们是。

不过这对我们来说不是什么大问题，因为有一种方法可以解决这个问题:

**如果使用`data()`函数或`Vue.observable`创建一个反应对象，当使用`provide`和`inject`传递时，它仍然是反应的。**

如果你使用的是数据函数:

```
export default {
  provide() {
    return {
    reactive: this.reactive
    };
  },

  data() {
    return {
    reactive: {
        value: "Hello there"
    }
    };
  }
};

```

现在，当`reactive`被注入到另一个组件中时，它将像任何其他道具一样工作，对`reactive`的更改将触发 Vue 应用程序的更新！

## TL；速度三角形定位法(dead reckoning)

在本文中，我们将讨论:

*   为什么`data()`函数是解决这个问题的好方法
*   使对象反应的最简单方法，**即使在 Vue 之外**
*   一个由 Vue 核心团队成员创建的库，为您处理这一切
*   帮助您确定何时使用反应式提供/注入的最佳时机的问题
*   为什么要小心**避免双向数据绑定**

## 但是首先，给你一个警告

这应该*只有*在特殊情况下使用。这种技术不应该用来代替道具和事件。如果你所需要的只是一个传递数据的基本方法，道具和事件就很棒，很简单，其他每个阅读你代码的人都会明白发生了什么。

那么你应该什么时候使用它呢？

我们稍后会谈到这一点，但首先，让我们看看我们可以使 provide 和 inject 反应的三种不同方式。

## 使用 data()函数

第一种技术是通过在我们的`data()`函数中初始化对象来使其具有反应性:

```
export default {
  provide() {
    return {
    reactive: this.reactive
    };
  },

  data() {
    return {
    reactive: {
        value: "Hello there"
    }
    };
  }
};

```

任何在这里初始化的对象都会被 Vue 激活。一旦它是反应性的，无论它在哪里被使用，它都是反应性的。

您甚至可以将对象传递给 Vue 应用程序外部的一些随机 JavaScript，对 reactive 对象的任何更改都将触发 Vue 应用程序内部的更新。

还有第二种方法可以让我们的对象具有反应性，这对于 Vue 来说是相当新的。

## 使用 Vue.observable

[Vue 2.6](https://github.com/vuejs/vue/releases/tag/v2.6.0) 引入了`observable`功能，让我们[创建自己的](https://vuejs.org/v2/api/#Vue-observable)反应对象:

```
import Vue from 'vue';
const state = Vue.observable({ message: "Hello!" });

```

事实上，这与 Vue 在内部使用的`data()`函数是同一个函数，但在这里它被暴露给我们，以便我们可以在任何我们想要的地方使用它。

在 Vue 3 中，这个函数将被重命名为`reactive`，增加了[组合 API](https://github.com/vuejs/rfcs/pull/78) 。

让我们重写前面的例子来使用`Vue.observable`:

```
import Vue from 'vue';

const reactive = Vue.observable({ value: 'Hello there' });

export default {
  provide() {
    return {
    reactive: reactive,
    };
  },
};

```

由于我们不再依赖于`data`函数，这个函数给了我们更多的灵活性来创建我们的反应对象。

然而，在大多数情况下，您将使用前面的方法，因为您提供的状态通常已经由`data()`函数初始化。但是这是一个很好的工具，可以添加到您的工具箱中，以防您发现`data()`函数并不完全满足您的需要。

我们要介绍的最后一个方法不是 Vue 的原生特性，而是由 Vue 核心团队成员 Thorsten Lünborg 创建的 mixin。

## 最彻底的混凝剂

作为 Vue 核心团队的一员，Lünborg 创建了`vue-reactive-provide` mixin，你可以在 [Github](https://github.com/LinusBorg/vue-reactive-provide) 上找到它。

如果你不喜欢其他选项，这是一个让 provide/inject 反应的超级简单的方法，有两种主要的方法可以使用它。

## 组件选项

如果你想让它感觉类似于原生的`provide`，你只需要把它作为一个插件安装:

```
import Vue from 'vue';
import ReactiveProvide from 'vue-reactive-provide';

Vue.use(ReactiveProvide);

```

如果您使用的是 Vue CLI，您可以将它添加到您的`main.js`文件中。

然后在您想要提供值的组件中，您应该使用`reactiveProvide`来代替:

```
export default {
  reactiveProvide: {
    name: 'injectedName',
    include: ['reactive'],
  }

  data() {
    return {
    reactive: 'hello',
    };
  },
};

```

你需要给你的反应对象一个`name`,这样我们就知道该给孩子注入什么值。然后，您可以使用`include`数组包含任意数量的字段。

要将这个反应对象注入到一个组件中，你可以像平常一样注入它，使用你之前设置的`name`值:

```
export default {
  inject: ['injectedName']
};

```

## 米欣

将它作为一个 mixin 使用几乎是同样的过程，但是你不需要首先将它注册为一个插件:

```
import { ReactiveProvideMixin } from 'vue-reactive-provide'

export default {
  mixins: [
    ReactiveProvideMixin({
    name: 'injectedName',
    include: ['reactive'],
    })
  ],

  data() {
    return {
    reactive: 'hello',
    };
  },
};

```

我们使用`ReactiveProvideMixin`函数来动态创建一个 mixin。mixin 将以一种反应的方式为我们提供包含的价值。

为了注入值，我们使用与前面完全一样的方法:

```
export default {
  inject: ['injectedName']
};
```

## 何时使用反应式供应/注入

一般来说，你应该尽量避免使用 props/inject，而是使用 props 和 events 来传递数据。这将使您在大多数情况下到达您需要去的地方，并且避免增加不必要的复杂性，使您的代码易于理解。

但是，在决定是否应该使用此功能时，您可以问自己一些具体的问题:

1.  **是否需要避免支柱钻孔？** —通过一层又一层的组件传递道具可能会很乏味，尤其是当那些中间组件实际上并不使用道具的时候。这也可以通过 [Vuex](https://vuex.vuejs.org/) 来解决，但是你有时候想要一个更简单更轻便的解决方案
2.  组件是否紧密耦合？ —如果您有一组总是一起使用的组件，那么可以依靠 provide/inject 来传递一些数据。否则，最好坚持使用道具和事件，这是大多数其他组件将使用的
3.  **Vuex 是否矫枉过正？Vuex 是一个很好的工具，但是如果你处理的是一个简单的状态，那么 Vuex 就太过了，并且引入了很多开销。如果组件集需要可重用，那么用 Vuex 耦合它也会引入不必要的复杂性**
4.  数据是否包含在几个组件中？ —如果正在传递的数据仅由几个组件使用，那么这种解决方案是有意义的。但是如果这里使用的数据在其他地方被使用，使用 Vuex 将它放在树的更高位置*或*可能是更好的解决方案

你的用例不需要通过所有这些规则的*,但是它应该至少符合其中的一两个。*

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

例如，让我们假设你正在开发一个状态非常简单的应用程序。Vuex 太过了，所以你决定不使用它。

每个页面都将用户的信息加载到一个`user`对象中，该对象在整个应用程序的所有地方都可以使用。显示用户的姓名、电子邮件地址和其他类似信息并不局限于应用程序中的特定部分。

为了避免将这些数据作为道具通过*我们应用程序中的每一个组件*，我们在顶层组件`provide`它，因此任何需要它的组件都可以`inject`这个`user`对象并直接访问它。

现在，这似乎违反了我们的第四条规则，但它击中了第一条和第三条的要害。所以这是一个很好的解决方案。

在我们结束之前，在使用这种技术时，还有一件事您应该确保避免。

## 避免双向数据绑定

当您像这样使用反应式注入时，一个常见的错误是将其视为双向绑定，其中值可以由提供它的组件以及注入它的组件来更改。

但这是一个可怕的想法？应该只允许提供该值的组件修改它。

这有两个主要原因:

1.  Vue 使用单向绑定系统，数据沿着树向下流动。在应用程序的一个地方使用不同的数据流模型会使它不一致，并且会引起很多混乱
2.  将所有的突变保存在一个组件中使得将来的更新、重构和追踪 bug 变得更加容易

## 结论

正如我们所看到的，使 provide 和 inject 反应是可能的，并且不需要太多的努力。有三种不同的方法可以做到这一点，所以你将能够找到适合你的项目。

这种技术非常有用，但是正如我提到的，它也会使您的代码变得不必要的复杂。如果对你有用的话，最好坚持常规的道具和活动。

如果道具和事件不能很好地工作，我们通过几个不同的问题，你可以问自己，以确定这种技术是否适合你的用例。

最后，我们讨论了什么是双向数据绑定以及为什么应该避免它。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。