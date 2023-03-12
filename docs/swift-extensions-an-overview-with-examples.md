# Swift 扩展:示例概述- LogRocket 博客

> 原文：<https://blog.logrocket.com/swift-extensions-an-overview-with-examples/>

在本教程中，我们将带您了解 Swift 扩展的基本概况。我们将通过构建一个简单的锻炼追踪应用程序来演示 [Swift extensions](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html) 如何工作。

我们将重点关注以下内容:

## 什么是 Swift 扩展？

扩展扩展了现有的 Swift 命名类型，即结构、类、枚举和协议，因此您可以向它们添加更多功能。这使您能够将自己的代码插入到现有的系统代码中，否则您将无法访问这些代码，例如 [Foundation framework](https://get.foundation/) 。您还可以使用扩展来扩展您自己的代码，并保持代码的整洁。

## 在 Swift 中创建扩展

创建扩展类似于在 Swift 中创建命名类型。当创建一个扩展时，在名称前添加单词`extension`。

```
extension SomeNamedType {
  // Extending SomeNamedType, and adding new
  // functionality to it.
}

```

## 类型属性

您可以扩展特定的命名类型，添加新的计算实例，并为其添加类型属性。例如，你可以扩展`Color`来添加你自己的颜色。假设我们的应用程序有一个品牌颜色，我们想在任何地方使用。我们可以通过经由`extension`扩展`Color`来创建一个常量类型属性`brand`。

我们的应用程序还在设置屏幕中为行的背景使用自定义颜色。为此，我们将定义一个变量类型属性，根据系统外观调整颜色。

```
extension Color {
  static let brand = Color(red: 75/255, green: 0, blue: 130/255)

  static var settingsBackground: Color {
    Color(UIColor { (trait) -> UIColor in
      return trait.userInterfaceStyle == .dark ? .systemGray5 : .systemGray6
    })
  }
}

```

你可以这样使用它:

```
struct SettingsRow: View {
  var title: String

  var body: some View {
    HStack(spacing: 8) {
      Text(title)
        .foregroundColor(.brand)

      Spacer()

      Image(systemName: "chevron.right")
    }
    .foregroundColor(.settingsBackground)
  }
}

```

## 突变方法

正如简介中提到的，即使您无法访问原始代码库，也可以扩展类型来添加自己的功能。例如，如果您想给`Double`添加一个函数，您可以在 struct 上编写一个扩展，而无需访问`Double` struct 的原始代码。

在我们的应用程序中，我们从 HealthKit 获取卡路里数据，但该函数以`Double`类型返回数据。我们希望显示四舍五入到小数点后一位的数据。我们可以这样在`Double`上写一个`extension`:

```
extension Double {
    mutating func roundTo(places: Int) {
        let divisor = pow(10.0, Double(places))
        self = (self * divisor).rounded() / divisor
    }
}

```

让我们用这种变异方法来解决我们的问题:

```
var caloriesBurned: Double? = 213.3244

if var calories = caloriesBurned {
    calories.roundTo(places: 1)
    print("\(calories) kcal") /// Prints "213.3 kcal"
}

```

## 分离代码

当我们的类符合协议时，我们通常在同一个类中添加所有的协议方法。例如，我们将添加所有的方法`UICollectionViewDataSource`、`UICollectionViewDelegate`和`UICollectionViewDelegateFlowLayout`。

我们可以使用扩展来分离每个方法。这使得代码更具可读性和可维护性。

```
class ExampleViewController: UIViewController {
    // Add the main code goes here
}

// MARK:- UICollectionViewDataSource
extension ExampleViewController: UICollectionViewDataSource {
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        //
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        //
    }
}

// MARK:- UICollectionViewDelegate
extension ExampleViewController: UICollectionViewDelegate {
    //
}

// MARK:- UICollectionViewDelegateFlowLayout
extension ExampleViewController: UICollectionViewDelegateFlowLayout {
    //
}

```

我们的应用程序使用 Google Sign-In 作为主要的身份验证来源，所以我们需要符合`GIDSignInDelegate`来接收成功登录的更新。我们可以使用扩展来分离所需的代码——你猜对了。

```
import GoogleSignIn

class AuthenticationViewModel: NSObject, ObservableObject {
  /// Main code goes here
}

// MARK:- GIDSignInDelegate
extension AuthenticationViewModel: GIDSignInDelegate {
    func sign(_ signIn: GIDSignIn!, didSignInFor user: GIDGoogleUser!, withError error: Error!) {
        if error == nil {
            // Authentication successful
        } else {
            print(error.debugDescription)
        }
    }
}

```

## SwiftUI 视图的扩展

现在，假设我们想添加一个定制的大标题文本，就像苹果公司在大多数应用程序中使用的标题一样。该文本将表示特定健身程序的日期。我们也想为设置屏幕使用精确的自定义文本。

为了在代码库中的任何地方重用这段代码，我们将通过添加一个`largeTitle(:)`方法来扩展`Text`。

```
extension Text {
    func largeTitle() -> some View {
        self
            .bold()
            .foregroundColor(.primary)
            .font(.largeTitle)
            .frame(maxWidth: .infinity, alignment: .leading)
            .padding(.top, 37)
    }
}

```

现在我们可以在我们的视图上使用这种方法:

```
VStack {
    Text("Settings").largeTitle()
}

```

类似地，假设我们想要创建一个心形按钮来收藏一组锻炼。我们将创建一个在双击时切换心形颜色的`ViewModifier`:

```
struct HeartButtonModifier: ViewModifier {
    @Binding var state: Bool

    func body(content: Content) -> some View {
        content
            .foregroundColor(state ? .red : .secondary)
            .onTapGesture(count: 2) {
                state.toggle()
            }
    }
}

```

现在让我们在`View`上创建一个`extension`，这样我们就可以在我们的视图中使用它:

```
extension View {
    func workoutLiked(state: Binding<Bool>) -> some View {
        self.modifier(HeartButtonModifier(state: state))
    }
}

```

最后，我们将把它作为修饰符添加到`Image`:

```
struct LikeView: View {
    @State private var state = false

    var body: some View {
        Image(systemName: "heart.fill")
            .workoutLiked(state: $state)
    }
}

```

## 向现有类型添加初始值设定项

我们可以使用一个`extension`来添加一个新的自定义初始化器，它接受现有类型的不同参数。

让我们假设你的设计师给你十六进制的颜色，而不是 RGB 值。使用前面添加计算类型属性到`Color`的例子，我们将创建一个接受十六进制值的初始化器。如果我们想用 RGB 值作为整数来制作颜色，我们可以添加另一个初始化器:

```
extension Color {
    init(hex: Int) {
        let red = (hex >> 16) & 0xFF
        let green = (hex >> 8) & 0xFF
        let blue = hex & 0xFF

        self.init(red: red, green: green, blue: blue)
    }

    init(red: Int, green: Int, blue: Int) {
        let red = Double(red) / 255
        let green = Double(green) / 255
        let blue = Double(blue) / 255

        self.init(red: red, green: green, blue: blue, opacity: 1.0)
    }
}

```

我们现在可以将它用作:

```
extension Color {
    static var brand: Color {
        Color(hex: 0x4B0082)
    }

    static var secondaryBrand: Color {
        Color(red: 41, green: 0, blue: 71)
    }
}

```

## 结论

Swift 中的扩展是将您自己的功能添加到您不拥有的类型中的强大方法。此处的扩展概述和示例旨在帮助您理解扩展如何工作，以便您能够在自己的 Swift 项目中实施和使用它们。

对于进一步的高级阅读，我推荐以下来自 Swift 文档的文章:

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)