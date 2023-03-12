# 使用 Vue 3 构建更好的高阶组件

> 原文：<https://blog.logrocket.com/build-better-higher-order-components-with-vue-3/>

Vue 3 很快会随着组合 API 的引入而发布。它带来了许多性能上的变化和改进。

高阶组件(hoc)是使用模板以声明方式向应用程序添加某些功能的组件。我相信，即使引入了组合 API，它们也将继续发挥重要作用。

hoc 在展示其功能的全部能力方面一直存在问题，因为它们在大多数 Vue 应用中并不常见，所以它们通常设计不佳，可能会带来限制。这是因为模板仅仅是一个模板，或者是一种表达某种逻辑的受限语言。然而，在 JavaScript 或 JSX 环境中，表达逻辑要容易得多，因为您有完整的 JavaScript 可供使用。

Vue 3 带来的是使用组合 API 和模板的声明简易性无缝混合和匹配 JavaScript 的表达能力。

我在为各种逻辑构建的应用程序中积极使用 hoc，比如网络、动画、UI 和样式、实用程序和开源库。我有几个关于如何构建 hoc 的技巧来分享，特别是对于即将发布的 Vue 3 Composition API。

## 模板

让我们假设下面的`fetch`组件。在我们开始讨论如何实现这样一个组件之前，您应该考虑如何使用您的组件。然后，你需要决定如何实现它。这类似于 TDD，但是没有测试——它更像是在工作之前玩弄概念。

理想情况下，组件将使用一个端点，并将其结果作为作用域槽本身返回:

```
<fetch endpoint="/api/users" v-slot="{ data }">
  <div v-if="data">
    <!-- Show the response data -->
  </div>
</fetch>
```

现在，虽然这个 API 的基本目的是通过网络获取一些数据并显示出来，但是它缺少了很多有用的东西。

让我们从错误处理开始。理想情况下，我们希望能够检测是否抛出了网络或响应错误，并向用户显示一些指示。让我们将其概括到我们的用法片段中:

```
<fetch endpoint="/api/users" v-slot="{ data, error }">
  <div v-if="data">
    <!-- Show the response data -->
  </div>

  <div v-if="error">
    {{ error.message }}
  </div>
</fetch>
```

到目前为止一切顺利。但是加载状态呢？如果我们走同样的路，我们会得到这样的结果:

```
<fetch endpoint="/api/users" v-slot="{ data, error, loading }">
  <div v-if="data">
    <!-- Show the response data -->
  </div>

  <div v-if="error">
    {{ error.message }}
  </div>

  <div v-if="loading">
    Loading....
  </div>
</fetch>
```

酷毙了。现在，让我们假设我们需要分页支持:

```
<fetch endpoint="/api/users" v-slot="{ data, error, loading, nextPage, prevPage }">
  <div v-if="data">
    <!-- Show the response data -->
  </div>

  <div v-if="!loading">
    <button @click="prevPage">Prev Page</button>
    <button @click="nextPage">Next Page</button>
  </div>

  <div v-if="error">
    {{ error.message }}
  </div>

  <div v-if="loading">
    Loading....
  </div>
</fetch>
```

你知道这是怎么回事，对吧？我们在默认的作用域槽中添加了太多的属性。相反，让我们将其分成多个时间段:

```
<fetch endpoint="/api/users">
  <template #default="{ data }">
    <!-- Show the response data -->
  </template>

  <template #pagination="{ prevPage, nextPage }">
    <button @click="prevPage">Prev Page</button>
    <button @click="nextPage">Next Page</button>
  </template>

  <template #error="{ message }">
    <p>{{ message }}</p>
  </div>

  <template #loading>
    Loading....
  </template>
</fetch>
```

虽然我们拥有的字符数量基本相同，但在组件的不同操作周期中，它使用多个槽来显示不同的 UI，从这个意义上来说，它要简洁得多。它甚至允许我们在每个插槽的基础上公开更多的数据，而不是将组件作为一个整体。

当然，这里还有改进的余地。但是让我们决定这些是你想要的组件的特性。

还没有任何东西起作用。您仍然需要实现实际的代码来让它工作。

从模板开始，我们只有 3 个使用`v-if`显示的插槽:

