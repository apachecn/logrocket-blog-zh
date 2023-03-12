# 如何解析 Flutter 中的 JSON 字符串

> 原文：<https://blog.logrocket.com/how-parse-json-strings-flutter/>

如果您要与 API 交互，您认为响应会是什么格式？它可能——或者几乎肯定——在 JSON 中。

JSON 是将类型化数据从一点传输到另一点的标准。如果你查询一个 API，或者甚至想在你的应用程序中保存设置，你迟早要使用 JSON。

在本文中，我们将看看如何在 Flutter 中解析 JSON 字符串。我们将回顾:

## JSON 中的序列化和反序列化

在处理 JSON 时，可能会发生两种主要的操作:

*   序列化——将数据对象从其本地语言类型转换为 JSON 字符串
*   反序列化——从 JSON 字符串转换为本机对象

实际上，这是我们现在就可以做的事情，就在你用来阅读这篇文章的同一个浏览器中。如果您按下键盘上的 F12 键，然后转向开发人员控制台，您可以键入以下内容:

```
JSON.stringify({serialization: 'fun'})

```

这样的结果就是:`'{"serialization":"fun"}'`。

好吧，刚才发生了什么？我们创建了一个内存中的 JavaScript 对象，然后通过`JSON.stringify`立即将该对象转换成 JSON。我们可以看到它是一个字符串，因为它被引号括起来了。

我们可以通过运行以下命令来反向运行这个过程:

```
JSON.parse('{"serialization":"fun"}')
```

这样做的结果如下:

![Converting Between A Json String And A Javascript Object With JSON.parse](img/6f86fa756673f4f8ba039b1600805999.png)

现在，我们将这个对象的字符串表示转换回真正的 JavaScript 对象。由于 JavaScript 是一种非类型化语言，我们不需要指定 JSON 字符串的序列化或反序列化类型。

在类型化语言中，如 C#，从对象到字符串的序列化和反序列化是相似的，反之亦然。然而，一个不同之处是，当我们反序列化时，我们需要通过使用以下内容来指定我们想要反序列化到什么类型:

```
JsonConvert.DeserializeObject<T>

```

在上面的命令中，`T`是我们试图反序列化的目标类型。

也许你会认为像在 Flutter 中序列化数据这样简单而普遍的事情和用 C#或 JavaScript 完成同样的事情一样容易。不幸的是，情况并非如此，但这一挑战是有充分理由的。

## 为什么在 Flutter 中解析 JSON 是一个挑战

