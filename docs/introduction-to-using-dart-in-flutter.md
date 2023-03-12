# 在 Flutter 中使用 Dart 的介绍

> 原文：<https://blog.logrocket.com/introduction-to-using-dart-in-flutter/>

当涉及到跨平台开发时，Flutter 是快速发展的技术之一，制作 Flutter 应用程序背后的秘密武器是 Dart 语言。

虽然即使你不熟悉 Dart，你也可以开始开发一个 Flutter 应用程序，但是本教程将会涵盖你需要知道的基本语法和信息，以便你能够轻松地构建一个 Flutter 应用程序。

## Dart 是什么？

Dart 是一种通用的客户端优化语言，用于在任何网络或移动平台上快速开发应用程序，可以在桌面和嵌入式设备上使用。Dart 语言的核心目标是为您提供一套工具和一种编程语言，使您富有成效，并随着开发人员的要求和需求的增长而发展。

Dart 是一种综合性语言，提供了优秀的语言能力，如[未来、流、声音空安全等](https://dart.dev/)。

Dart 旨在为大多数具有不同编程背景的开发人员所熟悉。因此，无论你是否有 JavaScript 和 TypeScript 的背景，或者你是否曾经是面向对象的程序员，你都会发现使用 Dart 很熟悉。

而且，由于 Dart 的架构，杀手级特性、热重载和声明式编程在 Flutter 中都是可能的。

更重要的是，Dart 还自带了很多内置库，比如 dart:async、dart:convert、dart:html、dart:io 等。，以及一个奇妙的生态系统和杰出的包管理器 [pub.dev](https://pub.dev) 。

无论您是否想使用 Flutter，Dart 都是您在下一个应用程序中学习和使用的好选择。

如果你想快速尝试一下，你可以在线使用 [dartpad.dev](https://dartpad.dev/?null_safety=true) 。

## 如何使用 Dart

在开始创建 Flutter 应用程序之前，您应该了解一些 Dart 概念:

### 1.Main()函数:

每个 app 的入口都是*`main()`功能。就算要在控制台打印东西，也要有一个`main()`部件。*

```
void main() {
  var list = ['apples', 'bananas', 'oranges'];
  list.forEach((item) {
    print('${list.indexOf(item)}: $item');
  });
}
```

在 Flutter 中，你将从`PROJECT_ROOT/lib/main.dart`中的`main()`函数启动你的应用程序，在这里你将主窗口部件传递给`runApp()`，它将把它附加到屏幕上。那是第一个主要入口。

```
void main() {
  runApp(MyApp());
}

```

### 2.`:`(分号):

在 Dart 中您需要`;`(分号)，正如您在上面的例子中看到的:

```
runApp(MyApp());

```

### 3.类型和空安全

Dart 是一种类型安全的语言。它使用静态类型检查和运行时检查。当你学会了语法，你就能更快地理解 Flutter 代码。下面是对一个简单变量的剖析:

[修饰符][类型][变量名称]=[值]；

```
// e.g: 
final String name = "Majid Hajian";

```

尽管类型是必需的，但由于类型推断，类型注释是可选的。所以，你可能会遇到这种情况:

```
var name = "Majid Hajian"; // from now on `name` is a String;

```

Dart 中最常见的初始化变量修饰符是`var`、`final`、`const`和`late`，但是请记住，当在变量名前使用 type 时，可以使用除`var`之外的所有修饰符。

```
var name = "Majid Hajian";
String name = "Majid Hajian";
final String name = "Majid Hajian";
const String name = "Majid Hajian";

```

使用`var`或不使用修饰符创建一个灵活的变量，这意味着你可以随时改变值。如果你不打算修改变量，你应该使用`final`，它只设置变量一次，或者使用`const`，它形成一个编译时常量。

但是还有更复杂的情况。我们来看看`Map`和`List`的类型定义:

```
// Type of a List (Array): List<TYPE_OF_MEMBER>
// e.g: 
List<String> names = ['Majid', 'Hajian'];

// Type of a Map (Key-Values): Map<Key_TYPE, VALUE_TYPE>
// e.g: 
Map<String, number> ages = {'sara': 35, 'susan: 20};

```

在许多情况下，您可能没有向 Dart 分析器提供足够的信息，并且您可能会面临类型转换错误。让我们看一个例子:

```
var names = [];

```

变量类型推断`List<dynamic>`和`dynamic`可以是任何类型，因为我们在初始化变量时没有提供数组的可能类型。因此，Dart 将类型转换为`List<dynamic>`，在那里它可以是任何东西。通过在初始化或启动变量时向值添加注释，我们可以防止这种类型的错误。

```
final names = <String>[];
// Or
final List<String> names = [];

```

从 Dart 2.12 开始，Dart 是一种健全的空安全语言，这意味着代码中的类型在默认情况下是不可空的，这表明一个变量不能包含`null`，除非你允许它们包含。

```
final String name = null;
// or
final String name; 

```

注意，上面的变量不再有效，因为我们用一个`null`值初始化了一个变量。因为我们还没有指定这一点，所以运行时 null-dereference 错误变成了编辑时分析错误。

这就是`?`派上用场的时候了。为了给变量赋值`null`,我们可以在类型声明中使用`?`。

```
final String? name = null;

```

你会在 Flutter 2.0.0+和 Dart 2.12.0+中经常看到这种语法。

最后，在 Flutter 应用程序中，Dart 中最常见的内置类型如下:

*   数字(int，double)
*   字符串(字符串)
*   Booleans (bool)
*   列表(List，也称为*数组*
*   集合(集合)
*   地图(地图)
*   符号(符号)
*   值 null(空值)

### 4.班级

Dart 是一种面向对象的语言，具有类和混合基本继承。这意味着你可以创建`abstract`类型、`class`，使用`implement`和`extends`。你也可以看到`with`，在那里你想要使用混音。

在 Dart 类中，`constructor`的名称与`className`相同，如下所示:

```
class MyApp {
  MyApp(); // constructor
}

```

如果不需要初始化实例变量或创建对象，就不需要构造函数。如果需要的话，应该通过构造函数参数传递它们。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
class MyApp {
MyApp(this.title);

final String? title;
}

```

在 Dart 2 中，你也不需要使用`new`关键字来实例化一个类。

```
final myapp = MyApp('Majid Hajian');
print(myapp.title);

```

Flutter 中的所有 widgets 都是`StatelessWidget`或`StatefulWidget`的扩展类。因此，您可以创建您的小部件(类):

```
class MyApp extends StatelessWidget {

}

```

与`StatefulWidget`对应的`StatelessWidget`和`State`对象都有`build()`方法来构建你的屏幕。因此，要实现要构建的小部件，必须@ override build()方法。

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}

```

惯例是以一个大写字母开始。

### 5.镖中的参数

无论是在类中还是在函数中，学习如何定义参数都是非常必要的，因为这是颤振开发的关键部分之一。

在 Dart 中，如果您想要定义一个必需的参数，您可以将它们传递给构造函数或函数。

```
String getUrl(String prefix, String host) {
  return '';
}
// OR 
class MyApp {
  MyApp(this.title);

  final String? title;
}

```

在这两种情况下，您都需要将参数正确地传递到预期的位置。这就是我们所说的位置参数。

在许多情况下，您会发现您想要使一个参数可选。例如，要更改上面的代码，我们可以这样编码:

```
String getUrl({String? prefix, String? host}) {
  return '';
}
// OR
class MyApp {
  MyApp({this.title});

  final String? title;
}

```

我们使用`{}`来定义我们的可选参数，这些参数如我们所描述的那样命名。现在，要使用命名参数，请使用参数的名称并赋值。

```
getUrl( host: '', prefix: '');
//Or 
MyApp(title: 'Majid');

```

正如您所看到的，使用这种方法的主要优点是您不需要将值传递到参数的确切位置。

更重要的是，您的函数和类参数是自文档化的。换句话说，您可以简单地定义 param 的名称并传递值。当您希望为一个方法或类指定多个参数时，定义名称参数很有帮助。

你会经常在 Flutter 中遇到命名参数。下面是 Flutter 中的`Text`小部件的一个例子:

```
Text(
    this.data, {
    Key key,
    this.style,
    this.strutStyle,
    this.textAlign,
    this.textDirection,
    this.locale,
    this.softWrap,
    this.overflow,
    this.textScaleFactor,
    this.maxLines,
    this.semanticsLabel,
    this.textWidthBasis,
    this.textHeightBehavior,
})

```

`this.data`是位置的，这意味着第一个参数是必须传入的，但是其余的参数是可选的，因为它们是在`{}`中定义的。

你可能不会问为什么一个命名参数是必需的而不是可选的。在 Dart 2.12+中，现在有了`required`关键字，使得参数成为必须传入的。让我们看看上面的例子。

```
class MyApp {
  MyApp({this.title}); // optional named parameter
  final String? title;
}

```

但是如果你在参数前使用了`required`关键字，我们将强制传递它，即使它是一个命名参数。

```
class MyApp {
  MyApp({required this.title});

  final String? title;
}

```

如果你现在实例化`MyApp()`类，你也必须传递`title`；否则，编译器将抛出错误。

```
print(MyApp(title: 'Majid'));

```

### 6.省道格式

Dart 附带了一个格式化工具，有助于提高代码的可读性。这里有一个技巧可以帮助你更好地格式化你的代码，特别是在 Flutter 中，你会有很多嵌套的小部件。能用的地方就用`,`！

```
 Column(
  mainAxisAlignment: MainAxisAlignment.center,
  children: <Widget>[
    Text('You have pushed the button this many times:'),
    Text('$_counter', style: Theme.of(context).textTheme.headline4),
  ],
),

```

这里有一个`Column`小部件，它有两个`Text`子部件。没有一个孩子在传递争论时使用`,`。文本很窄，不容易阅读，但是如果您在每个`Text`小部件的最后一个参数的末尾使用`,`，它将被格式化并且更加友好。

```
Column(
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

```

你可以通过命令行中的格式工具或者你选择的编辑器以及 Dart 插件免费获得现成的格式。

### 7.功能

你可以在一个类中定义一个函数，也就是方法，或者在顶层定义一个函数。创建一个像下面的语法一样简单的函数。

```
// top-level
getMyname() {
// logic
}

// OR 

class MyClass() {

  getMyName() { 
  }
}

```

### 7.异步/等待

Dart 通过 Future 或 Stream 提供异步编程。要定义一个`Future`，可以使用`async`关键字。

```
Future<String> getUrl({String? prefix, String? host}) async {
  return 'd';
}

```

并且要等到值被解析，可以使用`await`关键字。

```
main() async {  
  final url = await getUrl(prefix: '', host: '');
}

```

您应该使用包装在带有`async`关键字的函数/方法中的`await`关键字。

要创建一个`Stream`，您将使用`async*`关键字。现在，您可以订阅流，并在每次发出流时获取值，直到您取消此订阅。

```
 getUrl(prefix: '', host: '').listen(
    (String value) {
      print(value);
    },
  );

```

注意，`listen()`函数接受一个函数，即回调函数，因为在 Dart 中一切都是对象，所以即使在函数中也可以传递它们。这通常用于事件发生时的颤振，例如`onPressed`。

```
TextButton(
      onPressed: () {
        // pressed
        // logic
      },
      child: Text('Submit'),
)

```

## 了解 Flutter 小部件树

现在，您应该能够阅读、理解和编写 Flutter 代码。为了证明这一点，让我们举个例子:

```
class MyCustomWidget extends StatelessWidget {
  MyCustomWidget({this.counter});

  final String? counter;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          Text(
            'You have pushed the button this many times:',
          ),
          Text(
            '$counter',
            style: Theme.of(context).textTheme.headline4,
          ),
        ],
      ),
    );
  }
}

```

首先，您将在使用`extends`的地方创建自定义小部件。然后，你将`@override`这个`build()`方法。您将返回`Center`，这是一个带有几个名称参数的 Flutter 预定义小部件，包括您分配`Column`的`child`。

`Column`有几个名称参数，这里只使用了`mainAxisAlignment`和`children`。您将有两个`Text`小部件，它们既有位置参数又有命名参数，等等。

您将会看到理解这段代码是多么容易，现在您甚至可以编写自己的代码了！

## 结论

Flutter 是一项帮助您创建跨平台应用程序的神奇技术，Dart 是它的基础。知道从哪里开始，先学什么，Dart 就很好学。

在本文中，我们回顾了 Flutter 中最广泛使用的基础知识，这样您就可以打开一个 Flutter 应用程序，不仅可以理解初始语法，还可以编写 Dart 代码。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)*