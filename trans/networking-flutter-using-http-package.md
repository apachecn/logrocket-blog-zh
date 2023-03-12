# 使用 http 包在 Flutter 中联网

> 原文：<https://blog.logrocket.com/networking-flutter-using-http-package/>

大多数应用程序必须通过互联网执行网络请求。因此，优雅地处理网络调用以避免 API 调用中不必要的错误非常重要。

在本文中，我们将看看如何使用`http`包在 Flutter 中处理 REST API 请求。

## 入门指南

使用以下命令创建一个新的[颤振](https://blog.logrocket.com/tag/flutter/)项目:

```
flutter create flutter_http_networking

```

您可以使用您最喜欢的 IDE 打开项目，但是对于这个例子，我将使用 VS 代码:

```
code flutter_http_networking

```

将`[http](https://pub.dev/packages/http)`包添加到您的`pubspec.yaml`文件中:

```
dependencies:
  http: ^0.13.3

```

用以下基本结构替换`main.dart`文件的内容:

```
import 'package:flutter/material.dart';
import 'screens/home_page.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Networking',
      theme: ThemeData(
        primarySwatch: Colors.teal,
      ),
      debugShowCheckedModeBanner: false,
      home: HomePage(),
    );
  }
}

```

在查看了执行网络操作的 API 之后，我们将创建`HomePage`。

## 请求 API 数据

对于这个 API 请求的演示，我们将使用来自 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) 的样本`/posts`数据。我们将从使用 GET 请求获取单个 post 数据开始。您必须使用的端点是:

```
GET https://jsonplaceholder.typicode.com/posts/<id>

```

这里，您必须用一个表示您想要检索的文章 ID 的整数值来替换`<id>`。

如果您的请求成功，您将会收到如下示例 JSON 响应:

```
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}

```

## 指定模型类

模型类帮助您打包 API 调用返回的数据，或者使用网络请求发送数据。

