# Android 应用中的活动状态和片段生命周期

> 原文：<https://blog.logrocket.com/activity-state-fragment-lifecycle-android-apps-kotlin/>

在现代 Android 开发中，活动状态和片段生命周期起着至关重要的作用，影响着实现决策以及最终用户看到和体验到的最终输出。在本文中，我们将回顾这些概念是什么，它们是如何工作的，以及在 Android 应用程序中实现它们的步骤。

为了跟随本教程，你需要具备 Kotlin 编程语言的基础知识以及安装的 Android Studio 或 IntelliJ IDE。我们开始吧！

## 什么是活动状态？

一个[活动](https://developer.android.com/reference/kotlin/android/app/Activity)指的是用户可以执行的单个动作。在 Android 开发中，活动是一个 Java 类，它有一些预定义的函数，可以在不同的应用程序状态下触发，以执行任何类型的任务。该活动还负责创建、销毁和控制应用程序生命周期的其他状态。

一个[活动类](https://developer.android.com/guide/components/activities/activity-lifecycle)处理许多计算细节，比如为你创建一个窗口。

在 Android 中，可以有多个活动，但是只有一个`MainActivity`，是应用的入口，就像 Java 中的`main()`方法开始执行一个程序一样。当我们调用`MainActivity`类时，执行从`onCreate()`方法开始。

所有活动的子类都实现了两个方法:

*   `onPause`:允许用户暂停与活动的主动交互
*   `onCreate`:初始化您的活动。您必须以编程方式调用`setContentView()`

我们可以在下面的代码片段中表达这一点。我们使用`onCreate`方法来创建或启动一个活动，使用`super`关键字来调用超类构造函数，最后使用`setContentView`来设置 XML:

```
package com.example.myfirstandroidapplication

import android.os.Bundle
import com.google.android.material.snackbar.Snackbar
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.WindowCompat
import androidx.navigation.findNavController
import androidx.navigation.ui.AppBarConfiguration
import androidx.navigation.ui.navigateUp
import androidx.navigation.ui.setupActionBarWithNavController
import android.view.Menu
import android.view.MenuItem
import com.example.myfirstandroidapplication.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var appBarConfiguration: AppBarConfiguration
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        WindowCompat.setDecorFitsSystemWindows(window, false)
        super.onCreate(savedInstanceState)

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        setSupportActionBar(binding.toolbar)

        val navController = findNavController(R.id.nav_host_fragment_content_main)
        appBarConfiguration = AppBarConfiguration(navController.graph)
        setupActionBarWithNavController(navController, appBarConfiguration)

        binding.fab.setOnClickListener { view ->
            Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                .setAnchorView(R.id.fab)
                .setAction("Action", null).show()
        }
    }

    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        // Inflate the menu; this adds items to the action bar if it is present.
        menuInflater.inflate(R.menu.menu_main, menu)
        return true
    }

```

[活动状态](https://developer.android.com/guide/components/activities/state-changes)是指由系统或用户触发的事件，导致活动从一种状态转换到另一种状态。

例如，当用户在应用程序中处于活动状态时，他们通常会在点击主页上的应用程序图标到启动应用程序之间转换，然后最终退出应用程序，从而导致状态变化。这些从一种状态到另一种状态的变化被称为活动状态变化。

## 什么是活动生命周期？

系统中发生的活动变化在活动堆栈中管理。最新的活动通常放在当前堆栈的顶部，它成为正在运行的活动，而前一个活动在堆栈中仍然位于它的下面。

[活动生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle)是指用户执行的整个活动，从启动应用到关闭所有操作。几个活动方法定义了活动的整个生命周期。举个例子，`onCreate()`是应用被用户启动后的初始状态。下面的代码片段显示了`onCreate()`方法的语法:

```
protected void onCreate()

```

`onPause()`是暂停状态，当用户要决定下一步行动时，是退出还是继续使用应用程序:

```
protected void onPause()

```

当活动从暂停状态恢复时，调用`onStart()`状态，并使其对用户可见:

```
protected void onStart()

```

`onDestroy()`是所有活动关闭时产生的状态，导致关闭所有操作:

```
protected void onDestroy()

```

`onStop()`是用户点击 home 键产生的状态，通常是最小化 app。这不应被误认为是应用程序关闭时的情况:

```
protected void onStop()

```

`onResume()`是一个回调，当活动停止后重新启动时调用。下面的代码片段显示了该方法的语法:

```
protected void onResume()

```

上面列出的方法与`saveInstanceState()`方法结合使用来保存活动 UI 状态的配置:

```
public class Activity extends ApplicationContext {
      protected void onCreate(Bundle savedInstanceState);

      protected void onStart();

      protected void onRestart();

      protected void onResume();

      protected void onPause();

      protected void onStop();

      protected void onDestroy();
  }

```

## 什么是碎片生命周期？

片段是指活动 UI 的一部分，它允许我们在 Android 应用程序中创建多个屏幕。Android v3.0 中引入了片段，以支持平板电脑等大屏幕上更灵活的 UI 设计。在撰写本文时，Android 的最新版本 [v12.x 包含了额外的特性](https://www.android.com/android-12/),比如更具表现力和动态的系统 UI。

在应用程序中创建多个屏幕的一种方法是用一个片段替换另一个片段。

## 活动状态和片段生命周期之间的关系

活动通常被认为是操作系统进入应用程序的入口点。在 Android 应用程序中，活动可以包含多个片段和函数。这些充当包含 UI 片段的框架，提供围绕该片段的 UI 元素。

虽然一个活动可以在没有片段的情况下存在，但是你不能在没有活动的情况下使用片段。UI 片段的操作类似于活动布局中的视图。为此，您必须创建一个子类，如下面的代码所示:

```
class InfoFragment: Fragment{
@override fun onCreateView(...): View? {
     return inflater.inflate (
      R.layout.fragment_info,...)
  }
}

```

上面的代码片段显示了一个可重用的 UI 组件，它包含了扩展片段布局的 UI 逻辑。虽然大多数 UI 元素都在片段中实现，但操作系统只能打开活动。

注意，在活动内部，你可以通过调用`onCreate`中的`setContentView`来告诉 Android 使用哪个布局。在片段中，您必须手动膨胀并在`onCreateView`方法中返回膨胀的布局，这独立于`onCreate`方法。

下面的代码片段进一步强调了这一点:

```
// Activity 
override fun onCreate(savedInstanceState : Bundle?) {
  super.onCreate(savedInstanceState)
  val binding = DataBindingUtil
  .setContentView<ActivityMainBinding> (
  this, R.layout.activity_main)
  ...
}
```

```
// Fragment
override fun onCreateView (...): View? {
  return inflater.inflate (
  R.layout.fragment_message,...)
}
```

虽然活动继承自`context`类，但片段没有。您需要使用片段中的`context`属性来访问`context`可以访问的应用数据，比如字符串或图像。

```
// Activity 
public class ActivityCompat extends ContextCompat
```

```
//Fragment
open class Fragment: componentCallbacks
```

```
context!!.getString(R.string.app_name)
```

```
context!!.getDrawable(R.drawable.ic_launcher_background
```

在 Android 中，你可以在不同的活动之间导航。它们被排列在一个堆栈中，最新的活动位于堆栈跟踪的顶部，称为`back stack`。

片段有一个类似的`back stack`，但是整个堆栈都包含在活动中。这是由一个名为`FragmentManager`的类控制的，我们接下来将回顾这个类。

## `FragmentManager`和片段生命周期状态

在 Android 应用程序开发中，`[FragmentManager](https://developer.android.com/guide/fragments/fragmentmanager)`和片段生命周期负责应用程序经历的状态转换。

片段总是从在`INITIALIZED`状态下被实例化开始。为了让这个片段在其生命周期的不同状态之间转换，它必须被添加到`FragmentManager`中。`FragmentManager`决定了它的片段应该处于什么状态，也决定了片段的最大状态。

就像一个活动一样，片段也有自己的生命周期。每当用户与 Android 应用程序交互时，该片段在其生命周期中会经历不同的状态，包括:

*   `INITIALIZED`
*   `CREATED`
*   `STARTED`
*   `RESUMED`
*   `DESTROYED`

然而，了解片段的最好方法是实现它们。

## 为 Android 应用程序创建和添加片段

为了创建一个片段，我们将遵循下面的步骤:

1.  选择**文件**，点击**新建**
2.  点击下拉菜单中的**片段**和**片段(空白)**
3.  使用`TitleFragment`作为片段的名称
4.  取消选中**创建布局 XML**
5.  取消选中**包含片段工厂方法**
6.  取消选中**包含接口回调**
7.  选择**结束**

创建片段后，您必须将片段添加到您的应用程序中。要添加一个片段，首先点击 **`linear`** 布局，从`activity_main.xml`布局创建一个片段标签。给这个片段一个`fragment id`和一个`android:name`到片段类的完整路径。最后，将布局宽度和高度设置为`match_parent`，就可以开始了！

## 结论

在本教程中，我们使用 Kotlin 编程语言讲述了 Android 应用程序中活动状态和片段生命周期的一些基本和高级概念。

片段支持模块化和代码重用，允许我们在许多活动中使用相同的列表视图。它们也有助于为 Android 平板设备构建多窗格界面。另一方面，活动状态生命周期提供了 Android 应用程序在其七个状态中的行为信息。最后，我们创建了一些片段生命周期的例子，比较了它们在活动状态之间的异同。

作为一名 Android 开发人员，在 Android 应用程序开发过程中，始终了解最新的技术发布并遵循最佳实践非常重要。我希望你喜欢这篇文章，如果你有任何问题，请留下评论。编码快乐！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)