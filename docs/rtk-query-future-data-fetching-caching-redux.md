# RTK 查询:Redux 数据获取和缓存的未来

> 原文：<https://blog.logrocket.com/rtk-query-future-data-fetching-caching-redux/>

RTK Query 是 Redux 团队的一个实验库，主要目的是为您的 web 应用程序获取和缓存数据。它在引擎盖下利用 Redux，构建在 [Redux 工具](https://blog.logrocket.com/smarter-redux-with-redux-toolkit/) [k](https://blog.logrocket.com/smarter-redux-with-redux-toolkit/) [it (RTK)](https://blog.logrocket.com/smarter-redux-with-redux-toolkit/) 之上。RTK Query 提供了高级设置选项，以最灵活有效的方式处理您的获取和缓存需求。

虽然 RTK Query 在幕后使用了 Redux，但这并不意味着您需要对 Redux 有很深的理解才能使用它。但是学习 Redux 和 RTK 对于利用 RTK Query 为您的 web 应用程序提供的状态管理功能大有帮助。

## 我们不只是写 Redux，我们也谈论它。现在听着:

或者以后订阅

### 为什么要使用 RTK 查询？

## 今天，RTK Query 仍然处于它的 alpha 阶段，这意味着它的架构和 API 会有很多突破性的变化。尽管如此，它已经为数据缓存和获取提供了一个简单有效的解决方案。

构建 RTK Query 是为了解决使用 Redux 作为状态管理系统时获取数据的需要。RTK Query 可以作为中间件添加，并提供强大的 React 钩子来帮助获取数据。尽管它刚刚起步，但可以肯定地说，当 RTK Query 投入生产时，它将是在各种规模的 JavaScript 应用程序中获取数据的完美候选。

在典型的小型 React 应用程序(没有 Redux)中，您将能够使用集成的`[ApiProvider](https://rtk-query-docs.netlify.app/api/ApiProvider#apiprovider)`获取数据。另一方面，在更大的 app 中(使用 Redux)，你仍然可以在你的商店中插入 RTK Query 作为中间件。

RTK Query 的不可知论使得它很容易与任何能够使用 Redux (Vue.js，Svelte，Angular 等)的框架集成。).请注意，虽然 RTK Query 是不可知论者杜撰的，但它也非常固执己见，遵循 Redux 的既定范式。此外，RTK 查询是用 TypeScript 构建的，因此提供了一流的类型支持。

使用 RTK 查询获取和缓存数据

## 首先:您需要设置您的项目来使用 RTK Query。我们需要创建一个服务定义来获取我们的公共 API。对于这个例子，我们使用了一个[地下城&龙族 API](http://www.dnd5eapi.co/) :

