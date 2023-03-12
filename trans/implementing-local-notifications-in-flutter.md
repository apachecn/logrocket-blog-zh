# 在 Flutter - LogRocket 博客中实现本地通知

> 原文：<https://blog.logrocket.com/implementing-local-notifications-in-flutter/>

通知提醒用户有关他们订阅的应用程序和服务的重要信息。它们旨在改善用户体验，提高应用程序的参与度。

说到移动应用，有两种类型的通知，推送通知和本地通知。在本文中，我们将使用[flutter _ local _ notifications](https://pub.dev/packages/flutter_local_notifications)包在 Android 和 iOS 平台中实现本地通知。

# 什么是本地通知？

使用本地通知是一种在不使用互联网连接的情况下与用户互动并将他们的注意力吸引回你的应用程序的方式，提醒和待办事项应用程序等应用程序会大量使用它们。它们通常是预先安排好的，当用户在应用程序中执行某些操作时就会触发。

# 本地通知与推送通知

本地通知和推送通知的主要区别在于，本地通知由应用程序在本地安排，并由同一设备交付，而推送通知则从远程服务器发送。让我们构建一个项目，这样您就可以看到本地通知是如何工作的。

## 向 Flutter 应用程序添加依赖关系

第一步是在您的终端中运行下面的命令，将最新版本的 flutter_local_notifications 添加到您的`pubspec.yaml`文件中。

```
//run this command in the terminal 
$ flutter pub add flutter_local_notifications

```

接下来，创建一个名为`notification_service.dart`的新 Dart 文件。你可以给它任何你想要的文件名，但是我更喜欢根据它们的功能来命名文件。

在 Flutter 中，最好的做法是将逻辑从 UI 中分离出来。为此，我们将在`notification_service.dart`文件中创建一个名为`NotificationService`的类。这个类将处理所有的通知逻辑，并公开创建、发送、调度和取消通知的方法。

```
import 'package:flutter_local_notifications/flutter_local_notifications.dart';

class NotificationService {
  //Singleton pattern
  static final NotificationService _notificationService =
      NotificationService._internal();
  factory NotificationService() {
    return _notificationService;
  }
  NotificationService._internal();

    //instance of FlutterLocalNotificationsPlugin
  final FlutterLocalNotificationsPlugin flutterLocalNotificationsPlugin = 
      FlutterLocalNotificationsPlugin();
}

```

让我们分析一下上面的代码中发生了什么:

我们使用单例模式创建了`NotificationService`类。此外，在第 12 行，我们创建了一个`FlutterLocalNotificationsPlugin`的实例，它为 Android 和 iOS 平台初始化 Flutter 本地通知设置。

## 配置特定于平台的初始化设置

让我们来看看如何为 Android 和 iOS 平台配置初始化设置。

**配置 Android 初始化设置**
要配置 Android 初始化设置，我们需要传入一个必需的参数，它是将显示在通知栏中的应用程序图标:

```
final AndroidInitializationSettings initializationSettingsAndroid = 
  AndroidInitializationSettings('app_icon');

```

现在我们需要将我们的图标作为一个可提取的资源添加到 Android head 项目中。下面是完成此操作的完整路径:

`YOUR_APPLICATION_FOLDER_NAME\android\app\src\main\res\drawable\YOUR_APP_ICON.png`

**为 iOS 配置初始化设置**
为 iOS 配置这些设置有点复杂，因为我们必须考虑在不同版本的 iOS 操作系统中处理通知的多种方式。

首先，将下面几行添加到 iOS 项目的`AppDelegate.swift`文件中的`didFinishLaunchingWithOptions`方法中。

```
if #available(iOS 10.0, *) {
  UNUserNotificationCenter.current().delegate = self as? UNUserNotificationCenterDelegate
}

```

我们的`AppDelegate.swift`文件应该是这样的:

```
import UIKit
import Flutter
@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    if #available(iOS 10.0, *) {
      UNUserNotificationCenter.current().delegate = self as UNUserNotificationCenterDelegate
    }
    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}

```

`IOSInitializationSettings`对象接受三个参数:`requestSoundPermission`、`requestBadgePermission`和`requestAlertPermission`。这些参数控制用户请求的权限。

根据您的用例，您可以选择将所有通知权限设置为`false`，然后在应用程序的适当位置调用具有所需权限的`requestIOSPermissions`方法，如下所示。

```
//Initialization Settings for iOS devices 
    final IOSInitializationSettings initializationSettingsIOS =
        IOSInitializationSettings(
      requestSoundPermission: false,
      requestBadgePermission: false,
      requestAlertPermission: false,
    );

 void requestIOSPermissions(
    FlutterLocalNotificationsPlugin flutterLocalNotificationsPlugin) {
  flutterLocalNotificationsPlugin
      .resolvePlatformSpecificImplementation<
          IOSFlutterLocalNotificationsPlugin>()
      ?.requestPermissions(
        alert: true,
        badge: true,
        sound: true,
      );
}

```

## 创建`InitializationSettings`对象

下一步是创建一个`InitializationSettings`对象。这个插件用于初始化 Android 和 iOS 平台的设置。

一般来说，`InitializationSettings`有三个命名的可选参数，`android`、`iOS`和`macOS`，它们接受相应的平台初始化设置参数。

```
final InitializationSettings initializationSettings =
        InitializationSettings(
            android: initializationSettingsAndroid,
            iOS: initializationSettingsIOS);

```

在配置了特定于平台的初始化设置之后，我们将创建方法`init`，它将包含我们所有的初始化设置逻辑，并在应用程序启动时从我们的`main.dart`文件中被调用。

```
 Future<void> init() async {

    //Initialization Settings for Android
    final AndroidInitializationSettings initializationSettingsAndroid =
        AndroidInitializationSettings('app_icon');

    //Initialization Settings for iOS 
    final IOSInitializationSettings initializationSettingsIOS =
        IOSInitializationSettings(
      requestSoundPermission: false,
      requestBadgePermission: false,
      requestAlertPermission: false,
    );

    //InitializationSettings for initializing settings for both platforms (Android & iOS)
    final InitializationSettings initializationSettings =
        InitializationSettings(
            android: initializationSettingsAndroid,
            iOS: initializationSettingsIOS);

    await flutterLocalNotificationsPlugin.initialize(
      initializationSettings,
    );
  }

```

在上面的代码中，我们将特定于平台的初始化设置传递给了`InitializationSettings`对象。

下一步是在`FlutterLocalNotificationsPlugin`对象上调用`initialize`方法。这个方法接受两个参数,`InitializationSettings`对象和`onSelectNotification`属性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`onSelectNotification`属性接受一个回调函数，当通知被点击时该函数将被触发。这个函数包含一个名为`payload`的必需参数，它包含通过通知传递的任何数据。

```
Future selectNotification(String payload) async {
    await Navigator.push(
      context,
      MaterialPageRoute<void>(builder: (context) => SecondScreen(payload)),
    );
}

```

这里，这个回调函数将触发到`SecondScreen`的导航，并在用户点击通知时显示与通知相关联的`payload`。

我们的`init`方法现在应该是这样的:

```
 Future<void> init() async {

    //Initialization Settings for Android
    final AndroidInitializationSettings initializationSettingsAndroid =
        AndroidInitializationSettings('app_icon');

    //Initialization Settings for iOS 
    final IOSInitializationSettings initializationSettingsIOS =
        IOSInitializationSettings(
      requestSoundPermission: false,
      requestBadgePermission: false,
      requestAlertPermission: false,
    );

    //InitializationSettings for initializing settings for both platforms (Android & iOS)
    final InitializationSettings initializationSettings =
        InitializationSettings(
            android: initializationSettingsAndroid,
            iOS: initializationSettingsIOS);

    await flutterLocalNotificationsPlugin.initialize(
      initializationSettings, 
      onSelectNotification: selectNotification
    );
  }

Future selectNotification(String payload) async {
    await Navigator.push(
      context,
      MaterialPageRoute<void>(builder: (context) => SecondScreen(payload)),
    );
}

```

让我们回到我们的`main.dart`文件。在`main`函数中，当应用程序在 iOS 设备上启动时，我们将调用`init`方法和`requestiOSPermissions`方法向用户请求权限。

```
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await NotificationService().init(); // 
  await NotificationService().requestIOSPermissions(); // 
  runApp(MyApp());
}

```

## 在 Flutter 中显示通知

为了显示通知，我们需要创建一个特定于平台的`NotificationDetails`实例，该实例接受每个平台特有的参数。

`AndroidNotificationDetails`处理 Android 设备中通知的配置。它接受几个参数，比如`channelID`、`channelName`、`channelDescription`、`priority`、`importance`等等。

`iOSNotificationDetails`处理 iOS 设备中通知的配置，它接受诸如`presentAlert`、`presentBadge`、`badgeNumber`、`subtitle`、`sound`等参数。

下面是`AndroidNotificationDetails`实例的样子:

```
  AndroidNotificationDetails _androidNotificationDetails =
      AndroidNotificationDetails(
    'channel ID',
    'channel name',
    'channel description',
    playSound: true,
    priority: Priority.high,
    importance: Importance.high,
  );

```

下面是`iOSNotificationDetails`实例的样子:

```
 IOSNotificationDetails _iosNotificationDetails = IOSNotificationDetails(
    presentAlert: bool?,
    presentBadge: bool?,
    presentSound: bool?,
    badgeNumber: int?
    attachments: List<IOSNotificationAttachment>?
    subtitle: String?, 
        threadIdentifier: String?
  );

```

现在，下一步是创建一个`NotificationDetails`对象，它接受特定于平台的通知细节对象作为参数。

```
const NotificationDetails platformChannelSpecifics = 
  NotificationDetails(
    android: _androidNotificationDetails,
    iOS: _iOSNotificationDetails);

```

接下来我们需要调用`FlutterLocalNotificationsPlugin`的`show`方法。`show`方法负责创建推送通知，它需要一些参数，如`id`、`title`、`body`、`notificationDetails`和`payload`。

> `id`:通知的唯一标识符
> `title`:通知的标题
> `body`:通知消息
> `notificationDetails`:其中我们传入的`notificationDetails`对象
> `payload`:保存当通知被点击时通过通知传递的数据

```
await flutterLocalNotificationsPlugin.show(
      0,
      'Notification Title',
      'This is the Notification Body',
      platformChannelSpecifics,
      payload: 'Notification Payload',
    );

```

现在，让我们创建一个`showNotification`方法并将所有的逻辑封装在其中，然后我们可以从任何地方调用这个方法来显示通知。

```
class NotificationService {
  ....
  Future<void> showNotifications() async {
    await flutterLocalNotificationsPlugin.show(
      0,
      'Notification Title',
      'This is the Notification Body',
      platformChannelSpecifics,
      payload: 'Notification Payload',
    );
  }
}

```

## 在 Flutter 中调度本地通知

为了安排一个通知，我们需要调用`FlutterLocalNotificationsPlugin`的`zoneSchedule`方法。这个方法需要一个由`timezone`包提供的`TZDateTime`类的实例。

因为`flutter_local_notifications`插件已经依赖于`timezone`包，所以没有必要在我们的`pubspec.yaml`文件中添加`timezone`包作为依赖项。我们只需要将它导入到我们的`notification_service.dart`文件中并初始化它。

```
import 'package:flutter_local_notifications/flutter_local_notifications.dart';
import 'package:timezone/data/latest.dart' as tz;
import 'package:timezone/timezone.dart' as tz;

....

Future<void> init() async {
    final AndroidInitializationSettings initializationSettingsAndroid =
        AndroidInitializationSettings('app_icon');

    final IOSInitializationSettings initializationSettingsIOS =
        IOSInitializationSettings(
      requestSoundPermission: false,
      requestBadgePermission: false,
      requestAlertPermission: false,
    );

    final InitializationSettings initializationSettings =
        InitializationSettings(
            android: initializationSettingsAndroid,
            iOS: initializationSettingsIOS);

    //initialize timezone package here 
    tz.initializeTimeZones();  //  <----

    await flutterLocalNotificationsPlugin.initialize(
      initializationSettings, 
      onSelectNotification: selectNotification
    );
}

```

`zoneSchedule`方法接受几个参数，包括`id`、`title`、`body`、`scheduledDate`、`notificationDetails`、`payload`、`uiLocalNotificationDateInterpretation`和`androidAllowWhileIdle`。

> `scheduleDate`参数指定何时应该显示通知。`androidAllowWhileIdle`设置为`true`时，无论设备是否处于低功耗模式，都会确保显示预定通知。

```
await flutterLocalNotificationsPlugin.zonedSchedule(
        0,
        "Notification Title",
        "This is the Notification Body!",
        tz.TZDateTime.now(tz.local).add(const Duration(seconds: 5)),
        platformChannelSpecifics,
        androidAllowWhileIdle: true,
        uiLocalNotificationDateInterpretation:
            UILocalNotificationDateInterpretation.absoluteTime);

```

现在，让我们创建一个`scheduleNotification`方法并将所有的逻辑封装在其中，然后我们可以从任何地方调用这个方法来创建一个预定的通知。

```
class NotificationService {
  ....
  Future<void> scheduleNotifications() async {
    await flutterLocalNotificationsPlugin.zonedSchedule(
        0,
        "Notification Title",
        "This is the Notification Body!",
        tz.TZDateTime.now(tz.local).add(const Duration(minutes: 5)),
        platformChannelSpecifics,
        androidAllowWhileIdle: true,
        uiLocalNotificationDateInterpretation:
            UILocalNotificationDateInterpretation.absoluteTime);
  }
}

```

## 在 Flutter 中取消本地通知

取消通知时，您可以取消特定通知，也可以取消所有待定通知。让我们来看看您如何做到这一点:

**取消单个通知**
要取消一个特定的通知，让我们创建一个名为`cancelNotification`的新方法，它将包含来自`FlutterLocalNotificationsPlugin`对象的`cancel`方法。该方法需要一个参数，即通知的`id`。

```
class NotificationService {
  ....
  Future<void> cancelNotifications() async {
    await flutterLocalNotificationsPlugin.cancel(NOTIFICATION_ID);
  }
}

```

**取消所有通知**
要取消所有未决通知，让我们创建一个新方法`cancelAllNotifications`，它将包含来自`FlutterLocalNotificationsPlugin`对象的`cancelAll`方法。

与取消单个通知不同，取消通知的方法只接受一个参数，这个方法不接受任何参数。

```
class NotificationService {
  ....
  Future<void> cancelAllNotifications() async {
    await flutterLocalNotificationsPlugin.cancelAll();
  }
}

```

这里有一个 [GitHub 库](https://github.com/iloveteajay/flutter_local_notification/tree/master/tasker)，它包含了本教程的所有代码。如果您想看到最终的构建，只需克隆这个库并在您的计算机上运行它。

## 结论

本地通知对于通知或提醒用户重要信息非常有用，并且可以在没有互联网连接的情况下实现。

你可以阅读 flutter_local_notifications 包[文档](https://pub.dev/packages/flutter_local_notifications)来学习在你的项目中使用本地通知的其他方法。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)