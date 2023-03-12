# 在 Flutter 中使用 SharedPreferences 在本地存储数据

> 原文：<https://blog.logrocket.com/using-sharedpreferences-in-flutter-to-store-data-locally/>

可能会有一个时候，你想在你的 Flutter 应用持久化数据，这样你就可以在以后重用它。此功能的一个常见用例是存储登录凭据，以便用户下次启动应用程序时调用。

使用 SharedPreferences，您可以配置您的 Flutter 应用程序，即使在用户终止他们的活动后也能记住数据。SharedPreferences 可用于存储密码、令牌和复杂关系数据等关键数据。

在本教程中，我们将演示如何在 Flutter 应用程序中使用 SharedPreferences 持久化和修改数据。我们将通过实际例子介绍以下内容:

## Flutter 中的 SharedPreferences 是什么？

SharedPreferences 是 Android 和 iOS 应用程序用来在分配的空间中存储简单数据的工具。即使当应用程序关闭并再次启动时，这些数据仍然存在；我们仍然可以恢复原来的值。

存储在 SharedPreferences 中的数据可以编辑和删除。SharedPreferences 将数据存储在一个[键值对](https://flutter.dev/docs/cookbook/persistence/key-value)中。

为了在 Flutter 中使用 SharedPreferences，一个名为`[shared_preferences](https://gist.github.com/philipszdavido/aa5086ce7f0f85e50cddd008895ae4b6)`的插件使我们能够存储数据。该插件在 iOS 上包装了`NSUserDefaults`，在 Android 上包装了`SharedPreferences`。

## 搭建一个颤动的项目

让我们来看看如何搭建一个 Flutter 项目，将`shared_preferences`插件添加到一个 Flutter 项目中，以及插件向我们展示的方法。

你需要在你的机器上安装一些工具和二进制文件，包括 Flutter SDK 和 Android Studio。

### 颤振 SDK

要在我们的机器上安装 Flutter SDK，您需要根据您的机器在下面选择您的 Flutter 二进制文件:

安装完成后，您将可以在系统中全局使用 Flutter CLI。运行以下命令对此进行测试:

```
➜  flutter --version

Flutter 2.0.6 • channel stable • https://github.com/flutter/flutter.git
Framework • revision 1d9032c7e1 (3 months ago) • 2021-04-29 17:37:58 -0700
Engine • revision 05e680e202
Tools • Dart 2.12.3
```

您的版本可能与我的不同，但您应该能够验证 Flutter SDK 已成功安装。

### 安卓工作室

我们将为 Android 操作系统开发，我们需要一个模拟器来测试我们的应用程序运行。Android Studio 是用于构建、测试和发布 Android 应用程序的官方 IDE/SDK。
前往[官网](https://developer.android.com/studio)下载并在你的机器上安装 Android Studio。

要使用 Android Studio 开发 Flutter 项目，您需要安装以下插件:

要搭建颤振项目，请运行以下命令:

```
flutter create shared_pref

```

该命令创建一个文件夹`shared_pref`，并在其中放置一个 Flutter 项目。可以使用 VS 代码或者 Android Studio 打开项目。

## 安装`shared_preferences`

现在我们已经准备好了 Flutter SDK，是时候安装`shared_preferences`插件了。

打开`pubspec.yaml`文件，在`dependencies`部分添加`shared_preferences`:

```
dependencies:
  flutter:
    sdk: flutter
  shared_preferences:

```

这将安装该依赖项的最新版本。

要使用`shared_preferences`插件，我们必须将它导入到我们的文件中:

```
import 'package:shared_preferences/shared_preferences.dart';

```

`shared_preferences`插件导出一个`SharedPreferences`类，该类包含可用于在 SharedPreferences 中设置各种原始类型数据的方法。它有一个方法`getInstance`，用于创建一个`SharedPreferences`的实例。

```
SharedPreferences prefs = await SharedPreferences.getInstance();

```

`getInstance`创建并返回一个`SharedPreferences`实例。`prefs`持有`SharedPreferences`的实例。

## `shared_preferences`方法

在本节中，我们将演示如何向 SharedPreferences 添加数据，以及从其中获取、编辑和删除数据。

### 添加数据

可以向 SharedPreferences 添加基本类型的数据。我们可以添加以下基本类型:

每个基本类型都有相应的 setter 方法。(注意:键-值对的键在字符串中。)

#### 添加到

```
prefs.setInt('counter', 1);

```

这将向 SharedPreferences 的`counter`表中添加一个 int，`1`。

#### 添加字符串

```
prefs.setString('counter', "yes");

```

这将向`counter`添加一个字符串`"yes"`。

#### Add bool

```
prefs.setBool('counter', true);

```

这将向`counter`添加一个布尔值`true`。

#### 添加双精度

```
prefs.setDouble('counter', 10.2);

```

这为`counter`增加了一个双精度值`10.2`。

### 检索数据

现在，让我们看看如何从 SharedPreferences 中检索数据。因为我们有用于基本类型的 setter 方法，所以我们有用于检索数据的 getter 方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

#### 获取整数数据

```
int value = prefs.getInt('counter');

```

`getInt`方法用于从 SharedPreferences 中检索一个`int value`。

```
value // 1

```

#### Get bool data

```
bool value = prefs.getBool('counter');

```

这个方法`getBool`从 SharedPreferences 返回一个布尔值。

```
value // true

```

#### 获取双重数据

```
double value = prefs.getDouble('counter');

```

`getDouble`方法从 SharedPreferences 返回一个 double 值。

```
value // 10.2

```

#### 获取字符串数据

```
string value = prefs.getString('counter');

```

`getString`方法从 SharedPreferences 返回一个字符串值。

```
value // "yes"

```

#### `null` **值**

在 SharedPreferences 中不存在数据的情况下，返回一个`null`值:

```
prefs.getInt("_counter")
// null

prefs.getBool("_counter")
// null

prefs.getString("_counter")
// null

prefs.getDouble("_counter")
// null

```

### 删除数据

为了从 SharedPreferences 中删除数据，我们将使用`remove`方法。数据的键被传递给`.remove`方法，因此`SharedPreferences`中的键-值对数据被删除。

```
prefs.remove("counter");

```

这里删除了`SharedPreferences`中的`counter`。

### 验证数据是否存在

我们可以在读取或写入之前检查`SharedPreferences`中的数据是否存在。为此，我们将使用`containsKey`方法。

`containsKey`方法在其 arg 中接受数据的键，并用它来检查该键是否存在于 SharedPreferences 中。

该方法返回一个布尔值，`true`或`false`:

```
prefs.containsKey("counter")
// true

```

在上面的示例中，`counter`存在于 SharedPreferences 中。这就是它返回`true`的原因。

```
prefs.containsKey("_counter")
// false

```

`_counter`不存在，所以返回`false`。

## 在颤振中使用`shared_preferences`

默认情况下，每当您搭建一个新项目时，Flutter 都会添加一个计数器项目。这个计数器项目使我们能够在按下`FloatingActionButton`时增加一个计数器。计数器的当前状态随着它的增加而显示。

```
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

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

当应用程序被销毁或关闭时,`counter`恢复为`0`。我们将使用 SharedPreferences 来持久化计数器的状态，以便计数器值从应用程序关闭之前的位置开始。

让我们开始吧。

首先，导入`shared_preferences`插件:

```
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

```

接下来，在`MyHomePageState`小部件中添加一个`initState`生命周期方法。这个生命周期方法在有状态小部件初始化时被调用。

这里，我们将从 SharedPreferences 加载计数器。如果存在，我们就把它作为计数器值。如果不是，我们将`0`分配给计数器:

```
  void loadCounter() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0);
    });
  }

  @override
  void initState() {
    super.initState();
    loadCounter();
  }

