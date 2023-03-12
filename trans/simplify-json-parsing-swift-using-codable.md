# 使用 cotable-log rocket 博客简化 Swift 中的 JSON 解析

> 原文：<https://blog.logrocket.com/simplify-json-parsing-swift-using-codable/>

作为一名 iOS 开发人员，您的工作可能需要您从服务器检索数据，并将数据上传回服务器。这些数据可能包含指向图像、描述、订阅数据或有关用户是否成功登录的信息的链接。

为此，我们通常使用广泛接受的 JSON (JavaScript 对象表示法)格式。JSON 是一种轻量级的数据交换格式，易于开发人员读写，也易于机器解析和生成。

在本文中，我们将研究使用`Codable`简化 Swift 中的 JSON 解析。我们还将回顾几个实际的 JSON 用例。从简单的 JSON 到嵌套的多态 JSON，很快您将能够使用`Codable`在您的移动应用程序中更容易地解析和生成 JSON！

我们开始吧！

*向前跳转:*

## 了解 JSON

在我们深入到用例之前，重要的是要认识到 JSON 可以分为两种不同的结构:

*   名称/值对的集合，例如:

    ```
    {   "first_name": "Rudrank",   "last_name": "Riyam" }
    ```

*   数组形式的有序值列表，例如:

    ```
    {    "qualifications": [       {          "name": "high_school",          "passed": true       },       {          "name": "bachelors",          "passed": true       },       {          "name": "masters",          "passed": false       }    ] }
    ```

## 用于解码和编码数据的 Swift 协议

Swift 提供了多种协议来帮助我们改变数据的表示方式。

### `Decodable`协议

`Decodable`是一种可以从外部表示中解码自身的类型。

```
protocol Decodable

```

下面是一个简单的 JSON 示例:

```
{
  "name":"rudrank"
}

```

这个 JSON 可以被解码成符合`Decodable`协议的结构:

```
struct Information: Decodable {
  let name: String
}

```

这个协议有一个必需的初始化器`init(from: Decoder)`，它有一个默认的实现。当我们以定制的方式解码 JSON 时，我们可以使用这个初始化器来提供我们的实现。

### `Encodable`协议

`Encodable`是一种可以将自身编码为外部表示的类型。

```
protocol Encodable

```

使用上面的例子，我们可以使用相同的结构并遵循`Encodable`协议将`Information`的对象编码成 JSON:

```
struct Information: Encodable {
  let name: String
}

```

该协议有一个必需的方法`func encode(to: Encoder)`，带有默认实现。当我们以定制的方式编码 JSON 时，我们可以使用这个方法来提供我们的实现。

### `Codable`协议

