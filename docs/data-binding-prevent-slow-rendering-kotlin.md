# 在 Kotlin - LogRocket 博客中使用数据绑定防止渲染缓慢

> 原文：<https://blog.logrocket.com/data-binding-prevent-slow-rendering-kotlin/>

在当今的开发环境中构建现代应用程序时，需要考虑性能、内存泄漏、渲染速度等因素。

数据绑定是一个高级主题，它使开发人员能够防止 Android 应用程序呈现缓慢。使用数据绑定的一些优势包括防止内存泄漏或`nullPointerException`，减少`findByViewId`调用，以及创建声明性布局，等等。

在本教程中，我们将涵盖以下内容:

要阅读本文，您需要以下内容:

## 什么是数据绑定？

数据绑定是 Android 应用程序开发中采用的一种高级方法，用于以类型安全和空安全的方式访问视图。这是一个将两个数据源耦合在一起并使它们同步的过程，它有助于消除应用程序中对 DOM 操作的需求。

数据绑定可能简单，也可能复杂，这取决于所涉及的数据元素的数量。根据[微软](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/data/?view=netdesktop-6.0)的说法，简单的数据绑定可以绑定单个数据元素，而复杂的数据绑定可以绑定多个数据元素。使用数据绑定，对数据集中一个元素的更改会自动更新数据集中的其他元素。

数据绑定有助于简化网页元素中的数据表示，而无需复杂的编程过程。数据绑定通常在应用程序开发中用来链接应用程序的 UI 和它显示的数据。在这个上下文中，有一个数据源、一个数据提供者和其他数据集，它们是数据消费者。

有几种类型的数据绑定。例如，在单向绑定中，对数据提供者所做的更改会传递给数据使用者，而不是相反。它只允许一个方向的改变。另一方面，在单向到源的绑定中，对数据使用者所做的更改会传递到数据源，但反之则不然。

在双向绑定中，对数据使用者或数据提供者所做的更改可以被整个系统采用，并自动更新数据集。最后，在一次性绑定中，对数据提供者所做的更改不会自动反映在数据使用者身上。此过程主要用于静态数据或只需要数据快照的情况。

接下来，我们将介绍如何在 Android 应用程序中设置一个支持数据绑定的环境。

## 在 Android 应用程序中启用数据绑定