```

现在让我们把`_incrementCounter`函数变成`async`函数。在其中，我们将从 SharedPreferences 获取计数器，对其进行递增，然后用递增量设置计数器，并在 SharedPreferences 中设置递增量。

```
  void _incrementCounter() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = ((prefs.getInt('counter') ?? 0) + 1);
      prefs.setInt('counter', _counter);
    });
  }

```

现在，我们的 Flutter 应用程序可以在关闭和重启时记住计数器的最后一个值。

## 测试我们的颤振应用

首先，让我们启动我们的应用程序:

![Flutter SharedPreferences Demo: Start App](img/1a416d225e713dcc02c72d4a76d63f63.png)

增加到 7:

![Flutter SharedPreferences Demo: Increment to 3](img/3e9b42c850e15c4444e06ced66624a5c.png)

![Flutter SharedPreferences Demo: Increment to 7](img/70437ddd5e8a22056f1e5413707d1668.png)

现在让我们试着关掉这个应用程序:

![Flutter SharedPreferences Demo: Killing the App](img/539f6c169135f21e6f78668be1c8779f.png)

重新启动应用程序后，按钮数应该仍然显示为 7。

![Flutter SharedPreferences Demo: Restart App](img/9af0fac42e0727163e71f1bf0a554105.png)

![Flutter SharedPreferences Demo: Increment to 7](img/70437ddd5e8a22056f1e5413707d1668.png)

## 使用 SharedPreferences 实现闪屏

我们可以在 Flutter 中使用 shared preferences[实现闪屏。](https://blog.logrocket.com/make-splash-screen-flutter/)

首次启动应用程序时会出现闪屏，随后启动应用程序时不会再次出现。

假设我们有一个 app，有两个屏幕:`ProfileScreen`和`SplashScreen`。我们将挂钩到`MyApp`小部件中的`initState`方法。从那里，我们将总是检查 SharedPreferences 中的数据—比如说`newLaunch`。如果此`newLaunch`为`true`，则显示`SplashScreen`，一段时间后显示`ProfileScreen`。`SplashScreen`会将`newLaunch`重置为`false`。

如果`newLaunch`是`false`，跳过`SplashScreen`，显示`ProfileScreen`。

让我们编写我们的闪屏和简介屏幕！

在`lib`文件夹中创建一个文件夹`screens`:

```
mkdir lib/screens
touch lib/screens/ProfileScreen.dart lib/screens/SplashScreen.dart