为了轻松解析 JSON 和类似的格式，比如 XML 或属性列表(PLISTs)，我们可以利用 Swift 4 中引入的`Codable`协议。`Codable`是`Decodable`和`Encodable`协议的`typealias`，[意味着它为现有类型](https://blog.logrocket.com/mastering-typealias-swift/)提供了一个新名称:

```
typealias Codable = Decodable & Encodable

```

`Codable`是一种可以将自身转换成外部表示的类型，其中表示是 JSON 或类似的格式。

Swift 标准库包含已经符合`Codable`的`String`、`Int`、`Double`、`Date`、`Data`、`URL`等类型。如果我们创建一个定制的`struct`、`enum`或`class`，我们可以使它符合`Codable`协议，并且使用已经存在的类型，而不需要实现任何方法。

苹果为我们提供了两个用于解码和编码 JSON 对象的类，`JSONDecoder`和`JSONEncoder`。

### `JSONDecoder`阶级

`JSONDecoder`类从 JSON 对象中解码数据类型的实例。数据类型必须符合`Decodable`协议；它可以是预定义的类型，如`String`、`Double`或`Date`，也可以是定制的类、枚举或结构。

对于这个类，我们通常会使用`decode_:from:)`方法，其定义如下:

```
func decode<T>(_ type: T.Type, from data: Data) throws -> T where T : Decodable

```

该方法以`Data`类型和符合`Decodable`协议的通用类型的形式接受 JSON 响应。将`data`解码成给定的泛型类型后，该方法返回该类型。

让我们再次考虑前面的例子，其中的对象`Information`符合`Decodable`协议和相应的 JSON:

```
struct Information: Decodable {
  let name: String
}

let informationData = """
{
  "name":"rudrank"
}
""".data(using: .utf8)!

```

我们所要做的就是创建一个`JSONDecoder`的实例并调用`decode_:from:)`方法:

```
let decoder = JSONDecoder()
let information = try decoder.decode(Information.self, from: informationData)
print(information.name) // Prints "rudrank"

```

### `JSONEncoder`阶级

我们使用`JSONEncoder`将数据类型的实例编码为 JSON 对象，而不是解码。对于这个类，我们主要使用具有以下定义的`encode(_:)`方法:

```
func encode<T>(_ value: T) throws -> Data where T : Encodable

```

该方法采用一个符合`Encodable`协议的泛型类型，并在**将该类型编码**成数据后返回`Data`。

让我们再看一下上面的例子:

```
struct Information: Encodable {
  let name: String
}

let information = Information(name: "rudrank")

```

我们有一个结构`Information`,我们想把它编码成一个 JSON 响应。当您正在处理 POST 请求并希望将 JSON 作为请求体的资源添加时，以这种方式编码自定义结构或类非常有用。

我们创建了一个`JSONDecoder()`的实例，并使用`encode(_:)`方法对`information`常量进行编码:

```
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted

let data = try encoder.encode(information)

```

为了可视化 JSON 响应，我们从编码数据中创建一个字符串并打印出来:

```
print(String(data: data, encoding: .utf8)!) 
// Prints 
{
  "name":"rudrank"
}

```

本文将主要关注解码和简化 JSON 响应的解析，使之成为我们可以在 iOS 应用程序中轻松使用的结构。一旦您很好地理解了这些解码示例，将类型编码到 JSON 中应该是一件轻而易举的事情。

## 用例

本文中使用的例子来自不同的 [Apple Music API](https://developer.apple.com/documentation/applemusicapi/) 端点，代表了实际的、真实的用例。

### 基本示例

让我们从一个只包含一个 JSON 对象的简单例子开始。如果我们使用 Apple Music API 搜索建议，我们会得到以下响应:

```
{
  "kind":"terms",
  "searchTerm":"the weeknd",
  "displayTerm":"the weeknd"
}

```

为这个对象创建一个`struct`很简单。我们需要类型为`String`的属性`kind`、`searchTerm`和`displayTerm`:

```
struct Suggestion: Codable {
  let kind: String
  let searchTerm: String
  let displayTerm: String
}

```

为了将 JSON 解码成`struct`，我们将使用`JSONDecoder()`:

```
let suggestionResponse = """
{
  "kind":"terms",
  "searchTerm":"the weeknd",
  "displayTerm":"the weeknd"
}
"""

let suggestionData = Data(suggestionResponse.utf8)
let suggestion = try JSONDecoder().decode(Suggestion.self, from: suggestionData)
print(suggestion)

// MARK: - OUTPUT
Suggestion(kind: "terms", searchTerm: "the weeknd", displayTerm: "the weeknd")

```

### 嵌套示例

解码单个 JSON 对象很容易，但是如果一个键包含自己的对象，比如嵌套结构，该怎么办？为了处理这种情况，我们将为嵌套对象创建另一个`struct`，关键属性将是该对象的类型。

如果我们搜索 Apple Music API 流派端点，我们会得到以下响应:

```
{
  "id":"20",
  "type":"genres",
  "attributes":{
    "parentId":"34",
    "name":"Alternative",
    "parentName":"Music"
  }
}

```

您可以看到,`attributes`包含了它们自己的对象，这是一个嵌套 JSON 的例子。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了对此进行解码，我们将创建两个不同的`struct`，一个用于主对象，一个用于嵌套对象:

```
struct Genre: Codable {
  let id: String
  let type: String
  let attributes: Attributes
}

struct Attributes: Codable {
  let parentId: String
  let name: String
  let parentName: String
}

```

为了将嵌套的 JSON 解码成一个`struct`，我们将使用`JSONDecoder()`:

```
let genreResponse = """
{
  "id":"20",
  "type":"genres",
  "attributes":{
    "parentId":"34",
    "name":"Alternative",
    "parentName":"Music"
  }
}
"""

let genreData = Data(genreResponse.utf8)
let genre = try JSONDecoder().decode(Genre.self, from: genreData)

print(genre)

// MARK: - OUTPUT
Genre(id: "20", type: "genres", attributes: Attributes(parentId: "34", name: "Alternative", parentName: "Music"))

```

### 编码键示例

在上面的例子中，我们使用`parentId`作为变量的名称。如果我们想用`parentID`来代替呢？或者，假设我们更喜欢使用`parent`作为变量名，而不是`parentName`？

为了解决这些情况，我们可以在解码时创建备用密钥。我们将创建一个类型为`String`的`enum`,包含所有案例作为键，并符合`CodingKey`协议:

```
struct Attributes: Codable {
  let parentID: String
  let name: String
  let parent: String

  enum CodingKeys: String, CodingKey {
    case parentID = "parentId"
    case name 
    case parent = "parentName"
  }
}

```

然后，我们将编写自定义初始化器来解码数据:

```
extension Attributes {
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)

    parentID = try values.decode(String.self, forKey: .parentID)
    name = try values.decode(String.self, forKey: .name)
    parent = try values.decode(String.self, forKey: .parent)
  }
}

```

### 空值示例

再次考虑上面的例子，让我们假设一个特定的流派没有父流派。在这种情况下，JSON 对象的这些键将为空值，或者这些键将在对象中丢失。

在我们的例子中，没有`parentName`和`parentId`的键:

```
{
  "id":"34",
  "type":"genres",
  "attributes":{
    "name":"Music",
  }
}

```

如果您一直在与 Swift 合作，您应该熟悉[可选的](https://developer.apple.com/documentation/swift/optional)类型。我们可以在这里使用相同的概念，并更新结构`Attributes`以接受`parentName`和`parentId`属性的可选值:

```
struct Attributes: Codable {
  let parentId: String?
  let name: String
  let parentName: String?
}

```

然而，对于有许多结构的情况，这种方法会变得很麻烦。我们可以为解码器编写一个定制的初始化器，并利用`decodeIfPresent`方法，而不是为每个结构打开可选值:

```
struct Attributes: Codable {
  let parentID: String
  let name: String
  let parent: String

  enum CodingKeys: String, CodingKey {
    case parentID = "parentId"
    case name 
    case parent = "parentName"
  }
}

extension Attributes {
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)

    parentID = try values.decodeIfPresent(String.self, forKey: .parentID) ?? ""
    name = try values.decode(String.self, forKey: .name)
    parent = try values.decodeIfPresent(String.self, forKey: .parent) ?? ""
  }
}

```

使用这种技术，我们可以指定一个占位符值，如果响应返回一个丢失的键或空值，将提供这个占位符值。

### 数组示例

JSON 响应可能包含许多不同的对象，但也可能包含一组对象。

让我们回到简单的例子，我们有一个`Suggestion`结构，并将其扩展为一个`suggestions`数组:

```
{
  "results":{
    "suggestions":[
      {
        "kind":"terms",
        "searchTerm":"the weeknd",
        "displayTerm":"the weeknd"
      },
      {
        "kind":"terms",
        "searchTerm":"the weeknd & swedish house mafia",
        "displayTerm":"the weeknd & swedish house mafia"
      },
      {
        "kind":"terms",
        "searchTerm":"weeknd nigth",
        "displayTerm":"weeknd nigth"
      },
      {
        "kind":"terms",
        "searchTerm":"weeknd warriorz",
        "displayTerm":"weeknd warriorz"
      },
      {
        "kind":"terms",
        "searchTerm":"yeyo weeknd",
        "displayTerm":"yeyo weeknd"
      }
    ]
  }
}

```

您可以看到键`suggestions`包含一组类型为`Suggestion`的对象，而不是一个单独的`Suggestion`。为了创建 JSON 数组的结构，我们将创建一个类型为`[Suggestion]`的属性:

```
struct Suggestions: Codable {
  let results: Results

  struct Results: Codable {
    let suggestions: [Suggestion]
  }
}

struct Suggestion: Codable {
  let kind: String
  let searchTerm: String
  let displayTerm: String
}

```

我们将以与前面示例相同的方式解码数据，只是现在`struct`有一个数组属性:

```
let suggestionsResponse = """
{
  "results":{
    "suggestions":[
      {
        "kind":"terms",
        "searchTerm":"the weeknd",
        "displayTerm":"the weeknd"
      }
      /// rest of the JSON
    ]
  }
}
"""

let suggestionsData = Data(suggestionsResponse.utf8)
let suggestions = try JSONDecoder().decode(Suggestions.self, from: suggestionsData)

print(suggestions)

// MARK: - OUTPUT
Suggestions(results: 
Suggestions.Results(suggestions: [
Suggestion(kind: "terms", searchTerm: "the weeknd", displayTerm: "the weeknd"), 
Suggestion(kind: "terms", searchTerm: "the weeknd & swedish house mafia", displayTerm: "the weeknd & swedish house mafia"), 
Suggestion(kind: "terms", searchTerm: "weeknd nigth", displayTerm: "weeknd nigth"), 
Suggestion(kind: "terms", searchTerm: "weeknd warriorz", displayTerm: "weeknd warriorz"), 
Suggestion(kind: "terms", searchTerm: "yeyo weeknd", displayTerm: "yeyo weeknd")
])))

```

### 复杂示例

到目前为止，您应该已经理解了如何使用单个 JSON、嵌套 JSON 和 JSON 数组。作为一名 iOS 开发人员，你可能会在工作中遇到所有这些的组合。嵌套结构可以在层次结构中向下三至四层，并且最深的结构可以包含对象的数组。

让我们看一个剥离的 JSON 用于个人推荐响应的例子。它在`data`键下包含一个对象数组，每个对象包含不同的嵌套对象，如`attributes`、`artwork`和`title`。

`nextUpdateDate`方法包含一个字符串形式的日期，因此我们可以通过将其解码为 Swift 中的`Date`来简化用例:

```
{
  "data":[
    {
      "id":"6-27s5hU6azhJY",
      "type":"personal-recommendation",
      "attributes":{
        "resourceTypes":[
          "playlists"
        ],
        "artwork":{
          "width":1200,
          "height":1200,
          "url":"https://sampleimage.com/link/to/the/image.jpg"
        },
        "nextUpdateDate":"2022-04-16T19:00:00Z",
        "kind":"music-recommendations",
        "isGroupRecommendation":false,
        "title":{
          "stringForDisplay":"Made for You"
        }
      }
    }
  ]
}

```

现在，我们将创建以下结构:

```
struct PersonalRecommendation: Codable {
  let recommendations: [Recommendation]

  enum CodingKeys: String, CodingKey {
    case recommendations = "data"
  }
}

struct Recommendation: Codable {
  let id, type: String
  let href: URL
  let attributes: Attributes
}

struct Attributes: Codable {
  let resourceTypes: [String]
  let artwork: Artwork
  let nextUpdate: Date
  let kind: String
  let isGroupRecommendation: Bool
  let title: String

  struct Artwork: Codable {
    let width, height: Int
    let url: String
  }

  struct Title: Codable {
    let stringForDisplay: String
  }

  enum CodingKeys: String, CodingKey {
    case resourceTypes, artwork
    case nextUpdate = "nextUpdateDate"
    case kind, isGroupRecommendation
    case title
  }

  enum TitleCodingKeys: String, CodingKey {
    case stringForDisplay
  }
}

```

以下是我们需要解码的`Attributes`:

```
extension Attributes {
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)
    resourceTypes = try values.decode([String].self, forKey: .resourceTypes)
    artwork = try values.decode(Artwork.self, forKey: .artwork)
    nextUpdate = try values.decode(Date.self, forKey: .nextUpdate)
    kind = try values.decode(String.self, forKey: .kind)
    isGroupRecommendation = try values.decode(Bool.self, forKey: .isGroupRecommendation)

    let titleValues = try values.nestedContainer(keyedBy: TitleCodingKeys.self, forKey: .title)
    title = try titleValues.decode(String.self, forKey: .stringForDisplay)
  }
}

```

您可以看到我们解码`Attributes`的方式与 JSON 响应不同。响应包含像这样的对象`title`:

```
"title":{
  "stringForDisplay":"Made for You"
}

```

我们没有创建变量`stringForDisplay`，而是直接使用了`Attributes`结构中的`title`。为了实现这一点，我们创建了一个枚举`TitleCodingKeys`，它保存了`title`对象下的不同键:

```
enum TitleCodingKeys: String, CodingKey {
  case stringForDisplay
}

```

然后，在解码过程中，我们得到键`title`的对象的嵌套容器。基于嵌套容器，我们将`stringForDisplay`解码为变量`title`:

```
let titleValues = try values.nestedContainer(keyedBy: TitleCodingKeys.self, forKey: .title)
title = try titleValues.decode(String.self, forKey: .stringForDisplay)

```

所以，不用`attributes.title.stringForDisplay`，我们现在可以直接用它做`attributes.title`！

最后一步是解码响应。如前所述，响应包含一个日期。为了正确解码，我们利用了`JSONDecoder()`的`dataDecodingStrategy`。我们将属性设置为`iso8601`，这是一个国际标准，涵盖了日期和时间相关数据的全球交换和通信:

```
let recommendationResponse = """
{
  "data":[
    {
      "id":"6-27s5hU6azhJY",
      "type":"personal-recommendation",
      "href": "/v1/me/recommendations/6-27s5hU6azhJY",
      "attributes":{
        "resourceTypes":[
          "playlists"
        ],
        "artwork":{
          "width":1200,
          "height":1200,
          "url":"https://sampleimage.com/link/to/the/image.jpg"
        },
        "nextUpdateDate":"2022-04-16T19:00:00Z",
        "kind":"music-recommendations",
        "isGroupRecommendation":false,
        "title":{
          "stringForDisplay":"Made for You"
        }
      }
    }
  ]
}
"""

let recommendationData = Data(recommendationResponse.utf8)

let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .iso8601

let personalRecommendation = try decoder.decode(PersonalRecommendation.self, from: recommendationData)

print(personalRecommendation)

```

### 动态对象示例

您可能遇到的另一个用例是，响应有不同的对象，其中一些对象有相同的键，而其他对象有更多的动态键。在数组示例的基础上，假设响应现在也包含了排名靠前的结果。

你可以看到`kind`在两个对象中是一个公共键，但是在第一种情况下，我们有`searchTerm`和`displayTerm`键，但是第二个对象有一个嵌套对象`content`:

```
{
  "results":{
    "suggestions":[
      {
        "kind":"terms",
        "searchTerm":"the weeknd",
        "displayTerm":"the weeknd"
      },
      {
        "kind":"topResults",
        "content":{
          "id":"1488408568",
          "type":"songs",
          "attributes":{
            "artistName":"The Weeknd",
            "url":"https://music.apple.com/in/album/blinding-lights/1488408555?i=1488408568",
            "genreNames":[
              "R&B/Soul",
              "Music"
            ],
            "durationInMillis":201570,
            "releaseDate":"2019-11-29",
            "name":"Blinding Lights",
            "hasLyrics":true,
            "albumName":"Blinding Lights - Single"
          }
        }
      }
    ]
  }
}

```

解码和解析这个 JSON 的一种方法是拥有一个非可选的`kind`属性，以及三个可选属性:`searchTerm`、`displayTerm`和`content`。但是，在处理选项时，这种类型的数据阵列将很快变得很麻烦，并且不可伸缩。我们能做得更好吗？让我们来了解一下！

我们从类似的顶级层次结构开始，但是我们为术语和顶级结果创建了两个不同的`struct`、`TermSuggestion`和`TopResultsSuggestion`:

```
struct TermSuggestion: Codable {
  let kind: String
  let searchTerm: String
  let displayTerm: String
}

struct TopResultsSuggestion: Codable {
  let kind: String
  let content: Content
}

struct Content: Codable {
  let id, type: String
  let attributes: Attributes
}

struct Attributes: Codable {
  let name, albumName, artistName: String
  let url: URL?
  let genres: [String]
  let duration: Int
  let releaseDate: Date
  let hasLyrics: Bool
}

extension Attributes {
  enum CodingKeys: String, CodingKey {
    case name, albumName, artistName, url
    case genres = "genreNames"
    case duration = "durationInMillis"
    case releaseDate, hasLyrics
  }

  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)

    name = try values.decode(String.self, forKey: .name)
    albumName = try values.decode(String.self, forKey: .albumName)
    artistName = try values.decode(String.self, forKey: .artistName)
    url = URL(string: try values.decode(String.self, forKey: .url))
    genres = try values.decode([String].self, forKey: .genres)
    duration = try values.decode(Int.self, forKey: .duration)
    releaseDate = try values.decode(Date.self, forKey: .releaseDate)
    hasLyrics = try values.decode(Bool.self, forKey: .hasLyrics)
  }
}

```

注意`Attributes`结构如何有一个可选的`URL`。在初始化器中，我们将它解码为来自给定 URL 字符串的 URL。

我们可以创建一个枚举`SuggestionKind`，它有`kind`的编码键，帮助我们区分两个用例`terms`和`topResults`:

```
enum SuggestionKind: Codable {
  case terms(TermSuggestion)
  case topResults(TopResultsSuggestion)

  enum CodingKeys: String, CodingKey {
    case kind
  }

  enum SuggestionsKind: String, Codable {
    case terms
    case topResults
  }

  public init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)
    let kind = try values.decode(SuggestionsKind.self, forKey: .kind)

    switch kind {
      case .terms:
        let termSuggestion = try TermSuggestion(from: decoder)
        self = .terms(termSuggestion)

      case .topResults:
        let topResultsSuggestion = try TopResultsSuggestion(from: decoder)
        self = .topResults(topResultsSuggestion)
    }
  }

  func encode(to encoder: Encoder) throws {
    var values = encoder.container(keyedBy: CodingKeys.self)

    switch self {
      case .terms(let termSuggestion):
        try values.encode(termSuggestion, forKey: .kind)

      case .topResults(let topResultsSuggestion):
        try values.encode(topResultsSuggestion, forKey: .kind)
    }
  }
}

```

在检查了`kind`键之后，我们切换值来创建对象的两种情况，并根据相关的可编码结构将它们关联起来。在这种情况下，这两个可编码结构是`TermSuggestion`和`TopResultsSuggestion`。我们可以利用现有的`decoder`，因为这两个建筑都已经是`Decodable`。`Decoder`为我们完成繁重的工作并解码数据。

例如，如果`kind`的值为`terms`,`decoder`从 JSON 中取出与`terms`对应的对象，解码为`TermSuggestion`。然后，您将解码值设置为`self`。

对于解码，我们看到有一个自定义日期与`releaseDate`相关联。因此，我们利用`JSONDecoder`的属性`dateDecodingStrategy`并传递一个自定义数据格式化程序，如下所示:

```
let suggestionsData = Data(suggestionsResponse.utf8)

let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy-MM-dd"

let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .formatted(dateFormatter)

let suggestions = try decoder.decode(Suggestions.self, from: suggestionsData)

print(suggestions)

// MARK: - OUTPUT
Suggestions(results: Suggestions.Results(suggestions: [
SuggestionKind.terms(TermSuggestion(kind: "terms", searchTerm: "the weeknd", displayTerm: "the weeknd")), 
SuggestionKind.topResults(TopResultsSuggestion(kind: "topResults", content: Content(id: "1488408568", type: "songs", attributes: Attributes(name: "Blinding Lights", albumName: "Blinding Lights - Single", artistName: "The Weeknd", url: Optional(https://music.apple.com/in/album/blinding-lights/1488408555?i=1488408568), genres: ["R&B/Soul", "Music"], duration: 201570, releaseDate: 2019-11-28 18:30:00 +0000, hasLyrics: true))))
]))

```

## 结论

近年来，通过使用分别与`JSONDecoder`和`JSONEncoder`类结合的`Decodable`和`Encodable`协议，JSON 的解析和生成得到了简化。

在本文中，我们介绍了使用`Codable`协议简化 Swift 中 JSON 解析的几个用例。

在回顾了文章中的实际例子之后，从基本用例到复杂 JSON 对象和动态对象，我希望您已经准备好在您的 iOS 应用程序中轻松解析 JSON 了！