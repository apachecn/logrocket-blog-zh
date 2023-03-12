# 解决 React Native 中的常见错误

> 原文：<https://blog.logrocket.com/addressing-common-errors-react-native/>

React Native 是当今使用最多的 JavaScript 移动框架之一。React Native 使熟悉 JavaScript 和 React web 框架的开发人员能够使用类似的方法和原则开发移动应用程序。

作为 React 本地开发人员，您在开发应用程序时肯定会遇到一些错误。当编译器在运行代码时检测到错误时，它会终止进程并显示一条错误消息。

React Native 中的错误消息非常具有描述性。它们告诉开发人员发生了什么错误以及在哪里检测到了错误。此外，他们通常会提供清晰的说明，或者至少是如何解决问题的线索。

有些错误天生就比其他错误更容易调试。例如，由于使用错误的语法或访问未定义的变量或组件而导致的错误比由错误配置或不兼容的依赖关系导致的错误更容易调试。

不管错误的性质如何，您通常都需要帮助来解决它们。

在这篇文章中，我将介绍一些常见的 React 本地错误及其解决方案。其中一些错误会有不同的解决方案，这取决于最初导致错误的原因。对于这样的错误，一个接一个的去解决。

请使用下面的链接随意跳到以下任何部分:

## 无法安装应用程序

创建新的 React 本地项目后，当第一次尝试使用命令`react-native run-android`运行应用程序时，您可能会遇到以下错误:

```
BUILD FAILED in 13s

error Failed to install the app. Make sure you have an Android emulator running or a device connected. Run CLI with --verbose flag for more details.
Error: Command failed: ./gradlew app:installDebug -PreactNativeDevServerPort=8081

FAILURE: Build failed with an exception.
...

```

该错误消息指出构建过程没有成功，还指定了失败的特定命令。有时，您可以通过简单地使用新的命令提示符并重新启动虚拟设备来消除错误。

然而，错误经常是由于使用不兼容的 Gradle 版本来构建应用程序而导致的。

