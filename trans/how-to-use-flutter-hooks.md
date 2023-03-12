# 如何使用 Flutter 钩子

> 原文：<https://blog.logrocket.com/how-to-use-flutter-hooks/>

胡克斯，来见见颤振。受 React Hooks 和 Dan Abramov 的文章*理解 React Hooks* 的启发，Dash Overflow 的开发人员决定将 Hooks 引入 Flutter。

Flutter 小部件的行为类似于 React 组件，因为 React 组件中的许多生命周期都存在于 Flutter 小部件中。据 GitHub 页面上的创作者称:

> 钩子是一种管理小部件生命周期的新对象。它们的存在只有一个原因:通过删除重复项来增加小部件之间的代码共享。

`flutter_hooks`库通过增加小部件之间的代码共享和减少代码重复，提供了一种健壮和干净的方式来管理小部件的生命周期。

内置扑钩包括:

*   `useEffect`
*   `useState`
*   `useMemoized`
*   `useRef`
*   `useCallback`
*   `useContext`
*   `useValueChanged`

在本帖中，我们将关注其中的三个问题:

*   `useState`钩子管理应用程序中的本地状态
*   `useEffect`钩子从服务器获取数据，并将获取设置为本地状态
*   钩子将繁重的功能记忆起来，以实现应用程序的最佳性能

我们还将学习如何创建和使用来自`flutter_hooks`的定制钩子。

现在，让我们看看如何安装下面的`flutter_hooks`库。

## 安装`flutter_hooks`库

要使用来自`flutter_hooks`库的 Flutter 钩子，我们必须在一个 Flutter 项目的终端中运行以下命令来安装它:

```
flutter pub add flutter_hooks

```

这将在`dependencies`部分的`pubspec.yaml`文件中添加`flutter_hooks: VERSION_NUMER_HERE`。

另外，我们可以将`flutter_hooks`添加到`pubspec.yaml`文件的`dependencies`部分:

```
dependencies:
  flutter:
    sdk: flutter
  flutter_hooks:

```

保存文件后，Flutter 会安装依赖项。接下来，导入`flutter_hooks`库:

```
import 'package:flutter_hooks/flutter_hooks.dart';

```

现在我们可以走了！

## `useState`钩子

就像 React 中的`useState`一样，Flutter 中的`useState`帮助我们创建和管理小部件中的状态。

用我们想要在小部件中本地管理的状态来调用`useState`钩子。这个状态作为参数传递给`useState`钩子。此状态是初始状态，因为它会在小部件的生命周期内发生变化:

```
final state = useState(0);

```

这里，`0`传递到`useState`，成为初始状态。