```
<template>
  <div>
    <slot v-if="data" :data="data" />

    <slot v-if="!loading" name="pagination" v-bind="{ nextPage, prevPage }" />

    <slot v-if="error" name="error" :message="error.message" />

    <slot v-if="loading" name="loading" />
  </div>
</template>
```

在这里使用带有多个插槽的`v-if`是一种抽象，所以这个组件的消费者不必有条件地呈现他们的 UI。这是一个方便的功能。

组合 API 为构建更好的 hoc 提供了独一无二的机会，这也是本文首先要讨论的。

## JavaScript

有了模板，第一个简单的实现将在一个单独的`setup`函数中实现:

```
import { ref, onMounted } from 'vue';

export default {
  props: {
    endpoint: {
      type: String,
      required: true,
    }
  },
  setup({ endpoint }) {
    const data = ref(null);
    const loading = ref(true);
    const error = ref(null);
    const currentPage = ref(1);

    function fetchData(page = 1) {
      // ...
    }

    function nextPage() {
      return fetchData(currentPage.value + 1);
    }

    function prevPage() {
      if (currentPage.value <= 1) {
        return;
      }

      fetchData(currentPage.value - 1);
    }

    onMounted(() => {
      fetchData();
    });

    return {
      data,
      loading,
      error,
      nextPage,
      prevPage
    };
  }
};
```

这是对`setup`功能的概述。为了完成它，我们可以像这样实现`fetchData`函数:

```
function fetchData(page = 1) {
  loading.value = true;
  // I prefer to use fetch
  // you cause use axis as an alternative
  return fetch(`${endpoint}?page=${page}`, {
    // maybe add a prop to control this
    method: 'get',
    headers: {
      'content-type': 'application/json'
    }
  })
    .then(res => {
      // a non-200 response code
      if (!res.ok) {
        // create error instance with HTTP status text
        const error = new Error(res.statusText);
        error.json = res.json();
        throw error;
      }

      return res.json();
    })
    .then(json => {
      // set the response data
      data.value = json;
      // set the current page value
      currentPage.value = page;
    })
    .catch(err => {
      error.value = err;
      // incase a custom JSON error response was provided
      if (err.json) {
        return err.json.then(json => {
          // set the JSON response message
          error.value.message = json.message;
        });
      }
    })
    .then(() => {
      // turn off the loading state
      loading.value = false;
    });
}
```

所有这些就绪后，组件就可以使用了。您可以在这里找到它的工作示例。

但是，这个特设组件类似于 Vue 2 中的组件。您只是使用 composition API 重写了它，这虽然简洁，但几乎没有用。

我发现，要为 Vue 3 构建一个更好的组件(特别是像这样的面向逻辑的组件)，最好是以“组合-API-优先”的方式构建。即使你只打算运送一个特设。

你会发现我们已经这样做了。`fetch`组件的`setup`函数可以提取到它自己的函数中，这个函数叫做`useFetch`:

```
export function useFetch(endpoint) {
  // same code as the setup function
}
```

取而代之的是，我们的组件将如下所示:

```
import { useFetch } from '@/fetch';

export default {
  props: {
   // ...
  },
  setup({ endpoint }) {
      const api = useFetch(endpoint);

      return api;
  }
}
```

这种方法提供了一些机会。首先，它允许我们在完全脱离 UI 的情况下思考我们的逻辑。这使得我们的逻辑可以完全用 JavaScript 来表达。它可以在以后与 UI 挂钩，这是`fetch`组件的责任。

其次，它允许我们的`useFetch`函数将自己的逻辑分解成更小的函数。可以把它想象成把相似的东西“组合”在一起，也许通过包含和排除那些较小的特性来创建我们组件的变体。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 打破它

让我们通过将分页逻辑提取到它自己的函数来阐明这一点。问题变成了:我们如何将分页逻辑与获取逻辑分离开来？两者似乎交织在一起。

您可以通过关注分页逻辑的功能来解决这个问题。一个有趣的方法是把它拿走，检查你删除的代码。

目前，它所做的是通过追加一个`page`查询参数来修改`endpoint`，并在公开`next`和`previous`函数的同时维护`currentPage`状态的状态。这就是上一次迭代中所做的事情。

