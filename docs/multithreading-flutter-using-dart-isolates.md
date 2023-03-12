# 在 Flutter 中使用 Dart 隔离的多线程

> 原文：<https://blog.logrocket.com/multithreading-flutter-using-dart-isolates/>

Flutter 是编写跨平台代码的一个很好的框架，可以在许多设备和生态系统上工作。这类应用程序倾向于使用熟悉的模式，比如从网络上获取数据，将其序列化，并以 UI 的形式向用户显示结果。

在移动平台上工作的开发人员可能意识到，这个流程中的一些昂贵的操作要么需要异步，要么应该在后台线程上运行，因为处理元任务(如事件循环)的主线程过载将导致一些恶作剧行为。

Flutter 框架以`Streams`和`Futures`的形式提供了很好的 API 来构建异步行为，但是这些并不足以完全避免 jank。在本文中，我们将学习如何在 Flutter 中利用多线程在后台运行，并减轻主线程的负担。

我们将讨论以下主题，包括在 Flutter 中创建隔离的两种方法:

## Flutter 中的并发性与异步性

在 Flutter 中，您可以使用`async/await`函数和`Stream`API 来引入异步行为。然而，代码的并发性取决于 Flutter 提供的底层线程基础设施。

### 了解 Flutter 的线程基础设施

首先，Flutter 在 VM 级别维护一组线程池。当我们需要执行某些任务(如网络 I/O)时，会用到这些池。

其次，Flutter 提供了一个不同的并发原语，称为隔离，而不是公开线程。在 Flutter 中，整个 UI 和大部分代码都运行在所谓的根隔离上。

## 什么是颤振隔离？

