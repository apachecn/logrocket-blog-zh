# 将带有 Jetpack Compose 的 AlertDialog 添加到 Android apps - LogRocket 博客

> 原文：<https://blog.logrocket.com/adding-alertdialog-jetpack-compose-android-apps/>

Jetpack Compose 是一个很棒的新的 Android 声明式 UI 工具包，它支持用 Kotlin 创建 UI，取代了繁琐的 XML 布局。

本文给出了一个在项目中使用 Jetpack Compose 的简单示例，以及如何创建一个提醒对话框，以便在要求用户确认或取消重要操作时派上用场。

## 教程先决条件

如果您已经有一个基于 XML 布局的 Android 应用程序，并希望开始将 Compose UI 元素集成到其中，或者如果您只是启动一个新应用程序，并希望从一开始就在 Compose 中构建 UI，您可以遵循本教程。

为了在 Jetpack Compose 中获得最佳的开发体验，您需要 Android Studio 北极狐，它使您能够使用您构建的 UI 的内置预览。它还提供了一个向导来轻松创建新的合成项目。

## 创建新的 Jetpack 撰写应用程序

创建新的 app，打开 Android Studio，选择**文件** > **新建** > **新建项目**，在向导中选择**空缀活动**。然后，点击 **Finish** ，一个新的 Jetpack Compose 项目将被创建。

![Create New Jetpack Compose App, Shows Selecting The Certain Activity](img/c50a3887be0a95fbfc9c7e7089fdb9e1.png)

