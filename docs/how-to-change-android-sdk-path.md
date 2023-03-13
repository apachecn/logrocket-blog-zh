# 如何更改 Android SDK 路径

> 原文：<https://blog.logrocket.com/how-to-change-android-sdk-path/>

[Boemo Mmopelwa Follow](https://blog.logrocket.com/author/boemowamemmopelwa/) Boemo is a software developer who embraces innovative approaches. He likes diving deep into complex concepts in order to learn and write articles that can help the reader understand complex methodologies in a simple and fun way.

# 如何更改 Android SDK 路径

## 

2023 年 2 月 20 日 4 分钟阅读 1183 110

一套称为 Android SDK(软件开发工具包)的工具使程序员能够构建 Android 应用程序。Android SDK 包括调试器、库、手机仿真器、文档、样本代码和其他工具。

安装 Android SDK 时，它通常安装在机器上的默认位置。但是，有些情况下，您可能想要切换 Android SDK 的安装位置，例如当您想要使用较新版本的 SDK 或将其安装在不同的位置时。在本文中，我们将讨论如何在您的系统上更改 Android SDK 路径。

*向前跳跃*:

## 先决条件

开始之前，您需要做几件事:

1.  确保您已经在系统上安装了 Android SDK。如果你还没有安装 Android SDK，可以从 [Android 开发者网站](https://developer.android.com/studio)下载
2.  如果您已经将 Android SDK 安装到一个自定义位置，请记下当前路径，以便您可以在以后更新它
3.  如果您想将 Android SDK 安装到一个新位置，请确保您的系统上有足够的空间来容纳 SDK。Android SDK 需要大约 1gb 的可用空间

## 什么是安卓工作室花栗鼠？

用于创建 Android 应用程序的集成开发环境(IDE)称为 Android Studio。目前，花栗鼠是最新安卓工作室版本 T1 的名字。它是专为 Android 开发而创建的，基于 JetBrains 的 IntelliJ IDEA 软件。

Android Studio Chipmunk 提供了许多功能和工具来帮助开发人员更快地开发 Android 应用程序。可视化布局编辑器、代码编辑器、调试器、分析器和其他工具就是其中的一些功能。还支持许多编程语言，包括 Java、Kotlin 和 C++。

除了它的主要功能，Android Studio Chipmunk 还提供了对几个插件和集成的支持，包括对 Git 版本控制、测试和调试工具等的支持。通过促进与其他程序和服务的交互，这些功能和集成使开发人员能够开发出更加稳定和可扩展的 Android 应用程序。

## 使用 Android Studio 时，什么时候应该更改 Android SDK 路径？

有人可能想要改变他们系统上的 Android SDK 路径有几个原因。一些常见的原因包括:

*   **安装更新的 Android SDK 版本:**要安装更新的 Android SDK 版本，您必须修改 Android SDK 路径以指向新版本的位置。这将让您利用 Android 平台的最新特性和功能，并确保您的开发工具和库是最新的。
*   **更改 Android SDK 的位置:**如果您希望将 Android SDK 转移到系统上的不同位置，您必须修改 Android SDK 路径以指向新位置。如果您想要清理现有驱动器上的空间或在单独的驱动器上安装 SDK，这可能是必需的。
*   **使用多个版本的 Android SDK:** 在同一台机器上使用不同的 Android SDK 版本需要您修改 Android SDK 路径，以便它指向每个版本的适当位置。因此，您将能够在各种 Android 平台版本上测试您的应用程序，并根据需要在各种 SDK 版本之间进行转换。

## 如何在 Android Studio 花栗鼠中更改 Android SDK 路径

在本节中，您将学习如何在 Android 中从头开始安装 SDK。您还将了解如何为您的应用选择合适的 SDK。要更改 Android SDK 路径，请按照下列步骤操作:

首先单击 SDK 管理器图标，它位于搜索图标的左侧:
![Clicking The SDK Manager Icon](img/3e0d99e0b6fe066a69d457400921bf7d.png)

在 Android SDK 管理器窗口中，您将看到当前的 SDK 位置。要更改它，按下**编辑**文本:


接下来，导航到窗口底部显示的 SDK 位置，然后按下选项的文件夹图标进行更改:
![Clicking The Icon Folder To Change The SDK Location](img/bfa12139e350f36404168d2e84f689d0.png)

接下来，选择您想要切换到的 SDK 位置，然后按下 **OK** 。上一个窗口将再次显示，然后按**下一个**:


按下“下一步”按钮后，Android Studio 会要求您验证刚刚所做的更改。如果已经换到正确的 SDK 位置:
![Verifying SDK Location Changes](img/2a889b219ece48daec5b11e38b49d229.png)，按**完成**

如果成功更改了 SDK 位置，您将看到新位置显示为 SDK 位置:

![Our New SDK Location Is Displayed](img/4a2177a9f93d5ce2151dec97d014fab8.png)

## 哪些文件夹不应该是 SDK 位置？

很难说哪个 SDK 位置是“坏的”,因为这取决于上下文和特定的用例。但是，在选择 SDK 位置时，需要考虑一些一般原则。

避免存储 SDK:

*   在网络驱动器上，因为它们可能很慢而且不可靠
*   因为它们会很快填满并导致性能问题
*   在需要管理员权限才能访问的位置，因为这会使其他用户很难访问 SDK
*   在未备份的位置，因为如果 SDK 或计算机出现问题，这可能会导致数据丢失

最终，SDK 的最佳位置将取决于您的项目和组织的特定需求。咨询您的开发团队或 It 部门来确定 SDK 的最佳位置总是一个好主意。

## 如何安装 SDK 平台

要安装 SDK 平台，请按照前面章节中的方式打开 SDK 管理器。在 SDK 管理器窗口中，您将看到 SDK 平台选项卡。此选项卡将包含您可以安装的所有可用 SDK。SDK 是安全的。

下载最新的 SDK 意味着你的应用将仅限于少数使用最新版本 Android 的用户。是的，最新的 SDK 包含了所有最新的特性；如果你的应用非常依赖最新的 Android 特性，那么你最好安装最新的 SDK 版本。

现在，选中您想要安装的特定 SDK 平台的所有复选框，并单击窗口右下角的**应用**按钮:

![Selecting Which SDK Platforms To Install](img/c92aea7cb36c5aabe092ccfbf9fb0ed3.png)

接下来，Android Studio 会询问您是否要安装所选项目。如果您希望安装选定的 SDK，请按**确定**:

![Installing Selected SDKs](img/5a5f7fbed543e7c93b9eeeae934e226c.png)

Android Studio 将下载 SDK，但你必须确保你的网络连接稳定，否则如果有任何网络中断，下载将被取消:

![SDK Component Installer](img/78b5af5e8126a69dc25d9cdd6bc7c0df.png)

## 结论

在本文中，我们讨论了如何在您的系统上更改 Android SDK 路径。更改 Android SDK 路径是一个简单的过程，可以通过 Android SDK 管理器来完成。通过更改 Android SDK 路径，您可以将 Android SDK 安装到系统上的不同位置，或者使用不同版本的 Android SDK。

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