通过创建一个名为`usePagination`的函数，它只执行我们需要的部分，您将得到如下结果:

```
import { ref, computed } from 'vue';

export function usePagination(endpoint) {
  const currentPage = ref(1);
  const paginatedEndpoint = computed(() => {
    return `${endpoint}?page=${currentPage.value}`;
  });

  function nextPage() {
    currentPage.value++;
  }

  function prevPage() {
    if (currentPage.value <= 1) {
      return;
    }

    currentPage.value--;    
  }

  return {
    endpoint: paginatedEndpoint,
    nextPage,
    prevPage
  };
}
```

最棒的是我们对外部消费者隐藏了 ref，这是我最喜欢的组合 API 部分之一。我们可以很容易地对 API 消费者隐藏不重要的细节。

更新`useFetch`以反映该页面很有趣，因为它似乎需要跟踪由`usePagination`公开的新端点。幸运的是，`watch`保护了我们。

我们可以允许参数`endpoint`是一个反应值，而不是一个常规的字符串。这使我们能够观察它，每当分页页面改变时，它将产生一个新的端点值，触发重新获取。

```
import { watch, isRef } from 'vue';

export function useFetch(endpoint) {
  // ...
  function fetchData() {
    // ...

    // If it's a ref, get its value
    // otherwise use it directly
      return fetch(isRef(endpoint) ? endpoint.value : endpoint, {
        // Same fetch opts
      }) // ...
  }

  // watch the endpoint if its a ref/computed value
  if (isRef(endpoint)) {
    watch(endpoint, () => {
      // refetch the data again
      fetchData();
    });
  } 

  return {
    // ...
  };
}
```

请注意，`useFetch`和`usePagination`完全不知道对方，两者都是像对方不存在一样实现的。这为我们的特设小组提供了更大的灵活性。

您还会注意到，通过首先构建复合 API，我们创建了不知道您的 UI 的盲 JavaScript。根据我的经验，这对于正确建模数据非常有帮助，而不用考虑 UI 或让 UI 决定数据模型。

另一件很酷的事情是，我们可以创建两种不同的 HOC 变体:一种允许分页，另一种不允许。这为我们节省了几千字节。

下面是一个只做抓取的例子:

```
import { useFetch } from '@/fetch';

export default {
  setup({ endpoint }) {
    return useFetch(endpoint);
  }
};
```

下面是另一个两者兼而有之的例子:

```
import { useFetch, usePagination } from '@/fetch';

export default {
  setup(props) {
    const { endpoint, nextPage, prevPage } = usePagination(props.endpoint);
    const api = useFetch(endpoint);

    return {
      ...api,
      nextPage,
      prevPage
    };
  }
};
```

更好的是，您可以基于一个道具有条件地应用`usePagination`特性，以获得更大的灵活性:

```
import { useFetch, usePagination } from '@/fetch';

export default {
  props: {
      endpoint: String,
      paginate: Boolean
  },
  setup({ paginate, endpoint }) {
    // an object to dump any conditional APIs we may have
    let addonAPI = {};

    // only use the pagination API if requested by a prop
    if (paginate) {
      const pagination = usePagination(endpoint);
      endpoint = pagination.endpoint;
      addonAPI = {
        ...addonAPI,
        nextPage: pagination.nextPage,
        prevPage: pagination.prevPage
      };
    }

    const coreAPI = useFetch(endpoint);

    // Merge both APIs
    return {
      ...addonAPI,
      ...coreAPI,
    };
  }
};
```

这对你的需求来说可能太多了，但是它允许你的 hoc 更加灵活。否则，它们将是难以维护的僵化的代码体。它也绝对更加单元测试友好。

以下是实际操作的最终结果:

> 由 logaretm 使用 vue

结论

## 总而言之，首先把你的 HOCs 构建成 Composition API。然后，尽可能将逻辑部分分解成更小的可组合功能。在你的 HOCs 中组合它们来暴露最终的结果。

这种方法允许您构建组件的变体，甚至是一个不脆弱且难以维护的变体。通过以组合-API-first 的心态构建，您允许自己编写与 UI 无关的代码的隔离部分。这样，您就让您的 HOC 成为盲 JavaScript 和无功能 UI 之间的桥梁。

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).