隔离是线程之上的抽象。它类似于[事件循环](https://en.wikipedia.org/wiki/Event_loop)，但有一些不同:

*   孤立有它自己的存储空间
*   它不能与其他分离物共享可变值
*   隔离区之间传输的任何数据都是重复的

隔离意味着独立于其他隔离运行。这对 Dart VM 有很多好处，其中之一是垃圾收集更容易。

关于创建父隔离区进而创建子隔离区，需要记住的一点是，如果父隔离区终止，子隔离区也会终止。不管层级如何，父隔离不能访问子隔离的存储器。

还有一些通常与分离株相关的成分:

*   A `ReceivePort`:这是隔离用来接收数据的。这种情况的另一个实例也可以由父隔离物用来将数据发送到繁殖的隔离物
*   控制端口:这是一个特殊的端口，允许其所有者拥有暂停或终止隔离的能力
*   `Capability`:这些是用于隔离认证的对象实例，也就是说，每当我们希望发送像`pause`或`terminate`这样的控制端口命令时，我们还需要创建隔离时使用的`Capability`的相应实例，没有这些实例，命令将会失败

## 方法 1:使用`compute`

如上所述，有几种方法可以在 Flutter 中创建隔离。最简单的方法之一是使用`compute`函数。这将在不同的隔离中执行我们的代码，并将结果返回给我们的主隔离。

假设我们有一个名为`Person`的类，我们希望从一个 JSON 对象中反序列化它:

```
class Person {
  final String name;
  Person(this.name);
}

```

我们可以添加如下反序列化代码:

```
Person deserializePerson(String data) {
 // JSON decoding is a costly thing its preferable  
 // if we did this off the main thread
  Map<String, dynamic> dataMap = jsonDecode(data);
  return Person(dataMap["name"]);
}

```

现在，要将它与`compute`函数一起使用，我们需要做的就是:

```
Future<Person> fetchUser() async {
  String userData = await Api.getUser();
  return await compute(deserializePerson, userData);
}

```

这将在内部产生一个隔离，在其中运行解码逻辑，并将结果返回给我们的主隔离。这适用于不频繁或一次性的任务，因为我们不能重用隔离。

## 方法 2:使用`Isolate.spawn`

这种方法是处理分离物的基本方法之一，所以`compute`方法也使用这种方法也就不足为奇了。

下面是我们的反序列化代码的样子:

```
Future<Person> fetchUser() async {
      ReceivePort port = ReceivePort();
      String userData = await Api.getUser();
      final isolate = await Isolate.spawn<List<dynamic>>(
          deserializePerson, [port.sendPort, userData]);
      final person = await port.first;
      isolate.kill(priority: Isolate.immediate);
      return person;
}

void deserializePerson(List<dynamic> values) {
    SendPort sendPort = values[0];
    String data = values[1];
    Map<String, dynamic> dataMap = jsonDecode(data);
    sendPort.send(Person(dataMap["name"]));
}

```

我们应该做的第一件事是创建一个`ReceivePort`的实例。这使我们能够听到分离物的反应。

`spawn`函数接受两个参数:

1.  在新的隔离中调用的回调(在我们的例子中是`deserializePerson`)
2.  `deserializePerson`取的参数

我们将端口和序列化数据组合成一个列表，然后发送出去。接下来，我们使用`sendPort.send`将值返回到主隔离区，并使用`port.first`等待。最后，我们杀死隔离完成清理。

## 重复使用颤振隔离物

虽然前面的例子最适合用于单次任务，但我们可以通过设置两个双向通信端口，发送更多数据进行反序列化，同时监听`port`流以获得结果，从而轻松地重用上面创建的隔离。

为此，我们需要做的只是对我们的反序列化函数进行一些更改:

```
void deserializePerson(SendPort sendPort) {
  ReceivePort receivePort = ReceivePort();
  sendPort.send(receivePort.sendPort);
  receivePort.listen((message) {
    Map<String, dynamic> dataMap = jsonDecode(message);
    sendPort.send(Person(dataMap["name"]));
  });
}

```

正如您所看到的，我们的函数发出的第一项是一个相应的端口，调用函数可以使用它向我们的新隔离区连续发送数据。

注意，`SendPort`支持有限数量的数据类型——包括列表、地图、`SendPort`和`TransferrableTypedData` —除了原始数据类型。

这种方法在我们经常需要在后台执行重复性任务的情况下会很有效，比如从 API 中解码 JSON。通过这些更改，我们可以发送新的响应，并从同一个隔离中获得反序列化的响应。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 探索`flutter_isolate`包

双向通信使我们能够重用隔离，但更多的时候，我们希望实现线程池的等价物，即，实例化一组隔离一次，然后根据需要重用它们。

Flutter 的[隔离包](https://pub.dev/packages/isolate)为我们提供了几个实用工具来帮助实现这一点，其中最有用的是`LoadBalancer` API。这个类让我们创建和管理一个隔离池。当它接收到一个任务时，它会自动将该任务委托给一个自由隔离。

要使用它，我们需要做的就是将包包含在我们的`pubspec.yaml`中，就像这样:

```
 class="language-yaml hljs">dependencies:
  isolate: 2.1.1

```

然后更新我们的`UserService`类来使用 runner:

```
class UserService{
  LoadBalancer? balancer;

  Future<Person> fetchUser() async {
    String userData = await Api.getUser();
    balancer ??= await LoadBalancer.create(5, IsolateRunner.spawn);
    return await balancer!.run(deserializeJson , userData, load: 1);
  }

  Person deserializeJson(String data) {
    Map<String, dynamic> dataMap = jsonDecode(data);
    return Person(dataMap["name"]);
  }
}

```

这里，我们创建了一个由五个分离点组成的池，并在后续解码任务中重用它们。平衡器的`run`函数还带有一个名为`load`的可选参数，它是一个整数，表示解码任务在隔离上的负载。

如果我们希望运行某个任务的计算开销比其他任务大，我们也可以使用这种方法。例如，考虑以下代码:

```
LoadBalancer? balancer;

Future<Person> fetchInitialUsers() async {
  String userData = await Api.getUsers(count: 5);
  balancer ??= await LoadBalancer.create(2, IsolateRunner.spawn);
  return await balancer!.run(deserializeUsers, userData, load: 1);
}

Future<Person> fetchSecondaryUsers() async {
  String userData = await Api.getUsers(count: 15);
  balancer ??= await LoadBalancer.create(2, IsolateRunner.spawn);
  return await balancer!.run(deserializeUsers, userData, load: 3);
}

```

注意我们是如何将`fetchInitialUsers`的负载分配为`1`并将`fetchSecondaryUsers`的负载分配为`3`——这表示一个与被反序列化的用户数量成比例的数字。当我们最初调用`fetchInitialUsers`时，第一个隔离将运行反序列化；同时，如果调用`fetchSecondaryUsers`，负载平衡器将观察到第一个隔离忙于分配给它的负载`1`，并将其转移到第二个隔离。如果它是自由的，负载为`0`，第二个隔离将以负载`3`运行。平衡器确保任何新任务都排队到负载最低的隔离区。

当我们有需要一些隔离的重复任务时，我们可以使用负载平衡器。一个例子是需要根据目标视图尺寸缩小图像的图像加载器，我们可以使用平衡器池来对所有缩小任务进行排队。这防止了它压倒主要的分离物，也避免了与过于频繁地产生新的分离物相关的惩罚。

## 将隔离与流 API 集成

Flutter 提供了一个`asyncMap`操作器来整合我们的[现有流](https://blog.logrocket.com/understanding-flutter-streams/)和分离株。

例如，如果我们对文件中的分块数据进行操作——为了提高内存效率，通常以流的形式进行操作——文件读取流可以以这种方式连接到负载平衡隔离，以便在后台运行代码。

然后，流的接收者可以整理 UI/main isolate 上的数据。这类似于我们在反应式编程中如何切换线程。

我们可以在现有的流中使用它，如下所示。

```
//let's say we have to compute an md5 of a string along with a salt
encryptionSaltStream()
.asyncMap((salt) =>
 // Assuming our load balancer is already created   
    balancer!.run(generateMd5, salt.toString() + userData)
);

```

这将是一个很好的用例，我们希望缩小一组图像。我们可以将 URL 作为一个流发出，读取隔离区中的文件，在后台缩放它们，然后在接收器中收集更新的文件路径。

## 颤振隔离最佳实践

尽管根据我们的需要创建尽可能多的隔离似乎是有益的，但是产生隔离的成本因设备而异。重要的是要理解，当涉及到像图像处理这样的事情时，隔离效果非常好，但是对于更简单的用例来说，其成本有时是不合理的。

另一件要注意的事情是，隔离之间的数据是重复的，这在处理较大的数据集时会产生开销，并且会加重内存负担。为了避免这种情况，Flutter 提供了`TransferrableTypedData`，它充当了一个字节包装器，可以在隔离区之间传输，而没有开销。如果您计划处理隔离区中的大文件，请确保使用此选项。

## 结论

在本文中，我们已经了解了隔离，以及它们如何帮助将并发性引入我们的代码，从而使我们的 UI 线程避免不必要的开销。分析我们的应用程序仍然很重要，因为孤立产卵会吸引相当多的成本，并且可能不是一个好的用例。

您可以[在我创建的 GitHub repo 中探索完整的示例](https://github.com/humblerookie/isolate-demo)。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)