我们将定义一个模型类来处理单个 post 数据。您可以使用像 quicktype 这样的 [JSON-to-Dart 类转换工具来轻松生成模型类。将它复制并粘贴到名为`post.dart`的文件中:](https://app.quicktype.io/)

```
class Post {
  Post({
    this.id,
    this.userId,
    this.title,
    this.body,
  });

  int? id;
  int? userId;
  String? title;
  String? body;

  factory Post.fromJson(Map<String, dynamic> json) => Post(
        userId: json["userId"],
        id: json["id"],
        title: json["title"],
        body: json["body"],
      );

  Map<String, dynamic> toJson() => {
        "userId": userId,
        "id": id,
        "title": title,
        "body": body,
      };
}

```

或者，您也可以使用 JSON 序列化并自动生成`fromJson`和`toJson`方法，这有助于防止手动定义时可能出现的任何未被注意到的错误。

如果您使用 JSON 序列化，您将需要以下包:

将它们添加到您的`pubspec.yaml`文件中:

```
dependencies:
  json_annotation: ^4.0.1

dev_dependencies:
  json_serializable: ^4.1.3
  build_runner: ^2.0.4

```

要使用 JSON 序列化，您必须修改`Post`类，如下所示:

```
import 'package:json_annotation/json_annotation.dart';

part 'post.g.dart';

@JsonSerializable()
class Post {
  Post({
    this.id,
    this.userId,
    this.title,
    this.body,
  });

  int? id;
  int? userId;
  String? title;
  String? body;

  factory Post.fromJson(Map<String, dynamic> json) => _$PostFromJson(json);

  Map<String, dynamic> toJson() => _$PostToJson(this);
}

```

您可以使用以下命令触发代码生成工具:

```
flutter pub run build_runner build

```

如果您想让代码生成工具在后台运行—它将自动应用您对模型类所做的任何进一步修改—请使用命令:

```
flutter pub run build_runner serve --delete-conflicting-outputs

```

如果发现任何冲突，`--delete-conflicting-outputs`标志有助于重新生成生成类的一部分。

## 执行 API 请求

现在您可以开始在 REST API 上执行各种网络请求了。为了保持代码的整洁，可以在单独的类中定义与网络请求相关的方法。

创建一个名为`post_client.dart`的新文件，并在其中定义`PostClient`类:

```
class PostClient {
  // TODO: Define the methods for network requests
}

```

在变量中定义服务器的基本 URL 以及所需的端点:

```
class PostClient {
  static final baseURL = "https://jsonplaceholder.typicode.com";
  static final postsEndpoint = baseURL + "/posts";
}

```

我们将在执行请求时使用这些变量。

### 获取数据

您可以使用 GET 请求从 API 中检索信息。要获取单个 post 数据，可以像这样定义一个方法:

```
Future<Post> fetchPost(int postId) async {
  final url = Uri.parse(postsEndpoint + "/$postId");
  final response = await http.get(url);
}

```

这个方法试图根据传递给它的 ID 来检索 post 数据。`http.get()`方法使用 URL 从服务器获取存储在`response`变量中的数据。

通过检查 HTTP 状态代码来验证请求是否成功，如果成功，应该是`200`。您现在可以解码原始 JSON 数据，并使用模型类以一种良好的结构化方式使用`Post.fromJson()`来存储它。

```
Future<Post> fetchPost(int postId) async {
  final url = Uri.parse(postsEndpoint + "/$postId");
  final response = await http.get(url);

  if (response.statusCode == 200) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to load post: $postId');
  }
}

```

### 发送数据

您可以使用 POST 请求向 API 发送数据。我们将通过使用以下方法发送数据来创建一个新帖子:

```
Future<Post> createPost(String title, String body) async {
  final url = Uri.parse(postsEndpoint);
  final response = await http.post(
    url,
    headers: {
      'Content-Type': 'application/json; charset=UTF-8',
    },
    body: jsonEncode({
      'title': title,
      'body': body,
    }),
  );
}

```

发送数据时，必须在`headers`中指定报头类型，并在`body`中指定要发送到指定端点的报头类型。此外，JSON 数据应该使用`jsonEncode`方法以编码格式发送。

您可以使用 HTTP 状态代码检查您的 POST 请求是否成功。如果它返回一个状态代码`201`，那么请求是成功的，我们可以返回 post 数据。

```
Future<Post> createPost(String title, String body) async {
  // ...

  if (response.statusCode == 201) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to create post');
  }
}

```

### 更新数据

您可以使用 PUT 请求更新 API 服务器上的任何 post 信息。像这样定义方法:

```
Future<Post> updatePost(int postId, String title, String body) async {
  final url = Uri.parse(postsEndpoint + "/$postId");
  final response = await http.put(
    url,
    headers: {
      'Content-Type': 'application/json; charset=UTF-8',
    },
    body: jsonEncode({
      'title': title,
      'body': body,
    }),
  );
}

```

这里，我们使用帖子 ID 来指定要更新哪个帖子并将请求发送到哪个帖子。如果响应状态代码是`200`，那么请求是成功的，您可以返回服务器发送的更新后的帖子。

```
Future<Post> updatePost(int postId, String title, String body) async {
  // ...

  if (response.statusCode == 200) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to update post');
  }
}

```

### 删除数据

您可以使用 DELETE 请求从 API 服务器中删除帖子。该方法可以定义如下:

```
Future<Post> deletePost(int postId) async {
  final url = Uri.parse(postsEndpoint + "/$postId");
  final response = await http.delete(
    url,
    headers: {
      'Content-Type': 'application/json; charset=UTF-8',
    },
  );

  if (response.statusCode == 200) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to delete post: $postId');
  }
}

```

我们使用帖子 ID 来指定要删除哪个帖子，并将请求发送到相应的端点。您可以通过检查 HTTP 状态代码是否为`200`来验证请求是否成功。

您应该注意，在删除的情况下，响应返回空数据。

```
Future<Post> deletePost(int postId) async {
  //...

  if (response.statusCode == 200) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to delete post: $postId');
  }
}

```

## 构建用户界面

用户界面将在`HomePage`小部件中定义。它将是一个`StatefulWidget`,因为我们需要在每次网络请求后更新它的状态。

```
import 'package:flutter/material.dart';
import 'package:flutter_http_networking/utils/post_client.dart';
import 'package:http/http.dart' as http;

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold();
  }
}

```

首先，我们将定义两个变量，它们将存储 API 调用返回的帖子的`title`和`body`，然后我们将初始化`PostClient`类:

```
import 'package:flutter/material.dart';
import 'package:flutter_http_networking/utils/post_client.dart';
import 'package:http/http.dart' as http;

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  final PostClient _postClient = PostClient();

  String? _postTitle;
  String? _postBody;

  @override
  Widget build(BuildContext context) {
    return Scaffold();
  }
}

```

我们现在将创建触发网络请求方法的按钮，并用服务器返回的信息更新变量。下面是触发`fetchPost()`方法的代码片段:

```
ElevatedButton(
  onPressed: () async {
    final post = await _postClient.fetchPost(1);
    setState(() {
      _postTitle = post.title;
      _postBody = post.body;
    });
  },
  child: Text('GET'),
)

```

您可以类似地触发其余的网络请求方法。

最终的应用程序用户界面如下所示:

![Example of the final app's UI](img/5a9d34786c63d6435b9f8d6b9d0c5f3a.png)

## 测试网络请求

您可以使用 [Mockito](https://pub.dev/packages/mockito) 在 Dart 中测试 API 请求，这是一个帮助模拟网络请求并测试您的应用程序是否有效处理各种类型的请求(包括空响应和错误响应)的包。

要使用 Mockito 进行测试，请将其添加到您的`pubspec.yaml`文件中，并确保您还设置了`build_runner`和`flutter_test`依赖项:

```
dev_dependencies:
  flutter_test:
    sdk: flutter
  build_runner: ^2.0.4
  mockito: ^5.0.10

```

现在，您必须对想要测试的网络请求方法做一个小小的修改。我们将对`fetchPost()`方法进行修改。

向该方法提供一个`http.Client`，并使用客户端执行`get()`请求。修改后的方法将如下所示:

```
Future<Post> fetchPost(http.Client client, int postId) async {
  final url = Uri.parse(postsEndpoint + "/$postId");
  final response = await client.get(url);

  if (response.statusCode == 200) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Failed to load post: $postId');
  }
}

```

在`test`文件夹中创建一个名为`fetch_post_test.dart`的测试文件，用`@GenerateMocks([http.Client])`注释主函数。

```
import 'package:flutter_http_networking/models/post.dart';
import 'package:flutter_http_networking/utils/post_client.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:http/http.dart' as http;
import 'package:mockito/annotations.dart';
import 'package:mockito/mockito.dart';

import 'fetch_post_test.mocks.dart';

@GenerateMocks([http.Client])
void main() {}

```

这将有助于使用`build_runner`工具生成`MockClient`类。使用以下命令触发代码生成:

```
flutter pub run build_runner build

```

我们将只定义两个测试:一个针对成功的 API 请求，另一个针对出现错误的不成功请求。您可以使用 Mockito 提供的`when()`函数来测试这些条件。

```
@GenerateMocks([http.Client])
void main() {
  PostClient _postClient = PostClient();

  final postEndpoint =
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1');

  group('fetchPost', () {
    test('successful request', () async {
      final client = MockClient();

      when(
        client.get(postEndpoint),
      ).thenAnswer((_) async => http.Response(
          '{"userId": 1, "id": 2, "title": "mock post", "body": "post body"}',
          200));

      expect(await _postClient.fetchPost(client, 1), isA<Post>());
    });

    test('unsuccessful request', () {
      final client = MockClient();

      when(
        client.get(postEndpoint),
      ).thenAnswer((_) async => http.Response('Not Found', 404));

      expect(_postClient.fetchPost(client, 1), throwsException);
    });
  });
}

```

您可以使用以下命令运行测试:

```
flutter test test/fetch_post_test.dart

```

或者，您也可以直接在 ide 中运行测试。当我使用 VS 代码运行测试时，我收到了以下结果:

![Results of the tests when run in VS Code](img/79fc6b472e1f9260efef83a89954985a.png)

## 结论

`http`包有助于在 Flutter 中执行所有类型的网络请求。它还为简化 API 调用测试的 Mockito 提供支持。

如果您想对您的请求进行更高级的控制，那么您可以使用 Dio 包，它有助于避免一些样板代码，并且支持全局配置、拦截器、转换器和其他特性。

感谢您阅读本文！如果你对这篇文章或例子有任何建议或问题，请随时通过 [Twitter](https://twitter.com/sbis04) 或 [LinkedIn](https://www.linkedin.com/in/sbis04/) 与我联系。你可以在我的 GitHub 上找到示例应用[的库。](https://github.com/sbis04/flutter_http_networking)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)