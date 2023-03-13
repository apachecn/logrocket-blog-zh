# Kotlin SharedPreferences 数据存储指南

> 原文：<https://blog.logrocket.com/android-data-storage-guide-kotlin-sharedpreferences/>

你有没有想过如何在应用程序中存储一些值，而不会在关闭应用程序后丢失它们？Android 提供了一种方法，如果你有一个小的键值集合想要保存的话！

在本文中，我们将看看 Android 的`SharedPreferences`API，然后演示如何在用例中使用`SharedPreferences`。

[Jetpack DataStore](https://developer.android.com/topic/libraries/architecture/datastore) 是一个现代的解决方案，它使用 [Kotlin 协程和流](https://blog.logrocket.com/understanding-kotlin-design-patterns/)来异步存储数据。将来，您可能会考虑从`SharedPreferences`迁移到 Jetpack 数据存储。然而，在本文中，我们将关注于`SharedPreferences`,因为它仍然有用，并且在撰写本文时仍然非常流行。

*向前跳转:*

## 什么是`SharedPreferences`？

是一个 Android API，允许你创建和访问一个包含键值集合的文件。一个`SharedPreferences`对象提供了一些在文件上读写这些值的方法。每个`SharedPreferences`文件由框架管理，可以是应用程序私有的，也可以是共享的。

## 访问 Kotlin `SharedPreferences`文件

要创建或访问一个`SharedPreferences`文件，可以调用以下方法之一:`getSharedPreferences()`或`getDefaultSharedPreferences()`。

让我们来看看。

### getSharedPreferences(名称，模式)

如果您需要创建或访问一个由名称标识的`SharedPreferences`文件，请使用 [getSharedPreferences()方法](https://developer.android.com/reference/kotlin/android/content/SharedPreferences)。

这是这种方法的一个例子:

```
val sharedPreferences = activity?.getSharedPreferences(getString(R.string.preference_file_key), Context.MODE_PRIVATE)
```

### getDefaultSharedPreferences()

使用 [getDefaultSharedPreferences()方法](https://developer.android.com/reference/android/preference/PreferenceManager#getDefaultSharedPreferences(android.content.Context))获取整个应用程序的默认 SharedPreferences 文件。你应该知道这个方法需要额外的依赖。

此方法的以下示例需要在应用程序模块级别向`build.gradle`文件添加一个额外的依赖项:

这是依赖性:

```
implementation "androidx.preference:preference-ktx:1.2.0"
```

下面是该方法的一个示例:

```
val sharedPreferences = PreferenceManager.getDefaultSharedPreferences(context)
```

## 共享 Kotlin `SharedPreferences`文件

`getSharedPreferences`方法的第二个参数是模式。此参数与文件共享的权限相关。

### 与其他应用程序共享文件

要与不共享您的用户 ID 的应用程序共享私人文件，您可以在早于 7.0 的 Android 版本上使用`MODE_WORLD_READABLE`。如果你尝试使用这种模式，Android 7.0+会抛出安全异常；从 API 级开始，它就被弃用了。

如果您遇到这个问题，一个解决方法是使用一个意图设置为`[FLAG_GRANT_READ_URI_PERMISSION](https://developer.android.com/reference/android/content/Intent#FLAG_GRANT_READ_URI_PERMISSION)`的[文件提供者](https://developer.android.com/reference/androidx/core/content/FileProvider)。欲了解更多信息，也请参阅关于[共享文件](https://developer.android.com/training/secure-file-sharing)的官方文档。

### 与您的应用程序共享文件

要与任何共享您的用户 ID 的应用程序共享私人文件，您可以使用`MODE_PRIVATE`。

## 读取科特林`SharedPreferences`文件

要从一个`SharedPreferences`文件中检索值，您可以调用下面的任何方法，提供您想要的值的键，如果键不存在，还可以返回一个可选的默认值:

*   `contains(key: String)`使用此方法检查偏好文件是否包含特定偏好
*   `edit()`使用该方法创建一个新的编辑器，通过它你可以对`SharedPreferences`文件中的数据进行[原子改变](https://kotlinlang.org/docs/multiplatform-mobile-concurrent-mutability.html#atomics)
*   `getAll()`使用此方法检索`SharedPreferences`文件中的所有值
*   `getInt(key: String, defValue: Integer)`使用此方法从`SharedPreferences`文件中检索一个`Integer`值
*   `getBoolean(key: String, defValue: Boolean)`使用此方法从`SharedPreferences`文件中检索一个`Boolean`值
*   `getFloat(key: String, defValue: Float)`使用此方法从`SharedPreferences`文件中检索一个`Float`值
*   `getLong(key: String, defValue: Long)`使用此方法从`SharedPreferences`文件中检索一个`Long`值
*   `getString(key: String, defValue: String)`使用此方法从`SharedPreferences`文件中检索一个`String`值
*   `getStringSet(key: String, defValues: Set)`使用此方法从`SharedPreferences`文件中检索一组`String`值

## 编写 Kotlin `SharedPreferences`文件

要在`SharedPreferences`文件中写入值，通过调用`SharedPreferences`对象上的`edit()`来创建一个`[SharedPreferences.Editor](https://developer.android.com/reference/android/content/SharedPreferences.Editor)`。使用`edit()`，你将能够调用以下方法:

*   `putInt(key: String, value: Integer)`使用此方法在`SharedPreferences`文件中插入一个`Integer`值
*   `putBoolean(key: String, value: Boolean)`使用此方法在`SharedPreferences`文件中插入一个`Boolean`值
*   `putFloat(key: String, value: Float)`使用此方法在`SharedPreferences`文件中插入一个`Float`值
*   `putLong(key: String, value: Long)`使用此方法在`SharedPreferences`文件中插入一个`Long`值
*   `putString(key: String, value: String)`使用此方法在`SharedPreferences`文件中插入一个`String`值
*   `putStringSet(key: String, values: Set)`使用此方法在`SharedPreferences`文件中插入一组`String`值

写入更改后，请务必调用`apply()`或`commit()`来保存您的更改。根据[官方 Android 文档](https://developer.android.com/training/data-storage/shared-preferences),`apply()`和`commit()`都会立即更改内存中的`SharedPreferences`对象，但将更新写入磁盘的方式不同。`apply()`异步写入更新，而`commit()` 同步写入更新，如果从主线程调用，可能会导致 UI 渲染暂停。

这里有一个例子说明了上面的一些函数在一个`SharedPreferences`文件中检索和写入值:

```
val sharedPref = activity?.getPreferences(getString(R.string.preference_file_key), Context.MODE_PRIVATE) ?: return
with (sharedPref.edit()) {
    putInt(getString(R.string.saved_high_score_key), newHighScore)
    apply()
}
```

## 检测 Kotlin `SharedPreferences`文件中的更改

要检测`SharedPreferences`文件中的变化，您可以调用以下方法:

*   `registerOnSharedPreferenceChangeListener(listener: SharedPreferences.OnSharedPreferencechangeListener)`使用这个方法注册一个回调，当`SharedPreferences`文件中发生变化时，这个回调将被调用
*   `unregisterOnSharedPreferenceChangeListener(listener: SharedPreferences.OnSharedPreferencechangeListener)`使用这个方法取消注册一个回调函数，当`SharedPreferences`文件发生变化时调用这个函数

## `SharedPreferences`使用案例

现在，我们来看看一个常见的场景，其中`SharedPreferences`可能是有用的。假设您想在用户第一次打开应用程序时显示一个入职页面。

为了实现这一点，您需要在一个`SharedPreferences`文件中存储一个布尔值，以指示特定用户是否第一次打开该应用程序。

### 履行

首先，打开`res/values/strings.xml`并添加我们的`SharedPreferences`文件的名称。

接下来，将以下代码添加到`strings.xml`文件中:

```
<string name="app_shared_prefs">com.pp.sharedprefs.PREFERENCES_FILE_KEY</string>
```

您还需要添加一个标识我们的存储值的键。姑且称之为`FIRST_TIME_OPENING_KEY`。

将以下代码添加到`strings.xml`文件中:

```
<string name="first_time_opening">FIRST_TIME_OPENING_KEY</string>
```

现在，转到您的活动，在调用`setContentView()`后，在`onCreate(savedInstanceState: Bundle?)` 中添加以下代码:

```
getSharedPreferences(getString(R.string.app_shared_prefs), Context.MODE_PRIVATE)?.let { sharedPreferences ->
        val isFirstTimeOpening = sharedPreferences.getBoolean(getString(R.string.first_time_opening), true)

        if (isFirstTimeOpening) {
            showOnboarding()
            with (sharedPreferences.edit()) {
                putBoolean(getString(R.string.first_time_opening), false)
                apply()
            }
        }
}
```

在上面的代码中，我们通过调用`getSharedPreferences(name, mode)`来访问文件。

接下来，我们尝试从`SharedPreferences`文件中获取 `FIRST_TIME_OPENING_KEY`的值。如果值不存在，我们返回`true`。

如果我们返回`true`，这是用户第一次打开 app。在这种情况下，我们显示 onboarding 屏幕，并将`FIRST_TIME_OPENING_KEY`的值更改为`false`，这样用户就不会再看到 onboarding 屏幕。然后，我们调用`apply()`来结束更改。

应该可以了！

在我们的用例中，我们处理了一个 onboarding 屏幕。然而，这种解决方案适用于用户第一次打开应用程序时只运行一次的情况。

### 一点额外的小好处:减少代码长度

因为我们正在使用 Kotlin，所以我们可以利用它的强大功能，通过 [Kotlin 扩展](https://kotlinlang.org/docs/extensions.html)来减少一点代码长度。

要使用 Kotlin 扩展，创建一个名为`SharedPreferencesHelper`的单独文件，然后添加以下内容:

```
fun SharedPreferences.edit(actions: SharedPreferences.Editor.() -> Unit) {
    with (edit()) {
        actions(this)
        apply()
    }
}
```

上面的代码将在`SharedPreferences interface`中添加一个名为`edit`的扩展函数。`edit()`函数采用一个[科特林λ函数](https://kotlinlang.org/docs/lambdas.html)，它由花括号定义，是`SharedPreferences.Editor`的扩展。

在这个扩展函数中，我们已经在最后调用了`apply()`，所以当使用这个扩展在`SharedPreferences`文件中添加值时，没有必要重复这个过程。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

回到活动，替换以下代码:

```
with (sharedPreferences.edit()) {
        putBoolean(getString(R.string.first_time_opening), false)
        apply()
}
```

使用以下代码片段:

```
sharedPreferences.edit {
        putBoolean(getString(R.string.first_time_opening), false)
}
```

这段较短的代码应该会提供相同的结果！

## 结论

在本文中，我们研究并提供了使用 Kotlin `SharedPreferences` API 的解决方案。我们还演示了如何在一个涉及 onboarding 屏幕的例子中使用`SharedPreferences`。

关于这个主题的更多信息，请查看官方文档: [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences) 、 [Jetpack DataStore](https://developer.android.com/topic/libraries/architecture/datastore) 和 [Kotlin extensions](https://kotlinlang.org/docs/extensions.html) 。

感谢您的阅读。我希望这篇文章能让你在使用`SharedPreferences`的时候生活得更轻松！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)