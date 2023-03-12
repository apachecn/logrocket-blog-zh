# 在 Swift 中掌握 typealias

> 原文：<https://blog.logrocket.com/mastering-typealias-swift/>

别名通常是指一个人已知的一个假定的身份或一个替代名，可能是昵称、笔名、笔名或假名。在编程术语中，别名非常相似。

在 Swift 中，`typealias`是为现有类型提供新名称或别名的函数。这种类型可以是具体类型，如`Double`或定制结构，也可以是复合类型，如元组或复杂的闭包类型。通过为现有类型提供一个别名，`typealias`有助于使我们的代码可读性更强，更易于维护。

这里有一个有趣的事实:在撰写本文时，基金会框架中有 363 个`typealias`！所以，让我们来看看`typealias`的威力，并考虑如何在它的功能和有用性之间取得平衡！

*向前跳转:*

## 句法

在 Swift 中声明新的`typealias`的语法如下:

```
typealias aliasName = existingType

```

注意`aliasName`不是新类型；它引用现有类型。

假设我们的项目有一个`String`形式的消息:

```
var message: String?

```

相反，我们可以用一个`typealias`调用别名为`Message`的`String`:

```
typealias Message = String

```

然后，我们可以在代码库中使用它，就像这样:

```
var message: Message?

```

## 基本示例

下面是一些`typealias`特别有用的基本例子:

### 时间间隔

一个非常基本但广泛使用的例子是用特定别名指定时间间隔，即使类型是`Double`:

```
typealias TimeInterval = Double

```

### 用户标识

另一个例子是当我们为一个用户使用一个唯一的标识符时。我们可以创建一个`typealias`，像这样:

```
public typealias UserID = String

```

然后，每当我们声明一个用户 ID 时，我们可以简单地使用`typealias`名称:

```
public let userID: UserID

var currentUserID: UserID?

func createUser(for id: UserID) { 
/// implementation
}

```

### 得分

严重依赖显示和计算分数的应用程序是`typealias`的绝佳候选。我们可以为分数的类型创建一个`typealias`，就像这样:

```
typealias Score = Double

```

然后，我们可以在任何地方使用`Score`而不是使用`Double`:

```
struct RGBColorScore {
  var red: Score 
  var green: Score
  var blue: Score 

```

或者，在定义变量时使用它:

```
var currentScore: Score

```

或者，将它用作方法的返回类型:

```
func calculateMaximumScore(for score: RGBColorScore) -> Score {
  max(score.red, score.green, score.blue)
} 

```

### 密码

当我们想给更适合上下文的类型起一个合适的名字时,`typealias`是很有帮助的。例如，如果我们在一个与登录和注册相关的屏幕上工作，我们可以为密码创建一个`typealias`:

```
typealias Password = String

```

我们可以对密码执行各种验证检查，并使用`Password`而不是`String`,因为它在这种情况下更适合上下文:

```
func passwordLengthValidation(for password: Password) -> Bool {  
  password.count > 8
}

```

现在，让我们看看一些更高级的场景，在这些场景中`typealias`可以在我们的代码库中派上用场。

## 减少冗长

当处理过于冗长的类型时，您可能会发现使用替代名称会有所帮助。例如，使用不同的数据源是令人惊奇的，但是通用类的名字是冗长的。

在我们的代码库中散布这些冗长的类型会让人头疼。

解决过度冗长的一个方法是使用`typealias`。下面是一个对不同数据源使用`typealias`的例子:

```
typealias DataSource = UICollectionViewDiffableDataSource<Section, Item> 

private var dataSource: DataSource?

```

下面是在我们的代码库中使用它的一个例子:

```
typealias CurationSnapshot = NSDiffableDataSourceSnapshot<CurationCardSection, CurationCardModel>

typealias CurationDataSource = UICollectionViewDiffableDataSource<CurationCardSection, CurationCardModel>

```

如果您使用过 reducer 模式，那么您会对状态、动作和环境参数很熟悉。不断地写这些参数会变得很麻烦。

相反，我们可以使用`typealias`:

```
typealias AppReducer = Reducer<AppState, AppAction, AppEnvironment> 

```

另一个用例是当你在处理一个协议时，你重复使用相同的类型。

例如，假设我们创建了一个使用`Element`的协议`Stack`。所有的函数和变量都必须使用相关的类型作为`Iterator.Element`，而不是`Element`。

