# 了解 Flutter 的定时器类和 Timer.periodic - LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-flutter-timer-class-timer-periodic/>

在构建移动应用程序时，我们经常会遇到这样的场景:在某个持续时间之后，我们必须执行一项任务。还记得在进入应用程序之前看到闪亮的闪屏吗？或者，在一个美食应用程序上点了一个喜欢的菜后，一个放松的屏幕？

或者，我们可能需要一段代码在某个持续时间后重复执行，比如显示填充一次性密码的剩余时间限制，或者每秒改变一个小部件的颜色以创建一个漂亮的动画。

为了解决颤振应用中的这些需求，我们推出了`Timer`级。因此，在本文中，我们将讨论以下几点，以便更好地理解如何在您自己的 Flutter 应用程序中实现这些功能:

*   什么是`Timer`类，我们如何使用它
*   如何用`timer.periodic`创建周期性定时器
*   如何创建可重启定时器
*   使用`Timer`类的例子

就这样，让我们开始吧！

## 什么是`Timer`类？

Flutter 的`Timer`类允许我们创建一个倒计时器。在其生命周期中，它分别经历以下状态:

*   创建一个计时器
*   执行回调
*   计时器结束

要使用属于 Dart 异步库的[部分的`Timer`类，我们可以用下面的 import 语句导入它:](https://blog.logrocket.com/introduction-to-using-dart-in-flutter/)

```
import 'dart:async';

```

### 创建简单的计时器

现在，要创建一个简单的 3 秒钟计时器，添加以下代码，它会在执行后触发一个回调:

```
final timer = Timer(
  const Duration(seconds: 3),
  () {
    // Navigate to your favorite place
  },
);

```

例如，一旦回调触发，我们可以将用户导航到一个新的屏幕。但是，请注意，回调只触发一次。

### 创建一个简单的周期性定时器

使用`Timer.periodic`，我们可以创建一个在给定持续时间后执行的重复计时器。定期计时器保持活动状态，直到被手动取消。Flutter 有一个不同的工厂方法，名为`periodic`，来创建这样一个定时器。

定期计时器的一个很好的例子是显示时间敏感操作的剩余时间，比如在 10 分钟内完成支付。在下面的示例中，代码生成一个每秒触发一次回调的计时器:

```
final periodicTimer = Timer.periodic(
  const Duration(seconds: 1),
  (timer) {
    // Update user about remaining time
  },
);

```

请注意，默认情况下，定期定时器无限期保持活动状态。

很简单，不是吗？是的，但是在实际用例中使用它时，我们还必须回答其他问题:

*   如何取消激活的定时器
*   如何知道定时器是否仍然有效
*   如何知道过去的持续时间

## 如何使用`Timer`类

`Timer`类给了我们许多其他的选项来轻松地使用它。让我们深入了解如何使用这些其他选项，以及它们如何与正常和定期定时器一起工作。

### 如何取消激活的定时器

`Timer`类有一个`cancel()`方法，用于取消任何活动的计时器。对于普通的计时器，调用`cancel`不会调用回调。对于周期性定时器来说，`cancel`方法变得非常重要，因为我们必须完成定时器:

```
final periodicTimer = Timer.periodic(
  const Duration(seconds: 1),
  (timer) {
    // Update user about remaining time
  },
);

final shouldStop = true; //No more tick-tock now! Please

if (shouldStop) {
  timer.cancel();
}

```

注意，我们可以任意多次调用`cancel`而不会有任何副作用；进一步的调用将被忽略。

### 如何知道一个定时器是否仍然有效

如果回调没有触发，并且我们没有显式取消它，那么普通的计时器被调用`active`。

另一方面，如果我们没有特别取消定期计时器，它们总是处于活动状态:

```
final timer = Timer(
  const Duration(seconds: 3),
  () {
    // Navigate to your favorite place
  },
);

if (timer.isActive) {
  //Oh no!, it's taking longer than expected
}

```

### 如何知道时间流逝了多少

创建一个持续时间为 1 秒的定期计时器将在一分钟内滴答 60 次。虽然我们知道这本质上是对的，但我们怎么能肯定地知道这些数字呢？

这就是`tick`的用武之地。一个`tick`值从零开始，并且每当定时器事件发生时递增；该值是反映经过的持续时间的计数。

例如，一个持续时间为 10 秒的周期性定时器在一分钟内将有 6 个事件，而`tick`将给出相对于当前时间点的值。也就是说，半分钟后，`tick`值将为`3`，并在每个事件上继续递增:

```
final periodicTimer = Timer.periodic(
  const Duration(seconds: 10),
  (timer) {
    // Update user about remaining time
  },
);

final howMuchTimeBuddy = periodicTimer.tick;

```

在上面的代码中，我们创建了一个持续时间为 10 秒的周期性定时器。对于任何给定的时间点，我们都可以使用`periodicTimer.tick`得到`tick`值。

### 如何安排回电

`Timer`类还有一个更有趣的用例。使用`Timer`，我们可以调度一个回调，在异步模式下尽快执行。为此，只需启动一个带有`zero`持续时间的计时器:

```
final zeroDurationTimer = Timer(
  Duration.zero,
  () {
    //Execute this callback ASAP but asynchronously
  },
);

```

还有一个相当于上述代码的简便方法，但更简洁:

```
final zeroDurationTimer = Timer.run(() {
  //Execute this callback ASAP but asynchronously
});

```

## 在 Flutter 中创建一个可重启的定时器

正如我们在上面看到的，我们可以通过调用`cancel()`方法来取消`Timer`。然而，在`Timer`类中没有直接的方法来重启同一个计时器，除非我们重新创建计时器实例。

为了实现这一点，[旋舞就有了](https://pub.dev/documentation/async/latest/async/RestartableTimer-class.html) `[RestartableTimer](https://pub.dev/documentation/async/latest/async/RestartableTimer-class.html)`。这个可重启计时器的底层实现与创建计时器实例相同。由于`Timer`是一个抽象类，`RestartableTimer`是它的一个具体实现。

`[RestartableTimer](https://pub.dev/packages/async)` [是异步包](https://pub.dev/packages/async)的一部分，包含`dart:async`的实用程序类。它已经是 Flutter SDK 的一部分，可以通过以下内容导入:

```
import 'package:async/async.dart';

```

导入后，我们可以创建一个简单的 3 秒钟`RestartableTimer`:

```
final restartableTimer = RestartableTimer(
  const Duration(seconds: 3),
  () {
    //Callback
  },
);

//Restart the timer
restartableTimer.reset();

```

这里需要注意的是，重置定时器会从其原始持续时间重新创建一个定时器。`RestartableTimer`仅用于非周期性定时器。

## 颤振`Timer`示例和用例

### 在离开之前创建特定持续时间的屏幕

当开发应用程序时，我们必须经常创建一个在一段时间内保持活动的屏幕，然后继续应用程序的流程。[这可能是一个闪屏](https://blog.logrocket.com/make-splash-screen-flutter/)，一个“您的订单已下”屏幕，或任何其他过渡元素。

在本例中，我们有第一个屏幕`RelaxingScreen`，它将对用户可见 3 秒钟，并在时间结束时导航到`NextScreen`。

当使用`Timer`时，在 dispose 上取消是很重要的。这保证了当从树中删除相应的小部件时，没有计时器保持活动状态。

看看下面的代码，使用`Timer`类实现同样的功能:

```
import 'dart:async';

import 'package:flutter/material.dart';

const Color darkBlue = Color.fromARGB(255, 18, 32, 47);

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark().copyWith(
        scaffoldBackgroundColor: darkBlue,
      ),
      debugShowCheckedModeBanner: false,
      home: const Scaffold(
        body: Center(
          child: RelaxingScreen(),
        ),
      ),
    );
  }
}

/// Releaxing screen that stays visible for 3 seconds
class RelaxingScreen extends StatefulWidget {
  const RelaxingScreen({Key? key}) : super(key: key);

  @override
  _RelaxingScreenState createState() => _RelaxingScreenState();
}

class _RelaxingScreenState extends State<RelaxingScreen> {
  //Declare a timer
  Timer? timer;

  @override
  void initState() {
    super.initState();

    /// Initialize timer for 3 seconds, it will be active as soon as intialized
    timer = Timer(
      const Duration(seconds: 3),
      () {
        /// Navigate to seconds screen when timer callback in executed
        Navigator.push(
          context,
          MaterialPageRoute(
            builder: (context) => const NextScreen(),
          ),
        );
      },
    );
  }

  /// cancel the timer when widget is disposed, 
  /// to avoid any active timer that is not executed yet
  @override
  void dispose() {
    super.dispose();
    timer?.cancel();
  }

  @override
  Widget build(BuildContext context) {
    return const Text("Relaxing Screen!!");
  }
}

class NextScreen extends StatelessWidget {
  const NextScreen({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: Center(
        child: Text("Next Screen"),
      ),
    );
  }
}

```

在上面的例子中，我们首先让`RelaxingScreen`对用户可见，这是一个有状态的小部件，然后我们必须在`initState()`上注册`Timer`。

3 秒钟后，一个回调触发导航用户到`NextScreen`。重要的是取消`dispose()`方法上的计时器，以避免如果用户在回调触发之前离开`RelaxingScreen`可能产生的任何异常。

### 自动将计数器应用程序增加 1 秒

作为一个颤振开发者，你最有可能[熟悉非常著名的计数器应用](https://blog.logrocket.com/whats-new-in-flutter-2-5/#new-app-template)😅。在本例中，我们将创建一个类似的计数器应用程序，但不是按+按钮，而是每 1 秒钟自动增加一次计数器，并通过重建一个`Text`小部件向用户显示:

```
import 'dart:async';

import 'package:flutter/material.dart';

const Color darkBlue = Color.fromARGB(255, 18, 32, 47);

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark().copyWith(
        scaffoldBackgroundColor: darkBlue,
      ),
      debugShowCheckedModeBanner: false,
      home: const Scaffold(
        body: Center(
          child: CounterScreen(),
        ),
      ),
    );
  }
}

class CounterScreen extends StatefulWidget {
  const CounterScreen({Key? key}) : super(key: key);

  @override
  _CounterScreenState createState() => _CounterScreenState();
}

class _CounterScreenState extends State<CounterScreen> {

  /// declare a cound variable with initial value
  int count = 0;

  /// declare a timer
  Timer? timer;

  @override
  void initState() {
    super.initState();

    /// Initialize a periodic timer with 1 second duration
    timer = Timer.periodic(
      const Duration(seconds: 1),
      (timer) {
        /// callback will be executed every 1 second, increament a count value 
        /// on each callback
        setState(() {
          count++;
        });
      },
    );
  }

  /// Since periodic timer doesn't cancels untill expicitely called
  /// It is important to cancel them on dispose, so that it doesn't stays active
  /// when widget is not binded to tree
  @override
  void dispose() {
    super.dispose();
    timer?.cancel();
  }

  @override
  Widget build(BuildContext context) {
    return Text("Counter reached $count");
  }
}

```

在上面的例子中，有一个`StatefulWidget`、`CounterScreen`，我们在其中注册了一个每 1 秒滴答一次的周期性定时器。每触发一次回调，我们就增加状态变量`count`。然后一个`Text`小部件显示`count`的最新值。

也可以采用类似的方法来显示倒计时定时器或剩余持续时间(例如，一次性密码超时)。

## Flutter 的`Timer`级的局限性

当我们想到通用定时器时，通常会想到暂停或恢复定时器之类的实用程序。正如我们到目前为止所看到的，Flutter 的`Timer`类旨在为以后调度一个代码块，或者在特定的持续时间内重复执行它。

要在 Flutter 中实现暂停和恢复计时器等实用程序，[可以使用](https://api.flutter.dev/flutter/dart-core/Stopwatch-class.html) `[Stopwatch](https://api.flutter.dev/flutter/dart-core/Stopwatch-class.html)` [类](https://api.flutter.dev/flutter/dart-core/Stopwatch-class.html)。

## 包扎

Flutter 的`Timer`类处理每一个与倒计时定时器相关的用例。有了它，我们可以创建一个正常的和定期的计时器，具有完整的实用程序，如取消计时器，识别计时器是否活动，以及滴答计数。

我们还看到了如何使用`RestartableTimer`重新设置和启动定时器。

感谢您的阅读，不要忘记分享您在`Timer`的帮助下正在创造多么美丽的 UX🤩。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)