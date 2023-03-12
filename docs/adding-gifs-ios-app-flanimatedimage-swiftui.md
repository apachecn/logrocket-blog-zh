# 使用 FLAminatedImage 和 SwiftUI - LogRocket Blog 将 gif 添加到 iOS 应用程序

> 原文：<https://blog.logrocket.com/adding-gifs-ios-app-flanimatedimage-swiftui/>

为了响应消费者对使用表情符号和 gif 进行交流的兴趣，越来越多的公司将动画 gif 融入到他们的电子邮件活动、网站和移动应用程序中，以增加参与度和销售额。

图形交换格式文件是按顺序播放的图像的集合，因此它们看起来是移动的。gif 可用于共享演示、突出产品功能或变化、说明使用案例或展示品牌个性。

许多流行的聊天应用程序，如 iMessage 和 WhatsApp，以及社交平台，如 Reddit 或 Twitter，都支持发送和接收 gif。那么，iOS 应用呢？在撰写本文时，SwiftUI 或 UIKit 中还没有使用 gif 的原生内置支持。

为 iOS 构建一个高效的 GIF 图像加载器需要花费大量的时间和精力，但幸运的是，一些第三方框架是高性能的，可以显示 GIF 而没有任何帧延迟。

在本文中，我们将演示如何使用流行的 GIF 库，Flipboard 的 [FLAnimatedImage](https://github.com/Flipboard/FLAnimatedImage) ，只需几行代码就可以将 GIF 添加到您的 iOS 应用程序中。在本文的演示部分，我们将利用来自 [GIPHY](https://giphy.com) 的 gif，这是一个提供各种动画 gif 的流行数据库。

让我们开始吧，学习如何在我们的应用程序中包含一些令人惊叹的 gif！

*向前跳转:*

## 安装 FLAnimatedImage

以下三种依赖关系管理器中的任何一种都可以用于将 FLAnimatedImage 添加到项目中:

*   可可普斯
*   迦太基
*   Swift 软件包管理器

### 椰子足类

要使用 CocoaPods 将 FLAnimatedImage 添加到项目中，请将以下代码添加到 Podfile 中:

```
pod 'FLAnimatedImage'

```

然后，转到您终端的项目目录并安装 pod，如下所示:

```
pod install

```

### 迦太基

要使用 Carthage 将 FLAnimatedImage 添加到项目中，请在 Cartfile 中添加以下代码:

```
github "Flipboard/FLAnimatedImage"

```

然后，要仅更新该库，请转到终端的项目目录并运行以下命令:

```
carthage update FLAnimatedImage

```

### Swift 软件包管理器

要使用 Swift Package Manager 向项目添加 FLAnimatedImage，请打开 Xcode，进入菜单栏，选择**文件>添加包**。接下来，在项目 URL 字段中粘贴以下链接:

```
https://github.com/Flipboard/FLAnimatedImage

```

然后，点击下一个的**,选择需要添加库的项目目标。点击 continue，您已经将`FLAnimatedImage`添加到项目中了！**

## 将 FLAnimatedImage 与 Objective-C 一起使用

[GitHub repo](https://github.com/Flipboard/FLAnimatedImage) 中 FLAnimatedImage 的示例是用 Objective-C 编写的:

```
#import "FLAnimatedImage.h"

FLAnimatedImage *image = [FLAnimatedImage animatedImageWithGIFData:[NSData dataWithContentsOfURL:[NSURL URLWithString:@"https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_%28large%29.gif"]]];
FLAnimatedImageView *imageView = [[FLAnimatedImageView alloc] init];
imageView.animatedImage = image;
imageView.frame = CGRectMake(0.0, 0.0, 100.0, 100.0);
[self.view addSubview:imageView];

```

我们希望在 SwiftUI 框架中使用 FLAnimatedImage，但是在这样做之前，我们应该了解这个库的两个主要类:

*   `FLAnimatedImage`
*   `FLAnimatedImageView`

### `FLAnimatedImage`阶级

`FLAnimatedImage`是一个帮助以表演的方式传递帧的类。我们用它来设置`FLAnimatedImageView`类的图像属性。

为了加载 GIF 图像，我们将 GIF 转换成一个`Data`值类型。`FLAnimatedImage`有一个接受这个`Data`的初始化器:

```
convenience init(animatedGIFData data: Data!) 

```

在创建了类型为`FLAnimatedImage`的图像实例后，我们可以用它来设置`FLAnimatedImageView`的图像属性。

```
imageView.animatedImage

```

### `FLAnimatedImageView`阶级

`FLAnimatedImageView`是`UIImageView`的子类，取一个`FLAnimatedImage`。

```
class FLAnimatedImageView

```

`FLAnimatedImageView`自动播放视图层次中的 GIF，并在移除时停止播放。我们可以通过访问`animatedImage`属性来设置动画图像。

## 结合 Swift 使用 FLAnimatedImage

既然我们已经熟悉了 FLAnimatedImage 类及其用法，我们可以将上面的 Objective-C 示例翻译成 Swift，如下所示:

```
let url = URL(string: "https://upload.wikimedia.org/wikipedia/commons/2/2c/Rotating_earth_%28large%29.gif")!

let data = try! Data(contentsOf: url)

/// Creating an animated image from the given animated GIF data
let animatedImage = FLAnimatedImage(animatedGIFData: data)

/// Creating the image view 
let imageView = FLAnimatedImageView()

/// Setting the animated image to the image view
imageView.animatedImage = animatedImage
imageView.frame = CGRect(x: 0, y: 0, width: 100, height: 100)

view.addSubview(imageView)

```

这个例子与 UIKit 相关。将 FLAnimatedImage 与 UIKit 一起使用非常容易，因为它是原生的。然而，要在 SwiftUI 中使用 FLAnimatedImage，我们必须利用`UIViewRepresentable`创建一个自定义视图，这是一个 UIKit 视图的包装器，我们使用它将它集成到 SwiftUI 视图层次结构中。

因此，让我们创建自定义视图来简化 FLAnimatedImage 的工作！

### 吉夫检视

我们将在 SwiftUI 中创建一个名为`GIFView`的自定义视图，它帮助我们从本地资产和远程 URL 加载 gif。

在创建定制视图之前，让我们创建一个定义两种情况的枚举`URLType`:

*   `name`:本地文件的名称
*   `url`:必须从服务器获取的 GIF 的 URL

```
enum URLType {
  case name(String)
  case url(URL)

  var url: URL? {
    switch self {
      case .name(let name):
        return Bundle.main.url(forResource: name, withExtension: "gif")
      case .url(let remoteURL):
        return remoteURL
    }
  }
}
```

在上面的代码中，我们可以看到如果我们同时提供远程 URL 和 GIF 名称，还有一个计算属性`url`返回本地 URL。

我们创建一个新文件，`GIFView.swift`。在这个文件中，我们将导入`FLAnimatedImage`库:

```
import FLAnimatedImage

```

接下来，我们创建一个符合`UIViewRepresentable`协议的`struct`，GIFView。这个结构有一个接受 URL 类型的初始化器:

```
struct GIFView: UIViewRepresentable {
  private var type: URLType

  init(type: URLType) {
    self.type = type
  }
}

```

然后，我们添加两个闭包，`FLAnimatedImageView`和`UIActivityIndicatorView`的一个实例，在 GIF 加载时显示一个活动指示器:

```
private let imageView: FLAnimatedImageView = {
  let imageView = FLAnimatedImageView()
  imageView.translatesAutoresizingMaskIntoConstraints = false
  imageView.layer.cornerRadius = 24
  imageView.layer.masksToBounds = true
  return imageView
}()

private let activityIndicator: UIActivityIndicatorView = {
  let activityIndicator = UIActivityIndicatorView()
  activityIndicator.translatesAutoresizingMaskIntoConstraints = false
  return activityIndicator
}()

```

在上面的代码中，我们为`FLAnimatedImageView`的圆角半径指定了一个值`24`，但是我们可以按照自己的意愿定制这个值。

`UIViewRepresentable`协议有两个必须实现的必需方法。第一个是`makeUIView(context:)`，创建视图对象`UIView`，并配置初始状态。第二个是`updateUIView(_:context:)`，更新指定视图的状态。

在`makeUIView(context:)`方法中，我们:

*   创建一个`UIView`
*   将视图`makeUIView(context:)`和`updateUIView(_:context:)`作为子视图添加到`UIView`
*   激活必要的约束
*   返回`UIView`

下面是`makeUIView(context:)`方法的代码:

```
func makeUIView(context: Context) -> UIView {
  let view = UIView(frame: .zero)

  view.addSubview(activityIndicator)
  view.addSubview(imageView)

  imageView.heightAnchor.constraint(equalTo: view.heightAnchor).isActive = true
  imageView.widthAnchor.constraint(equalTo: view.widthAnchor).isActive = true

  activityIndicator.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
  activityIndicator.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true

  return view
}

```

在`updateUIView(_:context:)`方法中，我们:

*   开始制作活动指示器的动画
*   检查 URL 是否是可选的；如果它是可选的，我们从方法返回
*   创建一个包含 URL 内容的实例`Data`
*   创建一个传递动画 GIF 数据的实例`FLAnimatedImage`
*   停止活动指示器的动画，并将`FLAnimatedView`的动画图像属性设置为获取的图像

下面是`updateUIView(_:context:)`方法的代码:

```
func updateUIView(_ uiView: UIView, context: Context) {
  activityIndicator.startAnimating()
  guard let url = type.url else { return }

  DispatchQueue.global().async {
    if let data = try? Data(contentsOf: url) {
      let image = FLAnimatedImage(animatedGIFData: data)

      DispatchQueue.main.async {
        activityIndicator.stopAnimating()
        imageView.animatedImage = image
      }
    }
  }
}

```

加载动画 GIF 日期需要时间，所以我们在后台的并发线程上运行它，以避免阻塞 UI。然后，我们在主线程上设置图像。

下面是`GIFView`的最终代码:

```
import SwiftUI
import FLAnimatedImage

struct GIFView: UIViewRepresentable {
  private var type: URLType

  init(type: URLType) {
    self.type = type
  }

  private let imageView: FLAnimatedImageView = {
    let imageView = FLAnimatedImageView()
    imageView.translatesAutoresizingMaskIntoConstraints = false
    imageView.layer.cornerRadius = 24
    imageView.layer.masksToBounds = true
    return imageView
  }()

  private let activityIndicator: UIActivityIndicatorView = {
    let activityIndicator = UIActivityIndicatorView()
    activityIndicator.translatesAutoresizingMaskIntoConstraints = false
    return activityIndicator
  }()
}

extension GIFView {
  func makeUIView(context: Context) -> UIView {
    let view = UIView(frame: .zero)

    view.addSubview(activityIndicator)
    view.addSubview(imageView)

    imageView.heightAnchor.constraint(equalTo: view.heightAnchor).isActive = true
    imageView.widthAnchor.constraint(equalTo: view.widthAnchor).isActive = true

    activityIndicator.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    activityIndicator.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true

    return view
  }

  func updateUIView(_ uiView: UIView, context: Context) {
    activityIndicator.startAnimating()
    guard let url = type.url else { return }

    DispatchQueue.global().async {
      if let data = try? Data(contentsOf: url) {
        let image = FLAnimatedImage(animatedGIFData: data)

        DispatchQueue.main.async {
          activityIndicator.stopAnimating()
          imageView.animatedImage = image
        }
      }
    }
  }
}

```

现在我们有了在 SwiftUI 中使用`FLAnimatedImage`的视图，在 SwiftUI 中使用它就像在 UIKit 中一样容易。

让我们探索两个简单的例子，以及一个更复杂的真实世界的例子。

## 简单的 GIF 演示:FLAnimatedImage 和 SwiftUI

我们将从 assets 文件夹中的一个简单的 GIF 示例开始。首先，[下载](https://media.giphy.com/media/Dh5q0sShxgp13DwrvG/giphy.gif)GIF，`happy-work-from-home`，我们将在这个例子中使用。

我们使用前面描述的方法创建`GIFView`。接下来，我们要做的就是传入 GIF 的名称，就像这样:

```
struct ContentView: View {
  var body: some View {
    GIFView(type: .name("happy-work-from-home"))
      .frame(maxHeight: 300)
      .padding()
  }
}

```

在模拟器上运行这段代码为我们提供了一个动画 GIF:

![No Idea GIF](img/5e01c725efe59a5b9889a9f8e65a7742.png)

下面是另一个使用相同 GIF 的例子，但是是从远程 URL 获取的:

```
struct ContentView: View {
  var body: some View {
    GIFView(type: .url(URL(string: "https://media.giphy.com/media/Dh5q0sShxgp13DwrvG/giphy.gif")!))
      .frame(maxHeight: 300)
      .padding()
  }
}

```

如果我们在模拟器上运行这段代码，我们会看到一个动画 GIF。请注意，当提取 GIF 时，屏幕会显示一个活动指示器:

![Activity Indicator](img/111adff175a78be7c4552743bfcf345f.png)

现在，让我们来看一个更复杂的真实世界的例子！

## 复杂、真实的 GIF 演示

为了充分利用 FLAnimatedImage，让我们探索一个同时加载许多 gif 的例子。这个例子将通过显示和平滑滚动大量的 gif 来展示这个框架在内存压力下的性能。

GIPHY 是 gif 的最大市场。它还有一个开发者程序，允许我们使用提供的 API 获取他们的数据。我们将使用它的一个 API 来获取趋势 gif 并在列表中显示它们。

首先，在这里创建一个 GIPHY 帐户[。登录后，点击**新建 App** ，选择 **API** 。然后，输入您的应用程序名称和描述，并确认协议。接下来，点击**创建应用**按钮，并记下 **API 键**。](https://developers.giphy.com/)

趋势端点返回当天最相关和最吸引人的内容的列表。看起来是这样的:

```
http://api.giphy.com/v1/gifs/trending 

```

对于身份验证，我们将 API 密钥作为参数传递。我们还可以通过使用`limit`参数和`offset`来获得下一组响应，从而限制一个响应中 gif 的数量。

端点返回一个巨大的 JSON 文件，但是我们将对它进行删减，以满足我们的需求。为此，创建另一个文件`GIFs.swift`，并添加以下代码:

```
import Foundation

struct GIFs: Codable {
  let data: [GIF]
}

struct GIF: Codable, Identifiable {
  let id: String
  let title: String
  let images: Images
}

struct Images: Codable {
  let original: GIFURL
}

struct GIFURL: Codable {
  let url: String
}

```

端点返回一个数组`GIF`，每个`GIF`都有一个标题和一个图片 URL。

继续创建 UI，我们添加一个存储 gif 并跟踪`offset`的变量。我们的想法是，当我们用拖动刷新手势刷新屏幕时，它会获取下一批数据:

```
struct ContentView: View {
  @State private var gifs: [GIF] = []
  @State private var offset = 0
}

```

接下来，我们将添加一个从趋势端点获取 GIF 数据的方法:

```
extension ContentView {
  private func fetchGIFs() async {
    do {
      try await fetchGIFData(offset: offset)
    } catch {
      print(error)
    }
  }

  private func fetchGIFData(for limit: Int = 10, offset: Int) async throws {
    var components = URLComponents()
    components.scheme = "https"
    components.host = "api.giphy.com"
    components.path = "/v1/gifs/trending"

    components.queryItems = [
      .init(name: "api_key", value: "<API KEY>"), // <-- ADD THE API KEY HERE
      .init(name: "limit", value: "\(limit)"),
      .init(name: "offset", value: "\(offset)")
    ]

    guard let url = components.url else {
      throw URLError(.badURL)
    }

    let (data, _) = try await URLSession.shared.data(from: url)
    gifs = try JSONDecoder().decode(GIFs.self, from: data).data
  }
}

```

上述代码执行以下操作:

*   为趋势端点创建 URL，并为`api_key`、`limit`和`offset`添加查询参数
*   从 URL 获取数据，并使用`GIFs`结构对其进行解码
*   将数据设置到变量`gifs`

对于 UI，我们有一个加载到数组`gifs`上的列表，并在接受 URL 的`GIFView`中显示各个 gif:

```
extension ContentView {
  var body: some View {
    NavigationView {
      if gifs.isEmpty {
        VStack(spacing: 10) {
          ProgressView()
          Text("Loading your favorite GIFs...")
        }
      } else {
        List(gifs) { gif in
          if let url = URL(string: gif.images.original.url) {
            GIFView(type: .url(url))
              .frame(minHeight: 200)
              .listRowSeparator(.hidden)
          }
        }
        .listStyle(.plain)
        .navigationTitle("GIPHY")
      }
    }
    .navigationViewStyle(.stack)
    .task {
      await fetchGIFs()
    }
    .refreshable {
      offset += 10
      await fetchGIFs()
    }
  }
}

```

当通过下拉屏幕来刷新视图时，它将`offset`增加`10`，获取新的 gif，并更新屏幕。

下面是一个屏幕记录，显示了视图刷新时发生的情况:

![Scrolling Giphy and Refreshing GIFs](img/9ba8e63b263e73ca1427742a8d39ae55.png)

这样，我们就创建了一个完整的高性能应用程序，显示来自 GIPHY 的趋势 gif！

## 结论

我们必须投入大量的精力来创造一个处理 gif 的表演动画。或者，我们可以利用现有的 FLAnimatedImage 框架，该框架被许多流行的应用程序使用，如脸书、Instagram、Slack 和 Telegram。

通过自定义`GIFView`，我们只需要 SwiftUI 中的一行代码就可以在我们的应用程序中显示令人惊叹的 gif！体验和编码的乐趣！动画 gif 只是增强你的 iOS 应用的 UX 和 UI T2 的众多方法之一。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)