如果你完全不熟悉 Jetpack Compose，我推荐[阅读这篇优秀的介绍文章](https://blog.logrocket.com/building-faster-android-apps-jetpack-compose/)。它提供了可用组件的概述，并描述了 Jetpack Compose 背后的原理。然而，在我们阅读这篇文章的时候，我也会解释所有的事情。

这篇文章还假设你熟悉`[ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel)`(来自 Android 架构组件)，并从`ViewModel`通过 [Kotlin 协同程序](https://blog.logrocket.com/understanding-kotlin-coroutines/)的`[StateFlow](https://developer.android.com/kotlin/flow/stateflow-and-sharedflow)`提供 UI 状态。

## 将 Jetpack Compose 添加到现有项目

如果您有一个现有的 Android 项目，您必须添加一些配置才能使用 Jetpack Compose。

### 设置主项目

在主项目的`build.gradle.kts`中，确保你已经安装了 Android Gradle 插件 7.0.0 和 Kotlin 版本 1.5.31:

```
buildscript {
    // ...
    dependencies {
        classpath("com.android.tools.build:gradle:7.0.0")
        classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.31")
        // ...
    }
}

```

请注意，因为 Jetpack Compose 使用自己的 Kotlin 编译器插件(他们的 API 目前不稳定)，所以它与特定的 Kotlin 版本紧密耦合。因此，除非您也将 Jetpack Compose 更新到兼容版本，否则无法将 Kotlin 更新到较新版本。

### 设置`app`模块

在编写 UI 的实际`app`模块的`build.gradle.kts`中， 必须在中进行修改

```
android {
    buildFeatures {
        compose = true
    }
    composeOptions {
        kotlinCompilerExtensionVersion = "1.0.5"
    }
}

```

然后，您可以添加所需的依赖项。注意`compose-theme-adapter`拥有独立于其他组合依赖项的版本控制(这只是巧合，在本例中它也在版本 1.0.5 中):

```
dependencies {
  val composeVersion = 1.0.5
  implementation("androidx.compose.ui:ui:$composeVersion")
  implementation("androidx.compose.ui:ui-tooling:$composeVersion")
  implementation("androidx.compose.material:material:$composeVersion")
  implementation("com.google.android.material:compose-theme-adapter:1.0.5")
}

```

它们的功能如下:

*   `compose.ui:ui`提供核心功能
*   `compose.ui:ui-tooling`在 Android Studio 中启用预览
*   `compose.material`提供`AlertDialog`或`TextButton`等材料组件
*   `compose-theme-adapter`提供了一个包装器来重用现有的素材主题，以构成 UI 元素(在`themes.xml`中定义)

## 正在创建`AlertDialog`

Jetpack Compose 为用 Kotlin 开发 ui 提供了一种特定于领域的语言(DSL)。每个 UI 元素都是用一个用`@Composable`注释的函数定义的，这个函数可以带参数，也可以不带参数，但总是返回`Unit`。

这意味着这个函数只作为副作用修改 UI 组合，不返回任何东西。按照惯例，这些函数以大写字母开头，所以很容易将它们与类混淆。

因此，让我们来看看关于可组合材料的文档(我省略了参数，现在不需要):

```
@Composable
public fun AlertDialog(
    onDismissRequest: () → Unit,
    confirmButton: @Composable () → Unit,
    dismissButton: @Composable (() → Unit)?,
    title: @Composable (() → Unit)?,
    text: @Composable (() → Unit)?,
    // ...
): Unit

```

我们一眼看到的是它的参数是其他的`@Composable`函数。在 Compose 中构建 UI 时，这是一种常见的模式:将较简单的组件作为参数传递，以构建更复杂的 UI 元素。

这里我们感兴趣的`AlertDialog`参数是`onDismissRequest`、`confirmButton`、`dismissButton`、`title`和`text`。

使用`onDismissRequest`，我们可以传递一个回调函数，当用户在对话框外点击或者点击设备的后退按钮时，这个函数应该执行(但是当用户点击对话框的**取消**按钮时，这个函数不会执行)。

其他参数包括:

*   `confirmButton`，它是一个提供 **OK** 按钮 UI 和功能的组件
*   `dismissButton`，**取消**按钮与`confirmButton`按钮相同
*   `title`，这是一个为对话框标题提供布局的组件

最后，`text`是一个为对话消息提供布局的可组合组件。注意，尽管它被命名为`text`，但它不需要只由静态文本消息组成。

因为`text`将`@Composable`函数作为参数，所以您也可以在那里提供更复杂的布局。

### 为`AlertDialog`编写可组合函数

让我们在项目中为我们想要构建的警告对话框创建一个新文件。让我们调用文件`SimpleAlertDialog.kt`并在其中编写一个名为`SimpleAlertDialog()`的可组合函数。

在这个函数中，我们将创建`AlertDialog`；我们也将逐一探究我们传递的论点。

### 添加空的`onDismissRequest`回调

第一个参数是一个空的 lambda，作为解除请求的回调(我们稍后将填充它):

```
@Composable
fun SimpleAlertDialog() { 
    AlertDialog(
        onDismissRequest = { },
    )
}

```

### 添加确认按钮

对于确认按钮，让我们提供一个带有“OK”文本和一个空回调的`TextButton`。让我们看一下`TextButton`文档的摘录(在下面的代码示例中),看看`TextButton`实际需要什么(我再次省略了没有使用的参数):

```
@Composable
public fun TextButton(
    onClick: () → Unit,
    // ...
    content: @Composable RowScope.() → Unit
): Unit

```

这看起来很简单:一个`TextButton`需要一个`onClick`监听器和一个`content`作为它的 UI。

然而，您不能简单地将一个原始字符串传递给`TextButton`来显示它；字符串必须包装成一个`Text`可组合文件。

现在，我们希望按钮显示单词“OK”因此，确认按钮 UI 布局的`content`参数将如下所示:

```
{ Text(text = "OK") }

```

由于`content` lambda 是`TextButton`的最后一个参数，根据 Kotlin 约定，我们可以将其从括号中取出。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

完成以上步骤后，添加到我们的`AlertDialog`中的确认按钮看起来是这样的:

```
@Composable
fun SimpleAlertDialog() {
    AlertDialog(
        onDismissRequest = { },
        confirmButton = {
            TextButton(onClick = {})
            { Text(text = "OK") }
        },
    )
}

```

### 添加消除按钮

我们现在可以类似地定义表示“取消”的`dismissButton`:

```
@Composable
fun SimpleAlertDialog() {
    AlertDialog(
        onDismissRequest = { },
        confirmButton = {
            TextButton(onClick = {})
            { Text(text = "OK") }
        },
        dismissButton = {
            TextButton(onClick = {})
            { Text(text = "Cancel") }
        }
    )
}

```

### 添加标题和消息

让我们也添加一个`title`和`text`作为简单的`Text`元素来提供我们的消息。标题将显示“请确认”，消息将显示“我应该继续所请求的操作吗？”：

```
@Composable
fun SimpleAlertDialog() {
    AlertDialog(
        onDismissRequest = { },
        confirmButton = {
            TextButton(onClick = {})
            { Text(text = "OK") }
        },
        dismissButton = {
            TextButton(onClick = {})
            { Text(text = "Cancel") }
        },
        title = { Text(text = "Please confirm") },
        text = { Text(text = "Should I continue with the requested action?") }
    )
}

```

## 向布局添加`AlertDialog`

我们的对话框还没有提供任何功能，但是让我们试着看看它在屏幕上是什么样子。为此，我们必须将它添加到我们的布局中。这有两种不同的方式。

### 从向导中创建新的 Jetpack 合成项目

如果您使用项目向导构建了一个新的 Compose 项目，那么在`MainActivity.onCreate()`方法中您会发现对`setContent{}`的调用。这是你所有屏幕组件的位置。

要将`SimpleAlertDialog` composable 添加到您的`MainActivity`中，只需将它放在`MyApplicationTheme`中即可(如果您将应用程序命名为不同于`MyApplication`的名称，主题名称将会不同)。

您的代码应该如下所示:

```
class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            MyApplicationTheme {
                SimpleAlertDialog()
            }
        }
    }
}

```

### 使用现有的基于 XML 布局的项目

如果您有一个基于 XML 布局的现有项目，您必须向您的 XML 布局添加一个`ComposeView`:

```
<...>

```

```
    <androidx.compose.ui.platform.ComposeView
        android:id="@+id/compose_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
</...>

```

现在，在您的`Activity`中，您可以访问这个`compose_view`，例如，通过视图绑定，它将有一个`setContent{}`方法，您可以在其中设置所有的组件。

注意，为了让组件[使用您现有的材料应用程序主题](https://blog.logrocket.com/exploring-theming-jetpack-compose/)，您必须将它们包装在`MdcTheme`(材料设计组件主题包装器)中。

所以，在你的`Activity`中，你会有这样的东西:

```
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Inflate your existing layout as usual, e.g. using view binding
        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Access the added composeView through view binding and set the content
        binding.composeView.setContent {
            // Wrap all the composables in your app's XML material theme
            MdcTheme {
                SimpleAlertDialog()
            }
        }
    }
}

```

## 使用`SampleAlertDialog`测试应用程序

让我们运行这个项目，看看到目前为止我们取得了什么成果！

对话框看起来和预期的一样，有标题、消息和两个按钮。

![App With SampleAlertDialog, Showing Dialog To Either Confirm Or Cancel](img/ff5626cc5ac8ecf1fe3579f1baf03654.png)

然而…警报不能解除！按下**取消**或者**确定**按钮，在对话框外的屏幕上轻点，或者按下设备返回按钮都没关系；它不会消失。

与旧的基于 XML 的布局系统相比，这是一个很大的变化。在那里，UI 组件“自行处理”,一旦你点击其中一个按钮，一个`AlertDialog`就会自动消失(或者执行另一个动作来消除它)。

虽然 Jetpack Compose 赋予您强大的功能，但强大的功能也意味着巨大的责任；你对你的 UI 有完全的控制权，但是你也要对它的行为完全负责。

## 从`ViewModel`显示和关闭对话框

为了控制`AlertDialog`的显示和消失，我们将把它附加到一个`ViewModel`上。假设您已经在应用程序中使用了`ViewModels`，如果没有，您可以很容易地将下面的逻辑应用到您使用的表示层架构中。

### 创建`MainViewModel`以显示/隐藏`SimpleAlertDialog`

首先，将以下依赖项添加到您的`build.gradle.kts`中:

```
implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.4.0")

```

我们现在可以创建一个为`MainActivity`提供 UI 状态的`MainViewModel`。通过添加一个`showDialog`属性，您可以将对话框的可见/不可见状态作为一个包含一个`Boolean`的 Kotlin 协程`StateFlow`发出。

一个`true`值意味着对话框应该被显示；`false`表示应该隐藏。

这个`showDialog`状态可以通过以下回调来改变:

*   `onOpenDialogClicked()`，需要时显示对话框
*   `onDialogConfirm()`，每当用户在对话框中按下 **OK** 时调用
*   `onDialogDismiss()`，每当用户在对话框中按下**取消**时调用

让我们来看看这些是如何运作的:

```
class MainViewModel : ViewModel() {
    // Initial value is false so the dialog is hidden
    private val _showDialog = MutableStateFlow(false)    
    val showDialog: StateFlow<Boolean> = _showDialog.asStateFlow()

    fun onOpenDialogClicked() {
      _showDialog.value = true
    }

    fun onDialogConfirm() {
        _showDialog.value = false
        // Continue with executing the confirmed action
    }

    fun onDialogDismiss() {
        _showDialog.value = false
    }

    // The rest of your screen's logic...
}

```

### 向`SimpleAlertDialog`添加状态和回调

现在我们必须稍微修改一下我们的对话框。让我们回到`SimpleAlertDialog.kt`文件。

在那里我们必须做一些改变。首先，让我们给`SimpleAlertDialog()`可组合函数添加一个用于`show`状态的参数。

然后，在函数内部，我们可以将整个`AlertDialog`包装在一个大的`if (show)`语句中，这样它只在`ViewModel`告诉它的时候才显示。

我们还需要将`onConfirm`和`onDismiss`回调作为参数添加到`SimpleAlertDialog()`中，这样当用户取消或确认对话框时，对话框可以与`ViewModel`通信。

最后，将`onConfirm`回调设置为 OK 按钮的点击监听器，将`onDismiss`回调设置为 Cancel 按钮的点击监听器和`onDismissRequest`的回调(在对话框外点击/按下设备的 back 按钮)。

总的来说，它看起来像这样:

```
@Composable
fun SimpleAlertDialog(
    show: Boolean,
    onDismiss: () -> Unit,
    onConfirm: () -> Unit
) {
    if (show) {
        AlertDialog(
            onDismissRequest = onDismiss,
            confirmButton = {
                TextButton(onClick = onConfirm)
                { Text(text = "OK") }
            },
            dismissButton = {
                TextButton(onClick = onDismiss)
                { Text(text = "Cancel") }
            },
            title = { Text(text = "Please confirm") },
            text = { Text(text = "Should I continue with the requested action?") }
        )
    }
}

```

### 将`SimpleAlertDialog`连接到`MainViewModel`

现在，我们可以将`SimpleAlertDialog`连接到我们的`MainActivity`中的`MainViewModel`,这样它们就可以双向通信了。

为此，我们需要三样东西。首先，`MainActivity`需要一个对`MainViewModel`实例的引用(使用`by viewModels()`委托)。

其次，在`setContent`范围内，我们必须创建一个局部`showDialogState`变量，以便`SimpleAlertDialog`可以从`viewModel`观察到`showDialog`状态。

我们可以使用委托语法(使用`by`关键字)来做到这一点。然后，代理使用`collectAsState()`将`showDialog`包装到一个特殊的组合包装器`State`中。

`State`在 Compose 中用于观察收集到的值的变化。每当这个值改变时，视图就被重组(也就是说，所有的 UI 元素检查它们的状态是否改变，如果是，它们必须被重画)。

这个`showDialogState`变量现在可以作为参数传递给`SimpleAlertDialog`的`show`参数。如果它的值发生变化，对话框会相应地显示或隐藏。

然而，我们的`SimpleAlertDialog`还需要两个参数:`onDismiss`和`onConfirm`回调。这里，我们将简单地传递对适当的`viewModel`方法的引用:`viewModel::onDialogDismiss`和`viewModel::onDialogConfirm`。

做完以上步骤，我们的`MainActivity`看起来是这样的:

```
class MainActivity : ComponentActivity() {

    // Reference to our MainViewModel instance using the delegate
    private val viewModel: MainViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Opens the dialog immediately when the Activity is created
        // Of course in a real app you might want to change it
        viewModel.onOpenDialogClicked()

        setContent {
            // Delegate to observe the showDialog state in viewModel
            val showDialogState: Boolean by viewModel.showDialog.collectAsState()

            MyApplicationComposeTheme {
                SimpleAlertDialog(
                    show = showDialogState,
                    onDismiss = viewModel::onDialogDismiss,
                    onConfirm = viewModel::onDialogConfirm
                )
            }
        }
    }
}

```

注意，我们在这里调用的是`onCreate()`中的`viewModel.onOpenDialogClicked()`；在真正的应用程序中，我们应该调用它来响应用户的动作，比如按下屏幕上的按钮。

## 测试在应用程序中显示和隐藏`SimpleAlertDialog`

让我们再次运行我们的应用程序。现在，我们看到，我们可以通过按下 **OK** 或 **Cancel** 按钮，点击对话框外的屏幕上的任何地方，或按下设备 back 按钮来轻松关闭对话框。

我们在`ViewModel`中也有一个确认回调，它可以继续执行期望的动作。

## 摘要

现在，您已经体验到 Jetpack Compose 的理念是如何不同于旧的基于 XML 布局的 UI 开发的。Compose 给了你更多的控制和 UI 逻辑到你的`ViewModels`中的集成，但是也要求你自己定义所有的 UI 行为(甚至那些你认为理所当然的行为，比如关闭一个对话框)。

然而，将所有的逻辑都放在`ViewModel`中意味着你可以很容易地为它编写单元测试，并在将来需要的时候修改它。

组合 UI 元素作为 Kotlin 函数实现的事实意味着，与 XML 布局相比，您的 UI 代码可读性要差得多。您还可以在编写代码时直接访问 IDE 支持，如代码完成、一键式文档、编译时检查或类型安全。

通过将可组合的函数作为参数传递给其他函数，可以从简单的 UI 元素中构造出更复杂的 UI 元素，这增加了代码重用和 UI 的模块化。

它也可以很容易地定制，例如，在`SimpleAlertDialog`中，你可以添加一个参数来传递一个定制的布局来编辑文本而不是确认消息，创建一个`RenameDialog`。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)