现在，让我们看看如何在一个小部件中使用它。我们必须首先[将 Flutter 的`counter`例子](https://blog.logrocket.com/flutter-state-management-methods/)转换成使用`useState`。

以下是 Flutter 的原始`counter`示例:

```
class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.headline4,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

```

注意，使用`StatefulWidget`有时会使在小部件中本地维护状态变得复杂。我们还必须引入另一个扩展了一个`State`类的类，为一个`StatefulWidget`创建两个类。

然而，使用钩子，我们只用一个类来维护我们的代码，比`StatefulWidget`更容易维护。

下面是钩当量:

```
class MyHomePage extends HookWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  Widget build(BuildContext context) {
    final _counter = useState(0);
    return Scaffold(
      appBar: AppBar(
        title: Text(title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter.value',
              style: Theme.of(context).textTheme.headline4,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => _counter.value++,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

```

钩子的例子比同时代的要短。然而，在小部件中使用 Flutter 钩子之前，小部件必须扩展由`flutter_hooks`库提供的`HookWidget`。

通过用`0`调用`build`方法中的`useState`，我们将返回值存储在`_counter`中。这个`_counter`就是`ValueNotifier`的一个实例。

状态现在存储在`ValueNotifier`的`.value`属性中。因此，`_counter`状态的值被存储在`_counter.value`。

`useState`订阅`.value`属性中的状态，当`.value`的值被修改时，`useState`钩子重新构建小部件以显示新值。

在`FloatingActionButton`中，如果按下按钮，则`_counter.value`增加。这使得状态增加了`1`，并且`useState`重新构建`MyHomePage`小部件来显示新值。

## `useEffect`钩子

颤振中的`useEffect`钩子和 React 的`useEffect`钩子是一样的。挂钩将函数回调作为参数，并在小部件中运行副作用:

```
useEffect( () {
    // side effects code here.
    //subscription to a stream, opening a WebSocket connection, or performing HTTP requests
});

```

副作用可能包括流订阅、打开 WebSocket 连接或执行 HTTP 请求。它们也是在钩子内部完成的，所以当一个小部件被释放时，我们可以取消它们。

函数回调必须返回一个函数，并在小部件被释放时被调用。然后，在小部件从 UI 和小部件树中移除之前，我们可以在该函数中取消订阅或其他清理。其他清理包括:

*   取消订阅流
*   取消轮询
*   清除超时
*   取消活动的 HTTP 连接
*   取消 WebSockets 连接

这可以防止打开的连接(如 HTTP、WebSocket 连接、打开的流和打开的订阅)在打开它们的小部件被销毁后保留在小部件中，并且不再出现在小部件树中:

```
useEffect( () {
    // side effects code here.
    // - Unsubscribing from a stream.
    // - Cancelling polling
    // - Clearing timeouts
    // - Cancelling active HTTP connections.
    // - Cancelling WebSockets conncetions.
        return () {
        // clean up code
    }
});

```

`useEffect`中的函数回调是同步调用的，这意味着每次小部件渲染或重新渲染时都会调用它。

### `useEffect`的`keys`自变量

这个钩子还有一个可选的名为`keys`的第二个参数。`keys`参数是一个值列表，决定是否调用`useEffect`钩子中的函数回调。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`useEffect`将`keys`的当前值与其先前值进行比较。如果值不同，`useEffect`运行函数回调。如果`keys`中只有一个值保持不变，则不调用函数回调:

```
useEffect( () {
    // side effects code here.
    return () {
        // clean up code
    }
}, [keys]);

```

## `useMemoized`钩子

`useMemoized`钩子类似于 React 中的`useMemo`:它存储/缓存从构建器函数创建的复杂对象的实例。

这个函数传递给`useMemoized`钩子，然后`useMemoized`调用并存储函数的结果。如果没有调用重新呈现该函数的小部件，则调用`useMemoized`，并返回其先前的结果。

### `useMemoized`的`keys`自变量

与`useEffect`类似，`useMemoized`钩子有第二个可选参数，称为`keys`:

```
const result = useMemoized(() {}, [keys]);

```

这个`keys`参数是一个依赖列表，它决定了当小部件重新呈现时，传递给`useMemoized`的函数是否执行。

当小部件重建时，`useMemoized`检查它的`keys`,看看以前的值是否改变了。如果至少有一个值改变了，那么`useMemoized`钩子中的函数回调将被调用，`result`将呈现函数调用结果。

如果自上次检查以来没有任何值发生变化，`useMemoized`跳过调用该函数并使用其最后一个值。

## 定制挂钩

使我们能够通过两种方法创建我们自己的定制钩子:函数或类。

创建自定义挂钩时，需要遵循两条规则:

*   使用`use`作为前缀告诉开发者这个函数是一个钩子，而不是一个普通的函数
*   不要有条件地呈现钩子，只呈现钩子的结果

使用函数和类方法，我们将创建一个自定义钩子，它打印一个带有调试值的值，就像 React 的`useDebugValue`钩子一样。

让我们从函数方法开始。

### 功能方法

从函数方法开始，我们必须创建一个使用任何内置钩子的方法:

```
ValueNotifier<T> useDebugValue([T initialState],debugLabel) {
    final state = useState(initialState);
    print(debugLabel + ": " + initialState);
    return state;
}

```

在上面的代码中，使用内置的`useState`钩子保存函数中的状态，并打印状态的`debugLabel`和值。

然后我们可以返回`state`。因此，使用`debugLabel`，当小部件第一次被挂载到小部件树时，以及当修改状态值时，状态的标签会打印在控制台中。

接下来，让我们看看如何使用我们创建的`useDebugValue`钩子在挂载和重建小部件时打印`debutLabel`字符串和相应的状态:

```
final counter = useDebugValue(0, "Counter");
final score = useDebugValue(10, "Score");

// Counter: 0
// Score: 10

```

### 分类方法

现在，让我们使用一个类来重新创建`useDebugValue`定制钩子。这是通过创建一个`extends`一个`Hook`类来实现的:

```
ValueNotifier<T> useDebugValue<T>(T initialData, debugLabel) {
  return use(_StateHook(initialData: initialData, debugLabel));
}

class _StateHook<T> extends Hook<ValueNotifier<T>> {
  const _StateHook({required this.initialData, this.debugLabel});

  final T debugLabel;
  final T initialData;

  @override
  _StateHookState<T> createState() => _StateHookState();
}

class _StateHookState<T> extends HookState<ValueNotifier<T>, _StateHook<T>> {
  late final _state = ValueNotifier<T>(hook.initialData)
    ..addListener(_listener);

  @override
  void dispose() {
    _state.dispose();
  }

  @override
  ValueNotifier<T> build(BuildContext context) {
    print(this.debugLabel + ": " + _state.value);
      return _state;
  }

  void _listener() {
    setState(() {});
  }
}

```

在上面的代码中，我们有`useDebugValue`函数，这是我们的自定义钩子。它接受参数，比如钩子管理的初始状态值`initialData`，以及状态的标签`debugLabel`。

`_StateHook`类是编写钩子逻辑的地方。当调用`use`函数并在`_StateHook`类实例中传递时，它将`_StateHook`类注册到 Flutter 运行时。我们可以把`useDebugLabel`称为一个钩子。

因此，每当使用 class 方法创建一个钩子时，这个类必须扩展一个钩子类。也可以用`Hook.use()`代替`use()`。

## 结论

通过帮助将代码库的大小减少到一个相当小的尺寸，带来了我们如何构建 Flutter 小部件的重大改变。

正如我们已经看到的，`flutter_hooks`使开发人员能够去除像`StatefulWidget`这样的小部件，允许他们编写干净的、可维护的、易于共享和测试的代码。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)