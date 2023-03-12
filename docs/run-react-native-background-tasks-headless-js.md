# 使用 Headless JS 运行 React 本机后台任务

> 原文：<https://blog.logrocket.com/run-react-native-background-tasks-headless-js/>

后台处理是移动应用程序开发的一个重要部分，尤其是当您想要轮询服务器或定期向服务器发送数据时。通常，如果用户看不到任何活动，应用程序就在后台。

在 React Native 中，在后台执行任务一开始可能会让人望而生畏。它不像在 JavaScript 中编写函数那么简单，即使在活动被终止后，函数也会被执行。相反，React Native 使用 [Headless JS](https://reactnative.dev/docs/headless-js-android) 在后台执行 JavaScript 代码。

在本文中，我们将学习使用原生 API 和 Headless JS 在 React Native 中执行后台操作。Headless JS 只适用于 Android，因此，在本文中，我们将只关注 Android 开发。首先，让我们回顾一下我们将使用的一些基本工具。

### 无头 JS

Headless JS 是一种在后台运行 JavaScript 任务的方式。显然，我们不能在这些任务中接触 UI，但是我们可以像在`AppRegistry`中注册 React 应用程序一样注册一个`HeadlessTask`:

```
AppRegistry.registerHeadlessTask('DemoTask', () =>  require('DemoTask'));

```

### 本机模块

本地模块允许我们在 JavaScript 中执行本地代码。你可以把它看作是在 JavaScript 层和原生层之间架起一座桥梁。

### 工作管理器

[WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) 是 Android 中执行后台任务的推荐方式。WorkManager 能够以简单、可靠的方式安排一次性或周期性任务。

## 设置 Headless JS

让我们构建一个玩具应用程序，它将使用 Headless JS 执行定期任务。我们的应用程序的设置有三个步骤:

*   像通常使用 React Native CLI 一样设置 React Native 项目
*   将工作管理器依赖项添加到应用程序的`build.gradle`中，如下所示:

```
implementation 'androidx.work:work-runtime:workmanagerversion'
```

或者，您可以设置一个简单的 Node.js Echo 服务器，并将您的设备连接到与您的开发机器相同的网络。我更喜欢在真实的设备上测试，例如，我会[通过 Wi-Fi](https://reactnative.dev/docs/0.61/running-on-device#method-2-connect-via-wi-fi-2) 将我的设备连接到开发服务器，然后我就可以向运行在同一台机器上的节点服务器发出真实的请求。

## 构建我们的 React 本地应用程序

### 创建后台工作人员

我们将从创建我们的后台工作者开始。`BackgroundWorker`类扩展了`Worker`类，后者提供了一个`doWork`方法，所有后台任务将在该方法中发生:

```
public class BackgroundWorker extends Worker {
    private final Context context;

    public BackgroundWorker(@NonNull Context context, @NonNull WorkerParameters workerParams) {
        super(context, workerParams);
        this.context = context;
    }
    @NonNull
    @Override
    public Result doWork() {

        // background work will take place here
        Log.w("bg", "Worker do work");
        return Result.success();

    }
}

```

`Result.success()`告诉工作管理器工作已成功完成。我们将在`doWork`开始我们的无头服务。

告诉 WorkManager 我们的后台任务应该如何以及何时执行。在我们的例子中，我们想要一个周期性的任务，所以我们将使用`PeriodicWorkRequest`。对于一次性的工作任务，你可以使用`OneTimeWorkRequest`:

```
workRequest = new PeriodicWorkRequest.Builder(BackgroundWorker.class, 20, TimeUnit.MINUTES).build();

```

### 创建本机模块

接下来，我们将创建一个本机模块，它提供了一种通过单击按钮来创建和排队工作请求的方法。我们的工作请求导致 WorkManager 定期执行我们的工作。

我们的事件处理程序将使用 JavaScript 编写，因此我们需要一种使用 JavaScript 调用本机方法的方法:

```
// BackgroundModule.java
public class BackgroundModule extends ReactContextBaseJavaModule {
    private static final String MODULE_NAME = "BackgroundWorkManager";

    private Context mContext;
    private PeriodicWorkRequest workRequest;

    BackgroundModule(@Nonnull ReactApplicationContext reactContext) {
        super(reactContext);
        mContext = reactContext;
        workRequest = new PeriodicWorkRequest.Builder(BackgroundWorker.class, 20, TimeUnit.MINUTES).build();
    }

    @ReactMethod
    public void startBackgroundWork() {
        WorkManager.getInstance(mContext).enqueueUniquePeriodicWork("testWork", ExistingPeriodicWorkPolicy.KEEP,workRequest);
    }

    @ReactMethod
    public void stopBackgroundWork() {
        WorkManager.getInstance(mContext).cancelUniqueWork("testWork");
    }

    @Nonnull
    @Override
    public String getName() {
        return MODULE_NAME;
    }
}

```

`getName`方法应该返回模块的名称，所以我们可以在 JavaScript 代码中得到这个模块，如下所示:

```
const { BackgroundWorkManager } = ReactNative.NativeModules;

```

然后，可以使用以下方法访问标有`@ReactMethod`的方法:

```
BackgroundWorkManager.method_name()

```

最后，为了让我们的本机模块工作，我们需要创建一个`ReactPackage`，并将其添加到由`MainApplication.java`中的`getPackages()`返回的列表中:

```
// BackgroundPackage.java
public class BackgroundPackage implements ReactPackage {
    @Nonnull
    @Override
    public List<NativeModule> createNativeModules(@Nonnull ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        modules.add(new BackgroundModule(reactContext));
        return modules;
    }

    @Nonnull
    @Override
    public List<ViewManager> createViewManagers(@Nonnull ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
}
```

在`MainApplication.java`中，添加以下内容:

```
protected List<ReactPackage> getPackages() {
  @SuppressWarnings("UnnecessaryLocalVariable")
  List<ReactPackage> packages = new PackageList(this).getPackages();
  // Packages that cannot be autolinked yet can be added manually here, for example:
  // packages.add(new MyReactNativePackage());
    packages.add(new BackgroundPackage());
  return 

```

### 添加无头任务服务

有了我们的本机模块，让我们继续添加 Headless 任务服务，这是我们试图解决的难题中最重要的一块:

```
// BackgroundHeadlessTaskService.java
public class BackgroundHeadlessTaskService extends HeadlessJsTaskService {
    @Override
    protected @Nullable
    HeadlessJsTaskConfig getTaskConfig(Intent intent) {

        Bundle extras = intent.getExtras();
        if (extras != null) {
            return new HeadlessJsTaskConfig(
                    "BackgroundHeadlessTask",
                    Arguments.fromBundle(extras),
                    5000, // timeout for the task
                    true // optional: defines whether or not  the task is allowed in foreground. Default is false
            );
        }
        return null;
    }
}

```

taskKey `BackgroundHeadlessTask`是我们将在 React Native 的`AppRegistry`中注册的无头任务的名称。

可选属性`allowedInForeground`设置为 true，允许我们在应用程序处于前台时运行任务。通常，不建议这样做，因为这可能会降低用户界面的速度。然而，在我们的示例应用程序中，我们不会执行任何繁重的任务。

现在我们的服务已经准备好了，我们将把它添加到`AndroidManifest.xml`，如下所示:

```
&lt;service android:name="com.package_name.BackgroundHeadlessTaskService" />

```

### 开始我们的无头服务

既然我们的应用程序的基础已经准备好了，我们只需要让 WorkManager 定期启动我们的 Headless 服务。我们将在我们的`BackgroundWorker`的`doWork`方法中添加代码来启动我们的服务:

```
public Result doWork() {

    Log.w("bg", "Worker do work");

    Bundle extras = bundleExtras();
    Intent service = new Intent(this.context, BackgroundHeadlessTaskService.class);
    service.putExtras(extras);

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        createChannel();
        this.context.startForegroundService(service);
    } else {
        this.context.startService(service);
    }
    return Result.success();
}

```

如果 Android 版本是`O`以上，我们就启动前台服务，这是因为 Android v8.0 以后引入的后台限制。

`startForegroundService`期望在服务启动后的五秒钟内调用`startForeground`。因此，我们必须对我们的`BackgroundHeadlessTaskService`做一个小小的改变:

```
@Override
protected @Nullable
HeadlessJsTaskConfig getTaskConfig(Intent intent) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        createChannel();
        Notification notification = new NotificationCompat.Builder(getApplicationContext(), "demo")
                .setContentTitle("Headless Work")
                .setTicker("runn")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setOngoing(true)
                .build();
        startForeground(1, notification);
    }

    Bundle extras = intent.getExtras();
    if (extras != null) {
        return new HeadlessJsTaskConfig(
                "BackgroundHeadlessTask",
                Arguments.fromBundle(extras),
                5000, 
                true 
        );
    }
    return null;
}
@RequiresApi(Build.VERSION_CODES.O)
private void createChannel() {
    String description = "test channel";
    int importance = NotificationManager.IMPORTANCE_DEFAULT;
    NotificationChannel channel = new NotificationChannel("demo", "test", importance);
    channel.setDescription(description);
    NotificationManager notificationManager =
            (NotificationManager) getApplicationContext().getSystemService(NOTIFICATION_SERVICE);

    notificationManager.createNotificationChannel(channel);

} 

```

从[奥利奥](https://www.android.com/versions/oreo-8-0/)开始，安卓对后台服务进行限制。因此，我们的工作管理器无法启动后台服务。然而，Android 确实允许我们从后台启动前台服务。对于一个被称为前台的服务，我们必须添加一个通知，通知用户后台正在进行的工作。

现在，我们需要创建任务本身。转到您的 JavaScript 代码，创建一个名为`BackgroundHeadlessTask.js`的文件:

```
// BackgroundHeadlessTask.js
module.exports = async (taskData) => {
        console.log("Hey this is a headless JS task");
        await fetch(`http://192.168.1.7:3000/test-request`);
        return;
};

```

如果您的开发机器上运行着 Node.js 服务器，您可以像我一样创建一个`fetch`调用并等待它完成。

现在，当按钮被按下时，我们可以调用我们在本机模块中创建的方法，如下所示:

```
onStartBtnPress = async () => {
    NativeModules.BackgroundWorkManager.startBackgroundWork();
  };
onCancelBtnPress = async () => {
    NativeModules.BackgroundWorkManager.stopBackgroundWork();
  };

```

上面的代码将工作请求排队，并为我们定期运行任务，直到按下 cancel 按钮。您可以检查 Logcat 来验证后台任务是否定期运行:

![Logcat Verify Background Task Running](img/3f785e85324757168e2810898b25c47c.png)

Logs like this should appear periodically

![Logcat Verify Logs Running](img/85b7e10d7a229d388a9d005b98a972e8.png)

如果您设置了一个本地 Echo 服务器，那么当您的 Headless 任务中的`GET`请求成功时，您应该能够看到日志:

![Echo Server Get Request](img/c591dd5483440bb0a9024857544efcf5.png)

## 结论

在 Headless JS、本机模块和 WorkManager 的帮助下，在 React Native 中创建和运行 JavaScript 任务相当容易。在本文中，我们从头到尾介绍了在 [React Native Android](https://blog.logrocket.com/run-react-native-apps-android-emulator-macos/) 中设置和运行 Headless JS，允许我们在应用程序的后台运行任务。我希望你喜欢这个教程！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)