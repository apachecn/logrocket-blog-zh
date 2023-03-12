# Vue - LogRocket 博客中的高级数据获取技术

> 原文：<https://blog.logrocket.com/advanced-data-fetching-techniques-in-vue/>

当用前端框架构建应用程序时，产生很多意见的一个方面是数据获取的概念。在基本场景中，您可以结合使用 HTTP 客户端(例如 [Axios](https://github.com/axios/axios) )和 Vue 的生命周期方法来处理数据。

更复杂的情况，如具有多个组件的大型应用程序，在大多数情况下会被路由，将需要不同的方法。此外，获取数据的顺序——在加载某些组件之前或之后——也是一个问题。在这篇文章中，我们将看看在使用 Vue 的高级场景中获取数据的最佳实践。我们还将学习如何使用 Vue 3 的组合 API 来利用数据获取中的`stale-while-revalidate`技术。

## 导航布线元件时提取数据

当涉及各种路由组件时，数据获取可能会变得复杂。在某些情况下，我们可能希望在导航特定路线时显示一些数据。Vue Router 通过提供在导航之前或之后获取数据的选项，恰当地处理了这一问题。让我们来看看这两种情况。

### 导航前获取数据

Vue 路由器提供了许多钩子来处理导航前的数据获取:

*   带三个参数的`beforeRouteEnter()`钩子——`to`、`from`和`next`。顾名思义，它用于在导航到组件之前获取该组件所需的数据
*   `beforeRouteUpdate()`钩子也采用与`beforeRouteEnter()`相同的参数，用于用已经获取的数据更新组件

我们有一个带有两个路由组件的应用程序，显示我们银行账户的当前余额和分类账余额。当显示我们的分类帐余额时，我们希望在导航到组件之前获取数据。首先，我们将创建为分类帐余额提供数据的脚本:

```
// LedgerBalance.js

export default (callback) => {
  const LedgerBalance = "1500 USD";
  setTimeout(() => {
    callback(null, LedgerBalance);
  }, 3000);
};
```

接下来，我们将创建分类帐余额组件:

```
<!-- LedgerBalance.vue -->

<template>
  <div>Hello, your ledger balance is {{ balance }}</div>
</template>
<script>
import ledgerBalance from "../scripts/LedgerBalance";
export default {
  name: "Ledger",
  data() {
    return { balance: null };
  },
  // Here the component is yet to be loaded
  beforeRouteEnter(to, from, next) {
    ledgerBalance((err, balance) => {
      next(vm => vm.setBalance(err, balance));
    });
  },
  // On calling beforeRouteUpdate, the component is loaded and the route changes
  beforeRouteUpdate(to, from, next) {
    this.balance = null;
    ledgerBalance((err, balance) => {
      this.setBalance(err, balance);
      next();
    });
  },
  methods: {
    setBalance(err, balance) {
      if (err) {
        console.error(err);
      } else {
        this.balance = balance;
      }
    }
  }
};
</script>
```

在`LedgerBalance.vue`中，`beforeRouteEnter()`钩子确保在`Ledger`组件被加载之前从`LedgerBalance.js`获取数据。

接下来，当`Ledger`被加载并且路线改变时，`beforeRouteUpdate()`中的`setBalance()`方法被用于设置数据。

然后，我们将定义分类帐余额的传送路径:

```
// main.js

import Vue from "vue";
import VueRouter from "vue-router";
import App from "./App";
import Ledger from "./components/LedgerBalance";
Vue.use(VueRouter);
const router = new VueRouter({
  routes: [
    { path: "/Ledger", component: Ledger }
  ]
});
new Vue({
  render: (h) => h(App),
  router
}).$mount("#app");
```

定义路由路径后，我们将把它包含在主视图中:

```
<!-- App.vue -->

<template>
  <div id="app">
    <div class="nav">
      <router-link to="/Ledger">Ledger Balance</router-link>
    </div>
    <hr>
    <div class="router-view">
      <router-view></router-view>
    </div>
  </div>
</template>
<script>
export default { name: "App" };
</script>
```

当导航到`Ledger`组件时，我们可以观察到延迟(由于`LedgerBalance.js`中的`setTimeout()`函数),因为数据是在导航之前获取的:

 [https://www.youtube.com/embed/kd8OjJf-rdc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/kd8OjJf-rdc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

### 导航后获取数据

在某些情况下，我们可能希望在导航到组件后获取数据。这在我们处理实时变化的数据时会很有用。例如账户的当前余额。在这种情况下，我们将首先定义处理当前余额数据的函数:

```
// CurrentBalance.js

export default (callback) => {
  const CurrentBalance = "1000 USD";
  setTimeout(() => {
    callback(null, CurrentBalance);
  }, 3000);
};
```

接下来，当创建我们的`CurrentBalance`组件时，我们将使用`created()`生命周期钩子来调用`fetchBalance()`，我们的数据获取方法:

```
/<!-- CurrentBalance.vue -->

<template>
  <div>Hello, your current balance is {{ balance }}</div>
</template>
<script>
import currentBalance from "../scripts/CurrentBalance";
export default {
  name: "Current",
  data() {
    return { balance: null };
  },
  created() {
    this.fetchBalance();
  },
  methods: {
    fetchBalance() {
      currentBalance((err, balance) => {
        if (err) {
          console.error(err);
        } else {
          this.balance = balance;
        }
      });
    }
  }
};
</script>
```

考虑到数据将在导航发生后获取，进度条或微调器之类的加载组件会很有用，这样看起来就不会有错误:

 [https://www.youtube.com/embed/FS8n35sZ2P0?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/FS8n35sZ2P0?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 在 Vue 中重新验证时失效

传统上，Vue 应用程序使用`mounted()`钩子来获取数据。从 API 获取数据类似于下面的代码示例:

```
<template>
  <div :key="car.carmodel" v-for="car in cars">
    {{ car.carmodel }}
  </div>
</template>

<script>
export default {
  name: 'Cars',
  data() {
    return {
      cars: []
    }
  },
  mounted() {
    fetch('/api/cars')
      .then(res => res.json())
      .then(carJson => {
        this.cars = carJson
      })
  }
}
</script>
```

假设这里需要更多的数据，这可能导致从多个端点获取数据。这是不可行的，因为在不同组件之间导航将导致为每个导航发出一个 API 请求。这些多个 API 请求可能会降低速度，并给该应用程序的用户带来糟糕的体验:

```
<template>
   <div v-if="about">
     <div>{{ about.car.model }}</div>
     <div>{{ about.bio }}</div>
   </div>
   <div v-else>
     <Loading />
   </div>
 </template>

 <script>
 export default {
   name: 'Bio',
   props: {
     model: {
       type: String,
       required: true
     }
   },
   data() {
     return {
       about: null
     }
   },

   mounted() {
     fetch(`/api/car/${this.model}`)
       .then(res => res.json())
       .then(car => fetch(`/api/car/${car.id}/about`))
       .then(res => res.json())
       .then(about => {
         this.about = {
           ...about,
           car
         }
       })
   }
 }
 </script>
```

理想情况下，我们需要的是一种高效的方式来缓存已经访问过的组件，这样每个已经访问过的组件都可以方便地呈现数据，即使是在来回导航的时候。这就是“重新验证时失效”概念有用的地方。

Stale-while-revalidate `(swr)`使我们能够缓存已经获取的数据，同时获取其他请求的数据。在上面的代码示例中，每个查看汽车简介的请求都会重新触发查看汽车模型的请求，即使该模型已经被查看过。使用`swr`，我们可以缓存汽车的模型，这样当用户请求汽车的简介时，可以在获取汽车简介数据的同时看到模型。

在 Vue 中，通过一个库( [swrv](https://github.com/Kong/swrv) )可以实现 stale-while-revalidate 的概念，这个库很大程度上利用了 Vue 的组合 API。采用一种更具伸缩性的方法，我们可以使用`swrv`来获取汽车的细节，如下所示:

```
import carInfo from './carInfo'
import useSWRV from 'swrv'

export default {
  name: 'Bio',
  props: {
    model: {
      type: String,
      required: true
    }
  },

  setup(props) {
    const { data: car, error: carError } = useSWRV(
      `/api/cars/${props.model}`,
      carInfo
    )
    const { data: about, error: aboutError } = useSWRV(
      () => `/api/cars/${car.value.id}/about`,
      carInfo
    )

    return {
      about
    }
  }
}
```

在上面的代码示例中，第一个`useSWRV`钩子使用 API 的 URL 作为每个请求的唯一标识符，并接受一个`carInfo`函数。`carInfo`是一个异步函数，用于获取汽车的详细信息，它还使用 API 的 URL 作为唯一标识符。

第二个`useSWRV`钩子观察第一个钩子的变化，并根据这些变化重新验证它的数据。`data`和`error`值分别为对`carInfo`函数发出的请求的成功和失败响应而填充。

## 摘要

如果使用得当，在导航路由组件时获取数据和缓存已经获取的数据是在应用程序中创建令人印象深刻的用户体验的好方法。为了更深入地了解 stale-while-revalidate，我建议你看一下关于这个话题的 Markus Oberlehner 的文章。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。