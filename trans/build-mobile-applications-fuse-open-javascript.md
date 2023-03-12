# 使用 Fuse Open 和 JavaScript - LogRocket Blog 构建移动应用程序

> 原文：<https://blog.logrocket.com/build-mobile-applications-fuse-open-javascript/>

[Fuse Open](https://fuseopen.com/) 是一个混合移动开发框架，很少有人提到它可以替代流行的框架，如 [React Native](https://reactnative.dev/) 、 [Flutter](https://flutter.dev/) 或 [NativeScript](https://nativescript.org/) 。

也就是说，Fuse 可能是一个有趣的选择，特别是如果你已经有一些 web 开发经验，并且希望快速构建原型和漂亮的应用程序。

## 什么是保险丝熔断？

Fuse Open 是为设计人员和 JavaScript 开发人员设计的。编写 UI 感觉很像绘画或使用数字设计工具，如 Sketch 或 Figma。这使得从模型到实际代码的转换变得非常容易。

没有必要学习全新的框架——您的业务逻辑几乎是用纯 JavaScript 编写的。此外，Fuse 不是一个 web 视图。它向下编译到 C++以在移动设备上获得最佳的本机性能，并在需要时与 Objective-C (iOS)和 Java (Android)无缝互操作。Fuse 简单易学，写起来很有趣，而且非常强大。

## 什么是 Fuse 生态系统？

*   [Fuse Open](https://fuseopen.com/) 是一个开源的混合移动开发框架，它允许你用你已经知道的工具来构建 iOS 和 Android 应用，比如 JavaScript 和一种类似 HTML 的标记语言
*   [Fuse Studio](https://github.com/fuse-open/fuse-studio) 是在 macOS 和 Windows 上使用 Fuse Open framework 的可视化桌面工具套件(目前有一个新版本正在开发和测试中，名为 [Fuse X](https://fuse-x.com/)
*   Fuse Preview ( [iOS](https://apps.apple.com/nz/app/fuse-preview/id1296280076) ， [Android](https://play.google.com/store/apps/details?id=com.fusetools.fusepreview&hl=en&gl=US) )是一个独立的应用程序，是迄今为止预览项目最简单的方式，不需要安装 Xcode 或 Android SDKs

## 用 Fusetools 构建你的第一个应用

我们在本教程中的目标是创建一个简单的主从式加密货币跟踪器应用程序，该应用程序将从 REST API 获取数据，显示一个概览列表，并允许我们导航到各个页面。

我们将在主页上展示最新和最大的加密货币，并为每枚硬币提供一个专用的详细页面，上面有其徽标、名称、价格和详细描述。这就是我们的密码追踪器的样子:

![](img/4b7bf5e5ce93c3807a76f5d4406a5eed.png)

## 先决条件

*   对 HTML 和 JavaScript 有基本了解
*   您选择的文本编辑器(我推荐带有 [Fuse 扩展](https://github.com/fuse-open/vscode-extension)的 VS 代码，它允许我们有语法高亮、代码完成和其他有用的工具)
*   Fuse Studio 1.10 安装在您的操作系统上(说明[此处](https://github.com/fuse-open/fuse-studio))
*   CoinMarketCap API 密匙(你可以在这里注册一个[免费密匙)](https://pro.coinmarketcap.com/signup)
*   构建优秀应用的动机

## 使用 Fuse Studio 创建新项目

首先，我们将开始创建一个全新的项目，并设置我们的开发环境。打开 Fuse Studio 应用程序，点击`New Fuse project`，给你的项目起个名字。

![fuse-studio-javascript-new-project](img/879891b46cb015603ff90d369f6ac95b.png)

或者，您也可以使用 CLI 将`fuse create app CryptoTracker`、然后`cd`写入目录，最后运行`fuse preview`来启动预览窗口。如果您正确设置了 Fuse Studio，新的 Fuse open 项目应该已经成功加载，您应该能够看到您的应用程序显示为空白页面。

## Fuse 是如何工作的？

在深入我们的主要目标之前，让我们对 Fuse 的工作原理有一个基本的了解。我继续将我的文本编辑器放在应用预览旁边，并添加了几行类似典型 Hello World 示例的代码:

![fuse open javascript hello world application](img/2e398751cd37d108105f4ff4270594b0.png)

就是这样。这就是用 Fuse 创建 Hello World 应用程序的全部工作。每当我们在 VSCode 中保存项目时，Fuse 会自动热加载预览。我们正在处理两个文件:`MainView.ux`和`CryptoTracker.unoproj`。

`MainView.ux`是应用程序的入口和核心，而`.unoproj`文件列出了应用程序的所有依赖项。

如你所见，我们正在使用一种类似 HTML 的结构，叫做 UX 标记。你放在`<App></App>`标签之间的所有东西将构成你的应用程序的 UI。`<ClientPanel>`补偿屏幕键盘、状态栏和其他特定于操作系统的元素在屏幕顶部和底部占据的空间。

`<StackPanel>`默认情况下垂直堆叠子节点。如果我们去掉`StackPanel`，两个文本会相互重叠。`<Text>`是 Fuse 的原始元素之一，帮助我们渲染文本。

原始元素是更复杂的视觉元素的基本构件。这些图元包括文本、矩形、圆形、图像和视频。我们稍后将在我们的密码追踪器中使用其中的一些，所以请保持关注。

## 用 JavaScript 添加业务逻辑

要在我们的应用程序中使用 JavaScript，我们所要做的就是将 JavaScript 代码包装在一个`<JavaScript></JavaScript>`块中:

```
<App Background="#F7F7F8">
    <ClientPanel>
        <StackPanel Alignment="Center">
            <Text FontSize="20">Javascript Observable Example</Text>
            <Text FontSize="15" Color="Blue" Margin="0,20,0,0">Normal Counter</Text>
            <Text Value="{normalCounter}" Clicked="{increaseNormalCounter}" Alignment="Center" Margin="0,10,0,0" />
            <Text FontSize="15" Color="Purple" Margin="0,20,0,0">Observable Counter</Text>
            <Text Value="{observableCounter}" Clicked="{increaseObservableCounter}" Alignment="Center" Margin="0,10,0,0" />
        </StackPanel>
    </ClientPanel>

    <JavaScript>
        var Observable = require("FuseJS/Observable");

        // avoid this 👇
        var normalCounter = 1
        function increaseNormalCounter() {
            normalCounter = normalCounter + 1
        }

        // do this to have reactive data 👇
        var observableCounter = Observable(1)
        function increaseObservableCounter() {
            observableCounter.value = observableCounter.value + 1
        }

        module.exports = {
            normalCounter,
            observableCounter,
            increaseObservableCounter,
            increaseNormalCounter
        }
    </JavaScript>
</App>

```

现在，这可能看起来很难理解，但实际上，这很简单。上面的例子展示了一个应用程序，它显示了两个初始值为`1`的文本元素。单击时，该值应增加 1。

注意，我们已经设置了两个变量:一个叫做`normalCounter`，另一个叫做`observableCounter`。当我们使用 Fuse 时，我们希望使用 Fuse 自己的[可观察 API](https://fuseopen.com/docs/fusejs/observable.html) 来改变我们的 UX 文件中的数据。这样，它将自动观察该值的动态变化，并实时更新 UI。

我们还需要确保我们总是`module.export`我们的变量和函数。正如您在下面看到的，只有我们的`observableCounter`在 UI 中得到更新:

![Fuse-studio-javascript-observablecounter](img/c13df33d511800f3f6d05c7cc588cc72.png)

这就是你现在需要的所有业务逻辑。现在，我们将更深入地了解如何在构建项目时使用 JavaScript 和 Fuse。

好了，现在我们知道了基础知识，让我们来制作追踪器。我们走吧。

## 用 JavaScript 和 Fuse 构建 CryptoTracker:创建文件结构

就像我之前提到的，你放在`<App></App>`标签之间的所有东西都将构成你的应用程序的 UI。听起来，当你构建一个完整的应用程序时，它可能会很大，对吗？

Fuse 的伟大之处在于它被设计成尽可能模块化，这样就不会发生这种情况。你可以用任何最适合你的方式来构建你的应用。以下是我们将在项目中使用的结构:

```
CryptoTracker
├── build
├── CryptoTracker.unoproj
├── MainView.ux
└── Pages
│     └── Overview.ux
│     └── Detail.ux
└── Components
│     └── (CryptoCard.ux)   
└── Assets
     └── imgs
        └── logo.png

```

### 设置`MainView.ux`

让我们删除上面的 hello world 代码，并用下面的代码替换它:

```
<App Background="#F7F7F8">
        <ClientPanel>
                 <Router ux:Name="router" /> 
                 <Navigator DefaultPath="Overview">
                         <Overview ux:Template="Overview" router="router" />
                         <Detail ux:Template="Detail" router="router"/>
                 </Navigator>
        </ClientPanel>
</App>

```

在我们的项目中，我们希望能够从概览页面导航到详细页面。为了告诉 Fuse 我们想要在页面之间显示和导航，我们需要结合使用`<Navigator>`和`<Router>`标签。

导航器需要模板，而不是其子节点的实例。通过定义`ux:Template`属性，我们可以告诉导航器使用我们的概览页面作为`DefaultPath`。每当我们启动应用程序时，导航器将默认显示概览页面。

现在我们使用了导航器和模板，是时候告诉导航器我们想要导航哪个页面了。这就是`<Router>`的用武之地！路由器管理路由，包括指定我们将在应用中导航到哪里，以及实际将我们带到哪里。

更具体地说，路由器将使用给定的路线在我们的应用程序中导航，这决定了我们想要导航到的某种“目标”，并可能包括一些附加数据。

路由器还可以记录我们以前去过的路线的历史，如果我们愿意，还可以再次导航到那里。我们给它一个`ux:Name`以便在我们的页面中引用它。在 web 开发中，可以把`ux:Name`看作一个类似于 CSS id 或类的惟一标识符。

### 创建我们的第一页:`overview.ux`

首先，让我们在下面添加标题为“CryptoTracker”的徽标:

```
<Page ux:Class="Overview">
  <Router ux:Dependency="router" />
    <DockPanel>
        <StackPanel Dock="Top" Margin="0,50,0,0">
           <Image Width="60" Alignment="Center" File="../Assets/imgs/logo.png" />
           <Text FontSize="25" Alignment="Center" Margin="0,20,0,0" Value="CryptoTracker" />
        </StackPanel>
    </DockPanel>
</Page>

```

我们使用`<DockPanel>`将它的子元素一个接一个地停靠在不同的边上。这将允许我们将我们的标志停靠在页面的顶部，并在其下方添加内容。

本质上，我们避免创建太多的堆栈面板，使代码更加易读。接下来，我们使用原语`<Rectangle>`和`<Circle>`来设计我们的“密码卡”。

现在，我们将显示带有`Value`属性的硬编码数据，而不是将它包装在一个`<Text>`标记中:

![fuse open javascript cryptotracker](img/203096256fa63ced6cfa983a3cc004f0.png)

### 用 Fuse 和 JavaScript 显示数据列表

现在，我们在应用程序中只显示一个“CryptoCard”。你可能想知道我们如何使用 UX 建立一个列表？答案是`Each`类。

`Each`是一个类，可以用来为数组中的每一项复制一个对象。`Each`有一个属性叫做`Items`，我们可以将它绑定到一个数组。然后，它将对数组中的每个项目复制一次它所拥有的任何子元素。

首先，让我们在一个`<JavaScript>`块中创建一个硬编码的可观察数组，并将其导出为一个常量，这样我们就可以在我们的 UX 中使用它。请注意，JavaScript 块必须放在`<Page</Page>`块内。

```
<JavaScript>
        var Observable = require("FuseJS/Observable");

        const cryptocurrencies = Observable(
            {symbol: "BTC", name: "Bitcoin", price_usd: 38000},
            {symbol: "ETH", name: "Ethereum", price_usd: 12000},
            {symbol: "USDT", name: "Tether", price_usd: 1}

        );
        module.exports = {
            cryptocurrencies
        }
  </JavaScript>
</Page>

```

接下来，让我们在 UX 中使用数组的数据。首先，我们将使用`<Each>`标签包装矩形，并通过使用 items 属性`Items="{cryptocurrencies}"`将其传递给我们的数组。然后，我们将用我们定义的对象的键名替换硬编码的文本值属性。

我们将使用`<Text Value="{price_usd}" />`而不是`<Text Value="$38000" />`来动态显示价格。最后，我们给父代`StackPanel`一个`ItemSpacing="20"`，这样我们的密码卡之间就有了一个很好的边界。

```
<StackPanel Margin="0,50,0,0" ItemSpacing="20">
    <Each Items="{cryptocurrencies}">
```

![fuse open javascript cryptotracker text](img/54338fbf713d8ba0545468e36d375a71.png)

厉害！看到我们的代码如此清晰简洁是不是很棒？

### 从 CoinMarketCap API 获取数据

接下来，让我们从 CoinMarketCap API 获取一些实际数据并显示出来:

```
<JavaScript>
        var Observable = require("FuseJS/Observable");

        const API_KEY = "XXX-YOUR-API-KEY-YYYY"  
        var cryptocurrencies = Observable();

        function cryptocurrency(item) {
            this.symbol = item.symbol
            this.name = item.name
            this.price_usd = item.quote.USD.price.toFixed(2)
        }

        fetch(`https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest?CMC_PRO_API_KEY=${API_KEY}`)
        .then(function(response) { return response.json(); })
        .then(function(responseObject) {
            const data = responseObject.data
            for (var i in data) {
              cryptocurrencies.add(new cryptocurrency(data[i]))
            }
        });

        module.exports = {
            cryptocurrencies
        }
</JavaScript>

```

首先，我们将`cryptocurrencies`变量声明为一个空的可观察变量。然后，我们获取 API，并遍历返回的 JSON 结果。在`for loop`中，我们使用 Fuse 的可观察 API 列表操作符`add()`将我们的货币添加到可观察加密货币列表中。

我们使用这个特定的 API 而不是典型的`push()`来确保我们的列表是反应性的，并且在我们的 UX 中得到更新。就是这样。

现在，让我们将我们的密码卡`StackPanel`包装在`<ScrollView>`中。这允许我们滚动浏览由`Each`类显示的所有元素。

![fuse open javascript cryptotracker](img/3d317877b59feda763afba4affab8c7b.png)

如果您在预览中看不到更改，请在 Mac 上按`CMD+SHIFT+R`或在 Windows 上按`F6`来手动强制重新编译。

### 导航至`Detail.ux`页面

接下来，让我们设置一个函数，以便导航到我们的`Detail.ux`页面:

```
function goToDetail(arg) {
    const overviewData = arg.data
    router.push("detail", overviewData)
}

```

别忘了加到我们的`module.exports`里。这就是从我们的概览页面导航到详细页面所需的全部内容。接下来，我们想给我们的“CryptoCard”一个`Clicked`属性，这样一旦卡片被点击，我们就可以将用户发送到详细页面:

这将导致以下结果:

![](img/4630bf14034870a4a3c595965ed4abe2.png)

在我们继续之前，让我们考虑一下如何使我们的代码更简洁。

### 组件化以提高可读性

我们希望保持我们的代码库简洁和模块化。所以，一看到机会就要组件化。我们的“密码卡”是这方面的完美候选。

为此，我们应该使用属性`ux:Class`。每当我们想要创建一个可重用的组件时，我们都会用到它。虽然您可以在代码库中间使用一个`ux:Class`，但是最好的做法是将每个`ux:Class`拆分到一个单独的文件中。

我们已经用`<Page ux:Class="Overview">`和`<Page ux:Class="Detail">`实现了我们的两个页面。为了教程的简单，我们将跳过这一部分，但是我强烈建议阅读更多关于[组件化的内容。](https://fuseopen.com/docs/componentization.html)

### 在`Detail.ux`上显示硬币的数据

我继续用详细视图的基本结构代码替换了详细页面的虚拟代码。现在，您应该熟悉语法了:

```
<Page ux:Class="Detail">
  <Router ux:Dependency="router" />
    <DockPanel>
        <StackPanel Dock="Top" Margin="0,50,0,0">
           <Image Width="60" Alignment="Center" Url="{logoUrl}" />
           <Text FontSize="25" Alignment="Center" Margin="0,20,0,0" Value="{name}" />
           <Text Value="${price_usd}" Alignment="Center" FontSize="18" Margin="0,10,0,0" Color="#1DDAB8"  />
        </StackPanel>

        <StackPanel Margin="0,30,0,0">
           <Rectangle Color="White" Width="90%"  Height="100%" Padding="25,25,25,25" CornerRadius="12">
              <DropShadow Size="8" Distance="4" Spread="0.03" Color="#DEDEDF" Angle="90" />          
              <Text Value="{description}" TextWrapping="Wrap" />
          </Rectangle>
        </StackPanel>
    </DockPanel>
</Page>

```

我们的下一个目标是检索从 overview.ux 页面推送的数据，并使用我们的`overviewData`的`symbol`值来获取所选加密货币的一些元数据。我们要显示标志，名称，价格和描述。我们将通过添加一些业务逻辑到带有`<JavaScript>`标签的 UX 页面来实现这一点:

```
<JavaScript>
     var Observable = require("FuseJS/Observable");

     const API_KEY = "XXX-YOUR-API-KEY-YYYY"  

     var name = Observable()
     var price_usd = Observable()
     var logoUrl = Observable()
     var description = Observable()

     this.Parameter.onValueChanged(module, function(param){
      // display price we get from already fetched overviewData
      name.value = param.name
      price_usd.value = param.price_usd
      console.log(JSON.stringify(param))
      // fetch description info based on symbol from already fetched overviewData
      fetch(`https://pro-api.coinmarketcap.com/v1/cryptocurrency/info?symbol=${param.symbol}&CMC_PRO_API_KEY=${API_KEY}`)
      .then(function(response) { return response.json(); })
      .then(function(responseObject) {
              const data = responseObject.data[param.symbol]
              logoUrl.value = data.logo
              description.value = data.description
           });
      });

     module.exports = {
       name,
       price_usd,
       logoUrl,
       description
     }

  </JavaScript>
</Page> // don't forget to place the JavaScript tag inside the Page tag

```

神奇的事情发生在`this.Paramater.onValueChanged()`事件监听器内部。这种方法允许我们监听路由器传递给我们的详细页面的数据。

![fuse open javascript cryptocurrency event listener](img/7d63144ee097469d0219f91e18760588.png)

### 导航回概览页面

最后，我们希望能够导航回我们的概述页面。我们只需要在 JavaScript 标签中添加
`function goBack() {router.goBack()}`，导出它，并在我们的 UX 代码中添加一个“返回概览”按钮。

还有一个功能我想介绍一下:熔断手势`<WhilePressed>`。这允许我们在一个元素被按下时改变我们的 UX。在我们的例子中，我们将比例增加 10 %,并将文本颜色改为蓝色:

```
<Text Clicked="{goBack}" Name="backButton" Alignment="Center" Margin="0,30,0,0" Value="👈  Back to Overview">
    <WhilePressed>
        <Scale Factor="1.1" Duration="0.1"/>
        <Change backButton.Color="#3417A6" />
    </WhilePressed>
</Text>

```

![fuse open javascript whilepressed](img/0310241b19c35fb0a27f32bad1c9f611.png)

太棒了，你知道吗？我们已经到达了我们的密码追踪器教程的结尾。祝贺你，你做得很好！让我们想想接下来的步骤。

## 在 Fuse 中创建应用程序的后续步骤

下一步是用 Fuse Preview 应用程序(iOS 或 Android)检查你的应用程序在手机上的外观，这是一个独立的应用程序，是迄今为止预览项目的最简单方式，不需要安装 Xcode 或 Android SDKs。

满意后，你应该通过 XCode 或 Android Studio 检查你编译的应用程序。然后，剩下的就是[导出、签名并上传](https://fuseopen.com/docs/basics/preview-and-export.html)你的应用到苹果应用或谷歌 Play 商店。

## 使用 Fuse 和 JavaScript 时的常见问题

#### 1.我可以使用 npm 包吗？

是的，你可以。只要他们没有使用任何浏览器或系统原生 API，他们应该工作。

#### 2.设计是否具有响应性，例如，它能适应不同的布局吗，比如 iPad？

是的，Fuse 有一个内置的[响应布局系统](https://fuseopen.com/docs/layout/responsive-layout.html)，您可以使用它根据各种设备尺寸来调整视图。

## 结论

恭喜你！在本教程中，您已经成功地使用 Fuse 构建了您的第一个移动应用程序。更酷的是，你创造了一个可以向你的朋友炫耀的密码追踪器。我们已经使用 Fuse 的标记语言 UX 来构建我们的 UI，并基于 Fuse 的可观察 API 使用 JavaScript 来添加动态数据。

我们今天所讨论的只是 Fuse 所能实现的一些皮毛。我强烈推荐查看他们的[完整文档](https://fuseopen.com/docs/)和[示例](https://fuseopen.com/examples/)。享受构建精彩应用的乐趣！

你可以在 [GitHub](https://github.com/rylax/fuseopen-CryptoTracker/tree/main) 找到完整的源代码。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)