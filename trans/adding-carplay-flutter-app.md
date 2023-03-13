# 将 CarPlay 添加到您的 Flutter 应用程序

> 原文：<https://blog.logrocket.com/adding-carplay-flutter-app/>

就在不久前，你所能期待的手机和汽车之间最高水平的集成是从手机向汽车扬声器播放音乐。

随着时间的推移，随着 Android Auto 和苹果 CarPlay 等技术的引入，这种情况发生了巨大的变化。现在，您选择的音频、快餐或导航应用程序可以直接传输到您的汽车娱乐系统，以便轻松互动。

虽然 Apple CarPlay 和 Android Audio 的意图相似，但在本文中，我们将看看如何用一些 CarPlay 功能来补充我们的 Flutter 应用程序。我们将涵盖:

## 设定我们的期望

现在，作为这篇文章的作者，我很乐意投入到如何做到这一点的激动人心的技术细节中去(因为我相信你会喜欢阅读它们！).然而，在我这样做之前，我们需要简要地提到一些可能会阻碍你获得 Flutter CarPlay 应用程序的事情。

作为开发人员，我们总是渴望找到一种方法来交付某种产品或特性。当我们被告知我们不能做某件事，或者某件事不受支持时，我们无论如何都想去做，只是为了看看我们是否能成为第一个做某事的人，或者因为我们真的想为我们的客户提供服务。

然而，在开发利用苹果 CarPlay 的应用程序的情况下，我们需要定义一些现实的期望。这篇文章与 Flutter 应用程序有关，但也适用于任何利用 CarPlay 的应用程序。让我们开始吧。

### 您的 CarPlay 应用程序需要 Apple 的特别批准

创建一个包含 CarPlay 组件的 Flutter 应用程序并不简单，只是放入一个包，设置一些值，然后部署给用户。任何使用 CarPlay 的应用程序都必须经过苹果公司的特别批准。

这个批准过程可能很长，有时长达数月。也有报道称，苹果公司根本没有回复要求这一功能的开发者。你不能做什么或说什么来加速这个过程——你完全在苹果的时间线上。

尽管如此，这并不像勾选一个声明你的应用程序使用 CarPlay 的复选框那么简单。你的应用程序必须属于苹果公司预先定义的类别，这就引出了我们的下一个问题。

### 你不能在 CarPlay 上运行任何类型的应用程序

要在你的应用程序中使用 CarPlay，它必须符合苹果规定的特定类别。这些类别基本上是:

*   音频应用
*   沟通应用程序(如团队、懈怠或不和谐)
*   “驾驶任务”app(比如记录用户车内里程 iOS 16 新增)
*   电动汽车充电应用
*   加油应用程序(通过 CarPlay 支付燃料费用 iOS 16 中的新功能)
*   导航应用程序(如谷歌地图或苹果地图)
*   停车 app(支付停车费)
*   快速订餐应用程序(如 DoorDash 或 Uber Eats)

仅此而已。

也许你觉得苹果公司对你能为 CarPlay 开发什么和不能为 CarPlay 开发什么施加这样的限制太苛刻了。然而，考虑一下 CarPlay 的前提:它是人们在车里用来完成基本任务的东西，不应该分散他们的驾驶注意力。

![Stock Photo Showing Infotainment System Displayed On Front Dashboard Of Car](img/5b5f41e56ee444f581922fd5c70c7666.png)

如果你开发了一个应用程序，让人们在开车时在他们的信息娱乐系统上玩 Flappy Bird，并且他们撞了他们的车，那么司机确实会因为如此粗心而犯错。但是，苹果难道不应该为最初允许这款应用承担一些责任吗？从这个角度来看，这些限制似乎是合理的。

因此，考虑到上述情况，我们只有在以下情况下才会考虑将 CarPlay 添加到我们的 Flutter 应用程序中:

如果我们忽略上述内容，只是将应用程序发送给苹果公司认可，他们可能不会花时间来帮助你的应用程序变得合规。他们会拒绝你的申请，或者更糟的是，永远不会回复你，让你的应用程序在没有官方批准(你可以使用)或拒绝(你可能会升级)的情况下搁浅。

万一苹果不批准我们的应用程序，我们不要把应用程序的成败寄托在拥有 CarPlay 功能上，这一点很重要，尤其是考虑到苹果可能不会告诉我们应用程序被拒绝的原因。

所有这些都解决了，让我们深入研究如何将 CarPlay 添加到我们的 Flutter 应用程序中。

## 设置我们的 CarPlay Flutter 应用程序的基础

在今天的例子中，我们将把 CarPlay 功能引入我们的快餐订购应用程序 EatUp。在我们的手机上，这个应用程序是这样的:

![Phone Screen Showing Example Fast Food Ordering App Features In Action, From Order Screen To Pizza Options And Ordering Feature](img/a2d25034c8f8a6ece70a426842f098b6.png)

