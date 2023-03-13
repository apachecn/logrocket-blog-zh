# Android 意图过滤器入门

> 原文：<https://blog.logrocket.com/android-intent-filters/>

自从 Android 操作系统出现以来，意图的概念就一直是操作系统导航的主要构建模块之一。正如谷歌定义的[，一个`Intent`是对要执行的操作的抽象描述。正如](https://developer.android.com/reference/android/content/Intent) [Android 官方文档](https://developer.android.com/guide/components/intents-filters)进一步解释的那样，它也可以被视为一个消息传递对象，您可以使用它来请求另一个应用程序组件的操作，众所周知，它主要用于三种情况:启动一个`Activity`、一个`Service`，以及向一个`BroadcastReceiver`传递消息。

有两种主要类型的意图:明确的和隐含的意图。不需要太多的细节和两者之间的差异，正是后一种类型的意图积极地处理意图过滤器 ***、*** 本文的主题 ***。***

向前跳:

## 什么是意图过滤器？

意图过滤器被 [Android 文档](https://developer.android.com/guide/components/intents-filters#Types)定义为“应用程序的`Manifest.xml`文件中的一个表达式，它指定了组件想要接收的意图类型。”正如前面提到的，意图过滤器仅在我们使用隐式意图时起作用，因为这种意图需要 Android 系统通过将意图的内容与设备上其他应用程序的`Manifest`文件中声明的意图过滤器进行比较来找到合适的组件。

在本文中，我们将探索`<intent-filter>`的本质，它与 Android 操作系统的关系，以及它在`Manifest.xml`文件和生态系统中的用法。然后，我们将回顾一些常见的意图过滤器示例，以及一些更高级的使用方法，目的是更好地理解这些关键信息在 Android 应用程序中扮演的角色。

意图过滤器无情地与隐式意图结合在一起，因为它们声明了要执行的一般动作，正如 [Android 文档](https://developer.android.com/guide/components/intents-filters#:~:text=in%20the%20background.-,Implicit%20intents,-do%20not%20name)所解释的，然后允许来自另一个应用程序的组件来处理它。

一个容易想到的例子是，每当我们想要发送电子邮件时，我们都会调用一个隐含的意图，电子邮件应用程序可能会消费并帮助我们实现我们的最终目标。正是因为隐式意图和意图过滤器之间的这种强有力的关系，从现在开始，在本文中每次我们提到意图时，我们将明确地谈论隐式意图，除非另有说明。

## 深入探究意图过滤器

如前所述，意图过滤器是在`Manifest.xml`文件内的代码中定义的。清单通常用于任何应用程序的大部分主要设置，因为它声明了应用程序名称、图标、它需要的权限等。清单还负责声明活动、服务和广播接收器，我们正是在这些组件中定义我们的意图过滤器。

最简单的`<intent-filter>`的例子可以在应用程序的主活动中找到——或者用户首先看到的活动——我们添加了一个意图过滤器，声明它是应用程序的主活动，以及启动活动:

```
<activity
    android:name="com.ivangarzab.examples.MainActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

```

在本例中，没有必要在所有类型的应用程序组件(即活动、服务、广播接收器)中明确设置`android:exported`的属性，否则应用程序将无法在运行 Android 12 或更高版本的设备上成功安装。

## 意图过滤器属性

说到属性，意图过滤器有一个小的属性池，可以在其中设置，以便指定该组件接受的意图类型:动作、数据和类别。以下是根据 [Android 开发者指南](https://developer.android.com/guide/components/intents-filters#Receiving)对这些元素的定义:

*   `<action>`–在`name`属性中声明意图动作被接受。该值必须是操作的文字字符串值，而不是类常量
*   `<data>`–使用一个或多个指定数据 URI ( `scheme`、`host`、`port`、`path`)和 MIME 类型各个方面的属性，声明接受的数据类型
*   `<category>`–声明在`name`属性中接受的意图类别。该值必须是操作的文字字符串值，而不是类常量

如果我们回头看看上面的第一个例子，我们可以看到`<action>`和`<category>`属性的最基本用法，其中将`Activity`声明到主活动中的动作读作`android.intent.action.MAIN`，而将同一活动声明为启动活动的类别读作`android.intent.category.LAUNCHER`。

让我们跳到另一个例子，进一步演示如何同时使用所有这些不同的意图过滤器元素:

```
<activity android:name="com.ivangarzab.examples.SendActivity"
    android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>

```

对于第二个例子，我们声明了另一个名为`SendActivity`的`Activity`，它包含一个嵌套的`<intent-filter>`，声明了其中所有的三种类型的属性。它有一个类型为`ACTION_SEND`的动作，一个类型为`DEFAULT`的类别，以及一个数据元素，该元素包含我们希望使用传入意图发送的数据类型，即`text/plain`。

显然，这里使用的这种意图是为了将一些数据传递给其他人，根据[其官方文档](https://developer.android.com/reference/android/content/Intent#ACTION_SEND)，例如，这种类型的意图通常由电子邮件应用程序声明。

应该注意的是，您的活动正确接收隐式意图的唯一方式是声明`CATEGORY_DEFAULT`过滤器。默认情况下，函数`startActivity()`和`startActivityForResult()`在被调用时都会声明该类别，这意味着意图过滤器也应该期待来自接收端的类别。

## 发送和接收`Activity`意图

到目前为止，我们只看到了如何在清单文件中声明意图过滤器，这允许应用程序接收意图。相比之下，发送意图是直接在代码中完成的，并且需要进行一些设置，以确保我们包括所有需要的属性和信息，以便成功地将意图传递给另一个应用程序。

相反，对传入意图的实际接收和处理，即已经由清单过滤并由我们的应用程序使用的意图，也需要在它们各自的应用程序组件中使用条件块语句进行处理。

### 有目的地发送

让我们从展示如何建立和发送意向开始。在我们上一个关于`ACTION_SEND`意图的例子中，我们简单地提到了它可以用来向其他人发送信息，比如在电子邮件中。

您设置一个由上面指定的代码正确过滤的`ACTION_SEND`意图的方式需要如下内容:

```
fun composeEmail(data: EmailData) =
    Intent(Intent.ACTION_SEND).apply {
        type = "text/plain"
        putExtra(Intent.EXTRA_EMAIL, data.addresses)
        putExtra(Intent.EXTRA_SUBJECT, data.subject)
    }.let { intent ->
        if (intent.resolveActivity(packageManager) != null) {
            startActivity(intent)
        }
    }

```

在这个例子中，我们有一个函数，它首先创建一个`Intent`对象，在它的`type`字段中指定内容类型，然后添加一些接收端可以利用的额外字段。之后，我们确保有一个`<activity>`组件可以被解析为我们的规范，如果是这样，我们继续调用`startActivity(intent)`传递最近创建的意图，希望将我们的消息传递给另一个应用程序。

同样，发送一个`Service`或`BroadcastReceiver`意图与发送一个`Activity`意图没有太大的不同，因为它们会简单地分别调用`startService(intent)`和`sendBroadcast(intent)`。但是，这些替代类型的组件可能需要不同类型的设置，以便被其他应用程序正确过滤。

### 接收意图

不同但平行的是，需要设置应用程序组件来处理传入的意图。这通常是在组件的 main override 方法中完成的，比如在`Activity`组件的`onCreate()`中。

以使用`ACTION_SEND`意图的一系列例子结束，我们在上面两个例子的`Manifest`文件中声明的`SendActivity`类需要将下面的代码块添加到它的`onCreate(..)`方法中，以便能够正确地处理意图:

```
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    intent?.let {
        when (it.action) {
            Intent.ACTION_SEND -> {
                // All send intents land here
                if (it.type == "text/plain") {
                    // Handle sending text
                } else // Handle all other type of send intent actions
            }
        }
    }
    ...
}

```

在`ACTION_SEND`系列的最后一个例子中，我们设置了一个`Activity`的`onCreate()`方法，以便它能够处理给定类型的意图，甚至添加了一个进一步的条件语句，通过它们持有的数据类型来区分`ACTION_SEND`的意图。

现在我们已经更好地理解了什么是意图过滤器，它们如何与意图相关，以及它们如何被发送和接收，让我们看看不同类型的意图过滤器，以及它们对其他 Android 组件的一些不同用途。

## 实现示例:显示地图

在`Manifest`中隐含意图声明的一个常见例子是在地图上显示一个位置。

地图应用程序以及驾驶或送餐应用程序可能会将其活动暴露给操作系统，从而允许它们接收试图在地图上显示位置的意图。以下代码显示了实现该目标所需的意图过滤器类型:

```
<activity 
        android:name="com.ivangarzab.examples.MapActivity"
    android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="geo" />
    </intent-filter>
</activity>

```

## 实现示例:图像捕获

另一个几乎任何应用程序都可以实现的非常常见的例子是使用内置摄像头捕捉图片或视频。下面的代码显示了如何设置正确的`<intent-filter>`来实现图片或视频的捕捉，并将其返回给调用应用程序:

```
<activity 
        android:name="com.ivangarzab.examples.CaptureActivity"
    android:exported="false">
    <intent-filter>
        <action android:name="android.media.action.IMAGE_CAPTURE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>

```

## 服务和接收器

到目前为止，我们所经历的大多数例子都是在处理与`Activity`组件相关的意图和意图过滤器。然而，活动并不是唯一可以使用意图启动的应用程序组件。

服务和广播接收器也可以使用意图来启动和处理，并且这两种组件类型也在其清单声明中使用意图过滤器:

```
<service
    android:name="com.ivangarzab.examples.AudioService"
    android:exported="false">
    <intent-filter>
        <action android:name="android.media.browse.MediaBrowserService" />
    </intent-filter>
</service>

<receiver
    android:name="androidx.media.session.MediaButtonReceiver"
    android:exported="false">
    <intent-filter>
            <action android:name="android.intent.action.MEDIA_BUTTON" />
    </intent-filter>
</receiver>

```

在这个例子中，我们展示了一个通用的`<service>`和`<receiver>`设置，用于处理音频播放服务及其伴随的媒体通知。很明显，我们忽略了很多上下文，但是相对于前面的`<activity>`例子，相似和不同之处应该是显而易见的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 使用意图过滤器的高级方法

现在，我们已经看到了一些典型意图及其意图过滤器的常见示例，以及试图与服务和广播接收器等活动之外的组件进行通信的意图，让我们最后看一个示例，在这个示例中，我们将演示使用意图过滤器的两种高级方法:实现自定义意图操作，以及在同一个组件中使用多个过滤器:

```
<activity
    android:name="com.ivangarzab.examples.MainActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <intent-filter>
        <action android:name="ivangarzab.examples.action.TODO" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>

```

如前所述，上面的例子展示了意图过滤器的两种高级情况。首先，我们从第一个例子中获取`MainActivity`,并通过添加一个辅助`<intent-filter>`来扩展它，该辅助`<intent-filter>`定义了一个`TODO`类型的自定义动作。在单个组件中拥有多个意图过滤器允许给定的组件处理不同种类的输入意图。

显而易见，这个第二意图过滤器的 action 元素不是我们从 Android SDK 中派生出来的。相反，可以为每个应用程序定义自定义操作，并可以用来扩展从应用程序外部触发的功能。

例如，我们的应用程序可以有一个待办事项列表特性，允许外部应用程序发送新的待办事项元素作为意图的一部分。我们的应用程序不仅需要在活动的清单声明中定义自定义意图过滤器动作，还需要在给定的`Activity`类中处理此类意图的到达:

```
class MainActivity : AppCompatActivity() {
    ...
    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        intent?.let {
            when (it.action) {
                "ivangarzab.examples.action.TODO" -> {
                    // Handle TODO custom action
                }
            }
        }
        ...
    }
    ...
}

```

这个最后的代码块应该看起来非常熟悉，因为对定制动作的处理与我们对几乎任何其他类型的特定`Intent`的处理完全相同。上面的代码与我们在接收意图部分的例子之间的主要区别在于，我们不是从 Android SDK 内部的预定义类中获取意图动作定义，而是手动定义意图动作的全名。

最后，外部应用程序也必须知道自定义意图的确切定义，即`ivangarzab.examples.action.TODO`，以便要求操作系统找到一个其意图过滤器接受该类动作的组件:

```
// Outside application
fun createToDo(data: ToDoData) =
        Intent("ivangarzab.examples.action.TODO").apply {
                putExtra(KEY_TASK, data.task)
                putExtra(KEY_DATE, data.date)
                startActivity(this)
        }

```

## 结论

总而言之，意图是一个非常广泛的概念，可能会出现在 Android 平台的无数主题中。意图对于 Android 操作系统变得如此重要，以至于被称为 [MVI 架构](https://proandroiddev.com/mvi-architecture-with-kotlin-flows-and-channels-d36820b2028d)的整个软件架构范例被创建并致力于意图、它们的功能和它们的优势。

在这篇文章中，我们仅仅触及了意图能做什么的表面，通过专注于隐式意图的使用，当涉及到显式意图时，我们忽略了整个另一个世界。此外，广播接收器和服务具有完全不同的意图处理方法，并且通常发现它们以自己的方式使用它们，作为更好地服务于那种应用组件的边缘情况。

对于那些想了解更多关于公共意图和它们的意图过滤器的人来说，看看[这个公共意图列表](https://developer.android.com/guide/components/intents-common)来尝试找到最好的隐含意图来集成到你的应用程序中。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)