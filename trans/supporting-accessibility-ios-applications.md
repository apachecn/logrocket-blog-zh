# 支持 iOS 应用程序的可访问性

> 原文：<https://blog.logrocket.com/supporting-accessibility-ios-applications/>

## 介绍

为了开发最好的产品，我们应该为每个人创造良好的用户体验。凡事都要考虑残疾人。

当涉及到软件产品时，它们需要是可访问的。残疾不应该成为使用它们的障碍。产品应该对每个人都有用。通过使你的应用程序更易访问，你可以接触到更多的受众，并使其更易于使用。

操作系统确实为用户提供了辅助功能，iOS 设备的移动开发者应该考虑从这些辅助功能中获益。今天，我将重点讨论支持可访问性的 iOS 应用程序开发。

!["I design experiences that make people's lives simple."](img/7ae96937ad7ed42adcab660349674877.png)

Photo by [Ben Kolde](https://unsplash.com/@benkolde) on Unsplash

在 iOS 设备中，当你打开**设置**时，你会看到一个名为**辅助功能**的标题。正如我之前提到的，可访问性是一个通用的概念，因此有许多选项可以帮助人们在最佳用例中使用应用程序。

![iOS Settings > Accessibility Menu](img/0f603c57589e46e95092a4fcbe0edca1.png)

为了支持 iOS 应用程序的可访问性，我们可以做的是研究 iOS 的功能和特性，以及如何在我们的应用程序中使用这些特性。今天我将提供几个常见的例子给你一个思路。

### 可访问性示例:对比

正如我之前提到的，设计师有一些责任。在我们开始技术内容之前，我想给出一个支持可访问性的基本技巧。

如果你自己开发一个应用程序，关注颜色对比。为了让你的应用程序易于阅读和查看，背景和文本颜色应该相互对比。这对每个人都有好处，尤其是对视力低下和阅读障碍的人。

让我们看看下面的例子。左边的比右边的对比度大。标签和按钮文本可读性更好，对吗？

![Panda High Contract vs. Low Contrast Colors](img/3ed98814e40aac6d94ac6d6b629cab10.png)

这是一个在创建应用程序时要记住的与视觉设计相关的可访问性的例子。

## iOS 上的辅助功能

现在，我们可以回顾一下 iOS 的辅助功能。我创建了一个示例 iOS 应用程序，我们将逐步介绍它。

这个应用程序由两个屏幕组成。第一个屏幕有一个标签、图像和一个用于导航第二个屏幕的按钮。第二个包括标签、一个由循环中的四个图像组成的动画和一个滑块。让我们来看看这个应用程序的辅助功能。

![iOS Accessibility Demo App](img/16ea04de7fcb5ff18110c87436efd25a.png)

### 画外音

我会经常提到画外音，所以我需要先说说它是什么。VoiceOver 是 iOS 和 iPadOS 设备上的屏幕阅读器。它读取设备的用户界面，以帮助有视觉障碍的人，它也用于晕车的人和在行驶的车辆中。

VoiceOver 尽可能地阅读屏幕，但开发人员也应该确保它连贯地阅读。例如，我们在屏幕上显示文本，并使用一个星形图标按钮将文本添加到收藏夹。很可能，它会将按钮读为“星，按钮”，开发者可以改变它的读取方式，并给出一个类似“添加到收藏夹，按钮”的标签最好通过代码来检查并赋予 VoiceOver 一些属性，以便为用户提供更多信息。

通过牢记 VoiceOver 进行编码，您可以决定:

*   VoiceOver 可以读取或不读取哪个 UI 元素
*   当它读取该元素时，文本将会是什么
*   读取该元素时的值是什么(例如，按钮是否被选中)
*   该元素有什么提示来通知用户 moreisAccessibilityElement:Bool

这显示了 UI 元素是否可访问的值。如果使用 UIKit 元素，缺省值是`true`。如果创建自定义 UI 元素，需要将该属性设置为`true`。

```
accessibilityLabel: String?

```

这是一个字符串，辅助应用程序(在本例中为 VoiceOver)读取该 UI 元素。默认情况下，VoiceOver 会朗读`UILabel`或`UIButton`的文本。然而，你仍然可以给`accessibilityLabel`提供信息。

```
accessibilityValue: String?

```

accessibility 元素的当前值由字符串表示。例如，它可以是来自`UILabel`的文本值，来自`UISlider`的百分比，等等。

```
accessibilityHint: String?

```

在某些情况下，您可能需要使用它，但并不总是如此。如果您想在`accessibilityLabel`中给出太多的文本来阅读，您可以将其中一些移动到`accessibilityHint`。`accessibilityHint`给出了如果他们按下那个按钮或者使用一些 UI 元素会发生什么的线索。

💡请记住，通常情况下，UIKit 元素有默认值来支持可访问性。但是，如果您创建一个定制的`UIView`子类，您需要创建一个`UIAccessibilityElement`的实例。这样，你就能够使用`UIAccessibility`属性。

首先，我不需要编写任何与 VoiceOver 相关的代码，就可以用 VoiceOver 运行我的项目。这是演示:

 [https://www.youtube.com/embed/k149o79-LKU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/k149o79-LKU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

[画外音:在](https://blog.logrocket.com/wp-content/uploads/2021/12/ios-accessibility-voice-over-before.mov)之前

我发现它不把图像看作一个元素，也不读取与它们相关的任何东西。除此之外，我还想定制一些值，因为 VoiceOver 正在读取`UILabel`的默认文本。

⁕:我在我的`UILabel`上添加了一个可访问标签，它显示欢迎文本。但是，VoiceOver 把它读成了“示例辅助功能标签”，因为我定制了它。

```
let pandaLabel = UILabel()
pandaLabel.text = "Welcome"
pandaLabel.accessibilityLabel = "Example Accessibility Label"

```

⁕ `pandaImageView`是我称之为我的图像视图，我添加了`pandaImageView.isAccessibilityElement =` `true`来通知 VoiceOver 它是可访问的。

⁕:我给我的图像视图添加了一个`accessibilityLabel`,并赋予了`pandaImageView.accessibilityLabel = "Panda"`

⁕ I 还在底部的下一个按钮上添加了`accessibilityHint`,通知用户这个按钮被按下时会执行什么操作。

```
let nextButton = UIButton()
nextButton.setTitle("Next", for: .normal)
nextButton.accessibilityHint = "Navigates to the next screen"

```

那么，让我们看看它是如何改变的:

 [https://www.youtube.com/embed/_HE43fJuQ98?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/_HE43fJuQ98?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

[画外音:在](https://blog.logrocket.com/wp-content/uploads/2021/12/ios-accessibility-voice-over-after.mov)之后

### 文本大小

在开发时，我们一般会给出符合设计的字体大小值。然而，它不允许用户根据他们的视觉能力和偏好来改变字体大小。

对于这个例子，我首先创建了一个屏幕，包括硬编码的字体大小。

```
let pandaLabel = UILabel()
pandaLabel.font = .boldSystemFont(ofSize: 24)

```

正如我之前提到的，在这种情况下，字体大小不会根据可访问性设置而改变。

![](img/1f4e36a78365c1d107507fec15551986.png)

我们在这里可以做的是给标签一个动态的文本大小来满足用户的意图。

注意，有时用户的选择会导致无法访问 app UI。例如，更大的文本尺寸可以覆盖整个屏幕，并取代一个按钮。

让我们看看如何给标签一个动态的文本大小。

```
let pandaLabel = UILabel()
pandaLabel.font = UIFont.preferredFont(forTextStyle: .body)
pandaLabel.adjustsFontForContentSizeCategory = true
pandaLabel.numberOfLines = 3

```

![iOS Accessibility Change in Text Size](img/7283e070b5c57cdfaf64783007bd9e0f.png)

### 减少运动

减少运动用于对运动效果和屏幕移动敏感的人。在某些应用程序中，减少运动会禁用动画。

如果我们有任何动画，打开**减少运动**功能可能不会禁用我们应用程序中的动画。如果通过使用`isReduceMotionEnabled`属性启用了**减少运动**功能，我们可以在这里移除动画。

使用这个代码片段，我们可以从这个特性中获益。

```
if (UIAccessibility.isReduceMotionEnabled) {
// add what your app should do with reduced motion version

} else {
// add what your app should do with motions are allowed

}

```

在我的示例项目中，我创建了一个`UIImageView`，一个包含四个图像的数组，并创建了一个循环来在图像视图中显示这些图像。一开始，当我启用`ReduceMotion`开关时，它并没有影响我的应用程序。因此，我决定显示一个启用的图像，如下所示。

```
if (UIAccessibility.isReduceMotionEnabled) {
// Settings > Accessibility > Motion > Reduce Motion switch is turned on
    pandaImageView.image = UIImage(named: "panda2")
} else {
// Settings > Accessibility > Motion > Reduce Motion switch is turned off
    let animationImages = [UIImage(named: "panda.png")!, UIImage(named: "panda2.png")!, UIImage(named: "panda3.png")!, UIImage(named: "panda4.png")!]
    pandaImageView.animationImages = animationImages
    pandaImageView.animationDuration = 1.5
    pandaImageView.startAnimating()
}

```

最后，变成了这样:

![iOS Accessibility Reduce Motion](img/a2b790a11f82f272a3d0375677c218f2.png)

## 结论

我们生活在一个数字时代，为什么不让每个人都可以使用它呢？如果我们想为人类创造一个更美好的世界，我们可以从这里开始。

每次重大的 iOS 版本更新都会带来新的辅助功能。我们可以从了解这些特性的最新功能以及如何在我们的应用程序中支持它们开始。

今天，我讲述了一些在 iOS 应用程序中支持可访问性的技巧。这是一个宽泛的概念，所以我尽可能多地提到了。你可以点击进入我的[演示应用。](https://github.com/iremkaraoglu/iOS-Accessibility-Example-UIKit)

如果您想提供任何反馈、问题或分享什么，请随时通过[【电子邮件保护】](/cdn-cgi/l/email-protection)联系我。要了解更多信息，我建议您查看参考资料部分的资源。

下一篇文章再见！🥳

## 参考

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).