这篇文章的重点不是 [Flutter 应用程序开发](https://blog.logrocket.com/pros-cons-flutter-app-development/)本身，而是如何将 CarPlay 添加到应用程序中。欢迎你到[仔细阅读源代码](https://github.com/flutterfromscratch/using-flutter-with-carplay)来了解我们是如何实现上面显示的布局的，但是我们不会在这里深入细节。

## 将 CarPlay 添加到我们的 Flutter 项目中

`flutter_carplay`的配置步骤在的发布网站[上可以找到，所以现在让我们浏览一下。](https://pub.dev/packages/flutter_carplay#get-started)

请注意，在这样做的时候，您将对您的 Flutter 项目进行更改。如果您正在将 CarPlay 添加到现有的应用程序中，那么现在是将您的更改提交到源代码控制的理想时机。这样，如果您的更改引起问题，您可以轻松地恢复它们。

首先，我们想在依赖项中添加对我们的`pubspec.yaml`的引用:

```
dependencies:
  flutter:
    sdk: flutter
  ...other dependencies...
  flutter_carplay: ^1.0.3

```

然后，我们应该在项目中运行`flutter pub get`命令。这将创建一个`Podfile`，帮助将`flutter_carplay`的本地部分与我们的项目集成。

让我们打开`ios/Podfile`文件并取消顶行的注释，将全球平台设置为 iOS 14.0:

```
# Uncomment this line to define a global platform for your project
platform :ios, '14.0'

```

将目录切换到`ios`文件夹并运行`pod install --repo-update`。

## 修改我们的 XCode 项目

因为 CarPlay 是一个原生的 iOS 特性，我们需要对 XCode 项目进行修改。右击`ios`文件夹，然后选择`Flutter > Open iOS module in Xcode`:

![Demonstration Of Steps To Modify Xcode Project Showing Submenus After Right Clicking Ios Folder And Hovering Over Flutter Menu Item](img/87ff4708d2d0601a26b2f3a14820be50.png)

在我们的项目中，导航到`Runner > Runner > AppDelegate`并用一个简单的`return true`替换我们的`application`函数的内容。在我的例子中，我注释掉了旧代码，并直接在它下面添加了新代码，所以它看起来应该是一样的:

![Appdelegate Folder Contents With Edited Application Function](img/39e97c2437ef6ce028a840365307d589.png)

在我们的`Runner`文件夹(不是最顶层的，而是下一层的)中，创建一个`SceneDelegate.swift`文件，并将内容设置如下:

```
@available(iOS 13.0, *)
class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = scene as? UIWindowScene else { return }

        window = UIWindow(windowScene: windowScene)

        let flutterEngine = FlutterEngine(name: "SceneDelegateEngine")
        flutterEngine.run()
        GeneratedPluginRegistrant.register(with: flutterEngine)
        let controller = FlutterViewController.init(engine: flutterEngine, nibName: nil, bundle: nil)
        window?.rootViewController = controller
        window?.makeKeyAndVisible()
    }
}

```

完成这些后，`SceneDelegate`应该是这样的:

![Scenedelegate File Contents](img/707f061c115bfa7eebafa5cb882ed1e5.png)

现在，右键单击`Info.plist`文件，然后单击`Open As > Source Code`。这将以纯文本方式打开`Info.plist`,以便您可以轻松地将下一个配置粘贴到文件中。

在这一步，我们应该格外小心，因为如果我们输入了错误的内容，很容易意外破坏这个文件:

![Steps Within Runner File Showing How To Right Click Info File, Navigate To Open As Menu Option, And Select Correct Source Code File That Should Be Opened](img/763e49282b5bf93255ca07b834c98602.png)

在结束`</dict>`之前，添加以下文字。这是默认配置，我们稍后将返回来对其进行自定义:

```
<key>UIApplicationSceneManifest</key>
<dict>
  <key>UIApplicationSupportsMultipleScenes</key>
  <true />
  <key>UISceneConfigurations</key>
  <dict>
    <key>CPTemplateApplicationSceneSessionRoleApplication</key>
    <array>
      <dict>
        <key>UISceneConfigurationName</key>
        <string>CarPlay Configuration</string>
        <key>UISceneDelegateClassName</key>
        <string>flutter_carplay.FlutterCarPlaySceneDelegate</string>
      </dict>
    </array>
    <key>UIWindowSceneSessionRoleApplication</key>
    <array>
      <dict>
        <key>UISceneConfigurationName</key>
        <string>Default Configuration</string>
        <key>UISceneDelegateClassName</key>
        <string>$(PRODUCT_MODULE_NAME).SceneDelegate</string>
        <key>UISceneStoryboardFile</key>
        <string>Main</string>
      </dict>
    </array>
  </dict>
</dict>

```

## 设置我们的权利

即使在开发阶段，我们也需要允许我们的应用程序与我们电脑上的 CarPlay 模拟器进行交互。现在让我们配置这些权利。

在 XCode 中，点击`Runner`，然后点击`Signing & Certificates`，再点击`All`左边的`+`按钮:

![Opening List Of All Signing And Certificates Options To Configure Entitlements](img/effcc465c92092eda294e55be44c1d08.png)

在出现的列表中，选择`Keychain Sharing`。您的应用程序实际上不使用钥匙串共享，但这样做可以在您的项目中设置一个`Runner.entitlements`文件:

![Red Arrow Pointing Left To Runner Entitlements File Resulting From Previous Step](img/17c51ab32e6e919bfc4a227b3fcbec00.png)

在这个文件中，删除`Keychain Access Groups`并添加一个新的权限，这取决于您正在开发的应用程序的类型。你可以查看[苹果公司的可用授权列表](https://developer.apple.com/documentation/carplay/requesting_carplay_entitlements)，看看你的项目使用哪一个。

完成后，您的`Runner.entitlements`文件应该如下所示:

![Runner Entitlements File Contents With Keychain Access Groups Removed And New Entitlement Added](img/8e9359ddab81ee4e0a6b5f1ae33bad15.png)

现在我们终于准备好为我们的 Flutter 应用程序添加一些功能了！🎉

## 从我们的 Flutter 应用程序调用 CarPlay 功能

现在，我们将在应用程序的 CarPlay 组件中添加以下 UI。

![Gif Showing Carplay Ui For Pizza Ordering App Including Pizza Selection Options, Order Confirmation Options, And Order Confirmation Dismissal](img/c989c6d0e12c670cfc2789dbe0e6facd.png)

为了实现这一目标，我们需要做到以下几点:

1.  设置一个`RootTemplate`,它将构成我们 CarPlay 体验的基础
2.  向模板添加详细信息
3.  在我们的应用程序中设置回调

现在，让我们继续处理这些步骤。

## 设置一个`RootTemplate`

在我们的`main.dart`文件中，我们将创建一个新的`FlutterCarplay`对象。该对象将负责处理我们与 CarPlay 的连接，并让我们控制 CarPlay 体验中发生的事情:

```
final FlutterCarplay _flutterCarplay = FlutterCarplay();

```

接下来，在小部件的`initState`中，我们想要设置带有一些选项的`RootTemplate`。创建一个网格模板，设置一个标题，然后描述一些我们希望能够被按下的按钮是相当容易的:

```
@override
void initState() {
  FlutterCarplay.setRootTemplate(
    rootTemplate: CPGridTemplate(
      title: "What pizza?",
      buttons: [
        ...Data.FoodItems.map(
          (e) => CPGridButton(
            titleVariants: [e.name],
            image: 'assets/images/${e.asset}',
            onPress: () {
              showOrderingConfirmationSheet(e);
              // showActionSheet();
            },
          ),
        )
      ],
    ),
    animated: true,
  );

  _flutterCarplay.forceUpdateRootTemplate(); // This makes the CarPlay experience reload on hot reload, useful during development.

  // TODO: implement initState
  super.initState();
}

```

正如所料，结果是这样的:

![Six Types Of Pizza Shown In Pizza Ordering Options Displayed Within Carplay Flutter App Ui](img/ff392899b302a1aba5bffe584e41d61c.png)

现在，让我们实现`showOrderingConfirmationSheet`:

```
void showOrderingConfirmationSheet(FoodItem food) {
  FlutterCarplay.showActionSheet(
    template: CPActionSheetTemplate(
      title: "Order ${food.name}?",
      message: "Your pizza will be ready soon after confirmation.",
      actions: [
        CPAlertAction(
          title: "Cancel",
          style: CPAlertActionStyles.cancel,
          onPress: () {
            // print("Cancel pressed in action sheet");
            FlutterCarplay.popModal(animated: true);
          },
        ),
        CPAlertAction(
          title: "Ok",
          style: CPAlertActionStyles.normal,
          onPress: () {
            print("Ok pressed in action sheet");
            FlutterCarplay.popModal(animated: true);
            FlutterCarplay.showAlert(
                template: CPAlertTemplate(titleVariants: [
              '${food.name} ordered!'
            ], actions: [
              CPAlertAction(
                title: "Dismiss",
                style: CPAlertActionStyles.destructive,
                onPress: () {
                  print("Dismiss pressed in action sheet");
                  FlutterCarplay.popModal(animated: true);
                },
              ),
            ]));
          },
        ),
      ],
    ),
  );
}

```

这导致特定食品被打开并显示确认消息。在这个阶段，你还可以在你的 Flutter 应用程序中调用本地功能，比如根据你的需要在内部数据库中添加或删除项目。

## 结论

和往常一样，您可以在 Github 上检索到源代码的完整副本。在我实现 CarPlay 之前有一个提交，在我实现它之后有一个提交，所以你可以用这个来比较什么改变了。如果您在实现 CarPlay 时遇到困难，这种比较会很有用。

所以，这就是你如何将 CarPlay 添加到你的 Flutter 应用程序中。添加 CarPlay 最耗时的部分是让苹果批准你在应用中使用 CarPlay。如果你想在你的 Flutter 应用程序中使用 CarPlay，你应该尽快开始工作。

将 CarPlay 添加到您的 Flutter 应用程序可能是一个相当复杂的过程，但如果 CarPlay 非常适合您的应用程序，它可以为您的应用程序体验增加很多价值。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)