首先，我们必须[设置我们的开发环境](https://developer.android.com/studio/releases/gradle-plugin#updating-plugin)，以便能够使用[数据绑定库](https://developer.android.com/topic/libraries/data-binding)。为此，您可以通过在您的`build.gradle`文件中启用数据绑定构建选项来配置您的应用程序，如下面的代码片段所示:

```
android{
      ...
      buildFeatures{
       dataBinding true
    }
}

```

Android Studio 为数据绑定代码的许多可编辑特性提供了强大的支持，比如 XML 代码完成、语法高亮显示和语言语法错误通知。您可以点击此 [repo](https://github.com/android/databinding-samples/tree/main/BasicSample) 获取一些数据绑定代码示例:

![Build Gradle Android Studio File Structure](img/0b3fc22eea8ca50a75da9d9646ab71d0.png)

为了启用数据绑定，我们导航到 Android Studio IDE 的`Gradle Scripts`部分并点击`build.gradle`，它位于`app module`内。我们可以使用下面的代码片段在其中启用`dataBinding`:

```
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    compileSdk 32

    defaultConfig {
        applicationId "com.example.myfirstandroidapplication"
        minSdk 21
        targetSdk 32
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
    buildFeatures {
        dataBinding true
    }
}

dependencies {

    implementation 'androidx.core:core-ktx:1.7.0'
    implementation 'androidx.appcompat:appcompat:1.3.0'
    implementation 'com.google.android.material:material:1.5.0-alpha04'
    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'
    implementation 'androidx.navigation:navigation-fragment-ktx:2.3.5'
    implementation 'androidx.navigation:navigation-ui-ktx:2.3.5'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
}

```

我们也可以决定使用下面的命令:

```
dataBinding {
   enabled = true
}

```

数据绑定方法比[视图绑定](https://developer.android.com/topic/libraries/view-binding)更有效，后者涉及遍历`view hierarchy`以找到运行时创建或重新创建的视图。对于一个大的`view hierarchy`，这可能需要很长时间，可能会减慢用户的查看速度，并对 UX 产生不良影响。

为了解决这个问题，数据绑定允许布局在编译时连接到活动或片段。这个编译器生成一个名为`binding class`的助手类。我们将在下一节进一步讨论这个问题。

## 为大型 Android 布局使用数据绑定帮助器类

数据绑定助手类对于具有大布局的大型 Android 项目来说非常方便。助手类使我们能够在没有任何额外开销的情况下访问视图。在下一节中，我们将演示在 Android 应用程序中向`PersonalInfo`类添加数据绑定所需的步骤。

## 数据绑定为什么比`findByViewId`方法更快

在 Android 生命周期的早期，开发人员必须经历一个被称为`viewBinding`的非常昂贵的操作，这涉及到遍历视图层次结构，导致开发时间的挑战。

数据绑定背后的思想是创建对象，在编译时将两个相距遥远的信息绑定在一起，使它们在运行时可用，因此您不必搜索它们。这个对象由编译器创建，称为`binding object`。

数据绑定试图消除`findViewById`代码片段，如下所示:

```
findViewById(R.id.age).apply {
text = viewModel.age
}

```

更有效的方法是使用`dataBinding`，如下面的代码片段所示:

```
dataBinding {
  enabled = true
}

```

在应用程序的`build.gradle`文件中启用了`dataBinding`之后，就像上一节一样，我们可以实现`dataBinding`。

首先，确保将`activity_main.xml`和名称空间声明中的所有视图包装到一个`<layout>`标签中。然后，使用下面的代码片段在`main activity`中创建一个`binding object`:

```
private lateinit var binding: ActivityMainBinding

```

接下来，使用`DataBindingUtil`设置`onCreate`中的内容视图:

```
binding = DataBindingUtil.setContentView(this, R.layout.activity_main)

```

用`binding object`替换对`findViewById`的所有调用:

```
binding.doneButton.setOnClickListener

```

为您想要显示的数据集创建一个`data class`。我们将创建一个名为`PersonalInfo`的类:

```
data class PersonalInfo(var name: String = "", var age: String = "")

```

接下来，我们可以给`activity_main.xml`添加一个`<data>`块。将布局标签内的数据块添加到`view`标签之前。在数据块内部，为`PersonalInfo`类添加一个变量:

```
<data>
<!-- Declare a variable by specifying a name and a data type. -->
<!-- Use fully qualified name for the type. -->
<variable
    name="PersonalInfo"
    type="com.example.myfirstandroidapplication.aboutme.PersonalInfo" />
</data>

```

在`name_text, age_edit`和`age_text`中，将字符串文本资源的引用替换为变量的引用，例如:

```
android:text="@={PersonalInfo.name}"

```

在`MainActivity`中，创建一个`PersonalInfo`的实例:

```
Instance of PersonalInfo data class.
private val PersonalInfo: PersonalInfo = PersonalInfo("Elena Allison")

```

将`binding.personalInfo`设置为`onCreate()`:

```
binding.personalInfo = personalInfo

```

在`addAge`中，可以通过调用`invalidateAll()`来设置绑定对象中`age`的值，绑定对象就是`personaInfo`。数据应该在您的视图中显示如下:

```
personalInfo?.age = ageEdit.text.toString()
// Invalidate all binding expressions and request a new rebind to refresh UI
invalidateAll()

```

## 绑定点击监听器

`ClickHandler`有助于确保`onButtonClick()`方法中的代码在用户触发或点击按钮时运行。下面的代码片段向您展示了如何在 Android 应用程序中启用点击监听器:

```
public interface ClickHandler {
    public void onButtonClick(View v);
}

```

接下来，我们将使用以下代码片段配置布局 XML:

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="handler"
            type="com.example.ClickHandler"/>
    </data>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="click me"
            android:onClick="@{handler.onButtonClick}"/>
    </RelativeLayout>
</layout>

```

在配置好我们的布局 XML 之后，我们可以使用下面的代码片段在我们的`MainActivity`类上实现`ClickHandler`接口:

```
public class MainActivity extends Activity implements ClickHandler {

    private ActivityMainBinding binding;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this,R.layout.activity_main);
        binding.setHandler(this);
    }

    @Override
    public void onButtonClick(View v) {
        Toast.makeText(context,"Button clicked",Toast.LENGTH_LONG).show();
    }
}

```

## 结论

在本教程中，我们概述了通过数据绑定在 Android 应用程序开发中防止缓慢渲染的步骤。我们从定义数据绑定的含义以及它如何帮助 Android 开发人员减少编写样板代码开始。

作为一名 Android 开发人员，采用本文概述的步骤可以帮助您在下一个 Android 项目中提高性能和用户体验。我希望你喜欢这篇文章，并快乐编码！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)