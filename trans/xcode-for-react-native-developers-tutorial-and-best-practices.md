# Xcode for React Native developers:教程和最佳实践

> 原文：<https://blog.logrocket.com/xcode-for-react-native-developers-tutorial-and-best-practices/>

开发移动应用程序时，您需要在模拟器或设备上构建并运行您的应用程序，以测试它并查看更改。这与使用 web 应用程序不同，在 web 应用程序中，您不需要每次都构建应用程序。

当使用 [React Native 和 iOS](https://blog.logrocket.com/getting-started-with-hermes-in-react-native/) 时，通常使用 Xcode 作为构建工具。构建在 iOS 上运行的应用程序至少需要一些 Xcode 知识。

在本教程中，我们将带您了解一些最佳实践，帮助您开始使用 [Xcode](https://developer.apple.com/xcode/) ，并提供一些提示，这些提示将改变您使用 React Native 开发 iOS 应用程序的方式。

以下是我们将要介绍的内容:

## Xcode 是什么？

Xcode 是苹果公司开发的 IDE 和代码编辑器，用于帮助开发者开发苹果产品，即 iOS 的移动应用和 macOS 的桌面应用。

Xcode 是一个完整的环境，包含一组集成的工具和应用程序，旨在帮助所有开发过程，包括测试、构建、部署、编译和调试。

## 用 Xcode 在 iOS 上构建 React 原生应用

您可以在模拟器或真实设备上构建 React 原生应用程序。如果您打算在 iOS 设备或模拟器上运行它，通常需要使用 Xcode。

您可以使用 [React Native CLI](https://github.com/react-native-community/cli) 或 Xcode 来构建 React Native 应用程序，React Native CLI 允许您从终端运行构建命令。我更喜欢使用 Xcode，原因如下:

*   Xcode 实时显示关于构建过程的更多细节，比如日志
*   Xcode 使您能够进行可定制的构建配置。直接使用 Xcode 时，您可以根据自己的喜好调整构建过程。例如，您可以选择模拟器或设备的类型，使用自定义方案进行构建，等等。
*   React Native CLI 在幕后使用脚本代码运行一些 Xcode 功能。CLI 提供了运行和构建代码的简单性，只需一个命令行
*   仅使用 CLI 时，您可能会在构建时遇到意外问题。直接使用 Xcode 会让您有一个清晰的画面，并帮助您确定给定问题的原因

让我们来看看如何使用 React Native CLI 构建和运行 React Native 应用程序。然后，我们将在真实设备上直接进行同样的操作。

### 使用 React 本机 CLI

React Native CLI 是让 React 本机应用程序运行的一种简单而直接的方式。当您第一次使用 CLI 安装 React 本机应用程序时，它会提供简单的说明，并允许您通过一个简单的命令启动、构建和运行应用程序:

```
react-native run-ios

```

也可以用 [npx](https://www.npmjs.com/package/npx) :

```
npx react-native run-ios

```

基本上，它在后台运行一堆脚本，这些脚本交互并指示 Xcode 运行和运行模拟器。如果您没有指定模拟器，CLI 将使用它在 Xcode 上找到的默认模拟器。以下是如何告诉 CLI 您想要在哪个模拟器上运行应用程序:

```
react-native run-ios --simulator "iPhone X"

```

如果您想要在设备上运行应用程序，您必须首先确保您的设备通过 USB 连接到您的电脑。然后，使用`--device"iPhone name"`运行 CLI 命令:

```
react-native run-ios --device "Said's iPhone"

```

### 直接使用 Xcode

构建和运行 React 原生 app 的第二种方法是直接使用 Xcode，这也是我使用的方法。

在 Xcode 中导航到`File → Open`并浏览到您的项目目录。在`iOS`文件夹中，选择`appName.xcodeproject`或`appName.xcodeworkspace`(如果有)。

您可以通过进入选项卡导航器(`Product→ Build`)或使用快捷键`[⌘](https://apple.stackexchange.com/questions/4074/what-do-i-type-to-produce-the-command-symbol-in-mac-os-x) + B`来构建应用程序:

![Xcode Shortcut Display](img/bb6ba7a6be88b4add85f06114fd6e48d.png)

使用模拟器运行和测试应用程序是一个很好的实践。然而，有时有必要在真实设备上测试 UI 的准确性。

## 在真实的 iOS 设备上运行 React 原生应用

在设备上运行应用程序最明显的好处是，它能让你以用户的方式体验你的应用程序。模拟器是有限的，并不总是完全准确地代表应用程序。例如，使用模拟器时，您无法访问设备摄像头。

我更喜欢一直使用这款设备，因为它让我更完整地了解应用程序在用户端是如何工作的。

要在真实设备上运行和构建 React Native，您必须具备:

*   一个 iOS 设备；macOS 只允许你在 iOS 设备(iPhone、iPad、Apple TV 等)上运行 iOS 应用。)
*   将设备连接到 Mac 电脑的 USB 连接器
*   苹果开发者账户

![Run Build React Native Real Device Requirements List](img/58e164c70a49bf8438505382d5e9b78f.png)

连接 iPhone 后，Xcode 会将连接的设备显示为目标:

![iPhone Connected Xcode Target Devices List](img/2cb6d92977cbfaa3c558e5eab56fe525.png)

在我的情况下，我的 iPhone 的名字是“iPhone”。您可能会看到设备的不同名称，如“托尼的 iPhone”

选择你想要构建和运行应用的设备，然后点击**构建**按钮。React Native CLI 将自动打开终端并启动服务器。如果它还没有启动，您可以到您的终端运行`npm start`:

![React Native Cli Terminal Start Server](img/fa10e5db7aaa97bb08d3f6b724a89f5f.png)

如果构建成功，应用程序将在您的设备上自动启动:

![Successful Build Automatic App Launch Device](img/f22071b802386721414b8deca2313ed7.png)

为了确保设备连接到服务器，您可以到终端并按下`r`来重新加载应用程序或`d`来打开开发者菜单。或者，你可以摇动你的设备来弹出开发者菜单。

您应该会看到实时重新加载也在工作:

![Device Connected Live Reload Working](img/edfeb3d3fd1842a46dc7d8513350a5ac.png)

随着您在 React 原生开发之旅中的前进，您会发现自己正在处理意想不到的问题，尤其是在应用程序构建方面。这些问题通常源于 React 原生包和 iOS 之间的版本不匹配，或者用于构建应用程序的工具，如 Xcode。

随着时间的推移，你会熟悉这些问题，并更有效地解决它们。

## 使用模拟器运行和测试您的代码

模拟器只是一个虚拟设备，但它几乎拥有测试和运行代码所需的一切。让我们探索一些你可以用模拟器做的事情。

### 排除故障

模拟器为调试应用程序提供了多种功能，尤其是辅助功能。例如，您可以启用`Color blended Layers`来测试颜色的可访问性。

在 Xcode 中，打开**调试**选项卡:

![Xcode Simulator Debug Tab](img/b2f82294c6908196785b9958d01205f9.png)

### 测试黑暗模式

如果你有最新版本的 [Xcode (12.4)](https://developer.apple.com/documentation/xcode-release-notes/xcode-12_4-release-notes) ，有一个功能可以让你看到你的应用在黑暗模式下是什么样子。你可以通过点击 **Shift + Command + A** 或者导航到**功能**选项卡并点击**切换外观**来预览你的黑暗模式:

![Testing Dark Mode Xcode Display](img/4c757a902fca949be8a090730d335f63.png)

### GPS 定位模拟器

您可以使用模拟器模拟 GPS 定位。默认情况下，模拟器有一个 GPS 点，但您可以添加自定义 GPS 位置:

![GPS Location Simulator Xcode Example](img/3712dbd647345f7417a93ec9e354ba97.png)

如果您使用的是 Xcode 10 或更低版本，请导航到**调试→位置→自定义位置**。输入经度和纬度，模拟器将使用这些自定义坐标作为设备的默认位置。

![Debug Custom Location Latitude Longitude Default Coordinates](img/f56ad1bff5255e4b9158a55a8c357be3.png)

## Xcode 方案

Xcode scheme 定义了要构建的目标集合、构建时要使用的配置以及要执行的测试集合。

您可以使用方案为您的应用程序创建不同的构件。这使您能够在不同的环境中测试应用程序。例如，您可以仅为测试、试运行、开发和生产构建创建一个构建。

假设我们必须创建三个不同的构建:一个用于测试或试运行的构建，一个开发构建，以及一个用于生产的构建。每个构建都有一个唯一的构建标识符和一个自定义名称。

为了了解这是如何工作的，让我们来创建方案:登台、生产、开发。

进入**产品→管理方案**:

![Product Manage Xcode Create New Scheme](img/38c2fd55df8607bb32983a036ec86412.png)

点击 **+** 创建新方案:

![Xcode Scheme Create New Scheme](img/33ff77a4cb99132cdb5d1419c68e2eeb.png)

对`Production`和`Staging`进行同样的操作。确保取消选中默认方案:

![Create New Production Staging Uncheck Default](img/6c65a660cfe09f63fd9dacd843cd30fb.png)

检查您刚刚创建的所有三个构建:

![Check Three Created Builds Production Staging Debugging](img/185d6f126d3ed85a6bbabce897127e86.png)

下一步是为每个方案设置特定的构建设置。**进入产品→方案→编辑方案**:

![Xcode Set Specific Scheme Build Settings](img/d2518db9e3a847ba1bcec6b5574f61fd.png)

在许多其他选项中，您可以选择应用程序运行时要使用的构建配置类型:

![Select Build Configuration Type App Running](img/9f4dc8b12b623f12937fbff0f20ac878.png)

## 使用`info.plist`文件

`info.plist`是一个 XML 文件，用于存储一系列应用属性，如`AppName`、版本、权限描述、设置等。

创建 Xcode 项目时，默认情况下会附带一个`info.plist`。一个工作空间中可以有多个`plist`文件。您可以为不同的环境创建多个`plist`文件，例如`Staging.plist`、`Production.plist`、`Development.plist`。一次只能使用一个文件。

假设我们创建了一个用于暂存的文件(`Staging.plist`)，右键单击项目目录并选择`New File`:

![Staging Plist XML File Store App Properties](img/ef732f1c9f66ecbef498f7c73c4ac4ed.png)

选择一个`Property Lis`文件并给它命名，比如`Staging`。

![Choose Property Lis Name Staging](img/d969ea8b4153790bb2da3c5a5349459a.png)

最好从`info.plist`中复制值，然后对其进行修改以避免任何语法问题。将文件作为源代码打开，然后复制并粘贴。

让我们更改`Staging.plist`文件中的应用程序`displayName`:

![Change App Display Name In Staging Plist File](img/bfa06062a458702541d237cbbe902389.png)

现在我们需要 Xcode 在构建时使用`Staging.plist`而不是`info.plist`。

首先，我们需要创建一个构建配置，并将登台文件与其相关联。点击项目图标，然后在**信息选项卡**中寻找**配置**。只需复制一个**调试**或**发布**配置，创建一个新配置，并将其命名为`Staging`:

![Create Build Staging Configuration](img/942c0fbf89f085b5ed76ca4845592c89.png)

创建分段构建配置后，下一步是告诉 Xcode 在使用特定构建时应该使用什么属性列表文件。在我们的例子中，我们希望它使用`Staging`，所以我们必须添加`Staging.plist`的路径。

转到构建设置，找到**打包标签**并点击`info.plist`文件。它将扩展并显示我们拥有的三种构建配置— `Debug`、`Release`和`Staging`。添加到`Staging`的路径为`AppName/Staging.plist`:

![Packaging Tab Display Three Configurations](img/c40c4e5116b16ccf3e62a0982c16c0d6.png)

现在，当运行分段构建配置时，它将自动使用`Staging.plist`。

![Staging Plist Automatic Running Stage Build Configuration](img/96c385e72b345089e1057ca6d28ae8ae.png)

您可以使用`info.plist`来添加和声明您的应用程序可以访问的 iOS 功能的权限。如果您的应用程序试图使用需要许可的 API，您需要将用法描述添加到`info.plist`中。例如，如果您的应用程序正在使用原生摄像头功能，您需要添加使用说明，如下所示。

点击 **+** 按钮，寻找`Privacy- Camera Usage Description`:

![Privacy Camera Usage Description](img/f97e6cebaaa9d8943faf693ebfc306c8.png)

然后，在右侧添加用法的值描述:

![Privacy Camera Usage Value Description](img/479959a066c3fc3ac056924ed83409e6.png)

## 摘要

当用 React Native 构建 iOS 应用程序时，知道如何使用 Xcode 是一个游戏改变者。有了 Xcode 的坚实基础，您可以更轻松、更高效地识别和修复构建问题。最重要的是，知道如何最大限度地利用 Xcode 的功能将改善开发人员的整体体验，并帮助您在 React Native 中使用 iOS 应用程序做更多您之前认为可能做不到的事情。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)