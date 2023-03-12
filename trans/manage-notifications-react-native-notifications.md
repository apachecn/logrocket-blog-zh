# 使用 React-Native-notifications-log rocket 博客管理 React Native 中的通知

> 原文：<https://blog.logrocket.com/manage-notifications-react-native-notifications/>

通知在我们的应用程序中扮演着重要的角色。他们是推动用户参与我们应用的主要来源。通常，公司会通知用户新收到的消息，例如，钱包应用程序中的通知会通知用户即将支付的账单。

我们可以在应用中实现多种类型的通知，例如远程和本地通知、交互式通知和静默通知。在本文中，我们将介绍如何使用[react-native-notifications](https://github.com/wix/react-native-notifications)实现这些类型的通知。

## 什么是反应本地通知？

React-native-notifications 是由 [Wix](https://www.wix.com/) 开发的开源产品，可以帮助开发者在 React 原生应用中轻松实现通知。根据[文档](https://wix.github.io/react-native-notifications/docs/installation-ios)，你可能会将其与 iOS 10 和 Android 5 (API 21)集成。它也可以在 Windows、macOS 和 Linux 操作系统上编译。

在撰写本文时，该库支持:

*   远程通知
*   本地通知
*   服务器管理的通知
*   交互式通知

## 安装 react-native-通知

让我们从安装库开始。本指南假设您正在运行 React Native v10.x。

使用 npm:

```
npm install --save react-native-notifications

```

带纱线:

```
yarn add react-native-notifications

```

现在，我们需要对本机文件进行一些更改，以使用 react-native-notifications。

### 将 react-native-notifications 与 iOS 链接

#### 如果没有安装`cocoapods`

用`cocoapods`反应原生作品。如果您的计算机上没有安装，请运行以下命令:

```
sudo gem install cocoapods

```

现在使用`cd`移动到您的项目目录。

#### 如果在安装 react-native-notifications 之前安装了`cocoapods`

是时候将`cocoapods`安装到项目的`iOS`目录中了。当我们安装 react-native-notification 库时，它会自动将 pod 添加到`Podfile`中。所以，我们只需要运行这个命令:

```
pod install --project-directory=ios/
```

#### 如果`cocoapods`是后来安装的并且`react-native-notifications`没有将吊舱添加到`Podfile`

如果您刚刚安装了`cocoapods`或者`Podfile`中没有添加所需的`pod`，那么您可以手动添加:

打开`./ios/Podfile`(假设您在项目目录中)并将其添加到窗格列表中:

```
pod 'react-native-notifications', :podspec => '../node_modules/react-native-notifications/react-native-notifications.podspec'

```

现在运行这个:

```
cd iOS && pod install

```

下一步，我们将在`AppDelegate.m`文件中做一些修改。打开`./ios/{project_name}/AppDelegate.m`文件并完成以下步骤。

1.  将这一行添加到文件的顶部:

    ```
    #import "RNNotifications.h"
    ```

2.  从下面的代码片段开始【T0:

    ```
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {     [RNNotifications startMonitorNotifications]; // -> Add this line      return YES; }
    ```

3.  增加以下功能支持注册:

    ```
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {   [RNNotifications didRegisterForRemoteNotificationsWithDeviceToken:deviceToken]; }  - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error {   [RNNotifications didFailToRegisterForRemoteNotificationsWithError:error]; }
    ```

### 将 react-native-notifications 与 Android 链接

首先打开`MainApplication.java`并将其添加到库中:

```
import com.wix.reactnativenotifications.RNNotificationsPackage;

```

在 Android 上，推送通知由[Google Firebase Cloud Messaging(FCM)](https://firebase.google.com/docs/cloud-messaging)管理，所以你需要继续使用它。如果你以前没有在你的项目中使用过 Firebase，那么就按照[设置指南](https://firebase.google.com/docs/cloud-messaging/android/client)中提供的步骤操作。

我们需要在`./android/settings.gradle`文件中链接 react-native-notifications，就像这样:

```
include ':react-native-notifications'
project(':react-native-notifications').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-notifications/lib/android/app')

```

如果你愿意，你可以为 Android 中的通知设置一些默认设置，比如默认图标，文本颜色等。，要显示。如果我们在发送通知时不提供特定设置，将使用这些设置。

接下来，打开`AndroidManifest.xml`并添加以下代码:

```
<!-- Set custom default icon. This is used when no icon is set for incoming notification messages. -->
<meta-data
        android:name="com.google.firebase.messaging.default_notification_icon"
        android:resource="@drawable/notification_icon" />
<!-- Set color used with incoming notification messages. This is used when no color is set for the incoming notification message. -->
<meta-data
        android:name="com.google.firebase.messaging.default_notification_color"
        android:resource="@color/colorAccent" />

```

这里，我们设置了一个默认的通知图标，它存储在一个可绘制的文件夹中。请注意，图标名称应该是`notification_icon`。我们还设置了一个默认的文本颜色，我们将把它添加到`colors.xml`文件中。

```
<resources>
    <color name="colorAccent">#C60C30</color>
</resources>

```

## 在 React Native 中实现推送通知

首先，我们需要了解推送通知是如何工作的。每个设备都有唯一的设备 ID。要发送通知，我们需要此 ID。让我们一步步回顾一下如何实现推送通知。

1.  将设备注册到供应商的服务器(例如 FCM)或在 iOS 中获得权限
2.  获取刷新令牌并存储在通知服务器中，如[亚马逊 SNS](https://aws.amazon.com/sns/)
3.  使用存储的令牌发送通知

### 获得许可并注册设备

在 iOS 中，我们需要获得用户的许可才能显示通知。

![Notifications Prompt](img/7046e46602cff3aea464dfd25f986175.png)

Source: [Apple.com](https://developer.apple.com/documentation/usernotifications/asking_permission_to_use_notifications)

在 Android 上，我们在 FCM 上注册设备并获得刷新令牌。

这是通过调用`remoteNotificationsRegistered()`函数来完成的。它适用于两个平台。

```
import { Notifications } from 'react-native-notifications';

class App extends Component {
    constructor() {
        // Request permissions on iOS, refresh token on Android
        Notifications.registerRemoteNotifications();

        Notifications.events().registerRemoteNotificationsRegistered((event: Registered) => {
            // TO-DO: Send the token to my server so it could send back push notifications...
            console.log("Device Token Received", event.deviceToken);
        });
        Notifications.events().registerRemoteNotificationsRegistrationFailed((event: RegistrationError) => {
            console.error(event);
        });
    }
}

```

首先，我们在`App.js`中导入 react-native-notifications 库。在构造函数中，我们调用`registerRemoteNotifications()`，它会在 iOS 上显示权限对话框，在 Android 上的 FCM 上注册 app。

根据该函数的成功情况，将调用`registerRemoteNotificationsRegistered()`函数或`registerRemoteNotificationsRegistrationFailed()`函数。

如果失败，则不允许您发送通知。您可以稍后再次尝试权限，并希望成功。

如果成功，那么`registerRemoteNotificationsRegistered()`将提供`deviceToken`，我们需要将它发送到后端并向通知服务注册。

下面介绍如何检查用户在 iOS 上授予了哪些权限。

```
Notifications.ios.checkPermissions().then((currentPermissions) => {
    console.log('Badges enabled: ' + !!currentPermissions.badge);
    console.log('Sounds enabled: ' + !!currentPermissions.sound);
    console.log('Alerts enabled: ' + !!currentPermissions.alert);
    console.log('Car Play enabled: ' + !!currentPermissions.carPlay);
    console.log('Critical Alerts enabled: ' + !!currentPermissions.criticalAlert);
    console.log('Provisional enabled: ' + !!currentPermissions.provisional);
    console.log('Provides App Notification Settings enabled: ' + !!currentPermissions.providesAppNotificationSettings);
    console.log('Announcement enabled: ' + !!currentPermissions.announcement);
});
```

### 在 React Native 中处理收到的通知

从您的服务器，您可以使用刷新令牌发送通知。现在是时候在你的应用程序中处理它们了，但我们如何做取决于应用程序是在前台还是后台。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当应用程序在前台时——意味着它是活动的并被用户使用——触发`notificationReceivedForeground()`事件。同样，如果应用程序在后台运行，将触发`notificationReceivedBackground()`事件。

下面是应用程序是否在前台运行的代码块:

```
Notifications.events().registerNotificationReceivedForeground((notification: Notification, completion: (response: NotificationCompletion) => void) => {
      console.log("Notification Received - Foreground", notification.payload);

      // Calling completion on iOS with `alert: true` will present the native iOS inApp notification.
      completion({alert: true, sound: true, badge: false});
        });

```

重要的是调用`completion`回调来表明该过程已经完成。

如果应用程序在后台，下面是代码。

```
Notifications.events().registerNotificationReceivedBackground((notification: Notification, completion: (response: NotificationCompletion) => void) => {
      console.log("Notification Received - Background", notification.payload);

      // Calling completion on iOS with `alert: true` will present the native iOS inApp notification.
      completion({alert: true, sound: true, badge: false});
        });

```

如果一个通知在用户点击时被打开，一个`notificationOpened`事件将被触发。

```
Notifications.events().registerNotificationOpened((notification: Notification, completion: () => void, action: NotificationActionResponse) => {
      console.log("Notification opened by device user", notification.payload);
      console.log(`Notification opened with an action identifier: ${action.identifier} and response text: ${action.text}`);
      completion();
        });

```

在`notification.payload`中接收的数据是:

*   `identifier`
*   `title`
*   `subtitle`
*   `body`
*   `sound`
*   `badge`
*   `category`
*   `payload`

## 从 React 本机应用程序发送本地通知

当我们从应用程序发送通知时，它们被称为本地通知。使用`postLocalNotification()`在 React Native 中完全支持它们。

```
let localNotification = Notifications.postLocalNotification({
    body: "Local notification!",
    title: "Local Notification Title",
    sound: "chime.aiff",
    silent: false,
    category: "SOME_CATEGORY",
    userInfo: { },
    fireDate: new Date(),
});

```

有了它，我们可以传递各种选项，如正文、标题、声音和类别。`fireDate`属性将通知安排在一个日期和时间，当您想要在将来显示通知时，它是必需的。

此属性仅在 iOS 上有效。该事件返回一个惟一的通知 ID，我们将它存储在一个本地变量`localNotification`中。这用于在需要时取消预定的通知。

## 取消 React Native 中的本地通知

要取消本地通知，我们需要将`localNotification`变量传递给`cancelLocalNotification()`函数。

```
Notifications.cancelLocalNotification(localNotification);

```

对于 iOS，我们有更多的功能可以用来取消通知:

1.  `cancelAllLocalNotifications()`用于取消所有预定的本地通知。它不需要任何通知
2.  `removeDeliveredNotifications()`从[通知中心](https://developer.apple.com/documentation/foundation/notificationcenter)删除已经发送的通知。您需要传递通知 id 数组来移除它们
3.  从通知中心删除所有已发送的通知

## 结论

React-native-notifications 是 React native 和 Native 通知 API 之间的桥梁，它适用于 Android 和 iOS。

在这篇博文中，我们了解了推送和本地通知，以及它们如何帮助提高用户参与度。尽管许多选项是特定于平台的，但 react-native-notifications 可以很好地处理这两个平台，并尽可能提供通用功能。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)