正如[在文档](https://rtk-query-docs.netlify.app/introduction/getting-started#create-an-api-service)中提到的，RTK Query 更喜欢集中其数据获取配置，这在不同的数据获取库中是不太可能的——这是它固执己见的部分原因。集中我们的设置有它的优点和缺点。例如，将我们的获取挂钩放在一起并不是一个坏主意，但是如果使用大量的 API，这可能会变得很麻烦。

```
import { createApi, fetchBaseQuery } from "@rtk-incubator/rtk-query";

// Create your service using a base URL and expected endpoints
export const dndApi = createApi({
  reducerPath: "dndApi",
  baseQuery: fetchBaseQuery({ baseUrl: "https://www.dnd5eapi.co/api/" }),
  endpoints: (builder) => ({
    getMonstersByName: builder.query({
      query: (name: string) => `monsters/${name}`
    })
  })
});

export const { useGetMonstersByNameQuery } = dndApi;

```

下一步是通过添加我们生成的 reducer 和 API 中间件将这个服务添加到我们的商店中。这将打开缓存、预取、轮询和其他 RTK 查询功能。

接下来，您需要包装提供者，就像处理基本 Redux 存储一样，然后您可以使用之前定义的查询钩子在组件中进行查询。

```
export const store = configureStore({
  reducer: { [dndApi.reducerPath]: dndApi.reducer },
  middleware: (getDefaultMiddleware) => getDefaultMiddleware().concat(dndApi.middleware)
});

```

完整的应用程序代码可以在 CodeSandbox 中找到。

```
import * as React from "react";
import { useGetMonstersByNameQuery } from "./services/dnd";
export default function App() {
// Using a query hook automatically fetches data and returns query values
const { data, error, isLoading } = useGetMonstersByNameQuery("aboleth");
return (
  <div className="App">
    {error ? (²²²²²²²²²
    <>Oh no, there was an error</>
  ) : isLoading ? (
    <>Loading...</>
  ) : data ? (
    <>
    <h3>{data.name}</h3>
    <h4> {data.speed.walk} </h4>
    </>
  ) : null}
  </div>
);
}

```

变异数据

### 有时您需要创建或更新数据。RTK Query 以一种标准的方式帮助您做到这一点。提供的`useMutation`钩子返回一个包含触发器函数的元组和一个包含触发器结果的对象。与`useQuery`相反，突变只在触发器被调用时执行。

在一个更高级的设置中，您可能有一个用例，在这个用例中，您需要在执行变异后将本地缓存与服务器同步。这称为重新验证。RTK Query [提供了两个场景](https://rtk-query-docs.netlify.app/concepts/mutations#advanced-mutations-with-revalidation)来处理这个用例，您可以根据您的项目需求决定使用哪一个:

使某个类型的所有内容无效

*   选择性地使列表无效
*   在使用 RTK 查询变异时，您还将拥有实现乐观更新行为所需的所有工具:您可以使用变异的`onStart`阶段，在那里您可以通过`updateQueryResult`手动设置缓存的数据。如果出现错误，您可以使用`onError`和`patchQueryResult`回滚到之前的状态。

缓存在哪里发挥作用？

### [RTK 查询中自动](https://rtk-query-docs.netlify.app/concepts/queries/#avoiding-unnecessary-requests)缓存。如果您的数据发生变化(即无效)，仅针对发生变化的元素，[重新提取](https://rtk-query-docs.netlify.app/concepts/mutations#advanced-mutations-with-revalidation)会自动发生。这是通过 RTK Query 强大的`queryCachedKey`处理的。

一旦发出请求，RTK Query 将序列化参数以提供唯一的`queryCachedKey`。然后在所有将来的请求中验证该密钥，以防止不必要的重新提取。如果你需要检查这个行为，你总是可以调用钩子提供的`refetch`函数。

缓存行为在文档中用一个 CodeSandbox 示例进行了简洁的说明[。这显示了自动获取和缓存特性如何帮助减少请求数量。](https://rtk-query-docs.netlify.app/concepts/queries/#observing-caching-behavior)

查询状态

### 同样重要的是要注意提取查询返回的状态是多么痛苦。RTK Query 公开了请求状态，可以直接在我们的应用程序中使用，如下例所示:

条件提取

```
isUninitialized: false; // Query has not started yet.
isLoading: false; // Query is currently loading for the first time. No data yet.
isFetching: false; // Query is currently fetching, but might have data from an earlier request.
isSuccess: false; // Query has data from a successful load.
isError: false; // Query is currently in "error" state.

```

### 如上所述，`useQuery`自动获取您的数据并处理缓存。RTK Query 提供了一种方法，可以使用布尔`skip`参数来阻止查询自动运行，该参数可以[ad](https://rtk-query-docs.netlify.app/concepts/conditional-fetching#conditional-fetching)ded 到查询挂钩，这将有助于管理这种行为。将`skip`设置为`false` [会强烈影响](https://rtk-query-docs.netlify.app/concepts/conditional-fetching#conditional-fetching)获取和缓存数据的方式。

错误处理

### 错误通过钩子提供的`error`属性返回。RTK Query 希望您以特定的格式返回有效负载(错误或成功),以帮助进行类型推断。

如果您需要编辑您当前的返回格式，您可以使用一个自定义的`baseQuery`，它将帮助您塑造您返回的数据。

```
return { error: { status: 500, data: { message: 'error reasons' } }; 

```

为了帮助您优雅地处理错误，RTK Query 公开了一个`[retry](https://rtk-query-docs.netlify.app/concepts/error-handling#retrying-on-error)`实用程序，您可以用它来包装您的`baseQuery`以创建一个指定尝试次数的指数回退(`maxRetries`)。

此外，RTK Query 还使您能够在宏观层次上[管理您的错误，这可以帮助您记录未完成的异步调用的错误。](https://rtk-query-docs.netlify.app/concepts/error-handling#handling-errors-at-a-macro-level)

投票

### 您还可以通过使用您的`useQuery`钩子上暴露的`pollingInterval`来获得拥有实时服务器的感觉。该参数接受一个以毫秒为单位的数字，它将是您的数据刷新的时间间隔。此外，您还可以手动刷新您的数据。

预取

### 预取就是在实际需要之前获取数据，例如，如果您需要在实际请求之前获取分页数据的下一页。

RTK Query 通过允许您发送两个参数来处理这个问题:`isOlderThan`和`force`。`isOlderThan`将基于布尔值或时间戳运行查询，而`force`将忽略`isOlderThan`参数并运行查询，即使它存在于缓存中。

编码发生器

### 由于它是集中式的，并且 RTK Query 与钩子一起工作，所以在服务文件中编写完整的 API 端点可能会很快变得很麻烦。为了解决这个问题，RTK Query 提供了一个与 OpenAPI 模式一起工作的 [CodeGen](https://github.com/rtk-incubator/rtk-query-codegen) 。

可定制性

## 对于每个 API 客户端库来说，完全可定制是至关重要的；Axios 就是一个很好的例子。这使得开发人员能够处理默认行为、拦截器和身份验证，而无需重复代码。

`createApi`是配置 RTK 查询的主要点。它公开了如下参数:

`baseQuery`，它可以被定制来创建拦截器或模具返回格式

*   `endpoints`，这是您对服务器执行的一组操作
*   这是一个帮助以全局或粒度方式管理重取的实用程序
*   M [更多的](https://rtk-query-docs.netlify.app/api/createapi/)来处理你的 API 调用和 Redux 存储
*   与 react-query 比较

## RTK 查询在使用钩子的方式上类似 react-query。然而，这两个库的方法略有不同。

RTK Query 侧重于利用 Redux 的能力来提供一种更有效的声明性方法来获取数据。它还旨在本质上是不可知论的，紧密依赖于 Redux Toolkit。

它们的缓存策略也大不相同:RTK Query 在数据失效中是声明性的，它的缓存键是通过端点和参数创建的，而 react-query 使用手动缓存键进行失效，并通过用户定义的查询键进行缓存。

RTK Query 提供了更广泛的比较[这里](https://rtk-query-docs.netlify.app/introduction/comparison/#comparison)。

结论

## 当 RTK Query 进入生产阶段时，对于使用 Redux 进行状态管理的团队来说，它肯定会是一个很好的补充。早期迹象显示了巨大的希望——它已经提供了一个简单高效的解决方案。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).