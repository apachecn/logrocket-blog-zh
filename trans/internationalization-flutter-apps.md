# Flutter 应用程序的国际化

> 原文：<https://blog.logrocket.com/internationalization-flutter-apps/>

最近发布的 Flutter 3，凭借其出色的开发体验和在许多平台上工作的单一代码库的承诺，已经成为许多 iOS 和 Android 应用程序的瑞士军刀，桌面和 web 应用程序也在慢慢加快步伐。

当你发布一个将在全球使用的应用程序时，只有一种语言并不能为每个最终用户提供相同的体验。虽然英语是使用最广泛的语言之一，但翻译该应用程序可以让所有用户更容易理解。因此，我们将在本文中学习如何本地化我们的 Flutter 应用程序。

## 我们将建造什么？

我们将尝试通过使用一个计数器应用程序来理解本地化，这个应用程序在您创建一个 Flutter 项目时会派上用场，并在反例中做了一些更改来演示本地化。

我们将使用 Flutter 本地化包成功地将我们的应用程序翻译成另一种语言，包括单词和短语的插值以及单数和复数的正确翻译。

### 目录

## 项目配置

如上所示更改您的`pubspec.yaml`文件。`flutter localizations`包括一个本地本地化包和允许国际化和本地化的`intl`，包括消息翻译、复数和性别。`generate: true`行对于本地化包的自动代码生成是必不可少的，可以节省大量时间。

在我们项目的根目录下创建一个`l10n.yaml`文件；即`lib/l10n.yaml`。该文件指定我们的翻译文件的位置以及自动生成的 Dart 文件的名称:

```
arb-dir: lib/l10n
template-arb-file: app_en.arb
output-localization-file: app_localizations.dart

```

默认情况下，Flutter 本地化将其翻译存储在 ARB(应用程序资源包)文件中。这些只是像 JSON 一样的键值对文件。让我们首先为我们的默认英语创建一个名为`app_en.arb`的`arb`文件，并将它放在`lib/l10n/app_en.arb`中。

由于我们支持多个地区，我们必须为我们的应用程序支持的每个地区创建一个单独的文件。在本文中，我们仅支持英语和印地语作为示例。

您可以添加您想要支持的任意数量的语言环境的`arb`文件。目前，让我们创建两个单独的`arb`文件，因为在本例中我们只支持两种语言环境:

`lib/l10n/app_en.arb`

```
{
  "appTitle": "Demo App"
}

```

`lib/l10n/app_hi.arb`

```
{
  "appTitle": "डेमो ऐप"
}

```

接下来，让我们给`MaterialApp`添加本地化:

```
import 'package:flutter_gen/gen_l10n/app_localizations.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      localizationsDelegates: AppLocalizations.localizationsDelegates,
      supportedLocales: AppLocalizations.supportedLocales,
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const MyHomePage(title: 'Flutter Demo'),
    );
  }
}

```

`AppLocalizations.localizationsDelegates`负责我们应用的本地化。Flutter 团队已经提供了 Flutter 部件、Material 和 Cupertino 的本地化版本。例如，如果你使用`showDatePicker()`打开`DatePicker`，你会发现对话已经被翻译成设备的语言环境，不需要本地化文件。有了`AppLocalizations.supportedLocales`，Flutter 只在检测到新的语言环境并将其添加到`MaterialApp`的`supportedLocales`时才重建我们应用的 UI。

为了支持 iOS 中的本地化，我们需要对`Info.plist`进行以下更改:

```
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>hi</string>
</array>

```

### 本地化代码生成

为了使用我们之前添加的 ARB 文件的翻译，我们需要生成替代 ARB 文件的 Dart 文件，这些文件可以导入到我们想要使用本地化值的任何地方。要生成这些文件，只需在完成上述配置更改后启动应用程序。代码生成完成后，您应该会看到以下文件:

*   `.dart_tool/flutter_gen/gen_l10n/app_localizations.dart`
*   `.dart_tool/flutter_gen/gen_l10n/app_localizations_en.dart`
*   `.dart_tool/flutter_gen/gen_l10n/app_localizations_hi.dart`

> 注意:如果本地化代码没有自动生成，运行`flutter gen-l10n`就可以了。

如果您打开`app_localizations.dart`，您会看到文件包含一个抽象类`AppLocalizations`，它有`localizationsDelegates`和`supportedLocales`以及您在 ARB 文件中添加的区域设置值。其他文件`app_localizations_en.dart`和`app_localizations_hi.dart`包含用特定于地区的变量和方法扩展`AppLocalizations`的类。

## 让我们本地化我们的应用程序

为了使用`AppLocalizaions`类，您必须首先导入这个类:

```
import 'package:flutter_gen/gen_l10n/app_localizations.dart';

```

之后，您可以使用`AppLocalizations.of(context).appTitle`访问区域设置值。我们的`MaterialApp`现在看起来是这样的:

