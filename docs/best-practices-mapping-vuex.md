# Vuex 映射的最佳实践- LogRocket 博客

> 原文：<https://blog.logrocket.com/best-practices-mapping-vuex/>

编者按:这篇文章于 2021 年 11 月 29 日更新，以反映 Vuex 的升级。

Vuex 是一把双刃剑。如果使用得当，它可以让您在使用 Vue 时轻松许多。如果你不小心的话，它也会把你的代码弄得一团糟。

在使用 [Vuex 之前，你应该理解四个主要概念:状态、getters、突变和动作](https://blog.logrocket.com/using-vuex-4-with-vue-3/)。一个简单的 Vuex 状态操作存储中这些概念内的数据。Vuex 中的映射提供了一种很好的、干净的方式从它们中检索数据。

在本教程中，我们将演示如何映射来自 Vuex 商店的数据。如果你是熟悉 Vuex 基础知识的 Vue 开发人员，这些最佳实践将帮助你编写更干净、更易维护的代码。我们开始吧！

### 目录

## 什么是 Vuex 映射？

Vuex 中的映射使您能够将状态的任何属性(如 getters、mutations、actions 或 state)绑定到组件中的计算属性，并直接使用状态中的数据。

下面是一个简单的 Vuex 商店示例，测试数据处于以下状态:

```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    data: "test data"
  }
  }
})

```

如果您想从状态中访问`data`的值，请在 Vue.js 组件中添加以下内容:

```
computed: {
        getData(){
          return this.$store.state.data
        }
    }

```

上面的代码工作正常，但是随着州内数据开始增长，代码很快就变得难看了。例如:

```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    user: {
        id:1,
        age:23,
        role:user
        data:{
          name:"user name",
          address:"user address"
        }
    },
    services: {},
    medical_requests: {},
    appointments: {},
    }
  }
})

```

要从处于以下状态的用户对象获取用户名:

```
computed: {
        getUserName(){
          return this.$store.state.user.data.name
        }
    }

```

虽然我们可以用`this.$store.state.user.data.name`完成这项工作，但是我们可以使用地图助手将其简化为`this.event`。

## vuex〔t0〕

为了映射组件中的状态，我们首先导入`mapState`并向组件添加一个计算属性:

```
import { mapState } from 'vuex';

export default{
    computed: {
        ...mapState([
            'user',
        ])
    }
}

```

上面使用的三点语法是 ES6 语法，称为[扩展](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)语法。您现在可以访问组件中的整个`user`对象，因此，`user`成为一个计算组件。您可以做更多的事情，比如将对象从状态添加到`mapState`方法中:

```
import { mapState } from 'vuex';

export default{
    computed: {
        ...mapState([
            'user',
            'services'
        ])
    }
}

```

如您所见，这段代码非常简洁。您可以通过以下方式轻松访问用户名:

```
{{user.data.name}}

```

这同样适用于`services`对象和许多其他映射的值。你注意到我们是如何将一个数组传递给`mapState()`的吗？如果您需要给该值一个不同的名称，您可以改为传入一个对象，如下所示:

```
import { mapGetters } from 'vuex';

export default{
    computed: {
        ...mapState({
            userDetails:'user',
            userServices:'services'
        })
    }
}

```

现在，您可以通过简单地调用`userDetails`来引用`user`。

## 何时应该映射整个 Vuex 状态？

首先，您不应该映射 Vuex 状态。根据经验，只有当状态中有大量数据并且组件中需要这些数据时，才应该映射 Vuex 状态。

在上面的例子中，如果我们只需要一个值，例如`username`，那么映射整个用户对象就没有什么意义了。

当您映射状态时，整个对象被加载到内存中。您应该避免将不必要的数据加载到内存中，因为从长远来看，这将导致负面的性能影响。

## 什么是 Vuex `mapGetters`？

Getters 提供了一种从存储区获取派生计算状态的方法。映射 getters 在语法上类似于`mapState`函数:

```
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters({
    first: ‘firstCount’,
    another: ‘anotherGetter’,
    })
  }
}

```

上面的代码类似于编写如下的计算机属性:

```
export default {
  computed: {
    firstCount() {
      return this.$store.getters.firstCount
    },
    anotherGetter() {
      return this.$store.getters.anotherGetter
    },
  }
}

```

与映射状态一样，如果您想使用不同的名称，可以将一个对象传递给`mapGetters`函数:

```
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters({
    first: ‘firstCount’,
    another: ‘anotherGetter’,
    })
  }
}

```

## Vuex 贴图突变

突变是在 Vuex 存储中改变状态的一种方式。与在 Vue 的`computed property`中映射`mapState`和`mapGetters`以使状态上的数据与组件中的数据发生反应不同，`Mutations`在方法中被映射。

映射突变时，可以在 Vue 中使用以下语法提交突变:

```
this.$store.commit('mutationName`)

```

例如:

```
import { mapMutations } from 'vuex'

export default {
  methods: {
    ...mapMutations([
      'search', // map `this.search()` to `this.$store.commit('search')`

      // `mapMutations` also supports payloads:
      'searchBy' // map `this.searchBy(key)` to `this.$store.commit('searchBy', key)`
    ]),
  }
}

```

## Vuex 动作映射

映射动作很像映射突变，因为它也在方法中执行。动作也被映射到`methods`属性，而不是`computed`属性。使用映射器将`this.$store.dispatch('actionName')`绑定到映射器数组中的名称或对象的键:

```
import { mapActions } from 'vuex'

export default {
  methods: {
    ...mapActions([
      'search',
      // `mapActions` also supports payloads: searchBy(key)
      'searchBy'
    ]),
  }
}

```

请注意我们是如何使用数组来定义多个 map 操作的，这类似于编写计算机属性，如下所示:

```
export default {
  methods: {
    search() {
      return this.$store.dispatch('search');
    },
    searchBy(key) {
      return this.$store.dispatch('searchBy', key);
    },
  }
}

```

## 结论

现在，您应该对 Vuex 中的映射是如何工作的以及为什么应该使用它有了一个明确的理解。您应该能够映射 Vuex 存储中的所有组件，比如状态、getters、突变和动作。此外，您还了解了何时绘制商店地图，何时不应绘制。

映射您的动作、getters 和突变的另一个优点是，它将使您的代码更小，并节省您的编写时间。

如果您决定在下一个项目中使用 Vuex，这些最佳实践将会对您有极大的帮助。我希望你喜欢这个教程。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。