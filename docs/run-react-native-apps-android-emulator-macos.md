# 在 macOS 的 Android 模拟器上运行 React 本地应用程序

> 原文：<https://blog.logrocket.com/run-react-native-apps-android-emulator-macos/>

如果你是 React 原生开发者，迟早你会需要在 Mac 上运行 Android 模拟器的能力。无需在物理设备上安装应用程序，就能在本地计算机上测试应用程序，这大大加快了开发周期。

然而，狂热地在谷歌上搜索“macOS android 模拟器”后，你可能会发现没有多少可用的。

你可能知道，Android Studio 附带了一个很好的管理工具来管理你的仿真设备。可惜*，*作为 React 原生开发者，你用的不是 Android Studio。

更糟糕的是，Android Studio 提供了让底层仿真器工作的所有“正确”部分，就像正确版本的 Java 一样。在没有正确配置这些东西的情况下，试图让模拟器工作是行不通的，并且当它不工作时，您会得到神秘的错误消息。

在没有 Android Studio 的 macOS 上配置 Android 模拟器并不容易或简单。甚至官方文档都告诉你安装 Android Studio 就完事了。

但是，Android Studio 是一个完全成熟的 IDE，会占用你电脑上的大量空间。此外，它有很多你作为 React 原生开发者永远不会使用的功能，而且安装 Android Studio 只是为了使用模拟器，这需要很大的努力。

不要误解我的意思，这比我们在本文中尝试手动配置仿真器更简单直接。

但是，您应该在不单独安装 Android Studio 的情况下让 Android 模拟器工作，在没有 Android Studio 的过度破坏的情况下为您提供所需的确切功能。

如果您看到了这篇文章，那么您可能已经尝试了一段时间来使用它。

因此，让我们讨论一下在没有 Android Studio 的情况下运行模拟器时会遇到的一些问题，我们将如何克服这些问题，以及如何在 Android 模拟器上创建 React 应用程序。

## 在没有 Android Studio 的情况下运行模拟器时会出现预期问题

### Java 版本管理

