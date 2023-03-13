# 快速响应的移动 UX 应用程序设计者指南

> 原文：<https://blog.logrocket.com/ux-design/app-designers-guide-responsive-mobile-ux/>

移动设备已经接管了我们的生活，这已经不是什么秘密了。

十多年来，[通过移动互联网使用的网络流量](https://www.broadbandsearch.net/blog/mobile-desktop-internet-usage-statistics)在全球范围内从仅仅 6%飙升至令人印象深刻的 56%。

![Global Mobile Traffic Percentage](img/6e4d3c1352558abc36da8d372988b96e.png)

Source: [Broadband Search](https://www.broadbandsearch.net/blog/mobile-desktop-internet-usage-statistics)

而且不仅仅是浏览；[移动应用下载量](https://www.statista.com/statistics/271644/worldwide-free-and-paid-mobile-app-store-downloads/)也出现了大幅增长，从 2016 年的 1400 亿增长到 2022 年的 2550 亿，增幅超过 80%。

2021 年，[移动用户平均每天花 4 个小时在他们的设备上](https://www.oberlo.ca/statistics/mobile-commerce-sales)，无论他们是浏览社交媒体、在线购物还是移动银行。

这些趋势表明了移动设备在我们日常生活中日益增长的重要性，以及企业对移动友好用户体验的日益增长的需求。

在本文中，我们将深入移动 UX 设计的世界，探索一些最佳实践、常见挑战和应用程序的正确范例。

## 目录

## 响应式设计

移动设备，如智能手机和平板电脑，有各种各样的屏幕尺寸，可以在纵向或横向模式下调整方向。根据其用途，移动应用程序可能会针对一个方向进行优化，而不是另一个方向。

例如，一些应用程序，如抖音，是为纵向模式设计的，因为视频是垂直观看的。但其他应用程序，如手机游戏，是为风景模式设计的，因为它允许双手控制。

响应式设计可以适应不同的屏幕尺寸和方向，因此无论您是在横向模式下使用 iPad 还是在纵向模式下使用 iPhone，应用程序的布局和内容都会进行调整，以提供相同水平的用户体验。采用响应式设计可确保应用可在各种设备上访问和使用。

接近[响应式设计](https://blog.logrocket.com/ux-design/wireframing-responsive-design-good-ux/)的一个方法是考虑如何处理导航。在较小的屏幕上，您可能希望将一些菜单项隐藏在汉堡图标后面以节省空间。这样，用户仍然可以访问他们需要的一切，但不会占用屏幕太多空间。

另一种方法是使用灵活的列布局，可以根据屏幕大小调整显示的列数。通过这种方式，您的应用程序可以充分利用可用空间，并在不同设备之间提供无缝体验。这一切都是关于如何设计你的应用程序，使其尽可能的用户友好。

## 移动与桌面设计考虑事项

现在，让我们来谈谈移动平台和桌面平台之间的差异，这样您就可以将它们纳入用户体验的考虑范围。

### 屏幕空间

最明显的区别是可用的屏幕空间。手机屏幕小得多，这使得它很难容纳所有的重要信息而不显得杂乱。当东西杂乱时，会导致糟糕的用户体验。

你不想把所有东西都堆在一起，因为那会让人们很难使用。您可能会错过您试图轻按的按钮，或者很难找到您需要的信息。

### 用户交互

另一个区别是我们与设备的交互方式。在手机上，我们大多用手指做任何事情，比如滑动和点击。它并不总是像使用鼠标或键盘那样精确。对于有精细运动技能或视觉障碍的人来说，使用键盘比点击触摸屏上的小按钮要容易得多。

在移动设备上保持简单和可访问性可以确保每个人都能轻松使用它。

## iOS 与 Android 设计考虑事项

移动设备主要由两个主要玩家主导:iOS 和 Android。为这两者设计一个应用程序既有挑战性又耗时。以下是为这两个平台设计应用程序时的一些注意事项。

### 设计指南

两个玩家都有他们自己的一套你需要遵循的设计准则，他们处理某些交互的方式也不同。比如苹果 iOS 版的[人机界面指南(HIG)](https://developer.apple.com/design/human-interface-guidelines/guidelines/overview/) 推荐在屏幕顶部设置导航栏，底部设置标签栏。

![Tab Bar at Bottom](img/ab12ab492897a4a58d0f6cbd58114d53.png)

Source: [Apple](https://developer.apple.com/design/human-interface-guidelines/components/navigation-and-search/tab-bars/)

与此同时，Android 的[材料设计指南](https://m3.material.io/)建议使用从屏幕左侧滑出的导航抽屉。这两者之间的差异还有其他几个例子。

![Left-side Navigation Drawer](img/5b1a5ebc730fa1960156a216bc4d85b2.png)

Source: [Material Design](https://m2.material.io/components/navigation-drawer)

### 屏幕尺寸范围

另一个需要考虑的是，与 iOS 相比，Android 支持更广泛的屏幕尺寸和分辨率，因此设计师在为 Android 设计时需要考虑不同的长宽比和像素密度。

他们的开发者指南包括不同的[窗口大小类别](https://developer.android.com/guide/topics/large-screens/support-different-screen-sizes)，它们由宽度和高度断点定义。这些可以帮助确定何时应该调整应用程序的布局。

更常见的是使用宽度窗口大小类，而不是高度，因为垂直滚动允许用户轻松访问屏幕下方不太重要的信息。这种方法可以让你的应用在各种屏幕尺寸上轻松使用。

![Width Across Screens](img/ce4ba00517eb3eeb87252b06b9907da3.png)

Source: [Android Developers](https://developer.android.com/guide/topics/large-screens/support-different-screen-sizes)

## 移动手势

手势是与移动设备互动的基础。手势是用户在移动设备上与界面交互的手指移动。

一些最常见的手势包括点击、双击、长按、拖动或平移、滑动或轻击、挤压和旋转。

![Touch Gesture Reference Guide](img/6125e73bae79c3d8d734ee9077b9a60a.png)

Source: [Touch Gesture Reference Guide](https://static.lukew.com/TouchGestureGuide.pdf)

点击是手指在屏幕上快速而短暂的触摸，通常用于选择或激活项目，如按钮、链接或图标。它还用于打开和关闭菜单，启动操作和浏览内容。

双击是连续快速点击两次，通常用于放大图像或地图。双击手势最近被 Instagram 等社交媒体平台采用，允许用户“喜欢”一个帖子。

长按是一种按住手势，通常用于调出上下文菜单、提供附加信息或启动操作。

拖动或平移是手指在屏幕上的移动，通常用于导航、滚动或移动项目。

轻扫或轻拂是手指在屏幕上的快速移动，通常用于导航、滚动或更改内容。

收缩是两个手指相互靠近或远离的运动，通常用于放大或缩小图像或地图。

旋转是两个手指围绕一个中心点旋转的运动，通常用于旋转屏幕上的图像或 3D 对象。

### 外部一致性设计

当使用手势与移动应用程序交互时，用户已经习惯了某些行为。因此，当一个应用程序的行为与他们预期的不一样时，它可能会让他们感到困惑或沮丧。

为了设计外部一致性，通过以用户期望的方式使用移动手势来遵循已建立的设计模式。这样，用户可以毫无障碍地直接使用这个应用程序。

当要把你的设计交给开发人员时，一定要记录下每个手势的行为，这样他们才能正确地实现它。这确保了最终产品符合用户的期望。

## 移动无障碍

用户拥有各种不同类型的能力。有些人可能运动控制能力有限，或者有视力或听力损失等障碍。即使没有永久残疾的人也可能会有暂时的问题，比如手骨折或处于嘈杂的环境中。

在移动设计中，可访问性是一个重要的最佳实践，因为它可以确保应用程序可供尽可能多的人使用，包括残疾人。以下是为移动辅助功能设计的一些最佳实践。

### 关注用户目标

如前所述，试图在一个小空间里容纳太多信息会使手机屏幕变得杂乱。但是，为了避免这种情况，我们必须关注用户的目标。

想想用户想要完成什么，他们需要看到什么信息来实现他们的目标。此外，如果某些元素，如导航或菜单，不需要一直出现在屏幕上，可以考虑隐藏它们的方法。这样，你就能够展示所有必要的信息，而不会用混乱的界面让用户不知所措。在用户需要的时候给他们所需要的。

### 考虑使用环境

人们在各种不同的场合使用他们的移动设备。无论是在旅途中，在不同的环境中，甚至只是用一只手。当应用程序设计时没有考虑到这些环境，这可能会给用户带来令人沮丧的体验，他们可能会放弃应用程序。

通过进行研究和可用性测试，设计师可以了解他们的用户如何在不同的环境中使用他们的移动设备，并根据这些情况设计应用程序。

例如，一个徒步旅行应用程序可能会在户外使用，那里明亮的阳光或天气可能会影响屏幕的可见性。用户也可能戴上手套或其他装备，这可能会影响他们的触摸准确度。

这些只是我们在为移动设备进行设计时需要牢记的几个因素。对用户的同情可以帮助我们在设计时考虑他们的背景。

### 拇指区

随着这些年来移动设备屏幕变得越来越大，用拇指触摸屏幕上的一切变得越来越困难。

根据[史蒂文·胡伯](https://www.uxmatters.com/authors/archives/2012/04/steven_hoober.php)的[观察研究](https://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php)，大约一半的参与者用一只手握着他们的手机。这意味着他们仅仅依靠拇指与屏幕互动，而不是用双手操作。

![Different Ways to Hold Phone](img/eaff5c83f5faf4a8e53b33005c961859.png)

The three main ways that people hold and interact with mobile phones are one-handed, cradled, and two-handed. (Source: [A List Apart](https://alistapart.com/article/how-we-hold-our-gadgets/))

基于这种认识，我们应该在设计应用时考虑到这些单手用例。

一种方法是考虑移动屏幕的“拇指区”。拇指区在典型的移动屏幕上显示容易触及和难以触及的区域，可用于设计可访问的移动体验。

在下面的图表中，屏幕的区域由拇指感觉舒适的区域、需要拇指伸展的区域和感觉难以触及的区域来表示。

![Mobile Thumb Zone](img/8f8496b89bfaa39588108e53a5e17087.png)

The thumb zone (Source: [Luke W](https://www.smashingmagazine.com/2016/09/the-thumb-zone-designing-for-mobile-users/))

通过将主要的操作元素，如按钮或导航，放在靠近屏幕底部和中间的位置，用户会更容易够到它们。

### 触摸目标

另一个移动辅助功能最佳实践是遵循最小触摸目标尺寸。这意味着用户可能与之交互的任何元素，比如图标按钮，在 iOS 系统中最少应该是 44pt)，在 Android 系统中最少应该是 48dp)。

现在，按钮上的图标可以变小，但要确保它周围的填充符合最小尺寸。这有助于提高触摸精度，减少错过目标的数量。

![Touch Targets](img/71b2c843de621656e169609c3734452b.png)

Source: [Material Design](https://m3.material.io/foundations/accessible-design/accessibility-basics)

### 文本大小

在移动设备上很难阅读文本，尤其是对于有视觉障碍的人。设计者应该遵循[建议的正文文本大小 16px](https://accessibleweb.com/question-answer/minimum-font-size/) 。此外，用户应该能够将屏幕上的文本大小调整 200 %,以提高可读性。

### 屏幕阅读器

对于有视觉障碍的用户来说，像屏幕阅读器这样的辅助技术可以提供很大的帮助。iOS 和 Android 设备都有内置的屏幕阅读器，比如[画外音](https://developer.apple.com/design/human-interface-guidelines/foundations/accessibility/#voiceover)和[对讲](https://support.google.com/accessibility/android/answer/6283677?hl=en)。

要确保您的应用程序与屏幕阅读器兼容，请与您的开发团队合作，以确保所有交互元素都有正确的标签，并且图像都有替代文本。屏幕阅读器将大声读出为每个元素提供的标签，因此它是描述性的并表明其用途是很重要的。

此外，确保元素的分组方式允许合理的 tab 键顺序。屏幕阅读器用户可以左右滑动来浏览屏幕。

最后，对残疾人进行彻底的用户测试，以了解你的应用程序是否可以与屏幕阅读器一起使用。从真实用户那里收集见解，而不是假设你认为用户会如何使用你的应用。

## 示例:创建一个关于脸书的帖子

![Facebook Post Formatting](img/d2a055c835d9635d0e151f0950e44073.png)

Source: [Mobbin](https://mobbin.com/flows/9cd217ce-e3ff-4691-ba03-2601f6df6aa7)

脸书的移动应用遵循许多将用户放在第一位的移动最佳实践。在 Feed 上创建新帖子时，该应用会通过工具提示提醒用户默认的受众设置，确保该功能不会被忽略，并且用户知道他们的隐私选项。

该应用程序还可以通过将文章选项放在拇指区内的底部抽屉中来轻松访问文章选项，从而轻松导航和选择选项。

“受众设置”页面具有品牌插图、易于理解的文本以及多个受众选项和描述，可帮助用户做出明智的选择。

最后，创建新帖子的文本区域包括占位符文本，以提示用户分享他们的想法。该应用利用原生 iOS 键盘，从屏幕底部滑入，提供熟悉、直观的输入体验。

## 示例:订单和提货时抓取

![Placing Order on Grab](img/fac278a24ed0aa28cc3f39fae3294e02.png)

Source: [Mobbin](https://mobbin.com/flows/20115275-f697-4e4a-afee-61296064f002)

Grab 的移动点餐系统在设计时就考虑到了用户的便利性。结帐屏幕显示了确认订单所需的所有重要信息，如商店的名称、位置和距离以及预计完成时间。

订单摘要清晰易懂，显示每件商品的数量和描述，包括选择的任何定制选项。项目名称以粗体显示，表示层次结构，便于识别项目。此外，Grab 的 **People also ordered** 部分提供了一种有效的方式来追加销售额外的商品，并模拟现场购物体验。

支付细节清晰呈现，包括有助于区分可用支付选项的图标。最后，确认对话框是对用户的最后提醒，以防他们想改变主意或意外选择了提货。

![Final Steps of Ordering](img/132e058f71efa3905da4bb0193542efa.png)

Source: [Mobbin](https://mobbin.com/flows/20115275-f697-4e4a-afee-61296064f002)

下单后，Grab 提供了一个周到的功能，允许用户在订单发送给商家之前修改订单。这给了用户最后的机会来改变或取消他们的订单，以防他们改变主意。

为了让用户了解情况，在准备订单时，该应用程序会显示一个进度步进器，使用图标来指示订单的阶段，让用户清楚地了解订单的进度，减少焦虑。

此外，该应用程序允许用户继续探索，同时通过在屏幕底部显示一条小消息来跟踪他们的订单进度，这与用户建立了信任，并且不会中断他们的体验。

## 结论

随着越来越多的人转向智能手机来访问互联网，确保您的应用程序符合他们的需求是至关重要的。响应式设计是确保您的应用程序无缝适应不同屏幕尺寸的关键，无论是在 iOS 还是 Android 上。

卓越的移动体验关注细节。应用程序非常依赖手势，因此确保这些手势直观且易于使用非常重要。这不仅仅是让你的应用看起来很好，也是为用户创造一个可访问的和周到的体验。

从做得好的应用程序中获取灵感，例如脸书、Grab 和其他行业示例。通过首先为用户设计，你将能够创建人们喜欢使用的应用程序。

*精选图片来源:* [IconScout](https://iconscout.com/icon/mobile-concept-page-login-signin-popup-user)

## [LogRocket](https://lp.logrocket.com/blg/signup) :无需采访即可获得 UX 洞察的分析

[![](img/1af2ef21ae5da387d71d92a7a09c08e8.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 让您可以回放用户的产品体验，以可视化竞争，了解影响采用的问题，并结合定性和定量数据，以便您可以创建令人惊叹的数字体验。

查看设计选择、交互和问题如何影响您的用户— [立即尝试 LogRocket】。](hhttps://lp.logrocket.com/blg/signup)