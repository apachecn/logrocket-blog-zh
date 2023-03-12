# 颤振状态管理提供商快速指南

> 原文：<https://blog.logrocket.com/quick-guide-provider-flutter-state-management/>

状态管理的概念仍然是 Flutter 中最关键的主题之一。这是因为我们在 Flutter 中所做的一切，从与接收用户信息相关的操作到显示一段数据，都与状态有关。因此，以尽可能好的方式管理这些数据可以确保应用程序代码清晰、正确抽象、运行顺畅，并提供尽可能好的结果。

[多年来，已经开发了许多状态管理解决方案](https://blog.logrocket.com/provider-vs-riverpod-comparing-state-managers-in-flutter/)，每一个都基于相同的概念，即以最干净、最容易访问的方式操作或修改状态。在本文中，我们将使用 Flutter: Provider 的最佳状态管理包之一构建一个示例应用程序。

在我们开始之前，请注意，本文假设您的机器上有一个可操作的颤振开发环境，以及颤振的工作知识。

让我们来谈谈在一个 Flutter 应用程序中管理状态意味着什么。

## 颤振是什么状态？

Flutter 中的“状态”指的是存储在小部件中的数据，可以根据当前操作进行修改。应用程序的状态可以在应用程序启动时或页面重新加载时更新或完全改变。

这意味着小部件所做的一切都需要处理从用户那里获取的数据，并在它们之间传递数据以执行一个或多个操作。 [Flutter 还可以使用状态](https://blog.logrocket.com/flutter-state-management-methods/)向用户显示信息片段。

## 什么是提供商？

Remi Rousselet 创建的提供者包旨在尽可能干净地处理状态。在 Provider 中，小部件监听状态的变化，并在得到通知后立即更新。

因此，当出现状态更改时，不是重建整个小部件树，而是只更改受影响的小部件，从而减少工作量，使应用程序运行得更快、更流畅。

## 提供者的状态管理

回想一下我们之前讨论过的关于提供者的内容:小部件监听变化并在有重建时相互通知。一旦状态改变，特定的小部件就会重建，而不会影响树中的其他[小部件。](https://blog.logrocket.com/widgets-the-building-blocks-of-flutter-apps/)

三个主要组件使这一切成为可能:Flutter 中的`ChangeNotifier`类、`ChangeNotifierProvider`(主要用于我们的示例应用程序)和`Consumer`小部件。

从`ChangeNotifier`类观察到的任何状态变化都会导致监听小部件重新构建。提供商包提供了不同类型的提供商，下面列出了其中一些:

*   不管值类型如何，`Provider`类接受一个值并公开它
*   `ListenableProvider`是用于 listenable 对象的特定提供程序。它会监听，然后要求依赖于它并受状态变化影响的小部件在任何时候调用监听器时进行重建
*   `ChangeNotifierProvider`与`ListenableProvider`类似，只是针对`ChangeNotifier`对象，需要时自动调用`ChangeNotifier.dispose`
*   `ValueListenableProvider`监听一个`ValueListenable`并公开该值
*   `StreamProvider`监听流，公开发出的最新值，并要求依赖于流的小部件进行重建
*   `FutureProvider`接受一个`Future`类，并在未来完成时根据它更新小部件

## 入门指南

首先创建一个新项目，并将这一行添加到`pubspec.yaml`文件中的依赖块:

```
dependencies:
 provider: ^5.0.0

```

运行`pub get`命令获得包的本地副本:

```
flutter pub get

```

接下来，我们需要在`main.dart`文件中创建一个新的材料应用程序:

```
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
 return MaterialApp(
  title: 'Material App',
  home: Scaffold(
  appBar: AppBar(
   title: Text('Material App Bar'),
  ),
  body: Center(
   child: Container(
   child: Text('Hello World'),
   ),
  ),
  ),
 );
 }
}

```

## 管理状态数据

现在，创建一个包含应用程序所需的状态数据的新类。姑且称之为`UserDetailsProvider`。`UserDetailsProvider`类将在这里声明所有处理状态的方法。

这个类扩展了`ChangeNotifier`类；`ChangeNotifier`为我们提供了对`notifyListeners`方法的访问，当状态改变时，我们将使用该方法通知监听小部件进行重建。

我们为我们的`TextFormField`声明两个控制器:`name`和`age`。这个类中还声明了根据用户输入更新用户姓名和年龄的方法。

这里声明了与应用程序状态相关的所有内容:

```
class UserDetailsProvider extends ChangeNotifier {
 TextEditingController nameController = TextEditingController();
 TextEditingController ageController = TextEditingController();
 int _age = 0;
 String _userName = '';
 int get userAge => _age;
 String get userName => _userName;
 void updateAge(int age) {
 _age = age;
 notifyListeners();
 }
 void updateName(String name) {
 _userName = name;
 notifyListeners();
 }
}

```

## 更新状态

名称更新后，我们调用`notifyListeners`方法，通知监听小部件状态的变化，从而触发所有相关小部件的重建。

现在我们有了`UserDetailsProvider`类(它处理状态)，我们需要使用`ChangeNotifierProvider`将该类链接到屏幕。现在，用主块的`runApp`方法中的`ChangeNotifierProvider`包装整个应用程序。

`ChangeNotifierProvider`公开了两个重要的属性:`create`和`child`。我们声明的扩展了`ChangeNotifier`的类被传递到`create`属性中，将该类链接到屏幕:

```
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
void main() => runApp(
  ChangeNotifierProvider<UserDetailsProvider>(
  create: (_) => UserDetailsProvider(),
  child: MyApp(),
  ),
 );
// ignore: use_key_in_widget_constructors
class MyApp extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
 return const MaterialApp(
  title: 'Material App',
  home: HomeScreen(),
 );
 }
}

```

现在应用程序链接到提供状态的类；每当状态发生变化时，都会导致应用程序内屏幕的重建。

## 收集用户数据

目前，`HomeScreen`小部件包含一个带有两个`TextFormField`的表单，用于接收用户的姓名和年龄。此外，还包括一个`RawMaterialButton`，用于在用户输入所需的详细信息后保存更改。

在这组小部件之后，我们有两个`Text`小部件显示用户给定的值。每当应用程序状态发生变化时，这两个小部件是唯一需要更新的小部件。

这意味着我们不需要在每次状态发生变化时重建每个屏幕。因此，我们需要一种方法来选择性地只重建与状态变化相关的`Text`小部件。为此，我们有了`Consumer`小部件。

## 选择性地更新状态

`Consumer`小部件只允许重建子小部件，而不会影响小部件树中的其他小部件。如前所述，我们只希望显示用户提供的详细信息的`text`小部件进行更新。

我们通过用一个`Column`包装两个`Text`小部件并在由`Consumer`小部件公开的`builder`函数处返回它来实现这一点:

```
Consumer<UserDetailsProvider>(
  builder: (context, provider, child) {
  return Column(
   children: [
   Text(
    'Hi ' + provider.userName,
    style: const TextStyle(
    fontSize: 18,
    fontWeight: FontWeight.bold,
    ),
   ),
   Text(
    'You are ' + provider.userAge.toString() + ' years old',
    style: const TextStyle(
    fontSize: 18,
    fontWeight: FontWeight.w400,
    ),
   ),
   ],
  );
  },
 ),

```

现在，只要应用程序中的状态发生变化，只有`Text`小部件会更新。

确保尽可能使用最低级别的提供程序；您只能对受影响的小部件使用提供程序。在高层使用它会导致与状态变化无关的小部件重新构建。对于`Consumer`小部件也是如此；确保在特定级别使用，以避免重新构建整个小部件树。

我们的示例应用程序终于准备好了！

## 结论

对颤振状态管理重要性的强调不能过分。今天，我们已经剖析了 Provider 包，并用它来管理一个示例 Flutter 应用程序的状态。希望有了通过本文构建应用程序所获得的实践知识，您现在能够以一种简洁且更容易理解的方式正确地管理应用程序的状态。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)