# 用 Uni Links 理解 Flutter 中的深度链接

> 原文：<https://blog.logrocket.com/understanding-deep-linking-flutter-uni-links/>

深度链接为您提供了一个指向已安装应用程序特定部分的 web 浏览器链接。这些链接还可以设置为将用户导航到特定的内容页面(如活动、新闻更新等)，并传递自定义数据(如促销代码)。

例如，如果你想与朋友分享这篇文章，那么你可以发送一个指向这篇文章的 URL，而不是 blog.logrocket.com 网站来导航和查找这篇文章。这意味着您需要手动或使用深层链接来处理应用程序的触发方式。

此外，当深层链接被触发时，您的应用程序可能已经在运行，因此您也需要在后台运行的应用程序中处理深层链接点击。

在本教程中，您将学习如何使用 uni_links 来帮助您。

*注意，如果你是 Flutter 新手，请通过官方文档了解一下。*

## 什么是 Uni 链接？

[Uni Links (uni_links)](https://pub.dev/packages/uni_links) 是一个 Flutter 插件，用于接收传入的 App/深度链接(适用于 Android)以及通用链接和自定义 URL 方案(适用于 iOS)。

它目前支持 Android、iOS 和 web 平台。

## 设置 Uni 链接

在 pubspec 依赖关系中添加`uni_links`:

```
uni_links: ^0.5.1
```

接下来，您需要在 Android 或 iOS 配置文件中声明链接的模式。

### Android 配置

在 Android 中，有两种类型的 Uni 链接:

*   App 链接:这个链接需要一个指定的主机，一个托管的文件(`assetlinks.json`)，它只和`https`方案( [https://your_host](https://your_host) )一起工作。这是您需要添加到配置文件中的应用程序链接意图过滤器。你可以根据自己的喜好更换主持人:

    ```
    <!-- App Links -->       <intent-filter android:autoVerify="true">         <action android:name="android.intent.action.VIEW" />         <category android:name="android.intent.category.DEFAULT" />         <category android:name="android.intent.category.BROWSABLE" />         <!-- Accepts URIs that begin with https://YOUR_HOST -->         <data           android:scheme="https"           android:host="unilinks.example.com" />       </intent-filter>
    ```

*   深层链接:这种链接不需要主机、主机文件或任何自定义方案。它提供了一种使用 URL:your _ scheme://any _ host来利用你的应用的方法。这是您需要添加到配置中的深度链接意图过滤器。您还可以更改方案和主机:

    ```
    <!-- Deep Links --> <intent-filter> <action android:name="android.intent.action.VIEW" /> <category android:name="android.intent.category.DEFAULT" />  <category android:name="android.intent.category.BROWSABLE" />  <!-- Accepts URIs that begin with YOUR_SCHEME://YOUR_HOST -->  <data android:scheme="logrckt" android:host="unilinks.example.com" />  </intent-filter>
    ```

您需要在您的主 AndroidManifest.xml 文件(`android/app/src/main/AndroidManifest.xml`)中声明这些意图过滤器中的任何一个。

### iOS config

在 iOS 中，还有两种类型的 uni 链接:

*   通用链接:这些链接只适用于`https`方案，并且需要指定的主机、权限和托管文件。类似安卓里的 App 链接。您需要通过 Xcode 或编辑`ios/Runner/Runner.entitlements`文件:

    ```
    <?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"> <plist version="1.0"> <dict>   <!-- ... other keys -->   <key>com.apple.developer.associated-domains</key>   <array>     <string>applinks:[YOUR_HOST]</string>   </array>   <!-- ... other keys --> </dict> </plist>
    ```

    来添加或创建一个`com.apple.developer.associated-domains`权限
*   自定义 URL:该 URL 不需要主机、权限、托管文件或任何自定义方案。类似 Android 中的一个深度链接，需要在`ios/Runner/Info.plist`文件中添加主机和方案如下:

    ```
    <key>CFBundleURLTypes</key>   <array>       <dict>           <key>CFBundleTypeRole</key>           <string>Editor</string>           <key>CFBundleURLName</key>           <string>unilinks.example.com</string>           <key>CFBundleURLSchemes</key>           <array>               <string>logrckt</string>           </array>       </dict>   </array>
    ```

*N.B、任何 app 都可以在安卓深度链接、iOS 自定义网址的情况下认领你的方案和主机组合，所以要保证你的主机和方案尽可能的唯一。*

## 使用

如前所述，您的应用程序有两种方式来处理深层链接:

*   冷启动:如果应用程序被终止，冷启动将重新启动应用程序(不在后台运行)。在这种情况下，`_initURIHandler`将被调用并拥有初始链接
*   回到前台:如果应用程序在后台运行，你需要把它带回到前台，流将产生链接。初始链接可以为空，也可以是应用程序启动时使用的链接

在应用程序的生命周期中，`_initURIHandler`应该只处理一次，因为它用于启动应用程序，并且在应用程序的整个过程中不会改变。所以，在你的`main.dart`中的任何地方创建一个全局变量`_initialURILinkHandled`作为`false`:

```
bool _initialURILinkHandled = false;
```

在您的`main.dart`文件中，通过删除现有代码并创建新变量来清理您的`MyHomePage`小部件，如下所示:

```
Uri? _initialURI;
Uri? _currentURI;
Object? _err;

StreamSubscription? _streamSubscription;
```

你在这里声明:

*   两个`Uri`变量用于识别初始和有效/当前 URI，
*   一个`Object`用于在链接解析出现故障时存储错误
*   一个`StreamSubscription`对象，当应用程序在前台时，它会监听传入的链接

接下来，创建如下的`_initURIHandler`方法:

```
Future<void> _initURIHandler() async {
 // 1
 if (!_initialURILinkHandled) {
   _initialURILinkHandled = true;
   // 2
   Fluttertoast.showToast(
       msg: "Invoked _initURIHandler",
       toastLength: Toast.LENGTH_SHORT,
       gravity: ToastGravity.BOTTOM,
       timeInSecForIosWeb: 1,
       backgroundColor: Colors.green,
       textColor: Colors.white
   );
   try {
     // 3
     final initialURI = await getInitialUri();
     // 4
     if (initialURI != null) {
       debugPrint("Initial URI received $initialURI");
       if (!mounted) {
         return;
       }
       setState(() {
         _initialURI = initialURI;
       });
     } else {
       debugPrint("Null Initial URI received");
     }
   } on PlatformException { // 5
     debugPrint("Failed to receive initial uri");
   } on FormatException catch (err) { // 6
     if (!mounted) {
       return;
     }
     debugPrint('Malformed Initial URI received');
     setState(() => _err = err);
   }
 }
}
```

在上面的代码中，您执行了以下操作:

1.  这里使用了一个检查，这样即使在小部件被释放的情况下,`_initURIHandler`也只会被调用一次
2.  当这个方法被调用时，使用`[fluttertoast](https://pub.dev/packages/fluttertoast)`包显示一个 toast
3.  使用`getInitialUri`方法解析并返回链接，作为`initialURI`变量中的新 URI
4.  检查`initialURI`是否为空。如果不为空，设置`_initialURI`值 w.r.t `initialURI`
5.  使用`PlatformException`处理平台消息失败
6.  如果链接作为 URI 无效，则处理`FormatException`

接下来，创建用于在应用程序已经启动时接收链接的`_incomingLinkHandler`方法:

```
void _incomingLinkHandler() {
 // 1
 if (!kIsWeb) {
   // 2
   _streamSubscription = uriLinkStream.listen((Uri? uri) {
     if (!mounted) {
       return;
     }
     debugPrint('Received URI: $uri');
     setState(() {
       _currentURI = uri;
       _err = null;
     });
     // 3
   }, onError: (Object err) {
     if (!mounted) {
       return;
     }
     debugPrint('Error occurred: $err');
     setState(() {
       _currentURI = null;
       if (err is FormatException) {
         _err = err;
       } else {
         _err = null;
       }
     });
   });
 }
}
```

这段代码执行了以下操作:

1.  增加了一个检查平台的条件，因为网络平台只会将链接作为初始链接来处理
2.  监听传入链接流，并更新`_currentURI`和`_err`变量
3.  使用`onError`事件处理错误，并更新`_currentURI`和`_err`变量

在创建了这些方法来监听传入的链接之后，您需要在呈现小部件树之前调用它们。在`MyHomePage`小部件的`initState`中调用这些方法:

```
@override
void initState() {
 super.initState();
 _initURIHandler();
 _incomingLinkHandler();
}
```

类似地，要在应用程序终止时释放资源，请关闭`dispose`方法中的`StreamSubscription`对象:

```
@override
void dispose() {
 _streamSubscription?.cancel();
 super.dispose();
}
```

接下来，更新`build`方法:

```
@override
Widget build(BuildContext context) {
 return Scaffold(
     appBar: AppBar(
       title: Text(widget.title),
     ),
     body: Center(
         child: Padding(
       padding: const EdgeInsets.symmetric(horizontal: 20),
       child: Column(
         mainAxisAlignment: MainAxisAlignment.center,
         children: <Widget>[
           // 1
           ListTile(
             title: const Text("Initial Link"),
             subtitle: Text(_initialURI.toString()),
           ),
           // 2
           if (!kIsWeb) ...[
             // 3
             ListTile(
               title: const Text("Current Link Host"),
               subtitle: Text('${_currentURI?.host}'),
             ),
             // 4
             ListTile(
               title: const Text("Current Link Scheme"),
               subtitle: Text('${_currentURI?.scheme}'),
             ),
             // 5
             ListTile(
               title: const Text("Current Link"),
               subtitle: Text(_currentURI.toString()),
             ),
             // 6
             ListTile(
               title: const Text("Current Link Path"),
               subtitle: Text('${_currentURI?.path}'),
             )
           ],
           // 7
           if (_err != null)
             ListTile(
               title:
                   const Text('Error', style: TextStyle(color: Colors.red)),
               subtitle: Text(_err.toString()),
             ),
           const SizedBox(height: 20,),
           const Text("Check the blog for testing instructions")
         ],
       ),
     )));
}
```

在这里，您已经完成了以下操作:

1.  使用`_initialURI`变量显示收到的初始链接
2.  增加了一个复选框，只在移动平台上显示链接
3.  显示了传入链接的主机。我们已经在前面定义了主机
4.  与主机类似，显示了之前配置的传入链路的方案
5.  使用`_currentURI`变量显示当前或活动的输入链接
6.  显示了与主机和方案一起出现的路径
7.  如果不为空，则显示错误

构建并重新启动应用程序；它看起来会像这样:

![Uni Links Demo Desktop](img/0c59ffcf50b582b84085effbf313915f.png)

![Uni Links Demo Mobile](img/c019bd5a9905c6e7b0058ee985e4d830.png)

因为应用程序手动重启，所以`ListTile`的所有字幕在手机上都将是空的。在下一步中，您将测试您的更改。

## 测试你的深层链接

您可以通过使用 CLI 工具调用与您注册的方案的链接来测试您的更改。

### 机器人

如果已经安装了 Android Studio(带有 SDK 平台工具),您可以执行以下操作:

```
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "logrckt://host/path/subpath"'
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "logrckt://unilinks.example.com/path/portion/?uid=123&token=abc"'
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "logrckt://unilinks.example.com/?arr%5b%5d=123&arr%5b%5d=abc&addr=1%20Nowhere%20Rd&addr=Rand%20City%F0%9F%98%82"'
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "logrckt://[email protected]@malformed.invalid.url/path?"'
```

这将根据您的方案和主机发送呼叫到您的应用程序，您的应用程序将出现在前台。

如果您的路径中没有 ADB，但是有`$ANDROID_HOME` env 变量，那么使用`"$ANDROID_HOME"/platform-tools/…`和上面的命令。

或者，您可以简单地在 ADB shell 中运行该命令。

### ios

如果 Xcode 已经安装，您可以执行以下操作:

```
/usr/bin/xcrun simctl openurl booted "logrckt://host/path/subpath"
/usr/bin/xcrun simctl openurl booted "logrckt://unilinks.example.com/path/portion/?uid=123&token=abc"
/usr/bin/xcrun simctl openurl booted "logrckt://unilinks.example.com/?arr%5b%5d=123&arr%5b%5d=abc&addr=1%20Nowhere%20Rd&addr=Rand%20City%F0%9F%98%82"
/usr/bin/xcrun simctl openurl booted "logrckt://[email protected]@malformed.invalid.url/path?"
```

如果您的路径中有`xcrun`(或`simctl`)，您可以直接调用它。标志 booted 假定一个开放的模拟器带有一个引导的设备。

*注意，对于 App 链接或者通用链接，可以用`logrckt`的`https`方案试试上面的例子。*

![Uni Links Demo iOS](img/cae1dae5000c97ba3a4fec6909ac2184.png)

## 结论

你可以在这里找到最终项目。

在本教程中，您了解了深度链接以及如何在 Flutter 应用程序中使用它。下一步，您可以尝试使用 [Firebase 动态链接](https://firebase.flutter.dev/docs/dynamic-links/overview)进行深度链接，因为即使用户没有安装应用程序，也可以通过将它们重定向到 Play/App Store 进行安装。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)