Android SDK 管理器依赖于 Java 8。这可能看起来很老，但它实际上是 Java 的长期支持(LTS)版本，并且是 Java 的 [un](https://www.oracle.com/java/technologies/java-se-support-roadmap.html) [直到 2030](https://www.oracle.com/java/technologies/java-se-support-roadmap.html) 的[支持版本。](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)

当然，你的电脑上可能有其他版本的 Java。

如果你在电脑上安装了多个版本的 Java，破坏了你电脑上已经安装的依赖于某个版本的 Java 的应用程序，那么你很快就会失败。

为了减轻这种情况，我们将安装一个 Java 版本管理器，让我们在 Java 版本之间切换。

### 没有 GUI 可访问的安装程序

我们将面临的另一个问题是缺乏一个 GUI 可访问的工具来帮助我们安装 Android 模拟器和相关的位。这意味着我们将在 Mac 终端上完成大部分安装步骤。

### 文件夹结构

最后，模拟器在启动之前依赖于特定的文件夹设置，如果文件夹没有以特定的方式设置，它就不会启动。

幸运的是，我已经做了研究，所以你不必做了，我已经设法把它设置好了，我们将在这篇文章中回顾如何把它分类。

## 设置 Java 版本管理器

首先安装`brew`。在没有它的情况下，在 Mac 上安装应用程序在技术上是可行的，但它让这个过程变得如此简单，很难理解你为什么不使用它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

你可以[在这里](https://brew.sh/)得到 t [他包经理](https://brew.sh/) [或者你可以在你的终端上运行以下命令在你的计算机上设置它:](https://brew.sh/)

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

```

安装完成后，就该安装`jenv`了。`jenv`让我们维护已经安装在 Mac 上的 Java 版本。为此，运行`brew install jenv`。

在命令完成后，我们必须告诉我们的系统去哪里找，这样它就可以使用`jenv`，所以我们需要将它添加到我们的路径中。为此，我们必须在终端中运行以下命令:

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(jenv init -)"' >> ~/.zshrc

```

这将把`jenv`可执行文件添加到我们的路径中，并在我们启动终端时运行初始化脚本。我们可以重新打开终端窗口，或者在终端运行`source ~/.zshrc`来更新会话的`PATH`变量。

现在，该跑`jenv`了。在终端输入`jenv doctor`并观察输出。

![Running jenv doctor And Receiving Error Messages In Red](img/f0fcab0a907ab2ebf8cdd44573133722.png)

正如我们所看到的，它还没有工作，但这是意料之中的。我们还没有设置我们的 Java 安装，所以让我们现在就开始吧。

### 设置 Java 安装

我们希望安装 JDK8，因为这是与我们的 Android SDK 管理器一起工作的版本。在终端中键入以下内容:

```
brew install AdoptOpenJDK/openjdk/adoptopenjdk8

```

安装完成后，我们想看看我们的 Mac 可以访问哪些 JDK 环境。在命令行中键入以下内容以查看:

```
/usr/libexec/java_home -V

```

输出应该如下所示:

![Accessing Previously Downloaded Java Versions On Current Mac Computer](img/83a91bc74c46a0b60f61ab91a452f6a9.png)

这个列表显示了我们之前在 Mac 上安装的 Java 版本(如果适用的话)，以及我们刚刚安装的新的 OpenJDK8。现在，我们必须将每个 Java JDK 添加到`jenv`中，这样它就可以为我们管理合适的 Java 版本。

我们可以通过键入`jenv add ~path to JDK~`并使用我们从上面的命令中获取的路径来实现。在这篇文章中，它看起来像下面这样，但是你的会有所不同:

```
jenv add /Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home
jenv add /Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home

```

之后，我们希望将 Java 8 设置为系统范围的 Java 版本，这样我们就可以继续在我们的机器上配置 Android 模拟器。在撰写本文时，这意味着运行以下命令:

```
jenv global openjdk64-1.8.0.292

```

注意末尾的数字可能会改变，因为有更新的 LTS 版本的 Java 8 可用。

## 配置 Android SDK

随着 Java 环境的建立，是时候配置 Android SDK 了。关闭并重新打开终端窗口，以确保我们的环境变量已经设置好。然后，运行以下命令来验证我们环境的健全性:

```
jenv doctor

```

这将产生以下输出:

![Running jenv doctor To Validate The Sanity Of The Environment](img/39b1c5577e64676d5c6d457774467c70.png)

我们还必须通过键入`java -version`来检查我们的活动 Java 版本，这将给出以下输出:

![Running java -version To Check The](img/1e1ef9764c9b0f9b83ffa77a8c683790.png)

## 安装 Android SDK

现在我们有了正确的 Java 版本，是时候安装 Android SDK 了。

这就是很多过时的指南告诉你安装`android-sdk` ****brew 包的地方。不要这样做，因为**** 它在上游已被否决，可能随时停止工作。

相反，我们希望安装受支持的最新软件包`android-commandlinetools`。我们可以通过在终端中键入以下内容并按 enter 键来实现这一点:

```
brew install --cask android-commandlinetools

```

这将把`sdkmanager`命令添加到我们的`PATH`中，我们可以通过键入`sdkmanager emulator`并按回车键来安装核心仿真器。

你会收到满满一页法律术语，所以一旦你和你的律师看完这些(开玩笑)，点击`y`接受相应的许可。

![Android SDK Legalese To Accept Before Proceeding](img/94a5c6e2550d39cb47cf32bfd4e18e7c.png)

现在我们需要实际的模拟器图像本身。

### 选择模拟器图像

同样，我们会得到一整页的许可协议；点击`y`继续。这取决于你的 Mac 的架构来决定哪个模拟器映像是最好的，也就是说，在`x86_64`映像和`arm64-v8a`映像之间进行选择。

对于 M1 苹果电脑，请选择以下选项:

```
sdkmanager "system-images;android-30;google_apis_playstore;arm64-v8a" "platform-tools" "platforms;android-30"

```

对于英特尔 MAC 电脑，请选择以下选项:

```
sdkmanager "system-images;android-30;google_apis_playstore;x86_64" "platform-tools" "platforms;android-30"

```

最后，我们可以通过在打开的终端窗口中键入以下命令，基于我们刚刚下载的映像创建一个 Android 模拟器。同样，在这种情况下，使用 M1 Mac，我们使用`arm64-v8a`图像，但在英特尔 Mac 上，使用`x86_64`图像会有更好的结果。

## 创建 Android 模拟器

我们需要确定模拟器安装在哪里。我们可以通过在控制台中键入以下命令来做到这一点:

```
sdkmanager emulator --verbose

```

这将在终端中产生以下输出

```
Info: Parsing legacy package: /usr/local/share/android-commandlinetools/cmdline-tools/homebrew
Info: Parsing /usr/local/share/android-commandlinetools/emulator/package.xml
Info: Parsing /usr/local/share/android-commandlinetools/patcher/v4/package.xml
Info: Parsing /usr/local/share/android-commandlinetools/platform-tools/package.xml
Info: Parsing /usr/local/share/android-commandlinetools/platforms/android-30/package.xml
Info: Parsing /usr/local/share/android-commandlinetools/system-images/android-30/google_apis_playstore/x86_64/package.xml
Info: Parsing /usr/local/share/android-commandlinetools/tools/package.xml
[=======================================] 100% Computing updates...             

```

从第二行，我们可以看到模拟器安装在`/usr/local/share/android-commandlinetools/emulator/`。记住这条路径，因为我们将在本指南的后面使用它。

现在，我们可以通过在终端中键入以下命令来创建我们的模拟器了:

```
# Set up the 'android_emulator', with device type 'pixel' 
avdmanager create avd -n android_emulator -d pixel -k "system-images;android-30;google_apis_playstore;arm64-v8a" #Intel macs should get the x86_64 image instead!

# Enable the hardware keyboard for the emulators, so typing into the emulator via your keyboard works as expected
for f in ~/.android/avd/*.avd/config.ini; do echo 'hw.keyboard=yes' >> "$f"; done

# Add the 'emulator' binary to the system path, so we can call it from anywhere
# This is the path that we observed in the step prior; it may be different for your system!
echo 'export PATH=$PATH:/usr/local/share/android-commandlinetools/emulator/' >> ~/.zshrc

# Reload the environment variables
source ~/.zshrc

```

你的`/opt/homebrew/share/android-commandlinetools/` [包含](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value) `[emulator](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value)` [、](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value) `[platforms](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value)` [、](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value) `[platform-tools](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value)` [、](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value) `[system-images](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value)` [文件夹](https://stackoverflow.com/questions/39645178/panic-broken-avd-system-path-check-your-android-sdk-root-value)非常重要。如果没有它们，`emulator`二进制文件将无法启动，并显示 Android SDK 配置已损坏。

完成所有设置后，我们应该可以运行以下命令，Android 模拟器应该会启动:

```
emulator @android_emulator

```

![Final Android Emulator Homepage](img/5f95879e9104bda79e5748bbb8e90159.png)

现在是时候将这个命令添加到 shell 脚本中，并将其放在您的桌面上或其他可以访问的地方。这样，每当你需要一个模拟器时，只需打开脚本就可以了。

## 安装 React 本机 CLI 并设置断点

根据文档，您实际上不必安装 React Native CLI。相反，如果安装了 Node，您可以使用系统上的`npx`，并运行以下命令:

```
npx react-native init AwesomeProject

```

这将在您的计算机上配置 React Native 的正确版本，并让您开始使用一个示例项目。从这里，您可以在终端启动模拟器(如果您遵循了本指南，这意味着在您的终端中键入`emulator @android_emulator`)并在那里部署我们的示例应用程序。

随着模拟器的运行，我们[可以在我们的设备](https://blog.logrocket.com/how-to-build-ios-apps-using-react-native/)上运行 React Native 应用程序。这也是在我们的代码中设置断点的好时机，因此我们可以看到变量值被设置为什么，并参与其他故障排除行为。

这可能会因您使用的 IDE 而异，但通常情况下，您应该能够单击行号的左侧，并为您的应用程序设置适当的断点，如下所示:

![Setting Breakpoints In The Code On The Left-Hand Side, Shown With Red Dot](img/0b68317199401977db9b41b01d5c4e13.png)

有了仿真器设置和可通过断点调试的 [React 原生应用，您就可以开始了！](https://blog.logrocket.com/debugging-your-app-with-react-native-debugger/)

## 结论

在本文中，我们配置了 Android 模拟器所需的底层组件，使其像 JDK 和 Android SDK 一样工作。我们还使用了`jvm`来配置我们的 Java 环境，因此我们可以在 Android SDK 中使用正确的 Java 版本，并且我们创建了一个快捷方式来启动我们的仿真器。

这样做，我们避免了在 Mac 上安装 Android Studio。这是一件好事，因为作为一名 React 原生开发者，你永远不会真正使用 Android Studio，所以你可以利用这些磁盘空间来做你真正需要的事情。

剩下的就是享受你新创建的 Android 模拟器了！📱👨‍💻

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)