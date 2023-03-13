# 标准化 Android 和 iOS 应用程序中的表情符号显示

> 原文：<https://blog.logrocket.com/standardizing-emoji-displays-across-android-and-ios-apps/>

表情符号作为一种沟通媒介在各种设备上广泛使用，表达无法通过纯文本轻松传达的情感。

作为人类，我们倾向于口头的、面对面的对话，这让我们能够读懂面部表情和解读语气。这一点在书面交流中已经消失了一段时间。表情符号让发短信和写作变得有趣多了，不仅传达了隐藏的意思，还表达了赞同或不赞同。随着社交媒体和消息应用的兴起，表情符号已经成为文本交流的规范。

你知道表情符号在每个平台上都不一样吗？表情符号的外观取决于你使用的平台，即 Android、iOS、Windows 等。标准化表情符号，使其看起来一样，不管用户在什么设备上，这是一个很好的做法，可以确保整个应用程序的一致性。

在本文中，我们将探索在 iOS 和 Android 上标准化表情符号。要继续下去，你需要 [Android Studio](https://developer.android.com/studio) 和[Android](https://blog.logrocket.com/building-android-app-graphql/)的初级知识。我们开始吧！

*向前跳转:*

## 幕后表情符号

表情符号始于 2010 年，在被添加到计算系统文本编码的全球标准 [Unicode](https://home.unicode.org) 后，迅速成为一种官方的通用交流方式。负责维护 Unicode 的机构 Unicode Consortium 接受了来自谷歌和苹果的工程师团队的提议，对这些表情符号进行标准化，这意味着每个操作系统都支持所有表情符号的 Unicode。

然而，有一个警告；Unicode 并没有规定表情符号的外观。因此，像苹果和谷歌这样的个人供应商可以决定展示什么形状、颜色和设计。甚至应用程序开发人员也可以定义自己的一套独特的表情符号来映射到 Unicode。请随意查看表情符号 unicodes 的完整列表。

### Unicode 表情符号示例

让我们来看几个例子。下面是翻白眼的[脸](https://emojipedia.org/face-with-rolling-eyes/)表情符号。大多数供应商实现了向上看的眼睛和中性的嘴。然而，谷歌和脸书实施面临那种皱眉，使它成为一个悲伤的表情:

![Apple Rolling Eyes Logo](img/a508639411280d7cd331285355b3baea.png)

Apple

![Google Rolling Eyes Logo](img/df712aa9d9c9d3e7cde168843e529c01.png)

Google

![Twitter Rolling Eyes Logo](img/21de0de371de079c08686f311640650d.png)

Twitter

![Facebook Rolling Eyes Logo](img/4957585b56e483c6314a6fdfed63be45.png)

Facebook

[水枪](https://emojipedia.org/pistol/)表情符号是每个供应商或应用程序使用自己的颜色和主题的最明显的例子之一，因此表情符号因平台或应用程序而异:

![Apple Squirt Gun Logo](img/9db9a8480e5f38da748f2cff87a4dc7a.png)

Apple

![Twitter Squirt Gun Logo](img/5ade3febb85eec87e3cfee664104a00f.png)

Google

![Google Squirt Gun Logo](img/b6818441268f852b417390951c7337b7.png)

Twitter

![Whats App Squirt Gun Logo](img/a1d53a66a2fb230ccb37f7554328dfa2.png)

WhatsApp

表情百科是一个查看不同平台上不同表情图标的好地方。

## 标准化表情符号显示

作为一名应用程序开发人员，你应该标准化表情符号在 Android 和 iOS 设备上的外观，从而无论用户从什么平台访问应用程序，都保持应用程序的外观和感觉不变。在本文中，我们将一步步介绍如何在 Android 应用程序中显示苹果/iOS 风格的表情符号。

我们将使用[表情库，其中有四种不同的表情可供选择，包括](https://github.com/vanniktech/Emoji) [iOS](https://github.com/vanniktech/Emoji#ios-emojis) 、[谷歌](https://github.com/vanniktech/Emoji#google)、[脸书](https://github.com/vanniktech/Emoji#facebook)和 [Twitter](https://github.com/vanniktech/Emoji#twitter) 。这个库不仅维护得很好，而且使用起来也相当简单。

### 样品

我们将构建一个聊天屏幕来演示输入和输出，以及一个键盘来显示我们的 Android 应用程序中的 iOS 表情符号:

![Sample ios Emoji Logo](img/6c2e9af9bd8ef2036815d0eddda1f8db.png)

在您的应用程序级别`build.gradle`文件中包含以下 gradle 依赖关系:

```
implementation "com.vanniktech:emoji-ios:0.15.0"

```

将下面一行添加到您的`application`类中:

```
EmojiManager.install(IosEmojiProvider())

```

上面的代码将提供者安装在您的`application`类中。您可以决定安装哪个提供程序；出于本教程的目的，我们将使用 iOS 表情符号。

表情库提供了与大多数 Android 视图相对应的表情视图，因此您有以下内容:

*   `EmojiAutoCompleteTextView`
*   `EmojiButton`
*   `EmojiCheckbox`
*   `EmojiEditText`
*   `EmojiMultiAutoCompleteTextView`
*   `EmojiTextView`

为了方便，你也可以使用`EmojiLayoutFactory`，它在使用普通 Android 视图时提供自动表情符号支持，像`TextView`、`Checkbox`等。

为了显示消息列表，我们在聊天屏幕中使用了`EmojiTextView`。`EmojiTextView`提供了普通`TextView`的所有属性，无需任何额外的改动就能以同样的方式使用。下面是我们示例中的代码片段:

```
<com.vanniktech.emoji.EmojiTextView
   android:id="@+id/itemAdapterChatTextView"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"
   android:textIsSelectable="true"
   app:emojiSize="@dimen/emoji_size_default"
   tools:text="my text" />

```

对于用户输入，我们使用了`EmojiEditText`而不是默认的`EditText`，如下所示:

```
<com.vanniktech.emoji.EmojiEditText
   android:id="@+id/chatEditText"
   android:layout_width="0dp"
   android:layout_height="wrap_content"
   android:layout_weight="1"
   android:hint="Type a message ..."
   android:imeOptions="actionSend"
   android:importantForAutofill="no"
   android:inputType="textCapSentences|textMultiLine"
   android:maxLines="3"
   tools:ignore="HardcodedText,LabelFor" />

```

最后展示一个 iOS 风格的表情符号键盘，库给你提供了`EmojiPopup`。要打开它，请执行下面的代码:

```
val emojiPopup = EmojiPopup(
   rootView = binding.rootView,
   editText = binding.chatEditText,
   onEmojiPopupShownListener = { binding.chatEmoji.setImageResource(R.drawable.ic_keyboard) },
   onEmojiPopupDismissListener = { binding.chatEmoji.setImageResource(R.drawable.ic_emojis) },
   keyboardAnimationStyle = com.vanniktech.emoji.ios.R.style.emoji_fade_animation_style,
)

```

`rootView`是你布局 XML 文件的`rootView`，可以用来计算键盘的高度。`chatEditText`是您在布局 XML 文件中声明的`EmojiEditText`。

我们使用`onEmojiPopupShownListener`在表情符号弹出窗口显示后立即将左侧可绘制图标更改为键盘图标，而`onEmojiPopupDismissListener`在表情符号弹出窗口关闭后立即将左侧可绘制图标更改为表情符号图标，默认键盘变为可见。

最后，当用户在键盘和表情符号弹出窗口之间切换时，`keyboardAnimationStyle`提供了它们之间的淡入和淡出过渡。瞧，差不多就是这样！

## 结论

在这篇文章中，我们学习了如何在 Android 应用程序中显示 iOS 表情符号，了解了为什么表情符号在不同的平台上看起来不同。如果你想进一步探索这个问题，请点击这里查看表情库。

你可以在 [GitHub](https://github.com/BhavyaRattan/iOSEmoji) 上访问本文中使用的示例应用程序。感谢您的关注，祝您编码愉快！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)