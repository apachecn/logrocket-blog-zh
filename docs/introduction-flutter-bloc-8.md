# Flutter BLoC 8 - LogRocket 博客介绍

> 原文：<https://blog.logrocket.com/introduction-flutter-bloc-8/>

Flutter 是一个相对较新的跨平台软件开发框架，在其短暂的生命周期中发布了大量高质量、受良好支持的开源软件包。

这些包支持的一个 Flutter 领域是状态管理，BLoC 是 Flutter 中最古老的状态管理形式之一，最初于 2019 年底向公众发布。

偶尔，随着 Flutter 的改进，`flutter_bloc`库也会随之发展。

这种趋势一直在持续，随着最新发布的`flutter_bloc`，有一些突破性的变化，需要用户手动升级现有 Flutter 应用内的一些代码[。](https://blog.logrocket.com/whats-new-in-flutter-2-5/)

## `flutter_bloc` 8 突破性变化

很难更新一个包并发现它有大量的迁移需求，更糟糕的是，您必须手动执行这些迁移，这意味着您不能使用任何工具来自动完成。

这是代码维护最丑陋的形式:修复我们认为不是我们造成的问题。当然，如果`flutter_bloc`的维护者不去管事情，只实现改进，意味着我们不需要做任何事情，那会更好，对吗？

一般来说，我就是这么觉得的。有时我不得不为了一个突破性的改变而在不同的版本之间转换，这感觉不值得。

然而，我可以有把握地说,`flutter_bloc`的变化是值得的，并且极大地改善了已经很棒的产品的功能和稳定性。[查看我的视频](https://youtu.be/iXS17TFwEu8)，了解这些变化的简要总结，以及为什么它们是对我们今天的改进。

这些变化使得使用流更加容易，并使应用程序以更加一致和可靠的方式工作。让我们深入了解这些变化是什么样的，以及它们将如何影响你。

## 先前使用`mapEventToState`

`flutter_bloc`实现状态管理的 BLoC 方法的方式非常简单:事件进来，状态出去，这意味着我们将事件发送到我们的 BLoC 中，并产生任何范围的状态(如 loaded、success 或 failure)。

这在`flutter_bloc` 7 中的工作方式如下:

```
enum CounterEvent { increment, decrement }

class CounterBloc extends Bloc<CounterEvent, int> {
  CounterBloc() : super(0);

  @override
  Stream<int> mapEventToState(CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield state - 1;
        break;
      case CounterEvent.increment:
        yield state + 1;
        break;
    }
  }
}

```

在这里，我们的集团只是一个`Stream<int>`，这意味着它可以随着时间的推移不断产生新的价值。而且，因为我们在扩展了`Bloc<CounterEvent, int>`的`CounterBloc`中，所以我们可以访问`state`变量。

这让我们能够适当地对`increment`或`decrement`事件做出响应。

在一个接收事件之后，状态发生了变化，一个新的`int`产生了，在我们的 UI 中看到了这种变化。

这个功能的核心是`mapEventToState`函数，它接收事件并产生新的状态。这是在`flutter_bloc` 8.0.0 中弃用的函数，所以很容易看出这是对软件包的一个核心更改。

那么，`mapEventToState`怎么了？

### 与`mapEventToState`有关的问题

第一，`mapEventToState`很长。我们用了 17 条线来容纳一个简单的计数器块。更复杂的代码段显然比这更长，随着我们的块的长度增长，我们代码的可读性开始受到影响。

其次，`mapEventToState`返回一个`Stream<T>`。很有可能在我们的块中，我们会想要调用同样返回流的其他函数，然后我们必须将函数的返回值放入我们的块中。

这是使用 yield 生成器函数完成的(或者在 Dart 中，它们是标记为`async*`的函数)。使用这些并不是不可能的，但是新用户和经验丰富的用户在没有按预期工作时都会出错。

最后，Dart 实际上有一个非常小的计时问题，它会影响流的工作方式。[说来话长](https://github.com/dart-lang/sdk/issues/44616)，但我们需要关心的是，在`flutter_bloc` 8 中，新的实现不使用流，所以它不再受这个 bug 的影响。

所以让我们看看`flutter_bloc` 8 的发布是如何解决这些问题的。

### `flutter_bloc`引入事件处理程序

随着事件处理程序的引入，我们的块代码现在看起来更像这样:

```
class CounterIncrementPressed extends CounterEvent {}
class CounterDecrementPressed extends CounterEvent {}

class CounterBloc extends Bloc<CounterEvent, int> {
  /// {@macro counter_bloc}
  CounterBloc() : super(0) {
    on<CounterIncrementPressed>((event, emit) => emit(state + 1));
    on<CounterDecrementPressed>((event, emit) => emit(state - 1));
  }
}

```

让我们大声说出明显的变化:

第一，`mapEventToState`没了。相反，我们的事件注册为事件处理程序。我们对这些事件的响应不是产生一个新状态，而是用我们的新状态调用`emit`。

其次，它短了很多。我们只用了 10 行，而不是 17 行。这几乎减少了一半，提高了代码的可读性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这里没有画出通过使用流我们将得到的改进。我们将在下一个例子中讨论这个问题。

## 转换旧的区块模式以使用`flutter_bloc` 8

现在，让我们创建一个使用旧的 BLoC 模式的基本应用程序，并将其转换为适用于`flutter_bloc` 8.0.0。在这个过程中，我们还将看到新的集团模式如何让我们的生活变得更加轻松。

这个应用程序有点难看，但它会帮助我们理解这种新的做事方式。

![Final BLoC Pattern Flutter App With The Long-Running Operation, Streamed Operation, And The Long-Running Streamed Operation](img/79ab3352ddb5480b2f3e46b6d4f9ce41.png)

### 使用`flutter_bloc` 7 创建应用程序

我们通常在我们的块中完成两件事之一:要么执行一个使用`await`获得返回值的长时间运行的操作，要么执行一个使用`Stream<String>`或`Stream<T>`作为结果的长时间运行的操作，通常是为了简单的进度更新。

在我们的块中，适应这些类型请求的代码如下所示:

```
@override
Stream<HomeState> mapEventToState(event) async* {
  if (event is LoadHomeEvent) {
    yield HomeLoadedState();
  }
  if (event is RunLongRunningEvent) {
    yield HomeLoadingState('Running long running operation....');
    final response = await _fakeNetworkService.longRunningOperation();
    yield HomeLoadingState(response);
    await Future.delayed(Duration(seconds: 2));
    yield HomeLoadedState();
  }
  if (event is RunLongRunningStreamedEvent) {
    yield HomeLoadingState('Running long running streamed operation....');
    yield* _fakeNetworkService.longRunningStream().map((event) => HomeLoadingState(event));
    yield HomeLoadedState();
  }
  if (event is RunLongRunningStreamedComplexEvent) {
    yield HomeLoadingState('Running long running streamed operation with complex objects....');
    yield* _fakeNetworkService.longRunningComplexStream().map(
          (event) => HomeLoadingState(event.message, icon: event.icon),
        );
    yield HomeLoadedState();
  }
}
<
```

让我们分解这段代码，了解它是如何工作的。

#### 使用`await`的长时间运行操作

长时间运行操作的代码如下所示:

```
if (event is RunLongRunningEvent) {
  yield HomeLoadingState('Running long running operation....');
  final response = await _fakeNetworkService.longRunningOperation();
  yield HomeLoadingState(response);
  await Future.delayed(Duration(seconds: 2));
  yield HomeLoadedState();
}

```

这很简单；我们的控制流程遵循以下顺序:

1.  立即产生我们的`HomeLoadingState`,因为请求正在进行中
2.  使用我们的假长时间运行的操作并等待结果
3.  当它到达时产生响应
4.  再等 2 秒钟(以便用户可以阅读消息)
5.  最后产生了`HomeLoadedState`

这是在我们的块中产生异步数据的最简单的实现。让我们看看当我们引入流时事情是如何变得更复杂的。

#### 使用`Stream<String>`的长时间运行操作

有时我们的函数会随着时间的推移而让步，而不是返回一个值。也有可能我们自己实现了这个来报告应用程序中单个组件的进度。在这种情况下，我们的块如下所示:

```
if (event is RunLongRunningStreamedEvent) {
  yield HomeLoadingState('Running long running streamed operation....');
  yield* _fakeNetworkService.longRunningStream().map((event) => HomeLoadingState(event));
  yield HomeLoadedState();
}

```

然而，这方面的控制流有点棘手。让我们一步一步来。

首先，我们立即放弃我们的`HomeLoadingState`,因为一个请求正在进行中。然后，通过使用 yield generator 函数(`yield*`函数)连接到我们服务中的流，我们可以将流输出放入我们现有的块流中。

因为我们的块返回`Stream<HomeState>`而我们的服务返回`String`，所以我们必须使用`.map`操作符将服务的数据类型转换成我们的块的数据类型。

最后，我们可以让出`HomeLoadedState`。

现在，你要重读第二步多少次？你的眼睛有点呆滞了吗？而究竟什么是收益率生成器函数呢？我应该担心吗？

如果你有这种感觉，你完全有权利。以这种方式连接流是令人困惑的，并且很容易出错，如果你做错了，你的块将永远停止，永远不会完成。

新手和经验丰富的开发人员经常犯这种错误，这是一个令人沮丧的问题。

我还包含了一个映射复杂对象的例子，也就是我们自己创建的一个类。这与使用`map`操作符和 yield generator 函数的`Stream<String>`示例基本相同。

幸运的是，这个工作流程在`flutter_bloc 8.0.0`中有了显著的改进。让我们看看如何将这段代码迁移到更新版本的`flutter_bloc`。

### 将我们的代码迁移到`flutter_bloc 8.0.0`

我们必须做的第一件事是将我们的`pubspec.yaml`中的`flutter_bloc`包升级到`8.0.0`。它应该是这样的:

![Upgrading The flutter_bloc Package In The CLI](img/2bd6488b93f7f4e4aa9fa2733a90c514.png)

现在，我们可以开始迁移代码了。让我们回到我们的`home_bloc.dart`中，把我们的代码移到做事的✨new way✨。

#### `flutter_bloc` 8 中的`await`的长时间运行操作

因为我们没有更多的`mapEventToState`，我们现在必须设置事件处理程序，并使用我们的事件作为我们注册监听的事件类型。对于我们的第一个示例，我们的块现在看起来像这样:

```
on<RunLongRunningEvent>((event, emit) async {
  emit(HomeLoadingState('Running long running operation...'));
  final response = await _fakeNetworkService.longRunningOperation();
  emit(HomeLoadingState(response));
  await Future.delayed(Duration(seconds: 2));
  emit(HomeLoadedState());
});

```

我们仍然在等待我们的服务，但是我们没有调用`yield`，而是使用传入的`emit`函数将这些新状态发送到我们的块中。

我们真正开始受益于这种新方法的地方是当我们订阅长期运行的流时，所以现在让我们来看看这一点。

### 使用`Stream<String>`和`Stream<T>`的长时间运行操作

还记得我们如何使用收益率生成器功能，而在`mapEventToState`天，事情变得相当混乱吗？这是我们的代码迁移到新的工作方式后的样子:

```
on<RunLongRunningStreamedEvent>((event, emit) async {
  emit(HomeLoadingState('Running long running streamed operation...'));
  await for (final result in _fakeNetworkService.longRunningStream()) {
    emit(HomeLoadingState(result));
  }
  emit(HomeLoadedState());
});

```

我们可以使用`await for`来发出新的状态，因为我们的`Stream`提供了它们。我们不需要使用 yield generator 函数，我们对这部分块的控制流更有逻辑意义。在使用复杂类的流中也实现了类似的好处:

```
on<RunLongRunningStreamedComplexEvent>((event, emit) async {
  emit(HomeLoadingState('Running long running streamed complex operation...'));
  await for (final result in _fakeNetworkService.longRunningComplexStream()) {
    emit(HomeLoadingState(result.message, icon: result.icon));
  }
  emit(HomeLoadedState());
});

```

同样，我们在这里使用我们的`await for`方法来接收事件并将它们发送到我们的块中。不需要产生新的状态，也不需要使用产出生成器，这就更好了。

## 结论

所以，下一个版本的`flutter_bloc`已经准备好[让你更容易在 Flutter](https://blog.logrocket.com/pros-and-cons-of-flutter-app-development/) 中制作应用。它有一些突破性的变化，您必须手动迁移，但最终结果是值得的。

[你今天看到的所有代码都可以在这里](https://github.com/flutterfromscratch/bloc_migration)找到，我已经在 GitHub 中分别用`bloc-v7`和`bloc-v8`标记了提交。您可以在空闲时在提交之间切换，以查看代码在每个版本的`flutter_bloc`之间是如何变化的。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)