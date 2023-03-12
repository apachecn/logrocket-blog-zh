# 探索 React Native 的新架构——log rocket 博客

> 原文：<https://blog.logrocket.com/exploring-react-natives-new-architecture/>

在本文中，我们将探索即将推出的 React 原生版本以及 React 原生架构的变化。

让我们直接开始吧。

## React 本地架构发生了什么变化？

Native 的一个关键架构变化是新的 native TurboModule 系统和 Fabric Renderer。

在接下来的章节中，我们将通过升级 Android Gradle 插件以兼容 React Native 的最新夜间发布版本，来研究这些关键的架构变化如何影响 React Native 应用程序的工作。我们将具体介绍:

## 为新的 React 原生架构准备 Android 应用

对于我们的 Android 应用程序，必须满足以下先决条件:

*   Gradle v 7.x 和安卓 Gradle 插件(AGP) v 7.x
*   新的 React Gradle 插件
*   从源代码构建反应-本机。
*   Node.js ≥ v 14

布丁的味道就在吃的过程中，所以用这个小妙语，让我们通过启动一个新的 React 本机应用程序来弄脏我们的手。

```
npx react-native init reactNativeNew
```

一旦完成了这些，是时候让我们的应用程序准备好接收 React Native 最新版本的夜间发布了。

### 升级我们的 Gradle

```
cd android && ./gradlew wrapper -gradle-version 7.3 -distribution-type=all
```

AGP 版本更新顶层`build.gradle`文件。

安装新的 Gradle 插件:

```
cd ..
yarn add react-native-gradle-plugin
code android/settings.gradle
```

编辑`settings.gradle`文件，在文件末尾包含以下几行:

```
includeBuild('../node_modules/react-native-gradle-plugin')

if (settings.hasProperty("newArchEnabled") && settings.newArchEnabled == "true") {
    include(":ReactAndroid")
    project(":ReactAndroid").projectDir = file('../node_modules/react-native/ReactAndroid')
}

```

将 Gradle 插件添加到我们的构建脚本中:

```
code android/build.gradle
```

现在，添加以下代码块:

```
buildscript {
    dependencies {
        classpath("com.android.tools.build:gradle:7.0.4")
        classpath("com.facebook.react:react-native-gradle-plugin")
        classpath("de.undercouch:gradle-download-task:4.1.2")
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

```

编辑模块级 Gradle 文件，如下所示:

```
code android/app/build.Gradle
```

现在，在顶层添加以下代码块:

```
apply plugin: "com.android.application"

import com.android.build.OutputFile

// Add those lines
apply plugin: "com.facebook.react"
// Add those lines as well
react {
    reactRoot = rootProject.file("../node_modules/react-native/")
    codegenDir = rootProject.file("../node_modules/react-native-codegen/")
}

```

在`/android/app/build.gradle`文件的依赖项部分，添加以下代码:

```
dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar"])

    //noinspection GradleDynamicVersion
    // replace this
    implementation "com.facebook.react:react-native:+"  // From node_modules

    // with this
     implementation project(":ReactAndroid")  // From node_modules

```

### 访问 React Native 的最新版本

为了访问最新的更改以对本机做出反应，目标应用程序应该使用特定的夜间版本。在将应用程序升级到特定的夜间版本之前，建议首先将应用程序升级到最新的开源版本。

```
yarn add react-native
```

一旦应用程序成功升级到最新的开源版本，我们就可以瞄准夜间发布。下面的命令对此有所帮助。

```
yarn add [email protected]
```

安装 react-native-codegen 的最新版本，这是一个在构建时(而不是在运行时)自动兼容 JavaScript 和本机代码的工具。

```
yarn add react-native-codegen
```

随着我们的 Android 应用程序更新到 React Native 的最新版本，是时候探索新的渲染器系统:Fabric 了。

## 探索 Fabric，React Native 的新渲染系统