相反，我们可以使用`typealias`，就像这样:

```
protocol Stack {
    associatedtype Iterator: IteratorProtocol
    typealias Element = Iterator.Element

    var array: [Element] { get set }

    func push(_ element: Element)
    func pop() -> Element?
    func peak() -> Element?
}

```

## 提高可读性

`typealias`可以提高任何具有长名称的命名类型的可读性；用户定义的类型和基础框架提供的类型。

在 Foundation 框架中可以找到许多长类型名称的实例。例如，Apple 在其 2021 年全球开发者大会(WWDC21)上推出了不同的枚举，用于格式化和解析数值的精度、舍入和小数位数:

```
enum NumberFormatStyleConfiguration 

enum CurrencyFormatStyleConfiguration

```

要访问每个配置，我们需要使用点语法，比如`NumberFormatStyleConfiguration.Grouping`。但是，点语法使得名称更长，并且到处使用更长的名称很麻烦。

相反，我们可以用更短、更明确的名称创建自定义的`typealias`:

```
public typealias NumberGrouping = NumberFormatStyleConfiguration.Grouping

public typealias CurrencyPrecision = CurrencyFormatStyleConfiguration.Precision

```

下面是来自[基金会框架](https://developer.apple.com/documentation/foundation)的许多类似例子中的一些:

```
public typealias EncodingConversionOptions = NSString.EncodingConversionOptions

public typealias EnumerationOptions = NSString.EnumerationOptions

public typealias CompareOptions = NSString.CompareOptions

```

再比如， [Shopify 的 iOS SDK](https://github.com/Shopify/mobile-buy-sdk-ios/blob/62d2bc07c16521875d0b267171b46fd927ab552e/Buy/Generated/Storefront/FulfillmentLineItemConnection.swift) 使用`typealias`来创建更短的名字:

```
public typealias Query = FulfillmentLineItemConnectionQuery 

public typealias Response = FulfillmentLineItemConnection 

```

在第一方框架的许多实例中，我们可以通过在访问特定框架的类型时引入`typealias`来使名称更加简洁。

让我们以苹果的 [MusicKit 框架](https://developer.apple.com/musickit/)为例。它有一个通用的结构，`MusicItemCollection<MusicItemType>`，其中`MusicItemCollection`是音乐项的集合，`MusicItemType`符合`MusicItem`。

为了从 Apple Music 中获取多首匹配特定标识符的歌曲，我们编写了以下方法:

```
func catalogSongs(ids: [MusicItemID]) async throws -> MusicItemCollection<Song> {
    let musicRequest = MusicCatalogResourceRequest<Song>(matching: \.id, memberOf: ids)
    let response = try await musicRequest.response()
    return response.items
}

```

在我们的模型中，我们传递`ids`，然后将该方法返回的`songs`设置为一个变量:

```
var songs: MusicItemCollection<Song>?

songs = try await catalogSongs(ids: ["1109658204", "1508562321"])

```

现在，让我们使用`typealias`为`MusicItemCollection<Song>`创建一个更短的名字`Songs`。

我们可以在任何地方使用这个较短的名称来提高可读性:

```
typealias Songs = MusicItemCollection<Song>

func catalogSongs(ids: [MusicItemID]) async throws -> Songs {
    let musicRequest = MusicCatalogResourceRequest<Song>(matching: \.id, memberOf: ids)
    let response = try await musicRequest.response()
    return response.items
}

var songs: Songs?
songs = try await catalogSongs(ids: ["1109658204", "1508562321"])

/// More examples
var recommendedSongs: Songs?
var recentlyPlayedSongs: Songs?
var frequentlyPlayedSongs: Songs?

```

这种策略可以应用于所有通用结构，使它们更容易阅读和理解:

```
public typealias Artists = MusicItemCollection<Artist>
public typealias Genres = MusicItemCollection<Genre>
public typealias Albums = MusicItemCollection<Album>

```

更短、更简洁的别名提高了代码的可读性！

## 降低复杂性

当处理有几个参数的复杂类型时，我们可以利用`typealias`的力量。例如:

```
typealias QuoteCompletion = (Result<Quote, Error>) -> () 
typealias QuotesCompletion = (Result<[Quote], Error>) -> ()

```

然后，在我们的方法中，我们可以使用更简洁的名称:

```
typealias QuoteID = String

func fetchAllQuotes(completion: @escaping QuotesCompletion) {   
/// implementation
}          

func fetchQuote(for ID: QuoteID, completion: @escaping QuoteCompletion) {  
/// implementation
}

```

现在，让我们看一个使用`typealias`简化的更复杂的例子。下面是一个对类型有约束的通用闭包:

```
typealias Parser<A> = (String) -> [(A, String)] where A: Equatable

```

我们可以这样使用它:

```
func parsing<A>(_ string: String, for parser: Parser<A>) where A: Equatable {
}

```

## 提高清晰度

您可能遇到过您的`class`或`struct`符合许多协议的情况。为了清楚起见，我们可以将一系列协议一致性组合成一个`typealias`，然后在任何地方使用这个别名。

```
typealias CombinedType = FooProtocol & BarProtocol

```

一个经典的例子是当我们让我们的`UIViewController`符合不同的代表时。例如，假设我们有一个呈现为 popover 的控制器，我们希望符合`UIPopoverPresentationControllerDelegate`来获得委托方法。

如果我们有许多控制器，我们可以创建一个`typealias`:

```
typealias PresentableViewController = UIViewController & UIPopoverPresentationControllerDelegate

```

在 MusicKit 框架中，苹果对`typealias`采取了类似的做法。在这种情况下，`MusicTokenProvider`是需要成为`MusicUserTokenProvider`的子类的类型的`typealias`，符合`MusicDeveloperTokenProvider`协议:

```
public typealias MusicTokenProvider = MusicUserTokenProvider & MusicDeveloperTokenProvider

```

组合的另一个例子是当我们希望我们的结构符合`Codable`时。如果我们在 MusicKit 框架中创建一个定制的结构，我们可以通过提供一个`typealias`来使它符合`MusicItem`和`Codable`:

```
public typealias MusicCodableItem = MusicItem & Codable

```

在这里，`Codable`也是一个`typealias`！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们仔细阅读它的声明:

```
typealias Codable = Decodable & Encodable 

```

然后使用它，就像这样:

```
public struct UserMusicItem: MusicCodableItem {
// MusicItem requirements
// Codable requirements
}

```

为了更清楚起见，我们还可以将协议一致性分成不同的扩展:

```
public struct UserMusicItem {}

extension UserMusicItem: MusicItem {
// MusicItem requirements
}

extension UserMusicItem: Decodable {
// Decodable requirements
}

extension UserMusicItem: Encodable {
// Decodable requirements
}

```

如何在代码库中使用`typealias`来保持清晰性和有用性之间的平衡取决于你。

一个类似的例子是使类型符合`Decodable`和`ExpressibleByBooleanLiteral`:

```
typealias DecodableBooleanLiteral = Decodable & ExpressibleByBooleanLiteral

```

## 小心使用`typealias`

既然您对`typealias`如何使您的代码库更具可读性有了更好的理解，那么在任何地方使用这个函数都是很有诱惑力的。然而，不加选择地使用`typealias`也有缺点。

例如，您个人将熟悉在给定项目中为特定类型指定的别名。但是，当整个开发团队在使用`typealias`的项目上工作时，或者当一个新成员加入团队时，问题可能会出现。

更糟糕的是，一些别名可能会让其他开发人员感到困惑。例如，假设使用一个通用的`typealias`作为完成处理程序:

```
typealias Completion = (String?, AnyObject?) -> ()

```

起初，其他开发人员可能不清楚`Completion`是干什么的。

假设您将其重命名为`StringCompletion`:

```
typealias StringCompletion = (String?, AnyObject?) -> ()

```

这更好，但是代码库的新手仍然需要检查参数。

将`typealias`添加到您的代码库时，最好小心谨慎。尽量只在特别需要的地方和最有意义的地方使用。

## 结论

在本文中，我们查看了 Swift 中`typealias`功能的基本和高级示例。`typealias`可用于减少冗长和复杂性，提高可读性和清晰度。`typealias`在处理复杂的闭包类型和符合多种协议的闭包类型时特别强大。

不过，尽管它有很多优点，但最好不要到处介绍`typealias`。如果你的同事不得不查找每个`typealias`的实际类型，这将在上下文切换中浪费宝贵的时间，并且将首先违背`typealias`的目的。

要了解有关 Swift 中的`typealias`的更多信息，请参见[官方文档](https://docs.swift.org/swift-book/ReferenceManual/Declarations.html#ID361)。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)