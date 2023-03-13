# 使用 GraphQL 构建 Android 应用程序

> 原文：<https://blog.logrocket.com/building-android-app-graphql/>

在经典的客户端-服务器架构中，Android 应用程序可以充当前端客户端，依赖来自后端服务的数据。通常，开发人员依靠 [REST 和 GraphQL](https://blog.logrocket.com/graphql-vs-grpc-vs-rest-choosing-right-api/) web 规范来促进这种架构中的通信。

在本文中，我们将讨论 GraphQL 以及它优于 REST 的地方，最后，我们将编写一些代码来将 Android 应用程序连接到 GraphQL 服务器。

*向前跳转:*

## GraphQL 是什么？

GraphQL 是一种查询语言，用于从后端服务获取和变更数据。当与 GraphQL 服务交互时，只有一个入口点(URL)用于检索和变更数据。你可能会问，这怎么可能？嗯，有了 [GraphQL 查询](https://blog.logrocket.com/graphql-queries-in-simple-terms/)，你可以做任何简单的事情。

在向您展示一个示例查询之前，让我们先来讨论三个 GraphQL 操作符及其用法。GraphQL 操作符用于在服务器上执行 [CRUD 请求](https://blog.logrocket.com/configuring-cloud-firestore-in-adonisjs-for-crud-requests/)，并监听事件。GraphQL 中有三个主要的操作符: [`Query`、`Mutation`和`Subscription`](https://blog.logrocket.com/graphql-vs-grpc-vs-rest-choosing-right-api/#:~:text=values%20when%20queried.-,Defining%20queries%2C%20mutations%2C%20and%20subscriptions%20with%20schemas,-As%20mentioned%20before) :

*   `Query`:这对于从服务器获取数据很有用
*   `Mutation`:该操作符用于创建、编辑和删除服务器上的数据
*   这个操作符并不出现在普通的用例中。但是，在实现客户机和服务器之间的实时通信时，它会很方便

### GraphQL 的优势

首先，GraphQL 解决了过度提取和提取不足的问题。GraphQL 优于 REST 等其他 web 规范，因为客户端只能获取它需要的数据。

如果事情听起来仍然模糊不清，不要担心——当我们编写一些代码时，你会有所顿悟😊。

## 设置起始项目

为了让事情变得更有趣，我们将使用 [GitHub GraphQL API](https://docs.github.com/en/graphql) 重构一个 GitHub 项目。

以下是我们将要重构的项目的一些注意事项:

### 克隆项目

继续克隆这个 GitHub Lagos 项目示例应用程序，如下所示:

```
git clone https://github.com/Lamouresparus/GithubLagosProject.git

```

如果克隆成功，在 [Android Studio](https://developer.android.com/studio) 中打开项目并运行。如果一切正常，您将看到与下面截图中相同的内容:

![Building an Android App With GraphQL](img/fd2e44a7bd5a8efb6e447d13ca17cff0.png)

### 添加必要的梯度依赖项

```
implementation("com.squareup.okhttp3:logging-interceptor:4.10.0")
implementation("com.apollographql.apollo3:apollo-runtime:3.6.2")

```

需要`Logging`拦截器来帮助将网络流量打印到`Logcat`窗口。第二个依赖项， [Apollo](https://blog.logrocket.com/graphql-local-state-management-apollo/) ，是一个在 Android 上使用 GraphQL APIs 的库。Apollo 之于 GraphQL，就像 return 之于 REST 一样。

## 从 REST 迁移到 GraphQL

迁移可能是一项令人生畏的任务，尤其是如果您是一项技术的新手，并且在这项工作中有许多接触点。然而，在代码库结构良好和模块化的情况下，迁移变得不那么复杂和危险。您可以安全地定位和更新组件，而不会影响整个系统。

在接下来的几个步骤中，我将向您展示如何迁移我们的项目以使用 GitHub GraphQL API。

### 用`ApolloClient`替换改装参考

为了使用`ApolloClient`与 GitHub GraphQL API 通信，我们将需要进行迁移。首先，在`com.githublagos.di`中找到`NetworkModule`,用以下代码更新文件:

```
@Module
@InstallIn(SingletonComponent::class)
class NetworkModule {

    @Provides
    @Singleton
    fun okhttpClient(): OkHttpClient {
       val logging = HttpLoggingInterceptor()
           .apply { level = HttpLoggingInterceptor.Level.BODY  }
       return OkHttpClient.Builder()
           .addInterceptor(AuthInterceptor())
           .addInterceptor(logging)
           .build()
    }

    @Provides
    @Singleton
    fun provideApollo(client: OkHttpClient) : ApolloClient {
        return ApolloClient.Builder()
            .serverUrl("https://api.github.com/graphql")
            .okHttpClient(client).build()
    }
}

```

从上面的代码块来看，有两种对象类型:`OkHttpClient`和`ApolloClient`。这些被声明为依赖项，将在运行时通过带有句柄的[依赖项注入](https://blog.logrocket.com/kotlin-dependency-injection-koin-vs-hilt/)来提供。

`OkHttpClient`是一个 HTTP 引擎，由`ApolloClient`用于发出网络请求，而`ApolloClient`本身是一个库，用于向 GraphQL 服务器发起网络请求。

请注意我是如何用客户拦截器(`AuthInterceptor`)定制`OkHttpClient`的。我包含了拦截器来修改 HTTP 请求，使其包含一个授权头。

[GitHub GraphQL 服务器](https://docs.github.com/en/graphql/guides/forming-calls-with-graphql#authenticating-with-graphql)要求每个请求都使用正确的作用域进行认证。请参见下面不言自明的代码块:

```
class AuthInterceptor : Interceptor {
    override fun intercept(chain: Interceptor.Chain): Response {
        val request = chain.request().newBuilder()
            .addHeader("Authorization", "token put_your_personal_access_token_here")

       return chain.proceed(request.build())
    }
}

```

### 定义 GraphQL 模式和`Queries`

[GraphQL 模式](https://blog.logrocket.com/3-annotations-to-use-in-your-graphql-schema/)定义了 GitHub 的 API 支持的所有对象类型。此模式中的对象类型用于构建发出网络请求的查询。按照以下步骤将模式包含在项目中:

*   在`app/src/main`下创建一个`graphql`文件夹
*   下载 [GitHub 模式](https://docs.github.com/public/schema.docs.graphql)
*   将您下载的模式移动到我们在第一步中创建的文件夹中

现在，我们将构建我们的`query`来指定我们希望服务器返回给我们的数据。在同一个`GraphQL`文件夹中，创建一个`GetUsers.graphql`文件并粘贴到下面的代码块中:

```
query GetUsers($query: String!, $pageSize:Int!, $after: String) {
    search(query: $query, type: USER, first: $pageSize, after: $after) {
        nodes {
            ... on User {
                avatarUrl
                login
                url
            }
        }
        pageInfo {
            endCursor
            hasNextPage
        }
    }
}

```

简而言之，上面的代码块是一个查询，将用于从 GitHub GraphQL 服务器请求用户列表。我们将在下面检查这个查询的每个组成部分。

`GetUsers`是接受三个参数的查询:

*   `Query String`:这可以是像`location`、`language`等查询，以帮助缩小搜索结果的范围，满足我们的特定需求
*   `Page Size`:这是我们希望服务器在每次搜索查询中返回的用户数量
*   `After String`:这个字符串有助于在使用 GitHub GraphQL 服务器时实现[分页](https://blog.logrocket.com/ux-design/pagination-vs-infinite-scroll-ux/)。该值可以是最后一个用户对象的游标 ID。通常，对于初始搜索查询，我们将其设置为`null`

`nodes`基本上是一个搜索项列表，在我们的 GitHub 模式中定义为`[SearchResultItem]`。`SearchResultItem`是一个联合类型，它属于模式中定义的其他几种类型。在我们的例子中，我们对`User`类型感兴趣。在`User`对象上的尾随语法是我们如何指定我们感兴趣的`SearchResultItem`类型。

`pageInfo`请求基本页面信息。我们将使用返回的数据来实现分页。`endCursor`是当前页面最后一个`User`对象的光标 ID。`hasNextPage`是一个布尔值，它评估是否有另一个用户页面。

现在，我们已经定义了一个 GraphQL 文件，但是我们不能在 Kotlin 代码中直接使用它。我们必须为代码生成重新构建项目，并创建文件的 Kotlin 表示。如果 Gradle 构建成功，您会发现在您的构建目录中创建了`GetUsersQuery`:

![Example of the GetUser File in Android App With GraphQL](img/a0cc6f2e8f066a8ff925a39203922c60.png)

### 重构 GitHub API

在前面的步骤中，我们创建了`GetUsersQuery`和`ApolloClient`的[单例实例](https://blog.logrocket.com/youre-wrong-about-singletons/)。这些对象将用于在 GitHub GraphQL 服务器上执行搜索。将以下代码复制到您的 IDE 中:

```
class GithubApi @Inject constructor(
    private val apolloClient: ApolloClient
) {

    // const val QUERY_LOCATION = "location:lagos"

    suspend fun getUsers(
        location: String = QUERY_LOCATION,
        endCursor: String?,
        pageSize: Int
    ): UsersResponseModel {
        val nextCursor = Optional.presentIfNotNull(endCursor)

        val query = GetUsersQuery(location, pageSize, nextCursor)
        val response = apolloClient.query(query).execute()

        val cursor = response.data?.search?.pageInfo?.endCursor

        val users = response.data?.search?.nodes?.map {
            val userName = it?.onUser?.login.orEmpty()
            val avatar = it?.onUser?.avatarUrl.orEmpty()
            val htmlUrl = it?.onUser?.url.orEmpty()
            UserRemote(userName, avatar, htmlUrl)
        }?.filter { it.login.isNotEmpty() }.orEmpty()

        return UsersResponseModel(cursor, users)
    }
}

```

在上面的代码块中，我将`GithubApi`从一个接口重构为一个接受`ApolloClient`作为参数的类类型。这个对象负责执行`queries`、`mutations`和`subscriptions`。

`getUsers`的`method`签名接受一个可空的`endCursor`参数，使其成为可选的。在 Apollo GraphQL 中，我们使用一个`Optional`类型来表示一个可选值。这个对象是一个 [Kotlin 密封的类](https://blog.logrocket.com/guide-using-sealed-classes-kotlin/)，它可以是`Present`或`Absent`。

通过一行代码，我们可以执行一个 GraphQL 查询，如下所示:`apolloClient.query(query).execute()`。

这将完成所有繁重的工作，返回一个包含 GitHub 用户列表和页面信息的响应。

### 更新`GithubUserSource`

如果你能走到这一步，恭喜你！这是重构的最后一部分。我们希望分页源代码依赖于 Apollo GraphQL 来根据滚动请求执行多个查询。

首先，定位`GithubUserSource`并用下面的代码块修复突变:

```
class GithubUserSource @Inject constructor(private val api: GithubApi) :
    PagingSource<String, UserDomain>() {

    override fun getRefreshKey(state: PagingState<String, UserDomain>): String? {
        return null
    }

    override suspend fun load(params: LoadParams<String>): LoadResult<String, UserDomain> {
        return try {
            val previousKey = params.key
            val result  = api.getUsers(pageSize = PAGE_SIZE, endCursor = previousKey )
            val nextKey = result.endCursor
            val userList = result.items.map { it.mapToDomain() }
            LoadResult.Page(
                data = userList,
                prevKey = previousKey,
                nextKey = nextKey
            )
        } catch (exception: IOException) {
            return LoadResult.Error(exception)
        } catch (exception: HttpException) {
            return LoadResult.Error(exception)
        } catch (exception :Exception){
            return LoadResult.Error(exception)
        }
    }
}

```

前者和新实现的主要区别在于`key`类型。因为我们使用最后一个用户对象的光标来方便我们的分页，所以我们将`PagingSource`定义为有一个`string`键，而不是一个`integer`。

当请求将更多内容加载到`listview`时，调用`load()`方法。每当这个调用发生时，我们从服务器获取用户，指定`page size`和`end cursor`作为参数。

在第一次调用时，`previousKey`将是`null`,因为在这种情况下，在分页源中没有缓存`nextKey`。如果操作成功，我们将得到一个包含用户信息和`end cursor`的响应。这个响应被映射到一个`LoadResult.Page`对象，并被提供给分页源。

瞧啊。我们已经使用`ApolloClient`将一个基于 REST 的 Android 项目重构为 GraphQL。现在，您应该能够成功地构建和运行项目了。用户的屏幕上不会有可见的变化，因为我们只改变了数据源实现。

## 结论

从我作为移动开发者的角度来看，GraphQL 在 Android 上很容易使用。我能想到的唯一学习曲线是查询语言的语法。除此之外，简直易如反掌！GraphQL [文档](https://graphql.org/learn/)是了解更多信息的好地方。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)