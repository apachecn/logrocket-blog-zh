# 作为移动应用开发者开发 2D 移动游戏

> 原文：<https://blog.logrocket.com/developing-2d-mobile-game-mobile-app-developer/>

## 介绍

在从事移动应用程序开发几年后，我开始开发 2D 游戏。在本文中，我将与您分享我的观察，并比较移动开发范围内的两个不同概念。我将逐一介绍它们的不同之处，并从我的 Swift、SpriteKit 2D 游戏示例中添加示例代码片段。

![PandaClicker Mobile Game](img/b7537287be9c10b97a23509f8a2bc87b.png)

我的游戏叫熊猫点击器，目的是触摸屏幕中央的熊猫图像。在每次触摸中，一只小熊猫从屏幕顶部滑过，有声音播放，分数增加。当分数变成 10 的倍数时，设备会振动，用户会在屏幕上看到粒子动画。你可以在这里找到源代码。🐼我将在本文中详细介绍它。

### 节点和场景

在这篇文章中，我将提到节点和场景。先说说它们是什么。

在 SpriteKit 中，节点按层次组织成节点树，类似于视图和子视图的工作方式。[苹果的开发者文档是这样说的](https://developer.apple.com/documentation/spritekit/sknode/getting_started_with_nodes):“最常见的，节点树是以场景节点为根节点，其他内容节点为后代节点来定义的。场景节点运行动画循环，处理节点上的动作，模拟物理，并渲染节点树的内容以供显示。

考虑到这一点，我们准备讨论 2D 游戏和移动开发之间的比较！

当你在开发一个游戏时，你需要更多地考虑感官。当玩家点击那个按钮时，他们会听到什么？他们拿着手机会有什么感觉？他们会看到什么视觉效果？那个动画需要多长时间？用户的目标是什么？你可能需要用不同于开发移动应用的方式来思考。开始吧！🕵🏼‍♀️

## 随机性

你可能见过一个寻宝游戏的例子。作为一个基本的解释，它是一个游戏，玩家试图找到一些被隐藏的东西。

我正在添加一个模拟人生移动寻宝活动的例子。在下面的 GIF 示例中，两个景点有 50 件古代文物，一个景点有 30 只金色兔子。每次，该区域的奖励(以及有这些奖励的地点)都是随机产生的，并且有一次选择的机会——或者你需要花费一些 SimCash 来获得一次机会，但无论如何。🙂

![Sims Mobile Treasure Hunt example](img/5377bb65f7de2d57773f6bc95748caf8.png)

如果我们能把宝藏放在同样的位置，这个游戏就没有娱乐性了，对吗？如果我知道左边的总是金色的兔子，我会怎么兴奋？它会是一致的，稳定的，无聊的。我们在这里寻找随机性来获得乐趣。它让我们不确定宝藏在哪里。当我们成功时，它会给我们一点压力，但也会给我们带来喜悦。试图找到它会在哪里，并听取我对宝藏位置的直觉是这个游戏的有趣部分。

然而，我们不喜欢银行应用程序中的随机性。想象一下，你打开你的转会页面，你的转会没有按时间顺序排列。不会吧！每个用户都会抱怨。

我们更多的是在移动应用中寻找一致性，以便于使用。否则，用户会对找到他们需要的部分感到困惑。但是在手机游戏中，随机性是帮助你获得乐趣的因素之一！

我在下面添加了一个示例代码片段，给你一个关于随机性的概念。这是从[熊猫点击器，](https://github.com/iremkaraoglu/PandaClickerGame)这是我的例子项目。以下代码为场景创建一个节点。我添加了一张我在生育上画的图片，并把它命名为“熊猫”

它获取图像并创建一个名为“熊猫”的节点。它在`0`和屏幕宽度之间获得一个随机的 x 位置。然后，它将节点定位在屏幕顶部的随机 x 位置。它们如何在屏幕上滑动是我们稍后将介绍的动画主题:

```
let screenSize: CGRect = UIScreen.main.bounds
let panda = SKSpriteNode(imageNamed: "panda")
let randomXPos = CGFloat.random(in: 0..<screenSize.width)
panda.position = CGPoint(x: randomXPos, y: screenSize.height)

```

![Randomness Example PandaClicker](img/5c088eaae359ea316e2eb67b39ca0e4a.png)

## 计算

当我在移动应用程序中开发用户界面时，我不需要计算太多。paddings，高度和宽度是确定的，我可以达到 Figma 上的值。我在开发 UI 的时候从来没有计算过复杂的方程。

然而，开发手机游戏是不同的。正如我之前所说，在 2D 游戏中可能会有随机性，也许在屏幕的特定部分。你需要为这种随机性设定界限。

假设在那些随机的位置上会有图像。当图像的中心在边界内，但有些部分溢出时会发生什么？你还需要考虑你的图像大小。界限呢？它们可能因设备而异；一些设备具有较大的宽度，一些设备具有较大的高度。你需要考虑设备尺寸。这个你需要详细考虑，多计算！

[苹果的开发者文档中有这样一段话](https://developer.apple.com/documentation/spritekit/getting_started_with_physics):“虽然你可以控制一个场景中每一个节点的确切位置，但往往你希望这些节点互相交互，互相碰撞。你可能还想做动作系统不处理的事情，比如模拟重力和其他力。”

出于这些目的，你需要给你的游戏添加一个物理引擎。SpriteKit 和 Unity 已经有这个功能了。想了解更多关于物理引擎的信息，你可以看看这个关于设计物理引擎的视频。

## 输入处理

在移动应用程序开发中，不需要手动处理输入。但是，在游戏开发中，投入是游戏最重要的部分。作为开发人员，我们根据从游戏手柄、触摸、鼠标垫、键盘等获取的输入进行编码，以提供最佳的用户体验。

在我们的例子中，这是一个手机游戏，因此触摸作为输入是很重要的。在应用程序开发中，UI 元素给出用户在屏幕上触摸的数据。在游戏开发中，我们是把屏幕位置转换成游戏摄像头，找到用户的触摸位置。

## 动画片

在移动应用程序中，如银行应用程序或聊天应用程序，您可能不会像在 2D 游戏中那样经常看到动画。用户对银行应用程序中的动画不感兴趣；他们希望以安全、快速和简单的方式使用银行应用程序！良好的用户体验是关键！

说到 2D 游戏，动画让游戏变得更加有趣。想想像糖果粉碎赛这样的三场比赛。想想这个游戏没有任何动画。当你点击的时候这个框就消失了。缺乏反馈会让用户失去兴趣。开发游戏时，动画不是必须的，但如果你想吸引用户，推荐使用动画。

下面只是一个基本的例子来说明有和没有动画的区别。

![Animation Count](img/2a62367eaa87ba2cb9b0e6470a6db5e4.png)

![Normal Count](img/554e572034b67d9caffa8ae5887cd558.png)

在随机性部分，我们看到熊猫在随机的 x 坐标上滑下屏幕。现在是时候听听他们是如何滑行的了。在下面的代码中，让我们回忆一下如何将节点添加到屏幕上。然后只是提供了一个语句来演示它:那个`moveTo()`函数。

![PandaClicker Randomness Example](img/42dcbc5f2902d62aaa56eb62c455b99c.png)

```
// recall creating a node and giving a random x position at the top of the screen 
let panda = SKSpriteNode(imageNamed: "panda")
let randomXPos = CGFloat.random(in: 0..<screenWidth)
panda.position = CGPoint(x: randomXPos, y: screenHeight)

// below codes moves the node to the -72 in the y coordinate, in the 0.80 seconds and removes the node from the scene. 
panda.run(.sequence([
      .moveTo(y: -72, duration: 0.80),
      .removeFromParent()
      ]))    

```

## 微粒

当我们考虑动画时，可以有在屏幕上移动的元素，或者称为粒子的效果。粒子系统是一个复杂的节点，在游戏场景中发射这些所谓的粒子。粒子效果主要用于视频游戏中的各种图形处理，如闪光、烟雾、爆炸、雨、雪等等。

与移动应用程序开发相比，当你只是给别人寄钱时，没有人会想到在屏幕上添加粒子，但在游戏中，它需要令人兴奋和引人入胜。幸运的是，粒子将帮助你实现这一点！使用粒子效果，为您的应用程序添加特殊效果，如闪烁或真实的烟雾火焰。

如果您使用 SpriteKit 进行开发，您可以使用 Xcode 的 SpriteKit 粒子编辑器或在代码中创建和配置粒子效果。在 PandaClicker 中，我创建了一个 SpriteKit 粒子文件，并将其命名为“MagicParticle”我改变了 Xcode 编辑器上的值，比如粒子的数量和颜色。当分数是 10 的倍数时，粒子出现，然后消失。

![Particles Gamedev Example](img/f01fd50d0e4776046b463ab9eab4f188.png)

![PandaClicker particles](img/f4638d545106b03609a05c27144894c6.png)

```
if let myEmitter =  SKEmitterNode(fileNamed: "MagicParticle.sks") {
      myEmitter.position = CGPoint(x: screenWidth / 2, y: screenHeight / 2)
      addChild(myEmitter)
}

```

在上面的代码片段中，我创建了一个发射器节点，并将该节点放置在屏幕的中央。然后我将它添加为场景根节点的子节点。

## 触觉论

到目前为止，我们已经介绍了一些视觉元素。我们换个话题，说说触觉。正是技术的运用刺激了触觉和运动感。换句话说，就是通过触觉传递和理解信息的科学技术。

正如 [macReports 所说的](https://macreports.com/iphone-system-haptics-what-they-are-enable-or-disable/)，“一些 iPhone 型号包括触觉反馈(也称为 Haptics 或系统触觉)。该功能使用 Taptic 引擎提供触觉反馈，并结合可听音和/或视觉反馈。Taptic 发动机产生振动和触觉反馈功能。”

![Apple Taptic Engine](img/0b8b747ac0fb8bd9bf6c8e1b2233a185.png)

Taptic Engine from [macReports](https://macreports.com/iphone-system-haptics-what-they-are-enable-or-disable/)

当我们处理玩家的感觉时，提供一些玩家会感觉到的东西是一个很好的选择。因此，在游戏中的那个时刻，通过你的手机去感受一些令人兴奋的事情是很吸引人的！

在熊猫点击器中，我已经添加了当用户达到乘数为 10 的分数时的触觉。我正在添加下面的触觉代码。我选择了`medium`风格，你可以通过试错来选择你想要的。如果你的设备不支持这项功能，但你想知道它是如何工作的，[点击这里](https://developer.apple.com/design/human-interface-guidelines/ios/user-interaction/haptics/)查看触觉信息。它向您展示了它们的声音和感觉:

```
let generator = UIImpactFeedbackGenerator(style: .medium)
generator.impactOccurred()

```

上面的视频显示了媒体如何影响触觉声音。此外，请记住，这个功能需要硬件，这是不可能的，看看你的触觉代码是否如你所愿在模拟器上运行。你必须在支持触觉的物理设备上测试它。

## 声音

我们讨论了触觉，但是听觉呢？当你开发一个游戏时，声音也是必不可少的。你可以放背景音乐，或者只加音效，或者两者兼而有之？你需要考虑游戏和声音之间的和谐。如果你和一个设计团队一起工作，他们可能会给你声音文件，但是如果你是一个独立的开发者，你需要考虑你自己的。

我找到了免版税的声音添加到熊猫点唱机，并将其命名为`panda_tap.mp3`。当玩家触摸屏幕上的大熊猫时，将运行以下代码并播放 panda_tap 声音:

```
SKAction.playSoundFileNamed("panda_tap.mp3", waitForCompletion: true)

```

我们以前走过随机性，当涉及到声音时，我们也可以随机化它们！我们可能会有几个声音的胜利情况下的游戏，使它更令人兴奋！

下面是一个示例代码片段。它不是来自熊猫点击器，但我把它作为声音随机性的一个例子。在这段代码中，我们在一个数组中有四个声音文件，该数组中有一个随机元素，并将它赋给一个`randomWinSound`变量。然后，`SKAction`扮演了`randomWinSound`:

```
var randomWinSound = ["AudioAssets/perfect.mp3",
                      "AudioAssets/awesome.mp3",
                      "AudioAssets/well_done.mp3",
                      "AudioAssets/congrats.mp3"].randomElement()!
SKAction.playSoundFileNamed(randomWinSound, waitForCompletion: true)

```

## 赢/输条件

在移动应用中，没有输赢之分。但是，我们在开发一款手游的时候，为了让用户玩的有目的，任何事情都最好有输赢！

如果开始开发，就要考虑:玩家的目的会是什么？实现这一目标的障碍是什么，或者是什么会让他们失败？你需要从玩家的角度思考，以及他们将如何参与游戏。

## 结论

这些都是我到现在为止的观察。希望能让你对手机 app 开发和手机游戏开发的对比有所了解。你们的经历可能不同，但我想分享我的旅程和观点。你可以在这里查看我的 2D 游戏示例[熊猫点击器。
您可以通过](https://github.com/iremkaraoglu/PandaClickerGame)[【电子邮件保护】](/cdn-cgi/l/email-protection)联系我，了解任何反馈或问题。
下篇见！🥰
保重🥳

## 参考

[https://www . ultraleap . com/company/news/blog/what-is-haptics/](https://www.ultraleap.com/company/news/blog/what-is-haptics/)
[https://developer.apple.com/documentation/uikit/animation*和*haptics](https://developer.apple.com/documentation/uikit/animationandhaptics)
[https://www . androidcentral . com/haptic-feedback-most-important-smart phone-feature-no-one-talks-about](https://www.androidcentral.com/haptic-feedback-most-important-smartphone-feature-no-one-talks-about)
[https://developer . apple . com/documentation/sprite kit/sk node/getting*started*with _ nodes](https://developer.apple.com/documentation/spritekit/sknode/gettingstartedwith_nodes)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)