[Android studio 构建系统需要](https://blog.logrocket.com/exploring-react-natives-new-architecture/#preparing-android-app-new-react-native-architecture)正确版本的 Gradle 才能成功构建 Android 应用。在这种情况下，您需要将应用程序中使用的 Gradle 版本升级到与您的 Android studio 构建系统兼容的版本。

请遵循以下步骤:

1.  在文本编辑器中打开 React 本地应用程序，如 VS 代码
2.  在应用程序的根文件夹中，导航至`android > gradle > wrapper`
3.  编辑`gradle-wrapper.properties`文件
4.  用 Gradle 兼容版本的 URL 更新`distributionUrl`变量
5.  再次运行`react-native run-android`以使用新版本构建应用程序。

以下是第二步和第三步的完整路径:

```
{your-project-folder}\android\gradle\wrapper\gradle-wrapper.properties

```

为了在第四步中获得准确的 Gradle 版本，[进入 Gradle 的发行列表](https://services.gradle.org/distributions/)并检查最新的`-all.zip` Gradle 版本。然后，更新`distributionUrl`变量，如下所示:

```
distributionUrl=https\://services.gradle.org/distributions/gradle-{latest version}.zip

```

要了解更多关于 Gradle 和 Android studio 的信息，请阅读 [Android Gradle 插件和 Android Studio 兼容性](https://developer.android.com/studio/releases/gradle-plugin#updating-gradle)文章。

## 无法加载脚本

开发人员在尝试运行 React 本地应用程序时经常遇到的另一个错误如下所示:

```
Unable to load script. Make sure you're either running a metro server (run 'react-native start') or that your bundle 'index.android.bundle' is packaged correctly for release.

```

这个错误总是显示在连接的 Android 设备上。这个错误有几个原因。因此，也有不同的解决方案。

### 解决方案 1:正确打包软件包

您的应用程序的所有 JavaScript 都被捆绑到`index.android.bundle`文件中。如果包文件不可用或者没有正确打包，您将得到`Unable to load script`错误。按照下面的说明来修复它。

进入`{your-project-folder}/android/app/src/main/`文件夹，检查其中是否存在`assets`文件夹。如果“资产”文件夹不存在，请创建它。

接下来，直接从根文件夹中运行:

```
cd android
./gradlew clean

```

接下来，打开一个命令终端，确保它指向项目的根文件夹。如果您的项目只有一个文件(即`index.js`)，运行以下命令:

```
react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res

```

如果有两个文件(即`index.android.js`和`index.ios.js`，则改为运行以下程序:

```
react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res

```

请注意，两者都是单个命令。

生成包后，运行`react-native run android`。

要一次执行以上所有步骤，可以将它们放在 package.json 的`scripts`部分，如下所示:

```
"android-script": "react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res && react-native run-android"

```

以上命令为`npm run android-script`。

### 解决方案 2:使用`adb reverse`

如果在实现第一个解决方案后，您一直得到相同的错误，那么您需要考虑其他原因。另一个常见原因是端口没有暴露。如果您在物理设备上运行应用程序，就会出现这种情况。

`adb reverse`命令允许你用你电脑上的一个 TCP 端口暴露你 Android 设备上的一个 TCP 端口。若要尝试修复错误，请运行以下命令:

```
adb reverse tcp:8081 tcp:8081

```

这里，您通过计算机上的端口 8081 暴露了电话上的 TCP 端口 8081。

如果您的 Windows `PATH`变量中没有 Android 平台工具组件，那么可以在以下路径找到`adb`可执行文件:

```
C:\Users\{your-username}\AppData\Local\Android\sdk\platform-tools

```

### 解决方案 3:添加明文支持

如果前两个选项没有解决 React Native 无法加载脚本的问题，那么很可能是网络通信问题导致了错误。

具体来说，由于明文支持被禁用，很可能无法从开发服务器[访问该应用，从 Android 9.0 (API 级别 28)开始就是这种情况。](https://blog.logrocket.com/integrate-react-native-components-native-apps/#enabling-cleartext-debug-builds)

要解决这个问题，请修改`AndroidManifest.xml`文件并添加明文支持，如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />
    <application
        ...
        android:usesCleartextTraffic="true"
        ...>
        ...
    </application>
</manifest>

```

您可以在以下位置找到您的`AndroidManifest.xml`文件:

```
{your-project-folder}/android/app/src/main/AndroidManifest.xml

```

重新启动应用程序以应用更改。

## 无法识别 React 本机`run-android`命令

有时，当您尝试在 Android 上运行 React 本机应用程序时，您可能会在命令提示符下收到以下错误消息:

```
Command run-android unrecognized. Did you mean to run this inside a react-native project?

```

错误消息已经暗示了错误的最常见原因:您没有在 React 本地文件夹中运行命令。在这种情况下，解决方案就是确保在运行应用程序之前导航到应用程序的根文件夹。这就是我的意思:

```
// After initializing a project:
react-native init AwesomeProject

// Make sure to navigate to the project folder:
cd AwesomeProject

// Before you run the app:
react-native run-android

```

否则，如果错误不是由于使用了错误的文件夹，那么可能是您没有安装项目的内容。为此，请运行:

```
npm install or yarn install

```

您的 react-native 或 react-native-cli 的全局安装也可能是旧的或损坏的。在这种情况下，只需使用以下命令之一全局重新安装库:

npm:

```
npm install -g react-native && npm install -g @react-native-community/cli

```

纱线:

```
yarn global add react-native && yarn global add @react-native-community/cli

```

注意，我推荐使用 [npx(包含在 npm v5.0+)](https://www.npmjs.com/package/npx) 按需安装库。

这意味着无论何时运行`npx react-native init <your-project-name>`或任何其他 React Native CLI 命令，它都会在创建项目之前首先询问您是否允许安装`react-native`。这可以确保您始终使用最新版本！

最后，如果以上所有方法都不起作用，您可能需要使用以下命令升级 npm:

```
npm install [email protected] -g

```

确保使用新的终端运行这些命令。这是为了防止在您的`.bashrc`文件中使用过期的路径源。

## `react-native`未找到命令

`command not found: react-native`是 React Native 的另一个常见错误。当您试图运行任何`react-native`命令时，比如当您试图初始化一个 React 本地项目时，您会遇到这个错误，如下所示:

```
react-native init MyProject

```

这个`command not found`错误有两个潜在的原因:要么您没有在本地机器上安装 CLI，要么您安装了，但是没有正确配置。

通过对所有 npm 可执行文件使用 npx，可以避免这两种情况。例如，要创建一个新的 React 本机应用程序，请使用 npx 运行它，如下所示:

```
npx react-native init MyProject

```

这将在初始化项目之前安装最新版本的`react-native`包。

## 无法识别的命令:“link”

当尝试在 React 原生项目中链接自定义字体或图标等资源时，您可能会在命令终端上看到以下错误:

```
error Unrecognized command "link". info Run "react-native --help" to see a list of all available commands.

```

当您试图使用手动链接功能(即`react-native link`和`react-native link unlink`命令)时，会出现此错误，该功能在 React Native 0.69 中已被[移除，并替换为自动链接。](https://reactnative.dev/blog/2022/06/21/version-069#breaking-changes)

为了避免这个错误，你需要使用第三方的[资产链接库，比如](https://www.npmjs.com/package/react-native-asset) `[react-native-asset](https://www.npmjs.com/package/react-native-asset)`来自动链接资产。

首先，使用以下命令之一安装库:

```
npm install -g react-native-asset
# or if you're using yarn: 
yarn global add react-native-asset

```

然后在项目文件夹的根级别创建一个`react-native.config`文件，并添加下面的代码片段:

```
module.exports = {
    project: {
        ios: {},
        android: {}
    },
    "assets": [
      "./src/assets/font",
      "./src/assets/mp3",
      "./src/assets/icons"
  ]
};

```

运行以下命令之一，在您的代码库中启用自动链接和取消链接。

npm:

```
npx react-native-asset

```

纱线:

```
yarn react-native-asset

```

现在，您可以在代码中使用任何指定的资产。例如，您可以在样式表中使用自定义字体:

```
fontFamily: 'my-custom-font'

```

## 重复资源

当您试图使用来自`Android Studio`的`Generate Signed APK`来[生成发布 APK](https://blog.logrocket.com/how-to-deploy-a-react-native-app-to-the-google-play-store/#generating-the-apk-release-build) 时，您可能面临的另一个常见错误是重复资源错误:

```
[drawable-mdpi-v4/jumper] /Users/admin/Projects/testApp/android/app/src/main/res/drawable-mdpi/jumper.png [drawable-mdpi-v4/jumper] /Users/admin/Projects/testApp/android/app/build/generated/res/react/release/drawable-mdpi-v4/jumper.png: Error: Duplicate resources
:app:mergeReleaseResources FAILED

FAILURE: Build failed with an exception.

...

```

构建失败是因为在 Android 文件夹内的 Android 项目中发现了重复的资源。根据不同的原因，有不同的解决方案。

### 解决方案 1:从终端清理 drawable 文件夹

通常你可以通过使用 Gradle 从终端清除`drawable`文件夹来解决这个错误。为此，将`cd`放入`android`文件夹，然后在尝试再次运行应用程序之前运行`./gradlew clean`:

```
react-native run android
```

如果失败，尝试下一个解决方案。

### 解决方案 2:添加一些助手代码

大多数时候，简单地清理`drawable`文件夹并不能解决问题。如果是这种情况，那么您需要在`react.gradle`文件中做一点小小的修改，以防止重复的资源冲突。

在`node_modules/react-native/react.gradle`中找到的`react.gradle`文件中添加以下助手代码。代码应该放在`doFirst`块之后:

```
doLast {
    def moveFunc = { resSuffix ->
        File originalDir = file("$buildDir/generated/res/react/release/drawable-${resSuffix}");
        if (originalDir.exists()) {
            File destDir = file("$buildDir/../src/main/res/drawable-${resSuffix}");
            ant.move(file: originalDir, tofile: destDir);
        }
    }
    moveFunc.curry("ldpi").call()
    moveFunc.curry("mdpi").call()
    moveFunc.curry("hdpi").call()
    moveFunc.curry("xhdpi").call()
    moveFunc.curry("xxhdpi").call()
    moveFunc.curry("xxxhdpi").call()
}

```

这个错误在 2018 年由 [GitHub 用户 echaso 解决，他提供了](https://github.com/facebook/react-native/issues/22234#issuecomment-437657528)上面的代码。

## 结论

在本文中，我们研究了 React Native 中的六个常见错误，以及如何调试每个错误。如果您遇到的错误不在这里，请不要犹豫[查看这篇文章，寻找可能的解决方案](https://blog.logrocket.com/troubleshooting-common-react-native-bugs/)。

不管怎样，谢谢你的阅读，下次再见！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)