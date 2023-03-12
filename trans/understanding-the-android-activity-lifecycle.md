# 了解 Android 活动生命周期- LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-the-android-activity-lifecycle/>

Android activity lifecycle 提供了当一个 activity 实例在应用程序的生命周期中改变状态时调用哪些方法的细节。在处理活动状态变化时，理解 Android 活动和片段生命周期是至关重要的。

掌握 [Android](https://blog.logrocket.com/kotlin-vs-flutter-android-development/) 生命周期将帮助你有效处理活动状态，防止你的应用在活动状态发生变化时表现异常，帮助你留住用户，减少对你的 Android 应用的挫败感。

因此，本教程将教你当一个活动或片段在 Android 应用程序中改变状态时调用的所有方法。此外，您还将学习如何适当地处理活动和片段状态更改。

## 分析 Android 活动生命周期

在 Android 中，活动是一个屏幕，为应用程序提供一个窗口来显示其 UI，并使用户能够与应用程序进行交互。该活动响应由用户或系统触发的不同状态变化。

例如，当用户在使用应用程序时按下后退按钮时，活动状态会发生变化。当用户按下后退按钮时，此操作会触发系统停止应用程序正在执行的所有操作。

程序员可以使用六个回调来处理这些活动状态的变化。它们如下:

### 1.`onCreate()`

Android 活动生命周期从`onCreate()`方法开始。当用户单击您的应用程序图标时，会调用此方法，从而导致此方法创建活动。每个活动都需要这个方法，因为它设置了布局。

`onCreate()`方法接受一个`Bundle`作为参数。这个`Bundle`将被活动使用，使用存储在`Bundle`中的数据将自身恢复到先前的状态，如下所示:

```
@Override
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_register);
Button btnregister = (Button) findViewById(R.id.btnregister);
}

```

### 2.`onStart()`

在初始化视图并在`onCreate()`方法中设置布局后，调用`onStart()`方法。该方法使活动对用户可见。

当用户按下 back 按钮并在几秒钟后打开活动时，这个方法也可以由`onRestart()`方法触发。只有当活动不可见但没有被系统销毁时，才会调用`onRestart()`方法。

```
@Override
protected void onStart() {
    super.onStart();
    }

```

### 3.`onResume()`

当活动对用户可见后，当用户开始与它交互时，调用`onResume()`方法。该活动将继续处于这种状态，直到用户离开该活动。

```
@Override
protected void onResume() {
    super.onResume();

}

```

### 4.`onPause()`

当用户离开当前活动时，系统暂停活动上发生的所有操作，并调用`onPause()`方法。这个方法也很重要，因为它用于在用户移动到另一个活动时保存用户的活动数据。

因此，在这个方法上实现的任务必须很快，以确保应用程序在转换到另一个活动时不会滞后。

```
@Override
protected void onPause() {
    super.onPause();
}

```

### 5.`onStop()`

当用户按下 back 按钮或导航到另一个活动时，调用`onStop()`方法，因为该活动对用户不再可见。你也可以用这种方法保存用户的数据。

```
@Override
protected void onStop() {
    super.onStop();
}

```

### 6.`onDestroy()`

在系统销毁活动之前调用此方法。一个活动可能由于配置的改变而被破坏，例如当用户改变设备方向和场所时。

```
@Override
protected void onDestroy() {
    super.onDestroy();

}

```

## 分析片段生命周期

片段是一个子活动，或者是一个活动的一部分，它有自己的布局和生命周期。片段用于在活动中提供多个屏幕。

当调用`onAttach()`方法时，片段通过附加到活动来开始它的生命周期。之后，系统使用`onCreate()`方法来初始化片段。

就像在活动生命周期中一样，`OnCreate()`方法也初始化片段生命周期中的片段。唯一的区别是它没有设置布局，系统调用`onCreateView()`来绘制片段的 UI。

成功创建片段后，它将经历以下几个阶段:

*   当片段现在对用户可见时，调用`onStart()`方法，然后调用`onResume()`方法，当片段是交互式的时，调用该方法
*   一旦片段不再交互，就调用`onPause()`方法。使用此方法可以清除硬件资源并保存用户的进度
*   当片段不再可见时，调用`onStop()`方法，然后调用`onDestroy()`和`onDestroyView()`方法，清理 UI 上任何需要清理的资源
*   当片段从活动中分离出来时，`onDetach()`方法结束了片段的生命周期

## 如何处理活动和片段状态变化

Android 活动生命周期的唯一目的是创建能够处理状态变化和防止应用程序崩溃的方法。即使只需要一个方法(`onCreate()`)，当活动不可见时，处理释放资源的额外任务也是必要的，包括其他在`onPause()`或`onStop()`方法中的最佳实践。

以下提示显示了为使应用程序平稳过渡而必须完成的必要任务。

### 保存用户的进度

现在的用户期望每个 app 即使不手动保存数据，也会保存自己的数据。总是在`onPause()`方法上保存用户的数据。

您可以将数据存储在 SQL 数据库或云存储平台上，如用于实时存储的 [Firebase](https://blog.logrocket.com/firebase-vs-supabase-which-is-better/) 。但是对于较少的数据，可以使用 [SharedPreferences](https://blog.logrocket.com/using-sharedpreferences-in-flutter-to-store-data-locally/) 进行存储。

以下代码向您展示了如何在`onPause()`方法中存储 SharedPreferences:

```
@Override
    protected void onPause() {
        super.onPause();

        SharedPreferences sharedPreferences = getSharedPreferences("MySharedPref", MODE_PRIVATE);
        SharedPreferences.Editor myEdit = sharedPreferences.edit();

//use the putString and putInt methods to store the users text.
        myEdit.putString("model", model.getText().toString());
        myEdit.putInt("price", Integer.parseInt(price.getText().toString()));

//save the text by invoking the apply() method
        myEdit.apply();
    }

```

### 当应用程序不活动时释放资源

当活动停止时，动画必须停止，传感器(硬件资源)必须取消注册，以节省用户的电池寿命。如果不释放它们，就会发生内存泄漏，从而降低系统的性能。

```
protected void onPause() {
    super.onPause();
    sensorManager.unregisterListener(this);
}

```

当释放硬件资源并在`onPause()`方法上保存用户数据时，在活动生命周期回调上实现的任务和方法应该快速而准确。

确保代码在执行任务时是高效的，因为只有在前一个活动完成了`onPause()`方法上的所有任务后，下一个活动才会开始。如果`onPause()`方法花费的时间太长，那么下一个活动获得焦点就需要更长的时间。

## 结论

通过阅读本文，您已经了解了 Android 活动生命周期和片段生命周期，以及如何处理活动状态变化。我希望您觉得这篇文章很有用，并且现在可以通过设计 Android 应用程序来适当地处理活动状态更改，这些应用程序可以:

*   当活动停止时保存用户的数据
*   在活动被破坏之前释放未使用的资源
*   创建活动时绑定数据

如果你想构建高质量的应用程序，掌握 Android 的生命周期是必要的。但是掌握 Android 生命周期不应该是你练习的唯一技能。了解更多关于 Android 惯例和最佳实践的知识也将有助于您高效地编程方法，并使应用程序在活动状态或片段状态之间快速平稳地转换。

如果你有任何问题或反馈，请在下面的评论区告诉我，我将很乐意回复。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)