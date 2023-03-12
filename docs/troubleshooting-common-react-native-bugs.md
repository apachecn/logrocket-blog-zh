# 常见 React 原生错误故障排除

> 原文：<https://blog.logrocket.com/troubleshooting-common-react-native-bugs/>

***编者按:**本帖于 2022 年 3 月 7 日更新，以反映 React 原生 v0.67* 的相关信息

React Native 是一个很好的框架，可以在 Android 和 iOS 平台上实现你的应用。但是尽管它得到了 React 社区和脸书的广泛支持，它离 1.0 版本还很远。

您可能会遇到的一些错误可能会产生误导，或者很难发现。最近，React Native 团队要求开发人员帮助他们确定这些恼人的错误中哪一个导致了最大的挫败感。虽然他们确实解决了一些最严重的错误，但仍有少数错误可能会被忽略。

让我们来看看以下问题，并讨论在您开发下一个 React 本机应用程序时，如果这些问题突然出现，该如何解决:

## 导入错误

> 固定冲突:元素类型无效:应为字符串(对于内置组件)或类/函数(对于复合组件)，但得到的是:未定义。您可能忘记了从定义组件的文件中导出组件，或者您可能混淆了默认导入和命名导入。
> 
> 请检查“App”的呈现方法。
> 
> 此错误位于:
> 
> 在 RCTScrollContentView(at ScrollView . js:1038)中
> 在 RCTScrollView(at ScrollView . js:1178)中
> 在 ScrollView (at App.js:25)中
> 在 rctsafeareview(at safea review . js:55)中
> 在 safea review(at App . js:24)中
> 在 App (at renderApplication.js:40)中
> 在 RCTView(at App container . js:js

自上一版本以来，React 原生团队已对此错误进行了更详细的描述。通常，混淆的默认和命名导入是罪魁祸首。

这仍然很棘手，因为正如你所见，错误是由导入到应用程序中的一个组件引起的，但我们无法判断哪个组件导入不正确。该提示不识别出现错误的组件，甚至不识别出现错误的行。

为了避免在创建和导出组件时出现这种错误，请记住不要混合默认导入和命名导入。有什么区别？

假设您的组件具有以下内容:

```
export const componentName

```

您必须像这样导入它:

```
import { componentName } from './file'

```

但是如果使用默认导出会怎么样呢？

```
export default componentName

```

在这种情况下，您必须不带花括号地导入它，但是命名并不重要。你可以这样做:

```
import componentName from './file' //ok
import someOtherName from './file' //ok
import { componentName } from './file' //wrong!

```

## `Animated.View` **错误**

> 不变违例:[453，" RCTView "，1，
> {"width":250，" height":50，" backgroundColor":4289781990，" opacity":1}]不能用作本机方法参数
> 
> 此错误位于:
> 
> 在 RCTView (at file.js:27)
> 在 FadeInView (at file.js:42)
> 在 RCTView (at file.js:41)
> 在 _default (at App.js:29)
> 在 RCTScrollContentView(at scroll view . js:1038)
> 在 RCTScrollView(at scroll view . js:1178)
> 在 SafeAreaView (at App.js:24) 【T6

开发者在使用 React Native 中的[动画元素时经常会遇到这个错误。这是您会遇到的最棘手的错误之一，根据不同的用例，提示可能看起来略有不同。](https://blog.logrocket.com/how-to-make-tinder-like-card-animations-with-react-native/)

尽管消息令人困惑，但错误是由一个简单的错误引起的:当创建动画组件时，您必须创建一个元素(例如，视图)，如下所示:

```
<Animated.View>

```

如果使用普通视图，会弹出上面的错误。

识别这个错误可能很困难，理解它可以为您节省大量时间。React Native repository 中列出了一个需要修复的[问题](https://github.com/facebook/react-native/issues/24626),以及其他令人讨厌的错误，该团队预计将在即将发布的版本中对其进行改进。

## 自动链接错误

> error React Native CLI 对本机依赖项使用自动链接，但以下模块是手动链接的:
> 
> …
> 
> 升级到 React Native v0.60 或更高版本时经常会遇到此错误。接下来，您可以通过`react-native unlink <dependency>`取消此依赖关系的链接，它将自动包含在您的应用程序中。如果库与自动链接不兼容，忽略此消息并通知库维护人员。

有了 React Native v0.60+，设置 app 就容易多了，因为我们不再需要使用`react-native link`命令。但是，这可能会触发新的错误，尤其是如果您使用的是不支持自动链接功能的旧库。

如果您在 React Native v0.60+项目中使用了`react-native link`，请运行`react-native unlink`，然后尝试运行您的应用程序。如果你没有使用`react-native link`命令，但仍然收到错误，很可能你的一个依赖项不适合自动链接。如果是这种情况，可以尝试使用`[patch-package]([https://www.npmjs.com/package/patch-package](https://www.npmjs.com/package/patch-package))`自行修复。

请确保使用您的解决方案创建对库存储库的拉请求；您可能会帮助其他开发人员节省大量时间。

## 无法识别的字体系列错误

> 无法识别字体系列' Lato-Regular '
> 
> [rctfnt update font:with family:size:weight:style:variant:scale multiplier:]
> rctfnt . mm:347
> rcttxtattributes effective font][rcttxtattributes effective text attributes][rctbsetextshadow view attributedtxtwithbasetext attributes:][rcttxtshadow view attribute dtxtwithmeasured size:]

当我们试图在 React 本地项目中添加自定义字体时，会出现此错误。

为了避免这个错误，在项目文件夹的根级别创建一个`react-native.co``n``fig`文件，并添加下面的代码片段:

```
module.exports = {
  project: {
    ios: {},
    android: {},
  },
  assets: ['./assets/fonts/'],
};

```

要将字体链接到 iOS 和 Android，运行以下命令之一:
`npx react-native link`或`yarn react-native link`

现在，我们可以在 React 本地样式中使用自定义字体。

这里有一个例子:`fontFamily: 'custom-font'`

## 命令`PhaseScriptExecution`失败

> 命令 PhaseScriptExecution 失败，返回非零退出代码
> phasescript execution[CP-User]\ Generate \ Specs/Users/BM/Library/Developer/Xcode/derived data/RNLocalAuthentication-bhitgdgehuvfyyewuiqxedwixzjg/Build/intermediates . no index/Pods . Build/Debug-iphone simulator/FBReactNativeSpec . Build/Script-5f4c 70 ef 7d 90 a5 5 bdaeb 4279 f 232 a . sh(在项目的目标“FBReactNativeSpec”中
> 
> CD/Users/BM/Documents/Projects/RNLocalAuthentication/IOs/Pods
> export ACTION \ = build
> export AD*HOC*CODE*SIGNING*ALLOWED \ = YES
> export ALLOW*TARGET*PLATFORM*specification \ = NO
> export ALTERNATE*GROUP \ = staff
> export ALTERNATE*MODE \ = u+w，go-w，a+rX
> export ALTERNATE*OWNER \ = BM

npm 软件包出现了此错误，这些软件包在 M1 本地 MAC 上不受支持。为了在 M1 MAC 上工作，我们将它添加到 podfile 中，以便运行并编译到 x86 模式。

```
 post_install do |installer|
    react_native_post_install(installer)
    __apply_Xcode_12_5_M1_post_install_workaround(installer)
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
            # Needed for building for simulator on M1 Macs
            config.build_settings['ONLY_ACTIVE_ARCH'] = 'NO'
        end
    end
  end

```

上述代码将其从活动拱门中排除。接下来，按照“Android 和 iOS 的其他故障诊断”一节中列出的其他必要步骤进行操作。

## 文本外字符串错误

> 不变冲突:文本字符串必须在组件中呈现。
> 
> 此错误位于:
> 
> 在一个(at App.js:29)
> 在 RCTScrollContentView(at scroll view . js:1038)
> 在 RCTScrollView(at scroll view . js:1178)
> 在 ScrollView (at App.js:25)
> 在 rctsafearaveview(at safearaveview . js:55)
> 在 safearaveview(at App . js:24)
> 在 App (at renderApplication.js:40

这个错误消息相当简单，您可以看到它位于第 29 行的`app.js`文件中，但是仍然值得一提可能的原因。

如果您遇到这个错误，您可能忘记了用一个`Text`组件来包装您的字符串，或者您可能输入了一个错别字，导致您的组件无法识别。后者是最常见的原因——这里多了一个括号，那里多了一个逗号。即使我们知道去哪里找，这些错别字也很难被发现。

如果在未来的版本中，错误消息能够包含更多有用的信息，比如导致问题的特定字符串，那就太好了。

## 依赖性错误

> 错误:undefined 无法从`App.js`解析模块`@babel/runtime/helpers/interopRequireDefault`:在项目中找不到@ babel/runtime/helpers/interopRequireDefault。
> 
> 如果您确定该模块存在，请尝试以下步骤:
> 
> 1.  清除守望者监视:守望者监视-全部
> 2.  删除节点*模块:rm -rf 节点*模块并运行纱线安装
> 3.  重置 Metro 的缓存:纱线开始––重置缓存
> 4.  移除缓存:rm -rf /tmp/metro-*

如果像这样的错误似乎是凭空出现的，首先应该怀疑是 npm 或 Yarn 以及`node_modules`文件夹中的依赖项。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，您可以尝试重新安装整个依赖项目录。如果您的存储库中的一个主要依赖项发生了某种程度的变化，这可能会导致问题。在主项目目录中运行命令，在那里可以看到`node_modules`文件夹，删除并重新安装它们。

```
rm -rf node_modules && yarn

```

如果这不起作用，您可以尝试按照错误消息中概述的步骤操作。下面是一个稍加修改的命令，可以复制到您的终端上:

```
watchman watch-del-all && rm -rf /tmp/haste-map-react-native-packager-* && rm -rf /tmp/metro-bundler-cache-* && rm -rf node_modules/ && yarn cache clean && yarn

```

这将清除看守者监视，重置 metro bundler 缓存，移除 haste 缓存，重新安装整个`node_modules`目录，并清除纱线缓存。

## Android 和 iOS 的其他故障排除

如果仍然有错误，您可以尝试清理您的项目。这些步骤会因您的开发平台而异。

Android
尝试从主项目目录中键入以下命令来清除 Gradle 缓存:

```
cd android && ./gradlew clean

```

如果你正从 Xcode 打开你的项目，尝试清理它。点击上层菜单栏中的**产品**和**清洗**。

您可能还想尝试运行`ios`目录中的`pod deintegrate`来完全删除 pod，然后再次运行`pod install`。

最后，删除派生数据非常有用:

```
rm -rf ~/Library/Developer/Xcode/DerivedData/*

```

## **结论**

现在，您应该能够解决在开发 React 本机应用程序时可能遇到的七个最常见的错误。当您阅读本文时，其中一些错误正在被修复，随着新版本的发布，错误消息将变得更具描述性和信息性。但现在，我们需要利用现有资源。了解这些错误背后的上下文可以为您节省大量调试时间。毕竟，虽然这些错误中的大多数很难发现，但是如果您知道自己在寻找什么，它们通常很容易修复。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)