```

让我们按如下方式编辑`MyApp`小部件:

```
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'screens/ProfileScreen.dart';
import 'screens/SplashScreen.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: 'SharedPreferences Demo',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: LoadPage() //MyHomePage(title: 'SharedPreferences Demo'),
        );
  }
}

class LoadPage extends StatefulWidget {
  LoadPage({Key key}) : super(key: key);

  @override
  LoadPageState createState() => LoadPageState();
}

class LoadPageState extends State {
  var newLaunch;

  @override
  void initState() {
    super.initState();
    loadNewLaunch();
  }

  loadNewLaunch() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      bool _newLaunch = ((prefs.getBool('newLaunch') ?? true));
      newLaunch = _newLaunch;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(body: newLaunch ? SplashScreen() : ProfileScreen());
  }
}

```

我们有`LoadPage`小部件和它的状态小部件`LoadPageState`。在`LoadPageState`中，我们有一个方法`loadNewLaunch`，它检查从 SharedPrefs 中获取`newLaunch`数据，并将其分配给`newLaunch`状态变量。

在`LoadPageState`的`build`方法中，小部件检查`newLaunch`状态。如果状态是`true`，这意味着应用程序第一次启动，并且显示`SplashScreen`小部件。如果不是`true`，则显示`ProfileScreen`。

让我们编写`SplashScreen`小部件的代码:

```
import 'dart:async';
import 'package:shared_preferences/shared_preferences.dart';
import 'package:flutter/material.dart';
import 'ProfileScreen.dart';

