# 提供商 vs. Riverpod:在 Flutter 中比较状态管理器

> 原文：<https://blog.logrocket.com/provider-vs-riverpod-comparing-state-managers-in-flutter/>

面对如此多的选项，在为您的应用程序选择状态管理器时很容易不知所措。似乎各种各样的状态管理解决方案发布的频率超出了人们的想象，每种解决方案都希望呈现一种独特的、更简单的状态处理方式。

在本文中，我们将介绍两种不同的状态管理工具:Provider 和 Riverpod。我们将简要介绍每个工具，以了解 Riverpod 提供的改进以及为什么人们会选择它而不是提供商，然后重点介绍提供商在 Riverpod 提供的解决方案方面存在的问题。

这篇文章假设你熟悉 Flutter。因为这不是对 Riverpod 或提供者状态管理包的介绍，所以我们不会对它们的特性进行太深入的研究——仅仅指出它们之间的比较就足够了。这篇文章关注 Riverpod 作为 Provider 的自然继承者。

## 什么是国家？

状态是小部件在构建时保存的一条信息，并且可以在小部件刷新时改变。在应用程序的小部件之间或内部存储和传递的某些数据或信息被称为“状态”

Flutter 中的一切都与处理和操纵精确的细节有关，要么从用户那里检索，要么以某种形式显示给用户。您选择的处理状态的方法会直接影响应用程序的行为和安全性。

## 状态管理

