# Flutter FutureBuilder 的异步回调

> 原文：<https://blog.logrocket.com/async-callbacks-with-flutter-futurebuilder/>

在很多情况下，我们需要异步构建一个小部件来反映应用程序或数据的正确状态。一个常见的例子是从 REST 端点获取数据。

在本教程中，我们将使用 [Dart 和 Flutter](https://blog.logrocket.com/introduction-to-using-dart-in-flutter/) 来处理这类请求。Dart 是一种单线程语言，它利用事件循环来运行异步任务。然而，在 Flutter 中构建方法是同步的。

我们开始吧！

## Dart 事件循环

一旦有人打开一个应用程序，许多不同的事件会以不可预测的顺序发生，直到该应用程序被关闭。每次事件发生时，它都会进入一个队列，等待处理。Dart 事件循环检索队列顶部的事件，对其进行处理，并触发回调，直到队列中的所有事件都完成。

Dart 中的 [`Future`和`Stream`类](https://dart.dev/tutorials/language/streams)以及 [`async` 和`await` 关键字](https://dart.dev/codelabs/async-await)都是基于这个简单的循环，使得异步编程成为可能。在下面的代码片段中，用户输入使用回调来响应按钮小部件上的交互:

```
ElevatedButton(
  child: Text("Hello Team"),
  onPressed: () {
    const url = 'https://majidhajian.com';
    final myFuture = http.get(url);
    myFuture.then((response) {
      // (3)
      if (response.statusCode == 200) {
        print('Success!');
      }
    });
  },
)

```

### `ElevatedButton`小工具

[`ElevatedButton`小部件](https://api.flutter.dev/flutter/material/ElevatedButton-class.html)提供了方便的参数来响应被按下的按钮。一旦触发了`onPressed`事件，它就在队列中等待。当事件循环到达该事件时，将执行匿名函数，过程继续。

既然我们已经了解了 Dart 中异步编程的工作原理，我们就理解了 Flutter 背后的秘密。现在，我们可以处理 [`future`请求](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html)并构建我们的 Flutter 小部件。

由于 Flutter 中的`build`方法是同步运行的，我们需要找到一种方法来确保应用程序将基于未来接收到的数据来构建小部件。

### `StatefulWidget`

一种方法是使用`[StatefulWidget](http://%E2%80%8B%E2%80%8Bhttps://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)`并在获得信息时设置状态:

```
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
Future<String> fetchName() async {
  final Uri uri = Uri.https('maijdhajian.com', '/getRandonName');
  final http.Response name = await http.get(uri);
  return jsonDecode(name.body);
 }
class MyFutureWidget extends StatefulWidget {
  @override
  _MyFutureWidgetState createState() => _MyFutureWidgetState();
}
class _MyFutureWidgetState extends State<MyFutureWidget> {
  String? value;
  @override
  void initState() {
    super.initState();

    // fetchName function is a asynchronously to GET http data
    fetchName().then((result) {
      // Once we receive our name we trigger rebuild.
      setState(() {
        value = result;
      });
    });
  }
  @override
  Widget build(BuildContext context) {
    // When value is null show loading indicator.
    if (value == null) {
      return const CircularProgressIndicator();
    }
    return Text('Fetched value: $value');
  }
}

```

在这个例子中，您可能已经注意到我们没有正确处理可能的异常，我们可以通过添加一个`error`变量来解决这个问题。上述过程将工作，但我们可以对它进行改进。

FutureBuilder 提供了一种更干净、更好的方式来处理 Flutter 中的`future`。`FutureBuilder`接受一个`future`并在数据被解析时构建一个小部件:

```
const FutureBuilder({ 
    Key? key, 
    this.future, 
    this.initialData, 
    required this.builder, 
  }) : assert(builder != null), super(key: key);

```

让我们仔细看看`FutureBuilder`小部件是如何工作的:

```
FutureBuilder<String>(
  future: FUTURE,
  intialData: null, 
  builder: (BuildContext context, AsyncSnapshot<String> snapshot) {

  }
);

```

`build`函数中的第二个参数是具有指定数据类型的`[AsyncSnapshot](https://api.flutter.dev/flutter/widgets/AsyncSnapshot-class.html)`类型。例如，在上面的代码中，我们定义了`String`。

快照是与异步计算的最近交互的不可变表示。它有几个属性。当异步计算发生时，了解当前连接的状态是有益的，这可以通过`snapshot.connectionState`实现。

`[connectionState](https://api.flutter.dev/flutter/widgets/ConnectionState-class.html)`有四种常见的流程:

1.  可能有一些初始数据
2.  `waiting`:异步运行已经开始。数据通常为空
3.  `active`:数据不为空，并且有可能随时间而改变
4.  `done`:数据非空

`snapshot.data`返回最新的数据，`snapshot.error`返回最新的错误对象。`snapshot.hasData`和`snapshot.hasError`是两个方便的获取器，用于检查是否接收到错误或数据。

`FutureBuilder`是使用状态作为快照的`StatefulWidget`。查看 [`FutureBuilder`源代码](https://github.com/flutter/flutter/blob/279f40d4877fe74d70b5d483088f054f0226bc89/packages/flutter/lib/src/widgets/async.dart#L745)，我们可以认出下面代码片段中显示的初始快照:

```
   _snapshot = widget.initialData == null
        ? AsyncSnapshot<T>.nothing()
        : AsyncSnapshot<T>.withData(ConnectionState.none, widget.initialData as T);

```

我们发送一个小部件订阅的`future`,根据它更新状态:

```
  void _subscribe() {
    if (widget.future != null) {
      final Object callbackIdentity = Object();
      _activeCallbackIdentity = callbackIdentity;
      widget.future!.then<void>((T data) {
        if (_activeCallbackIdentity == callbackIdentity) {
          setState(() {
            _snapshot = AsyncSnapshot<T>.withData(ConnectionState.done, data);
          });
        }
      }, onError: (Object error, StackTrace stackTrace) {
        if (_activeCallbackIdentity == callbackIdentity) {
          setState(() {
            _snapshot = AsyncSnapshot<T>.withError(ConnectionState.done, error, stackTrace);
          });
        }
      });
      _snapshot = _snapshot.inState(ConnectionState.waiting);
    }
  }

```

当我们处置小部件时，它取消订阅:

```
@override
void dispose() {
  _unsubscribe();
  super.dispose();
}

void _unsubscribe() {
  _activeCallbackIdentity = null;
}

```

让我们重构上面的例子，使用`FutureBuilder`:

```
class MyFutureWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: getName(),
      builder: (BuildContext context, AsyncSnapshot snapshot) {
        if (snapshot.connectionState == ConnectionState.waiting) {
          return CircularProgressIndicator();
        }

        if (snapshot.hasData) {
          return Text(snapshot.data);
        }

        return Container();
      },
    );
  }
}

```

注意，我在`build`方法中的`FutureBuilder`中直接使用了`getName()`函数。
每次重建`FutureBuilder`的父进程时，异步任务都会重新启动，这不是一个好的做法。

通过尽早移动要获取的`future`来解决这个问题——例如，在`StatefulWidget`上的`initState`期间:

```
class MyFutureWidget extends StatefulWidget {
  @override
  _MyFutureWidgetState createState() => _MyFutureWidgetState();
}

class _MyFutureWidgetState extends State<MyFutureWidget> {
  Future<String> _dataFuture;

  @override
  void initState() {
    super.initState();
    _dataFuture = getName();
  }

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: _dataFuture,
      builder: (BuildContext context, AsyncSnapshot snapshot) {
        if (snapshot.connectionState == ConnectionState.waiting) {
          return CircularProgressIndicator();
        }

        if (snapshot.hasData) {
          return Text(snapshot.data);
        }

        if (snapshot.hasError) {
          return Text('There is something wrong!');
        }

        return SizedBox();
      },
    );
  }
}

```

每次创建小部件时都会调用`initState()`。因此，`getName future`函数将被存储在一个变量中。虽然我的小部件每次都可以改变状态并重新构建，但我的数据将保持不变。

还值得看一看另一个处理`stream`的小部件`[StreamBuilder](https://api.flutter.dev/flutter/widgets/StreamBuilder-class.html)`。`StreamBuilder`和`FutureBuilder`几乎一模一样。然而，`StreamBuilder`周期性地传送数据，所以你必须比`FutureBuilder`听得更频繁，因为后者你必须只听一次。

`StreamBuilder`小部件自动订阅和取消订阅`stream`。当处理一个小部件时，您不必担心取消订阅，这可能会导致内存泄漏:

```
@override
  Widget build(BuildContext context) {
    return StreamBuilder<String>(
      stream: dataStream,
      builder: (BuildContext context, AsyncSnapshot<String> snapshot) {

      },
    );
  }

```

## 结论

在本教程中，您已经学习了如何在 Flutter 中执行异步回调来从 REST 端点获取数据。异步编程是节省开发人员时间和精力的强大力量。Flutter 提供了进一步简化过程的独特工具。

使用`FutureBuilder`和`StreamBuilder`构建小部件是使用 Dart 和 Flutter 构建 UI 的一个重要好处。希望现在您已经理解了这两个小部件在 Dart 事件循环中的基本工作原理。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)