class SplashScreen extends StatefulWidget {
  SplashScreen({Key key}) : super(key: key);

  @override
  _SplashScreenState createState() => _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {
  resetNewLaunch() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    if (prefs.containsKey("newLaunch")) {
      prefs.setBool('newLaunch', false);
    } else {
      prefs.setBool('newLaunch', false);
    }
  }

  @override
  void initState() {
    super.initState();
    Timer(
        Duration(seconds: 3),
        () => Navigator.pushReplacement(context,
                MaterialPageRoute(builder: (context) {
              resetNewLaunch();
              return ProfileScreen();
            })));
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Container(
            color: Colors.blue,
            child: Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: <Widget>[
                  Text('Splash Screen',
                      style: new TextStyle(color: Colors.white, fontSize: 40)),
                ],
              ),
        )
      )
    );
  }
}

```

`resetNewLaunch`方法将 SharedPrefs 中的`newLaunch`数据设置为`false`。

在`initState`方法中，`SplashScreen`调用`resetNewLaunch`方法将`newLaunch`数据设置为 SharedPrefs 中的`false`。这将导致应用程序在随后启动时仅显示`ProfileScreen`。

在显示`ProfileScreen`之前，计时器代码等待三秒钟。

现在让我们对`ProfileScreen`进行编码:

```
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

class ProfileScreen extends StatefulWidget {
  ProfileScreen({Key key}) : super(key: key);

  @override
  _ProfileScreenState createState() => _ProfileScreenState();
}

class _ProfileScreenState extends State<ProfileScreen> {
  clearSharedPrefs() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    prefs.remove('newLaunch');
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text("Profile"),
        ),
        body: Container(
          child: Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Text('Profile Screen', style: new TextStyle(fontSize: 40)),
                RaisedButton(
                  child: Text("Clear SharedPrefs"),
                  onPressed: clearSharedPrefs,
                )
              ],
            ),
          ),
        ));
  }
}

```

这里没什么可看的。我们有一个**清除 SharedPrefs** 按钮，它清除 SharedPrefs 中的`newLaunch`数据。此操作将使应用程序在下次启动时显示`SplashScreen`。

## 测试我们的闪屏

安装后首次启动时，闪屏和配置文件屏幕应如下所示:

![Flutter SharedPreferences Demo: Splash Screen on App Launch](img/cd4febec2fbdff4cc80939133ef3b180.png)

![Flutter SharedPreferences Demo: Profile Screen at Launch](img/a5fa87014d37f66bfbc1a9069701208f.png)

`SplashScreen`首先显示，随后是`ProfileScreen`。

后续启动应显示以下屏幕:

![Flutter SharedPreferences Demo: Profile Screen Upon Subsequent Launch](img/e28471a234b3ee3fd1eecc45e0bd866b.png)

![Flutter SharedPreferences Demo: Profile Screen Upon Subsequent Launch](img/c9b2e611fa9d2b77849119ec7335e924.png)![Flutter SharedPreferences Demo: Profile Screen Upon Subsequent Launch](img/40b3cfc9f05f28ddba2fd918787e1e4c.png)

`SplashScreen`永远不会再出现，而`ProfileScreen`则直线出现。

## 结论

在本教程中，我们介绍了什么是 SharedPreferences，以及如何在 Android 和 iOS 应用程序中使用它。我们还介绍了`shared_preferences`插件，并学习了如何用 SharedPreferences 设置 Flutter。

接下来，我们学习了如何导入`shared_preferences`插件，演示了如何初始化它的实例，并回顾了它的方法以及它们的作用。我们修改了一个普通的颤振计数器项目，以展示如何使用`shared_preferences`插件来保存计数器值，并在应用程序被终止并重启后检索它。

本教程中所有示例的源代码都可以在 [GitHub](https://github.com/flutter-prjs/shared_prefs) 上获得。