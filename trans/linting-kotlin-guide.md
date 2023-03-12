# 林挺在科特林:一个完整的指南

> 原文：<https://blog.logrocket.com/linting-kotlin-guide/>

Android 开发人员在无数其他编辑器中使用 Android Studio 进行应用开发——它是首选，不仅因为它是开发人员的主要原生应用编辑器，还因为它为他们提供了特殊的离线帮助。

这个关键特性确保我们编写的代码对于我们的 Android 应用程序来说是丰富而高效的，也是 Lint 工具的用武之地。

Lint 工具用于帮助发现错误，避免编写糟糕的代码。在本文中，您将了解科特林的林挺，以及如何开始使用它。我们直接进去吧！

## 先决条件

要完全理解这篇文章，读者应该满足以下先决条件:

*   努力学习 Kotlin 和 Android Studio 的中级知识。

## 目的

在本文中，我们将讨论以下主题:

## 安装 Android Studio

### **下载和定制 Android Studio**

点击[此处](https://developer.android.com/studio/)下载 Android Studio zip 文件。

![Android Studio Screenshot](img/c02ea2f3d8300e881953b51761ba3b94.png)

下载完成后，运行下载的文件。

![Android Studio Setup](img/5b709e32261ee4e49985aa36c1428546.png)

点击**下一个**按钮，按照提示信息询问是否拒绝安装 Android 虚拟设备(AVD)的选项。

(注意:不要取消选中 AVD，因为它需要在编译后查看我们的应用程序)

![Android Studio Setup Components](img/f712869a929da7946ca61284aebb1665.png)

在配置设置中，您可以更改安装位置。点击**下一个**按钮，保持当前安装位置。

继续点击下一个的**，直到安装完成。**

一旦完成，点击**完成**按钮。

![Complete Android Setup](img/f6379efacbaa5dccfa49574a6e292cd7.png)

(注意:**完成安装**对话框将询问是否导入以前的设置(即，如果您之前安装了 android studio，最好不要导入设置)

Android Studio 闪屏会自动出现。

![Android Studio Splash Screen](img/7efb2ad827faac69879a57b7f0e5a815.png)

(注:以下流程为定制 Android Studio)

![Finding Available SDK Components](img/060ee7d4d757e4b27e7ed0e4caaf1921.png)

闪屏会找到可用的 SDK 组件。这些组件显示为如下所示的提示消息。

找到 SDK 组件后，一旦 Android studio 将您重定向到“欢迎”屏幕，请单击**下一步**。

![Android Studio Welcome Screen](img/192edfa49bff8a4f51bbd26b0fb16ef6.png)

将安装类型保留为“标准”点击下一个的**，会出现一个 UI 主题对话框。**

(注意:您可以选择深色主题(Darcula)或浅色主题(IntelliJ))

![Android Studio Install Type](img/334bd2c0753c676833cd11cd4391e5e0.png)

根据需要选择一个首选主题，然后点击 **Next。**这样做允许 Android Studio 下载 SDK 组件:

(注意:与之前的设置相比，此过程可能需要更多时间，请耐心等待，直到下载完成)

![Android Studio Verify Settings](img/28aaf91b41e9978527b550d8f95a7290.png)

完成以上，Android Studio 配置成功。您现在可以启动和构建应用程序。点击**完成**按钮启动。

点击**新项目**创建新应用。

![Welcome To Android Studio](img/b82eb2c3ebd89de270506898da0c3dc1.png)

## Android Studio 的 Kotlin 设置

该版本 Android Studio(大黄蜂 2021.1.1 补丁 2)自动安装 Kotlin 插件。

## 什么是皮棉？

Lint 是 Android Studio 提供的一个代码工具，它可以在不构建应用程序的情况下检查、检查、建议和纠正项目中的代码问题。

Lint 会通知您代码中的任何错误或警告，并建议进行更改。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Android Studio 包括在您创建项目时自动执行检查过程的特性。同时，您可以选择手动或者使用 Lint 在命令行中检查您的代码。

Lint 最突出的特性之一是你可以用任何你喜欢的方式来应用它(它是多样化的)。Lint 在您的项目中提供了一种特定类型的错误，它只会显示这种类型的错误。

现在，让我们进一步详细说明 Lint 是如何使用的。

### **通过代码检查**

#### 弹出文本

在 Android Studio Editor 中，当 Lint 在您的代码中发现任何错误时，它会突出显示这样的问题:

*   **红色**:你的代码中有严重错误
*   **黄色**:对你的代码的警告

#### 手动检查代码

为了阐明这个概念，我们需要创建一个新的项目。为此，点击**新项目**并选择**空活动**。点击**下一步**按钮继续。
![Android Studio New Project](img/e7da3befaf5a6b181aaeb47d08687a89.png)

(注意:您也可以继续您当前的项目)

使用您选择的应用程序名称。另外，将当前的编程语言切换到 Kotlin，点击 **Finish** 开始。

![Android Studio New Project Save](img/61c9ea2c21f474929d4316938256d5a1.png)

加载项目需要一两分钟的时间。完成后，选择侧边栏上的 activity_main.xml。

![Selecting Activity Main Xml](img/7de6f16f8499da3a1af16be38416d497.png)

将文本的值从“你好！”到“Home”并观察代码行上的突出显示。

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Home"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>

```

之后，将鼠标导航至工具栏，点击**分析**。在**分析**按钮下方，选择**检测代码**。

![Android Studio Inspect Code](img/4c65f77c855537703842898dd750ffd3.png)

在**检查** **代码**部分，勾选**整体** **项目**，取消勾选**包含测试源**。然后点击 **OK** 开始检查。

![Specify Inspection Scope Whole Project](img/3c8c438f54cfd7be8456d9c63d28dd46.png)

完成后，检查报告将显示在终端中，如下图所示。您会注意到 Lint 显示的层次结构，旁边显示了警告的数量。

在这种情况下，我们得到一个内部化警告。当我们单击硬编码文本时，右侧会打开一个包含警告详细信息的选项卡。它提供了快速修复，在本例中，我们通过在突出显示的警告上按下 **alt+shift+enter** 来提取字符串资源。

![Android Studio Lint Warnings](img/74b8ca032fbf526e13323725f99d99f5.png)

#### **通过命令提示符**

您可以通过键入以下命令，使用 Gradle 包装器调用 Android Studio 上的 Lint 任务:

```
gradlew Lint

```

(注意:您应该在项目的根目录中执行此操作)

一旦它处理完毕，您的输出可能会向您显示报告文件的位置，但如果没有，请前往您项目的存储位置，选择 **App** 文件夹，然后选择 **Build** 文件夹，然后选择 **Report** 文件夹，您将会找到报告。

(注意:它们是 XML 和 HTML 格式的)

在“通过代码检查”一节中，我们给出了一个如何检查警告的例子。让我们假设我们想要保留文本(Home)并抑制警告；我们可以用下面的方法做到这一点:

### **与 Lint.xml 文件**

lint.xml 文件的用法很简单:如果您希望忽略一个警告，或者希望每个警告有不同类型的警告级别，就可以使用它。

使用 lint.xml 文件，您可以通过控制问题的严重性来取消 lint 检查。以下是实现这一目标的过程:

首先，进入应用程序根目录，创建一个名为“lint.xml”的新文件。

记住前面给出的警告是“内部化(硬编码文本)”。因此，使用下面的代码，我们可以忽略警告，继续我们的选择。

```
<?xml version="1.0" encoding="utf-8" ?>
<lint>
    <issue id="HardcodedText" severity="ignore"/>
</lint>

```

### **With @ file:Suppress in Kotlin**

为此，我们将按照前面的步骤创建一个新项目。唯一的区别是我们选择了底部导航，而不是空的活动。

滚动到工具栏，选择**分析**，然后**检查** **代码*，*** 然后 **Ok** 。

(注意:检查整个代码并取消选中**包括测试源**)

检查**问题**部分的 Kotlin 部分，并在那里选择任何一个 Kotlin 文件。这样做会打开一个侧标签；选择**抑制**。

一旦完成了这些，就转到您隐藏的 Kotlin 文件；你会注意到这样一个代码:`@file:Suppress("ReplaceGetOrSet")`。

代码是@file:Suppress，用于禁用我们项目中特定方法或类的 Lint 检查。复制代码并将其粘贴到您想要隐藏的其他 Kotlin 代码的第一行。

示例:

```
@file:Suppress("ReplaceGetOrSet")

package com.example.secondlinting.ui.home

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.fragment.app.Fragment
import androidx.lifecycle.ViewModelProvider
import com.example.secondlinting.databinding.FragmentHomeBinding

class HomeFragment : Fragment() {

    private var _binding: FragmentHomeBinding? = null

    // This property is only valid between onCreateView and

    // onDestroyView.

    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        val homeViewModel =
            ViewModelProvider(this).get(HomeViewModel::class.java)

        _binding = FragmentHomeBinding.inflate(inflater, container, false)

        val root: View = binding.root
        val textView: TextView = binding.textHome
        homeViewModel.text.observe(viewLifecycleOwner) {
            textView.text = it
        }
        return root
    }
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}

```

完成后，再次分析代码以确认是否已取消掉毛检查。

## **在 XML 中配置 Lint 检查**

使用“tools:ignore”属性将对 XML 文件的某些部分禁用 Lint 检查。

键入" namespace xmlns:tools = "[http://schemas.android.com/tools&# 8221](https://schemas.android.com/tools&#8221)；允许 lint 工具识别属性。

下面的例子显示了如何在 XML 布局文件的元素中关闭对`HardcodedText`问题的 lint 检查。父元素的子元素继承声明它的 ignore 属性。

```
<LinearLayout
  xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:tools="http://schemas.android.com/tools"
  tools:ignore=" HardcodedText " >

<Button  
  android:text="Agree And Continue" />

</LinearLayout>

```

您可能还想抑制 XML 文件中的 lint 检查问题。为此，您将使用如下的“all”关键字:

```
tools:ignore="all"

```

## 使用基线警告

在大型项目上工作需要准确和适当的检查，这可能很耗时。在开发过程中，您可能已经在 Lint 文件中设置了一些当前的警告，并且可能想要识别未来的错误——这就是基线出现的地方。

您可以为将来的检查建立一个基线，lint 将会产生在该基线之后发生的错误。这是有益的，因为 Lint 会通知您在新代码行中发现的问题，而忽略之前的代码问题。

要为您的项目创建基线，请通过添加以下代码行来修改您的 Gradle 文件:

```
lintOptions {
    disable 'TypographyFractions','TypographyQuotes'
    abortOnError false
}

```

完成这些过程后，从代码编辑器运行 Lint(**Analyze；** **检查代码**)如果您想要生成一个输出来查看 lint 记录，请使用如下命令行:

```
Gradlew lint

```

Lint 在 lint-results-debug.html 文件中记录当前的问题——因此，这被称为基线。

## 结论

Lint 是应用程序开发中的一个重要工具，它为开发人员在项目中省去很多麻烦。

在本文中，我们展示了安装 Kotlin、使用和配置 lint 工具以及在处理大型项目时使用基线警告的过程。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)