在 Flutter 和其他语言中序列化和反序列化 JSON 字符串的关键区别是 Flutter 不支持[一种被称为“反射”的运行时特性。有了反射，其他语言能够检查对象以获得有助于对象序列化的信息。](https://blog.logrocket.com/reflection-go-use-cases-tutorial/)

不过，Flutter 没有反思其实是件好事。没有反射，Flutter 代码避免了相关的性能警告，并为最终用户带来了更小的可安装二进制文件。

我们仍然可以在 Flutter 中轻松解析 JSON 字符串，但是我们需要做的不仅仅是指定一个类型。幸运的是，我们可以生成完成这个任务所需的所有代码，所以我们不必手工编写。

当您想在 Flutter 中序列化数据时，有两种情况。

在一种情况下，您自己控制着数据类，您希望将数据转换成 JSON 或从 JSON 转换过来，以便发送给 API 或存储在设备上。

在另一种情况下，您自己不能控制数据类，而是从 API 接收 JSON。您希望将这个 JSON 转换成可以在 Flutter 应用程序中操作的强类型数据。

我们将在本文中考虑这两种情况。首先，我们将看看当我们控制数据时，如何在应用程序中序列化一个类。然后，我们将回顾从远程数据源反序列化 Flutter 中的 JSON 数据。

## 在 Flutter 应用中用 JSON 序列化一个类

让我们想象一下，我们有一个音乐应用程序，用户可以在其中将歌曲设置为他们最喜欢的。为此，我们需要一个名为`Favorite`的类。在这个类中，我们通常有一些属性，比如歌曲的标题和艺术家。

最终结果会是这样的:

```
class Favorite {
  final String title;
  final String artist;

  Favorite(this.title, this.artist);
}

```

那么我们如何在 Flutter 中序列化和反序列化这样的类呢？您可以自己完成或者自动生成 JSON 处理代码。让我们来看看这两个选项。

### 定义您自己的`toJson`和`fromJson`方法

如果您只有几个想要序列化或反序列化的类，那么您可以选择自己动手。这意味着定义自己的`toJson`和`fromJson`方法，负责将数据从`Map<String, dynamic>`转换回原始数据模型。

如果我们这样做，我们的类看起来会像这样:

```
class Favorite {
  final String title;
  final String artist;

  Favorite(this.title, this.artist);

  /// Convert from JSON response stream to Favorite object
  Favorite.fromJson(Map<String, dynamic> json)
      : title = json['title'],
        artist = json['artist'];

  /// Convert an in-memory representation of a Favorite object to a Map<String, dynamic>
  Map<String, dynamic> toJson() => {
        'title': title,
        'artist': artist,
      };
}

```

然后，我们可以将`jsonEncode`或`jsonDecode`分别用于我们的`fromJson`和`toJson`方法。但是在 Flutter 中定义自己的 JSON 数据转换方法也有一些缺点。

例如，如果我们曾经更新过类成员，我们将不得不手动更新属性。我们也有可能引用了错误的成员，或者只是打了个错别字，无意中试图映射一个不存在的属性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这种方法也增加了在我们的 Flutter 应用程序中使用 JSON 的开销。使用现有的包来生成 JSON 序列化和反序列化代码会更方便。

### 自动生成您的 JSON 处理代码

官方的 Flutter 文档说你可以为更小的应用手工编写你自己的 JSON 映射代码。实际上，您最好生成 JSON 序列化和反序列化代码。

自动生成 JSON 处理代码的包经过了很好的测试，并且和 Flutter 一样早就存在了，所以你知道它们是有效的。

其中一个包是`[json_serializable](https://pub.dev/packages/json_serializable)`。在我们的 Flutter 代码中使用一些简单的属性，我们可以很快为 JSON 序列化生成代码。

首先，将`json_serializable`作为`dependency`和`dev_dependency`添加到您的`pubspec.yaml`文件中。我们还需要添加对`build_runner`的引用，因为`json_serializable`使用`build_runner`为我们创建功能。

接下来，用`@JsonSerializable`注释我们的类。这指示`json_serializable`包生成我们需要的代码，以便在 Flutter 中将这个类序列化到 JSON 和从 JSON 序列化。

我们还想将`part`指令添加到这个文件的顶部，并指定`favorite.g.dart`。当我们最初写这个的时候，我们会在下面得到红色的曲线，因为文件还没有被创建。但是我们需要在我们的文件中定义它，以便`json_serializable`包能够工作。

```
part 'favorite.g.dart'

@JsonSerializable
class Favorite {
  final String title;
  final String artist;

  Favorite(this.title, this.artist);
}

```

经过这些更改，现在我们可以在终端中运行以下命令:

```
flutter pub run build_runner build

```

在一些控制台输出之后，我们将能够看到我们的`favorites.g.dart`文件出现在我们的项目中。该文件的内容如下:

```
// GENERATED CODE - DO NOT MODIFY BY HAND

part of 'favorite.dart';

// **************************************************************************
// JsonSerializableGenerator
// **************************************************************************

Favorite _$FavoriteFromJson(Map<String, dynamic> json) => Favorite(
      json['title'] as String,
      json['artist'] as String,
    );

Map<String, dynamic> _$FavoriteToJson(Favorite instance) => <String, dynamic>{
      'title': instance.title,
      'artist': instance.artist,
    };

```

不幸的是，`json_serializable`没有从生成的代码中直接公开这些`toJson`或`fromJson`函数。这意味着由我们来决定是否将它们包含在原始文件中。总而言之，我们最终的`favorite.dart`文件应该是这样的:

```
import 'package:json_annotation/json_annotation.dart';

part 'favorite.g.dart';

@JsonSerializable()
class Favorite {
  final String title;
  final String artist;

  Favorite(this.title, this.artist);

  factory Favorite.fromJson(Map<String, dynamic> json) => _$FavoriteFromJson(json);

  Map<String, dynamic> toJson() => _$FavoriteToJson(this);
}

```

当我们想要序列化或编码`Favorite`对象时，我们使用下面的命令:

```
final favoriteJson = jsonEncode(Favorite('one', 'two'));

```

如果我们想将这个基于文本的 JSON 转换回原始对象，我们可以执行以下操作:

```
final favorite = Favorite.fromJson(jsonDecode(favoriteJson));

```

当我们在自己的应用程序中控制对象时，这就是我们管理 JSON 的方式。但是，当我们无法控制数据类时，我们该怎么办呢？例如，如果我们正在查询一个没有数据结构的远程 API？

## 从远程数据源反序列化 Flutter 中的 JSON 数据

如果我们有一个远程 API，我们可以使用一个在线工具从提供的 JSON 转换成我们可以在应用程序中使用的类，完成`toJson`和`fromJSON`方法。我们将使用的一个工具是 Github 上的一个 JSON 到 Dart 的转换器[。](https://javiercbk.github.io/json_to_dart/)

对于这个示例，我们将使用 [Bored API，如果我们感到无聊](https://www.boredapi.com/api/activity)，它会随机给我们一个任务。Bored API 在 JSON 中响应，非常适合这个目的。因为它是随机的，你收到的任务将会与我们下面使用的例子不同。

在我的例子中，我从 API 收到的响应如下:

```
{"activity":"Go for a walk","type":"relaxation","participants":1,"price":0,"link":"","key":"4286250","accessibility":0.1}

```

要在我们的 Flutter 应用程序中序列化和解析这些 JSON 数据，只需将其粘贴到 JSON 到 Dart 转换器中，如下所示:

![Json To Dart Converter With Json Data Pasted In And Generated BoredAPI Code](img/10b9cefaf71166c696a97d38892517f3.png)

现在，我们可以根据需要将`BoredAPI`类复制并粘贴到我们的应用程序中。在这种情况下，我们为 Bored API 生成的代码如下所示:

```
class BoredAPI {
  String? activity;
  String? type;
  int? participants;
  int? price;
  String? link;
  String? key;
  double? accessibility;

  BoredAPI(
      {this.activity,
      this.type,
      this.participants,
      this.price,
      this.link,
      this.key,
      this.accessibility});

  BoredAPI.fromJson(Map<String, dynamic> json) {
    activity = json['activity'];
    type = json['type'];
    participants = json['participants'];
    price = json['price'];
    link = json['link'];
    key = json['key'];
    accessibility = json['accessibility'];
  }

  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    data['activity'] = this.activity;
    data['type'] = this.type;
    data['participants'] = this.participants;
    data['price'] = this.price;
    data['link'] = this.link;
    data['key'] = this.key;
    data['accessibility'] = this.accessibility;
    return data;
  }
}

```

如我们所见，我们有所有的班级成员。我们还声明了`fromJson`和`toJson`函数。此外，我们生成的代码支持空安全，所以我们可以在 Flutter 的最新版本中使用它。

有了这些，我们现在可以安全地与这个 API 交互，并根据需要在 Flutter 中编码和解码 JSON 数据。

## 结论

在 Flutter 中解析 JSON 字符串比在其他语言中稍微困难一些。正如我们所见，这是由于运行时 Flutter 中缺少反射。

然而，在我看来，Flutter 让我们根据需要生成适用的类来序列化我们的 JSON，从而大大弥补了这些小麻烦。

我们还看到了如何通过为从 API 接收到的响应生成 JSON 类来创建与 JSON 相互转换的功能，即使我们并不拥有进入我们应用程序的数据。

如果您正在与一个 API 交互，并且与 JSON 之间的序列化让您感到沮丧，那么您也可以考虑使用类似 OpenAPI 或 Swagger 的东西来为您生成所有的 API 代码。这样，您将能够只处理 API 功能，而不是手动将对象与 JSON 相互转换。

享受连载之旅！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)