```
class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      localizationsDelegates: AppLocalizations.localizationsDelegates,
      supportedLocales: AppLocalizations.supportedLocales,
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const MyHomePage(title: AppLocalizations.of(context).appTitle),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({Key? key, required this.title}) : super(key: key);

  final String title;

  @override
  State<MyHomePage> createState() => _MyHomePageState();
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
            const Padding(
              padding:  EdgeInsets.only(top: 32),
              child: Text(
                'Flutter is Awesome',
                style:  TextStyle(fontSize: 24),
              ),
            ),
            Expanded(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Text('You have pushed this button :',
                      style: Theme.of(context).textTheme.headline6),
                  Text(
                    '$_counter times',
                    style: Theme.of(context).textTheme.headline4,
                  )
                ],
              ),
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: const Icon(Icons.add),
      ),
    );
  }
}

```

恭喜，我们的应用标题现已成功本地化。我们只需要更新其他的`String`值。

## 为国际化插入单词和短语

插值只是将某种东西插入另一种不同的东西中。当您希望某个单词或文本使用所有支持的区域设置的默认语言(例如英语)时，插值在本地化中非常有用。例如，在我们的演示应用程序中，我们希望显示一个文本“Flutter is awesome”，但问题是，无论用户设备上使用什么语言环境，我们都希望单词 Flutter 是英语。

让我们将插入的句子添加到我们的区域 ARB 文件中:

`lib/l10n/app_en.arb`:

```
{
  "appTitle": "Demo App",
  "appDescription": "{flutter} is Awesome",
  "@appDescription": {
    "placeholders": {
      "flutter": {
        "type": "String",
        "example": "Flutter"
      }
    }
  },
}

```

`lib/l10n/app_hi.arb`:

```
{
  "appTitle": "डेमो ऐप",
  "appDescription": "{flutter} बहुत बढ़िया है",
}

```

现在，我们必须使用添加到 ARB 文件中的插入文本描述。因为我们要在多个地方访问`AppLocalizations`，我们将创建一个实例变量`_locale`并在`didChangeDependencies()`中初始化它:

```
class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;
  late AppLocalizations _local;

  @override
  void didChangeDependencies() {
    _local = AppLocalizations.of(context);
    super.didChangeDependencies();
  }

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(_local.appTitle),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.only(top: 32),
              child: Text(
                _local.appDescription('Flutter'),
                style: const TextStyle(fontSize: 24),
              ),
            ),
            Expanded(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Text("You have pushed this button :",
                      style: Theme.of(context).textTheme.headline6),
                  Text(
                    "$_counter",
                    style: Theme.of(context).textTheme.headline4,
                  )
                ],
              ),
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: "Increment",
        child: const Icon(Icons.add),
      ),
    );
  }
}

```

运行应用程序，你会发现无论你的设备被设置为什么语言，单词“Flutter”都会是英语。

## 单复数支持

在本地化中，我们经常要处理单数和复数。例如——“恭喜你🎉，你赢得了一张优惠券，“或者，”恭喜🎉，你中了两张优惠券。”需要注意的是，不同的语言处理复数的方式不同，使用复数时要小心，因为你需要了解你要翻译的语言。

让我们进行必要的更改以支持本地化文件中的复数:

`lib/l10n/app_en.arb`:

```
{
  "appTitle": "Demo App",
  "appDescription": "{flutter} is Awesome",
  "@appDescription": {
    "placeholders": {
      "flutter": {
        "type": "String",
        "example": "Flutter"
      }
    }
  },
  "counterText": "You have pushed this button :",
  "counter": "{count,plural, =0{0} =1{1 time} other{{count} times}}",
  "@counter": {
    "placeholders": {
      "count": {
        "type": "int",
        "example": "count"
      }
    }
  },
  "counterButtonText": "Increment"
}

```

`lib/l10n/app_hi.arb`:

```
{
  "appTitle": "डेमो ऐप",
  "appDescription": "{flutter} बहुत बढ़िया है",
  "counterText": "आपने यह बटन दबा दिया है :",
  "counter": "{count,plural, =0{0} =1{1 बार} other{{count} बार}}",
  "counterButtonText": "जोड़ें"
}

```

现在我们已经为两个 ARB 文件在键`counter`中添加了复数，还添加了将用作增量按钮工具提示的`counterButtonText`，我们可以这样写:

```
class MyHomePage extends StatefulWidget {
  const MyHomePage({Key? key}) : super(key: key);

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;
  late AppLocalizations _local;

  @override
  void didChangeDependencies() {
    _local = AppLocalizations.of(context);
    super.didChangeDependencies();
  }

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(_local.appTitle),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.only(top: 32),
              child: Text(
                _local.appDescription('Flutter'),
                style: const TextStyle(fontSize: 24),
              ),
            ),
            Expanded(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Text(_local.counterText,
                      style: Theme.of(context).textTheme.headline6),
                  Text(
                    _local.counter(_counter),
                    style: Theme.of(context).textTheme.headline4,
                  )
                ],
              ),
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: _local.counterButtonText,
        child: const Icon(Icons.add),
      ),
    );
  }
}

```

注意我们是如何将`_counter`值传递给`AppLocalizations.counter()`的，它最终会检查该值是单数还是复数，并基于此返回一个`String`值。

## 结论

在本教程中，您学习了如何本地化您的 flutter 应用程序，并使其更易于用户访问。希望你不断尝试新事物！

现在我们已经做好了一切准备，您所要做的就是运行应用程序并开始享受。

祝你好运！开心飘飘！

如果你有任何问题，请随时发表。欢迎任何反馈。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)