状态管理指的是用于处理应用程序状态的技术或方法。状态管理技术多种多样，适合各种需求。任何状态管理技术都没有放之四海而皆准的方法；[你挑选一个符合你需求的](https://blog.logrocket.com/flutter-state-management-methods/)并且最适合你的。

## Riverpod

[Riverpod](https://riverpod.dev/) 是 Remi Rousselet(提供者的创建者)发布的状态管理包。Rousselet 通过重新排列单词“Provider”的字母得到了单词 Riverpod。

Riverpod 主要是为了解决 Provider 的缺陷而构建的(我们将在后面讨论其中的一些缺陷)。它快速易用，开箱即用，是一个快速、轻量级的状态管理包。

自从正式发布以来，Riverpod 已经在状态管理社区掀起了一股浪潮，因为它以编译安全的方式对状态进行了直接而强大的处理。

在 Riverpod 中，您可以声明提供者，并在任何您想使用它的地方调用它。Riverpod 简单快捷。

看看这个用 Riverpod 进行状态管理的例子。首先，我们将整个应用程序包装在一个`ProviderScope`中。`ProviderScope`确定应用程序中创建的所有提供者的范围，并允许全局使用任何声明的提供者:

```
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

void main() => runApp(ProviderScope(child: RiverPodApp()));

```

接下来，声明提供者:

```
final favoriteProvider = ChangeNotifierProvider((ref) => new Favorites());

```

这里我们使用了`ChangeNotifierProvider`。这个提供者将一直为我们提供`Favorites`类。

为了利用我们的小部件中的提供者，扩展`ConsumerWidget`:

```
class RiverPodApp extends ConsumerWidget {
 const RiverPodApp({Key? key}) : super(key: key);
 @override
 Widget build(BuildContext context, ScopedReader watch) {
  return MaterialApp(
   home: Scaffold(
    appBar: AppBar(
     title: Text('My favorite fruit is ' + watch(favoriteProvider).fruit),
    ),
    body: Center(
     child: Column(
      children: [
       FruitButton('Apples'),
       FruitButton('Oranges'),
       FruitButton('Bananas'),
      ],
     ),
    ),
   ),
  );
 }
}

```

注意，`ConsumerWidget`让我们可以访问`build`方法中的`ScopedReader`，它提供了对提供者内容的访问。

在这个例子中，我们为不同的水果创建了三个按钮。当每个按钮被点击时，应用程序栏中的水果名称会发生变化。当你启动应用程序时，应用程序栏上写着，“我最喜欢的水果是未知的。”当每个水果按钮被点击时，水果的名称改变。

这种改变是可能的，因为 app bar 监视在`Favorite`类中创建的变量`fruit`(默认情况下，它被称为“未知”)。当每个按钮被点击时，调用`changeFruit`函数，给水果变量赋一个新值并更新小部件:

```
class FruitButton extends StatelessWidget {
 final String fruit;
 FruitButton(this.fruit);
 @override
 Widget build(BuildContext context) {
  return ElevatedButton(
   child: Text(fruit),
   onPressed: () {
    context.read(favoriteProvider).changeFruit(fruit);
   },
  );
 }
}
class Favorites extends ChangeNotifier {
 String fruit = 'unknown';
 void changeFruit(String newFruit) {
  fruit = newFruit;
  notifyListeners();
 }
}

```

## 为什么选择 Riverpod？

下面列出了选择 Riverpod 的各种原因:

*   Riverpod 是编译时安全的
*   它不直接依赖于 Flutter SDK
*   Riverpod 可用于创建和实施具有不可变模型类的单向数据流(意味着它们不会改变)
*   Riverpod 不直接依赖于小部件树；其操作类似于服务定位器。这些提供程序是全局声明的，可以在应用程序中的任何地方使用
*   Riverpod 通过`ScopedReader`让小部件访问提供者，这被传递给 build 方法，并最终通过`ConsumerWidget`类消费

## Riverpod 解决的提供商问题

Riverpod 解决了 Provider 的几个缺陷。

首先，与 Riverpod 不同，提供商完全依赖于 Flutter。因为它的窗口小部件用于提供对象或状态，所以它完全依赖于 Flutter，导致 UI 代码和依赖注入的混合。

另一方面，Riverpod 不依赖于小部件；您可以在 Riverpod 中声明一个提供者，并在应用程序中的任何地方使用它，而不考虑父小部件。Riverpod 中的提供者被声明为全局变量并放在任何文件中。

提供者也只依赖对象类型来解析小部件请求的对象。如果你提供两个相同的，你只能得到一个更接近调用点。但是，Riverpod 支持同一类型的多个提供者，您可以随时随地使用它们。

使用 Provider，如果您尝试访问未提供的类型，将会在运行时出现错误。这个运行时错误不应该这样，因为我们应该在编译应用程序时尽可能多地捕捉错误。Riverpod 通过在编译应用程序时捕捉错误来解决这一问题，使用户体验更加无缝。

组合两个或更多的提供者会导致可怕的嵌套代码。Riverpod 使用`ProviderReference`来处理这个问题。提供者的依赖关系是随时注入和调用的，这意味着一个提供者可以依赖另一个提供者，并且可以通过`ProviderReference`轻松调用。

这里有一个例子:

```
Future<void> main() async {
 WidgetsFlutterBinding.ensureInitialized();
 final sharedPreferences = await SharedPreferences.getInstance();
 runApp(MultiProvider(
  providers: [
   Provider<SharedPreferences>(create: (_) => sharedPreferences),
   ChangeNotifierProxyProvider<SharedPreferences, HomeViewModel>(
    create: (_) => HomeViewModel(sharedPreferences),
    update: (context, sharedPreferences, _) =>
      HomeViewModel(sharedPreferences),
   ),
  ],
  child: Consumer<HomeViewModel>(
   builder: (_, viewModel) => HomeView(viewModel),
  ),
 ));
}

```

在这个例子中，我们有`HomeView`，它有一个`HomeViewModel`参数。但是因为`HomeViewModel`依赖于`SharedPreferences`，所以我们需要`MultiProvider`和`ProxyProvider`小部件来把所有东西放在一起。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

考虑到这一点，我们可以看到有太多的样板代码。如果所有这些提供者都在小部件之外，而不是在小部件树之内，那就更好了。

相比之下，在 Riverpod 中，以下是一个依赖于另一个提供者的提供者的示例，没有提供者出现的嵌套问题:

```
final appTokenProvider = StateProvider<String>((_) => '');

final authenticateFBUser = FutureProvider<void>(
  (ref) async {
    final authFBResult = await ref.read(authProvider).login();
    ref.read(appTokenProvider).state = authFBResult.token;
  },
);

```

在上面的例子中，`authenticateFBUser`提供者依赖于`appTokenProvider`，它通过提供的`ProviderReference` (ref) Riverpod 调用该提供者。

## 比较提供商和 Riverpod

以下是 Provider 和 Riverpod 之间的一些比较:

*   Provider 存在运行时异常，但 Riverpod 会处理和纠正这些异常
*   提供程序不是编译安全的，而 Riverpod 是
*   在 Provider 中，您不能声明多个相同类型的提供者，而在 Riverpod 中，您可以在不覆盖其他提供者的情况下做到这一点
*   您可以声明提供者及其类，而无需将应用程序的根文件分散在 Riverpod 中
*   在 Riverpod 中，提供者是全局声明的，可以在应用程序的任何地方使用`Consumer`小部件或`context.read`
*   在 Provider 中，依赖可能导致可怕的嵌套代码，而在 Riverpod 中，一个提供者使用`ProviderReference`消费另一个提供者是很容易的

## 结论

正如我前面提到的，Riverpod 是 Provider 的继任者，它们都是由 Remi Rousselet 创建的。Riverpod 可以被视为没有缺点的提供商；它纠正了提供商的许多缺陷。

然而，如前所述，每个状态管理包都有其优缺点，这完全取决于您的具体用例。我希望这篇文章为你提供了必要的比较，让你在两个选项之间做出正确的决定。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)