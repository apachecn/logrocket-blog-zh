# Android 中的数据绑定:示例教程

> 原文：<https://blog.logrocket.com/data-binding-android-tutorial-with-examples/>

## 介绍

Android Jetpack 是一组旨在帮助开发人员遵循最佳实践并快速简单地创建代码的库。[数据绑定库](https://developer.android.com/topic/libraries/data-binding)就是其中之一。

数据绑定允许您毫不费力地跨视图和数据源进行通信。这个模式对于很多 Android 设计都很重要，包括 [model view ViewModel (MVVM)](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel) ，这是目前最常见的 Android 架构模式之一。

根据 Android 开发者文档:

> 数据绑定库是一个支持库，允许您使用声明性格式将布局中的 UI 组件绑定到应用程序中的数据源，而不是以编程方式。

在本文中，您将了解如何在当前的 Android 应用程序中使用数据绑定库。

## Android 中的数据绑定是什么？

在继续之前，您应该熟悉在 Android 中使用数据绑定的意义。

数据绑定是将 XML 布局中的视图与数据对象集成的过程。数据绑定库负责生成该过程所需的类。

与其他类型的布局 XML 文件不同，数据绑定-布局 XML 文件以根标签`layout`开始，后面跟着一个`data`元素。每个布局文件都与一个由库生成的`Data Binding`类相关联。

大多数情况下，默认的类名对应于布局文件的名称，后跟`Binding`后缀，例如`HomeActivityBinding.kt`。

以下是在 Android 应用程序中使用数据绑定库的优势:

1.  您可以减少`findViewById`通话并增强应用程序的性能
2.  帮助消除内存泄漏或
3.  使用声明式布局，适应性更强
4.  通过编写无错误、更短、更易于理解、更易于维护的代码，提高开发人员的工作效率
5.  数据和视图是相互分离的
6.  由于类型安全，编译器会在编译时验证类型，并在您试图将不正确的类型赋给变量时显示错误

Google 的[推荐架构](https://developer.android.com/jetpack/guide#recommended-app-arch)也展示了数据绑定的真正潜力，通过以各种可能的方式使用该库，从声明表达式到绑定适配器——实现 UI 逻辑和测试 UI 变得更加容易。

## 配置项目以启用数据绑定

首先，您需要在应用程序中设置开发环境，以使用数据绑定库。

您必须在`app`级别的`build.gradle`文件中声明它，如下所示:

```
apply plugin: 'com.android.application'

android {

    ...

    dataBinding {
        enabled = true
    }

    defaultConfig {
        ...
    }
    ...

}

```

如果您的项目使用 [Kotlin](https://blog.logrocket.com/tag/kotlin) ，下面的声明将适用于您:

```
apply plugin: 'com.android.application'

android {

    ...

    dataBinding {
        android.buildFeatures.dataBinding = true
    }
    ...

}

```

这通知 Gradle 它应该使用数据绑定库来创建您的项目。

## 将 XML 布局转换为数据绑定布局

数据绑定库自动构建将视图链接到数据对象的类。您可以在布局中使用库的导入、变量和包含。

要将 XML 布局转换为数据绑定布局，请按照下列步骤操作:

1.  声明一个`<layout>`标签，它将在根级别包装您现有的布局文件
2.  在`<data>`标签下声明变量，这些变量将放在`<layout>`标签下
3.  声明必要的表达式来绑定视图元素中的数据

下面是在没有启用数据绑定的情况下在 Android Studio 中创建新的 Android 项目时提供的默认布局的代码示例。

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".HomeActivity">

    <TextView
        android:id="@+id/text_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="My name is Android!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

```

当您将上述标准布局转换为数据绑定布局时，结果将是:

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout 
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:id="@+id/text_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="My name is Android!"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>

```

一旦完成，就会自动生成文件，在`java (generated)`目录下保存 XML 的绑定引用。不建议编辑此文件，因为它是自动生成的，用于维护绑定引用。

## 活动、视图和片段中的数据绑定

现在，您需要更新代码文件中的业务逻辑。通常，当您在类文件中为视图编写声明时，类似于:

```
TextView textName = (TextView) findViewById(R.id.text_name);        // java
View customView = (MyCustomView) findViewById(R.id.custom_view);
RecyclerView list = (RecyclerView) findViewById(R.id.recycler_list);

OR 

private lateinit var textName: TextView                             // kotlin
private lateinit var customView: MyCustomView
private lateinit var list: RecyclerView

// in onCreate()
textName = findViewById(R.id.text_name)
customView = findViewById(R.id.custom_view)
list = findViewById(R.id.recycler_list)

```

值得注意的是，随着视图数量的增加或复杂层次的存在，声明可能会变得过于冗长。数据绑定可以方便地避免这种情况。

让我们看看以下步骤，以避免过度声明:

### 1.声明绑定

在根据前面的步骤确保 XML 布局被转换为数据绑定布局之后，您需要在关联的类文件中声明绑定变量，如下所示:

```
private lateinit var homeBinding: ActivityHomeBinding

OR

private lateinit var homebinding: FragmentHomeBinding

```

这一行产生了绑定类`ActivityHomeBinding`的一个新实例，它是从 XML 布局转换中自动生成的。当声明这一行时，您会注意到 Android Studio 在您的文件中自动添加了一个新的`import`语句:

```
import com.logrocket.databinding.ActivityHomeBinding

```

这是对生成实例的绑定类的原始引用。

### 2.绑定视图

下一步，您需要在`onCreate`方法中用数据绑定版本替换标准的`setContentView`声明。原始语句类似于:

```
@Override
protected void onCreate(Bundle savedInstanceState) {

    ...

    setContentView(R.layout.home_activity)
}

```

它需要更新为:

```
@Override
protected void onCreate(Bundle savedInstanceState) {

    ...

    val homeBinding: ActivityHomeBinding = DataBindingUtil.setContentView(this, R.layout.activity_home)

    ...
}

```

如您所见，这段代码利用数据绑定库类`DataBindingUtil`来配置内容视图。

### 3.移除`findViewById`参考

现在是时候从代码中移除`findViewById`声明了。`findViewById`是一种连接布局视图和类文件中编写的业务逻辑的常见模式。

但是它也有自己的缺点，例如:

*   运行时错误
*   类型转换问题
*   冗长的声明会增加应用程序的引用和大小

为了克服这一点，您可以直接使用您在上一步中声明的绑定变量，如下所示:

```
// For RecyclerView
homeBinding.list.layoutManager = LinearLayoutManager(this)
homeBinding.list.adapter = UsernameAdapter(this, viewModel.usernames)
homeBinding.list.adapter?.notifyDataSetChanged()

// For Text
homeBinding.textName = "Android 11"

```

这样，您可以访问活动、片段甚至自定义视图类文件中的视图。通过使用数据绑定声明，您不仅可以摆脱`findViewById`声明，还可以帮助您编写声明性的、无错误的代码。如果您通过将视图与不匹配的变量绑定来声明不正确的赋值，它会在编译时显示一个错误。

如果您正在访问任何不适当的视图元素——例如，通过使用 activity/fragment 中的`TextView`从 XML 访问`RecyclerView`,或者任何有类型转换问题的视图——您将在编译时而不是运行时被告知。

## 适配器中的数据绑定

让我们学习如何在适配器类中利用数据绑定。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

适配器用于有效地存储、显示和更新列表格式的可用数据。适配器可以与视图元素一起使用，比如`RecyclerView`、`ListView`，甚至是从相似的视图类型扩展而来的自定义视图声明。

看看下面的适配器类，它用来显示用户的名字。

```
// UsernameAdapter.kt

class UsernameAdapter() : ListAdapter<Username, UsernameAdapter.UsernameViewHolder>(UsernameDiffUtil()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UsernameViewHolder {
        val user = LayoutInflater.from(parent.context).inflate(R.layout.user_list_item, parent, false)
        return UsernameViewHolder(func, user)
    }

    override fun onBindViewHolder(holder: UsernameViewHolder, position: Int) {
        holder.bindData(getItem(position))
    }

    class UsernameViewHolder(val view: View) : RecyclerView.ViewHolder(UsernameItem item) {
        fun bind(item: UsernameItem) {
            username = "${item.name}"
        }
    }
}

```

您需要更新上面关于数据绑定实现的代码，如下所示:

```
// UsernameAdapter.kt

class UsernameAdapter() : ListAdapter<Username, UsernameAdapter.UsernameViewHolder>(UsernameDiffUtil()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UsernameViewHolder {
        val layoutInflater = LayoutInflater.from(parent.context)
        val userbinding: UsernameItemBinding = DataBindingUtil.inflate(layoutInflater, R.layout.user_list_item, parent, false)
        return ViewHolder(userbinding)
    }

    override fun onBindViewHolder(holder: UsernameViewHolder, position: Int) {
        holder.bind(items[position])
        holder.userbinding.setOnClickListener { // Do something }
    }

    class UsernameViewHolder(val userbinding: UsernameItemBinding) : RecyclerView.ViewHolder(userbinding.root) {
        fun bind(item: UsernameItem) {
            userbinding.apply {

            }
        }
    }
}

```

您会注意到，我们已经用数据绑定声明更新了`onBindViewHolder`和`UsernameViewHolder`。这里，使用的适配器负责在屏幕上显示数据。因为您已经绑定了绑定类，所以布局文件现在正在使用您的绑定声明。

目前，布局文件只知道指定的引用，但是它还没有使用上面写在`UsernameAdapter`下的代码显示数据。为了有效地利用数据绑定，现在您将看到如何使用变量绑定布局中的值。

## 变量和表达式中的数据绑定

如前所述，您可以通过`<data>`标签直接使用布局 XML 文件中的变量和数据。

这样有什么好处？由于数据绑定的`observability`属性，您的布局文件会监听数据中的所有更改，并立即将这些更改反映在用户的移动屏幕上。

实现这一点有两种主要方法:

### 1.使用变量

在这种方法中，您将在声明数据绑定时使用代码和 XML 文件中的`<data>`标记，例如:

```
// HomeActivity.kt

    homeBinding.user = User()

```

在声明了要在`onCreate`下使用的对象之后，您需要将其声明为`<variable>`，然后使用模型对象内部可访问的属性。下面是一个例子:

```
// HomeActivity.xml

<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>
        <variable
             name="username"
             type="com.logrocket.Model.User" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout 
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:id="@+id/text_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{username.name}"                     // Declaration
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>

```

### 2.使用表达式

假设您希望根据某个条件填充数据或更新视图。数据绑定允许你声明为你工作的表达式！

是的，您可以在布局 XML 文件中声明表达式。然而， [Android 指南](https://developer.android.com/topic/libraries/data-binding/expressions#expression_language)建议你保持你的表达式简单，以避免复杂的问题或错误，因为以这种方式声明的表达式不在 UI 或单元测试的测试覆盖范围之内。

下面是一个正确表达式声明的例子。在这里，基于`isSuccess`变量的值，您的视图将被填充一个背景，红色或绿色。

```
   ...

    <androidx.constraintlayout.widget.ConstraintLayout 
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:id="@+id/text_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="My name is Android!"
            android:background="@{isSuccess ? @color/red : @color/green}"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    ...

```

![The green value display of the isSuccess variable](img/e3b6a3d9d6c2216ceeb3af40c6d9e05a.png)

![The red value display of the isSuccess variable](img/19495b0f63d6b05e8618da0d7bcc02c8.png)

通过使用上述方法中的一种或两种，您可以删除大量样板代码并以编程方式显示您的视图，而数据绑定会处理其余的工作。

## 结论

在本文中，您已经学习了如何在 Android 应用程序中使用数据绑定库。数据绑定无疑是 Android 生态系统中最受欢迎和最强大的进步之一。通过使用一种现代的、更安全的方法，利用编译时错误而不是运行时错误，以及简洁的方法，您的应用程序肯定会在使用数据绑定时得到提升。

您可以从官方 Android 文档中探索和了解更多关于数据绑定库的用例。

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