Fabric 是 React Native 的新渲染系统。Fabric renderer 旨在提高 React Native 与主机平台的互操作性，主机平台负责将 React Native 嵌入 Android、iOS、macOS、Windows 等。

### 改进了 JavaScript 和本地线程之间的通信

在当前的架构中，当运行 React 本机应用程序时，JavaScript 代码被捆绑到一个名为 JS Bundle 的包中，本机代码保持独立。JavaScript 线程运行 JS 包，native/UI 线程运行本机模块并处理 UI 呈现。JS 和本地线程之间的通信是通过一个桥来实现的，桥将数据序列化为 JSON 后发送给本地线程。这个桥只能处理异步通信。

使用 Fabric，逻辑以 C++呈现，这提高了主机平台之间的互操作性。Fabric renderer 在 C++中实现，C++核心在平台之间共享，提供了更大的一致性，并使 React Native 更容易在新平台上采用。

此外，新的架构将 JavaScript 接口从引擎中分离出来，允许使用其他 JavaScript 引擎，如 [Hermes](https://hermesengine.dev/) 、 [V8](https://v8.dev/) 或 [Chakra](https://github.com/chakra-core/ChakraCore) 。

### 改进 React 本机和主机视图之间的互操作性

主机视图是主机平台(例如，Android、iOS)中视图的树形表示。

![Tree Representation Of Views In The Host Platform](img/0764f7dd990ae3ef4a2bb76044f23b20.png)

来源:reactnative.dev/

React Native 和主机视图之间改进的互操作性是由 C++核心实现的，它在不同的主机平台之间共享，并使 React Native 能够同步呈现 React 表面。

事情并不总是这样——在传统的 React 原生架构中，布局是异步的，这导致了在主机视图中嵌入 React 原生渲染视图时的[布局“跳转”问题](https://github.com/react-navigation/stack/issues/366)。

![Improved Interoperability Between React Native And Host Views](img/aa784699677dda34b163cc271f575b13.png)

来源: [React 导航 GitHub 第 6996 期](https://github.com/react-navigation/react-navigation/issues/6996)

### 数据提取的改进

由于与 [React 悬念](https://reactjs.org/docs/concurrent-mode-suspense.html)的集成，应用程序中的数据获取现在更加直观。React 中可用的其他新功能现在在 Fabric renderer 中启用，例如 React 18 中可用的新[并发功能](https://github.com/reactwg/react-18/discussions/4)。这包括`[startTransition](https://github.com/reactwg/react-18/discussions/41)`特性，它在昂贵的状态转换期间保持我们应用程序的 UI 响应。

使用新的渲染器，服务器端渲染也变得更加容易。

## 织物渲染管道的三个阶段

渲染管道分为三个阶段:

1.  渲染阶段
2.  提交阶段
3.  安装阶段

让我们更仔细地研究一下它们。

### 渲染阶段

在这个阶段，React 执行产品逻辑来创建由 React 元素组成的 React 元素树。React 元素是一个普通的 JavaScript 对象，它描述了应用程序屏幕上应该显示的内容。

React 元素树用于在 C++中渲染 React 阴影树。React 阴影树由 Fabric 渲染器创建，由 React 阴影节点组成，这些节点是表示要安装的 React 主机组件的对象，并且包含源自 JavaScript 的属性。

```
const App = () => {
  return (
    <View>
      <Text>App.js</Text>
    </View>
  );
};

```

在渲染阶段，当调用每个 React 元素时，渲染器会同步创建一个 React 阴影节点。请注意，React 影子节点的同步创建只针对 React 主机组件，而不针对 [React 复合组件](https://reactnative.dev/architecture/glossary#react-composite-components)。当被转换成 React 阴影块时，上面的代码会看到`<View>`被转换成一个`ViewShadowNode`对象。

新渲染器的一个优点是，React 元素节点之间的任何父子关系都将对应于 React 阴影节点之间的关系。上述过程显示了如何组装反应阴影树；一旦 React 阴影树完成，渲染器触发 React 元素树的提交。

下面是渲染阶段的可视化表示:

![Visual Representation Of The Render Phase](img/3676db657ed4535af5a9d07c30bf3ff4.png)

来源: [reactnative.dev](https://reactnative.dev)

### 提交阶段

跨平台布局引擎 [Yoga](https://yogalayout.com/) 在处理提交阶段发生的操作中非常重要，提交阶段包括两个操作:布局计算和树提升。

布局计算计算每个反应阴影节点的位置和大小。这是通过调用 Yoga 来计算每个 React 阴影节点的布局来实现的。

一旦计算确定了可用空间量，`Tree Promotion`操作会将新的 React 影像树提升为下一个要挂载的树。这个提升代表了 React 元素树的最新状态。

![The Commit Phase Diagram](img/211b51781bf1648a15446d83610dbcc6.png)

来源: [reactnative.dev](https://reactnative.dev)

### 安装阶段

在此阶段，React 阴影树(包含来自布局计算的数据)被转换为屏幕上具有渲染像素的主体视图树。Fabric 渲染器为每个 React 阴影节点创建一个相应的主机视图，并将其挂载到屏幕上。

![The Mount Phase Diagram](img/9b7277f3bccba568b05514bb1f93380e.png)

来源: [reactnative.dev](https://reactnative.dev/architecture/xplat-implementation)

## 涡轮模块

涡轮模块系统是对原生模块的增强。在他们当前的架构中，一个[表](https://github.com/facebook/react-native/blob/1151c096dab17e5d9a6ac05b61aacecd4305f3db/ReactCommon/cxxreact/ModuleRegistry.cpp#L70)保存模块注册表，当 JavaScript 代码调用特定的本机模块时，模块和方法的索引被传递给 Java/ObjC，后者调用特定的方法。

对于本机包的急切初始化，一个建议的解决方案是使用`[LazyReactPackage](https://github.com/facebook/react-native/blob/42146a7a4ad992a3597e07ead3aafdc36d58ac26/ReactAndroid/src/main/java/com/facebook/react/LazyReactPackage.java)`，但是这不是一个有效的方法，因为注释处理器`[ReactModuleSpecProcessor](https://github.com/facebook/react-native/blob/42146a7a4ad992a3597e07ead3aafdc36d58ac26/ReactAndroid/src/main/java/com/facebook/react/module/processing/ReactModuleSpecProcessor.java)`不与 Gradle 一起运行；因此，`LazyReactPackage`不能与开源版本一起工作。

有了新的实现，JavaScript 将公开一个名为`global.__turboModuleProxy`的顶级本机模块代理来访问本机模块。如果我们使用了`"SampleTurboModule"`的例子，应用程序代码将调用`require('NativeSampleTurboModule')`。在`NativeSampleTurboModule`中，调用`[TurboModuleRegistry.getEnforcing()](https://github.com/facebook/react-native/blob/7fbccdea22993b56988685174492583b16dc69db/Libraries/TurboModule/TurboModuleRegistry.js#L28)`函数，该函数保存对本地模块的引用，然后调用`global.__turboModuleProxy("SampleTurboModule")`。

这触发了 JSI 函数，并且调用了为 Java 和 ObjC 定义的 [getModule](https://github.com/facebook/react-native/blob/7fbccdea22993b56988685174492583b16dc69db/ReactAndroid/src/main/java/com/facebook/react/turbomodule/core/jni/TurboModuleManager.cpp#L56) 函数，并为特定的 TurboModule(即`GeoLocation`、`FileStorage, DeviceInformation`)返回 JSI 对象，该对象先前将在应用启动时被初始化。这允许 JavaScript 代码在需要时加载每个模块，而不是在应用程序打开之前初始化它们。

## 摘要

React Native 下一版本承诺改善启动时间和开发人员体验，并提高所有线程之间的互操作性，这是一个新的类似 web 的渲染系统(Fabric)。新架构让我兴奋的一个关键方面是能够轻松构建智能电视应用。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)