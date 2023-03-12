# 使用 GraphQL 和 Flutter:带示例的教程

> 原文：<https://blog.logrocket.com/using-graphql-with-flutter-a-tutorial-with-examples/>

GraphQL 和 Flutter 在首次发布时都引入了一种新的软件开发风格。GraphQL 使开发人员能够以他们想要的形式和格式获取数据。 [Flutter](https://blog.logrocket.com/tag/flutter/) 使得用一种语言构建一个移动应用程序并为其他平台交叉编译成为可能。

这两项革命性技术的结合开启了另一个可能性的世界。

在本教程中，我们将演示如何在 Flutter 应用程序中使用 GraphQL，包括如何使用 graphql_flutter 插件在 Flutter 应用程序中进行查询、变异和设置订阅。我们还将向您展示如何从 Flutter 应用程序中使用 GraphQL 端点。

以下是我们将要介绍的内容:

## 图 QL 和颤振

由脸书于 2012 年开发，并于 2015 年向公众发布， [GraphQL](https://graphql.org/learn/) 旨在彻底改变从后端提供数据的方式。有了 GraphQL，你可以陈述你想要的数据结构。

例如，假设我们的数据库中有以下表格模型:

```
Food {
    name
    description
    price
    chef
    origin
}

```

我们有一个`Food`的模型。这些字段代表食物的属性:

*   `name`属性是食物的名称，
*   `description`形容食物的全部
*   `price`代表食物的销售价格
*   保存烹饪食物的厨师的名字
*   `origin`陈述食物的历史

利用 REST，我们可以像这样获取食物:

```
/GET
localhost:8080/foods
[
    {
        name: "Joolof Rice",
        desciption: "A spicy food",
        price: "$50",
        chef: "nnamdi",
        origin: "West Africa"
    }
]

```

如你所见，通过 [REST](https://blog.logrocket.com/10-best-practices-for-rest-api-design/) ，每种食物的所有属性都被返回，不管我们是否需要它们。我们可能只需要在前端使用`name`和`price`属性，但是所有的属性都被返回。

GraphQL 帮助我们避免了这种冗余。使用 GraphQL，我们可以声明希望返回的属性，如下所示:

```
query foods {
    food {
        name
        price
    }
}

```

我们告诉服务器，我们只需要来自`Food`表的`name`和`price`属性。它给了我们所需要的一切:

```
{
  "data": [
    {
      "name": "Joolof Rice",
      "price": "$50"
    }
  ]
}

```

GraphQL 现在被成千上万的公司使用。它还有其他优势，使其成为首选，包括:

*   内置缓存和批处理机制
*   单端点结构(因此您不必处理多个 API/端点)
*   内置分页方法

GraphQL 是一种后端技术，而 Flutter 是一种前端 SDK，用于构建移动应用程序。对于移动应用程序，我们从后端获取移动应用程序上显示的数据。由于 GraphQL 是一个后端，我们可以让我们的 Flutter 从 GraphQL 后端获取数据。

构建一个从 GraphQL 后端获取数据的 Flutter 应用程序非常容易。您只需要从 Flutter 应用程序发出一个 HTTP 请求，然后使用返回的数据来设置 UI 并显示它们。

新的 [graphql_flutter](https://pub.dev/packages/graphql_flutter) 插件提供了 API 和小部件，使您能够轻松地从 graphql 后端获取和使用数据。

## 什么是 graphql_flutter？

新的 [graphql_flutter](https://pub.dev/packages/graphql_flutter) 插件提供了 API 和小部件，使您能够轻松地从 graphql 后端获取和使用数据。

顾名思义，graphql_flutter 是 flutter 的 graphql 客户端。它导出可用于从 GraphQL 后端获取数据的小部件和提供程序，包括:

*   `HttpLink` —用于设置后端的端点或 URL
*   `GraphQLClient` —该类用于从 GraphQL 端点获取查询/变异，并连接到 GraphQL 服务器
*   `GraphQLCache` —这个类用于缓存我们的[查询](https://blog.logrocket.com/graphql-queries-in-simple-terms/)和[突变](https://blog.logrocket.com/robust-graphql-mutations-the-relay-way/)。它有选项`store`,我们在缓存操作中将存储类型传递给它
*   `GraphQLProvider` —这个小部件包装了整个 graphql_flutter 小部件，因此它们可以进行查询/变异。要使用的 GraphQL 客户端被传递给这个小部件。这个客户端是这个提供者提供给树中所有小部件的
*   `Query` —这个小部件用于查询 GraphQL 后端
*   `Mutation` —这个小部件用于对 GraphQL 后端进行变异
*   `Subscription` —此小工具用于设置订阅

## 设置 graphql_flutter 和 GraphQLProvider

要使用 graphql_flutter 包，我们必须创建一个 flutter 项目:

```
flutter create flutter_graphql
cd flutter_graphql

```

接下来，安装`graphql_flutter`包:

```
flutter pub add graphql_flutter

```

上面的代码将安装 graphql_flutter 包。这将把包`graphql_flutter`添加到`pubspec.yaml`文件的依赖部分:

```
dependencies:
  graphql_flutter: ^5.0.0

```

要使用小部件，我们必须像这样导入包:

```
import 'package:graphql_flutter/graphql_flutter.dart';

```

首先，在我们开始进行 GraphQL 查询和变异之前，我们必须用`GraphQLProvider`包装我们的根小部件。必须为`GraphQLProvider`的`client`属性提供一个`GraphQLClient`实例。

```
GrpahQLProvider(
    client: GraphQLClient(...)
)

```

`GraphQLClient`提供了 GraphQL 服务器 URL 和缓存机制。

```
final httpLink = HttpLink(uri: "http://10.0.2.2:4000/");

ValueNotifier<GraphQLClient> client = ValueNotifier(
    GraphQLClient(
        cache: InMemoryCache(),
        link: httpLink
    )
);

```

GraphQL 服务器的 URL 是使用`HttpLink`创建的。在一个`link`属性中，`HttpLink`的实例被传递给`GraphQLClient`,该属性告诉 GraphQL 客户端 graph QL 端点的 URL。

传递给 GraphQLClient 的`cache`告诉它要使用的缓存机制。`InMemoryCache`实例利用内存中的数据库来持久化或存储缓存。

`GraphQLClient`的实例被传递给一个`ValueNotifier`。这个`ValueNotifer`用于保存单个值，并具有在单个值改变时发出通知的侦听器。当来自 graphql 端点的数据发生变化时，graphql flutter 使用它来通知它的小部件，这有助于保持 graphql flutter 的反应性。

现在，我们将用`GraphQLProvider`包装我们的`MaterialApp`小部件:

```
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return GraphQLProvider(
        client: client,
        child: MaterialApp(
            title: 'GraphQL Demo',
            theme: ThemeData(
                primarySwatch: Colors.blue,
            ),
            home: MyHomePage(
                title: 'GraphQL Demo'
            ),
        )
    );
  }
}

```

## 问题

为了使用`graphql_flutter`包进行查询，我们将使用`Query`小部件。

```
class MyHomePage extends StatelessWidget {
    @override
    Widget build(BuildContext) {
        return Query(
            options: QueryOptions(
                document: gql(readCounters),
                variables: {
                'counterId': 23,
                },
                pollInterval: Duration(seconds: 10),
            ),
            builder: (QueryResult result, { VoidCallback refetch, FetchMore fetchMore }) {
                if (result.hasException) {
                    return Text(result.exception.toString());
                }

                if (result.isLoading) {
                    return Text('Loading');
                }

                // it can be either Map or List
                List counters = result.data['counter'];

                return ListView.builder(
                itemCount: repositories.length,
                itemBuilder: (context, index) {
                    return Text(counters\[index\]['name']);
                });
            },
        )
    }
}

```

这里，`Query`小部件包含了`ListView`小部件，我们将在其中呈现从 GraphQL 服务器获取的计数器列表。所以`Query`小部件必须在您想要显示由`Query`小部件获取的数据的地方包装小部件。

`Query`小部件不能是树中最顶层的小部件。它可以放在任何其他地方，只要将使用其数据的小部件在它下面或被它包装。

此外，`Query`小部件传递了两个属性:`options`和`builder`。

### `options`

```
options: QueryOptions(
    document: gql(readCounters),
    variables: {
    'conuterId': 23,
    },
    pollInterval: Duration(seconds: 10),
),

```

`option`属性是将查询的配置传递给`Query`小部件的地方。这个`options`道具是`QueryOptions`的一个实例。`QueryOptions`类公开了我们用来为`Query`小部件设置选项的属性。

`document`属性用于设置查询字符串或传递我们希望`Query`小部件执行的查询。在这里，我们传入了`readCounters`字符串:

```
final String readCounters = """
    query readCounters(\$counterId: Int!) {
        counter {
            name
            id
        }
    }
""";

```

`variables`属性是查询变量被发送到`Query`小部件的地方。我们那里有`'counterId': 23,`。这将代替`readCounters`查询字符串中的`$counterId`被传递。

`pollInterval`是小部件`Query`轮询或刷新查询数据的时间间隔。时间设置为 10 秒，因此每 10 秒钟后，`Query`小部件将执行 HTTP 请求来刷新查询数据。

### `builder`

`builder`属性是一个函数。当`Query`小部件向 GraphQL 服务器端点发出 HTTP 请求时，调用该函数。`Query`小部件使用来自查询的数据调用`builder`函数，一个函数用于重新提取数据，一个函数用于分页。这用于获取更多数据。

`builder`函数返回`Query`小部件下面的小部件。`result`参数是`QueryResult`的一个实例。`QueryResult`有一些属性，我们可以用它们来了解查询的状态和由`Query`小部件返回的数据。

*   如果查询遇到错误，则设置`QueryResult.hasException`。
*   如果查询仍在进行中，则设置`QueryResult.isLoading`。我们可以使用这个属性向用户显示 UI 进度，告诉他们正在进行中
*   `QueryResult.data`保存由 GraphQL 端点返回的数据

## 突变

让我们看看如何使用 graphql_flutter 中的`Mutation`小部件进行变异查询。

`Mutation`小部件的用法如下:

```
Mutation(
  options: MutationOptions(
    document: gql(addCounter),
    update: (GraphQLDataProxy cache, QueryResult result) {
      return cache;
    },
    onCompleted: (dynamic resultData) {
      print(resultData);
    },
  ),
  builder: (
    RunMutation runMutation,
    QueryResult result,
  ) {
    return FlatButton(
      onPressed: () => runMutation({
        'counterId': 21,
      }),
      child: Text('Add Counter')
    );
  },
);

```

就像`Query`小部件一样，`Mutation`小部件接受一些属性。

*   `options`是`MutationOptions`类的一个实例。这是突变字符串和其他配置发生的地方
*   `document`用于设置突变字符串。这里我们有一个`addCounter`突变传递给了`document`。它将由`Mutation`小部件运行
*   当我们想要更新缓存时，就会调用`update`。用之前的缓存(`cache`)和变异的结果`result`调用`update`函数。从`update`返回的任何东西都成为缓存的新值。这里，我们根据结果更新缓存
*   当 GraphQL 端点上的突变被调用时，调用`onCompleted`。然后用变异结果调用`onCompleted`函数
*   `builder`用于返回`Mutation` widget 树下的 widget。这个函数由一个`RunMutation`实例`runMutation`和一个`QueryResult`实例`result`调用。
*   `runMutation`用于运行这个`Mutation`小部件中的变异。无论何时被调用，`Mutattion`小部件都会触发变异。这个`runMutation`函数将突变变量作为参数传递。这里，`runMutation`是用`counterId`变量`21`调用的

现在，当从`Mutation`开始的突变完成时，调用`builder`，因此`Mutation`重建它的树。`runMutation`并且突变的结果被传递给`builder`函数。

## 捐款

GraphQL 中的订阅就像一个事件系统监听一个 [WebSocket](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/) 并在事件被发送到流中时调用一个函数。

WebSocket 从客户端打开到 GraphQL 服务器。每当服务器从其一端发出一个事件，该事件就被传递给 WebSocket。所以这是实时的东西。

在 Flutter 中，`graphql_flutter`插件利用 WebSockets 和 [Dart](https://blog.logrocket.com/introduction-to-using-dart-in-flutter/) 流打开并提供来自服务器的实时更新。

graphql_flutter 有一个`Subscription`小部件，我们可以用它来打开到 graphql 服务器的实时连接和通信。

让我们看看如何使用`Subscription`小部件在我们的 Flutter 应用程序中建立实时连接。首先，我们定义订阅字符串:

```
final counterSubscription = '''
subscription counterAdded {
    counterAdded {
        name
        id
    }
}
''';

```

当一个新的计数器添加到我们的 GraphQL 服务器时，这个订阅将为我们提供实时更新。

```
Subscription(
    options: SubscriptionOptions(
        document: gql(counterSubscription),
    ),
    builder: (result) {
        if (result.hasException) {
            return Text("Error occured: " + result.exception.toString());
        }

        if (result.isLoading) {
            return Center(
                child: const CircularProgressIndicator(),
            );
        }

        return ResultAccumulator.appendUniqueEntries(
            latest: result.data,
            builder: (context, {results}) => ...
        );
    }
),

```

我们看到`Subscription`小部件有许多属性:

`options`保存`Subscription`小部件的配置`document`保存订阅字符串`builder`返回`Subscription`小部件的小部件树。

使用订阅的结果调用`builder`函数。`result`有一些有用的属性:

如果`Subscription`小部件在轮询来自 GraphQL 服务器的更新时遇到错误，则`result.hasException`被设置；如果来自服务器的轮询正在进行中，则`result.isLoading`被设置

我们接到了这个电话。根据 [graphql_flutter 的 pub.dev 页面](https://pub.dev/packages/graphql_flutter) , `ResultAccumulator`是一个用于整理订阅结果的辅助小部件。

## 结论

我们在这个 Flutter 和 GraphQL 教程中讨论了很多。我们首先简单介绍了 GraphQL 是什么以及它是如何工作的。然后，我们介绍了 graphql_flutter，并用例子演示了如何从 flutter 应用程序中进行查询、突变和订阅。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.