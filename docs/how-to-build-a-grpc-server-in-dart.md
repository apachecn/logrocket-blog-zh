# 如何在 Dart 中构建 gRPC 服务器

> 原文：<https://blog.logrocket.com/how-to-build-a-grpc-server-in-dart/>

在本教程中，我们将介绍 gRPC(一个高性能、开源、通用的 RPC 框架)的基础知识，回顾一下 Dart 编程语言，并演示如何在 Dart 中构建 gRPC 服务器。

我们将带您了解以下内容:

## What is gRPC?

[gRPC](https://grpc.io/) 是 Google 构建的进程间通信(RPC)框架，于 2015 年发布。它是开源的、语言中立的，并且具有紧凑的二进制大小。gRPC 还支持 HTTP/2，并且是跨平台兼容的。

gRPC 与传统的 RPC 非常不同，因为它使用协议缓冲区作为 IDL 来定义它的服务接口。Protocol buffers 是 Google 开发的一个序列化工具，它允许你定义你的数据结构，然后使用 protocol buffers 编译器从这些数据结构生成你选择的语言的源代码。生成的语言用于在我们需要的任何上下文中读写数据结构。根据[官方文件](https://developers.google.com/protocol-buffers)，“协议缓冲区是谷歌的语言中立、平台中立、可扩展的机制，用于序列化结构化数据——想想 XML，但更小、更快、更简单。”

协议缓冲区用于编写服务定义接口，服务定义接口用于定义数据结构和方法。数据结构类似于静态类型语言(如 Java)中的数据类型；它们告诉编译器/解释器如何使用数据。服务定义接口中的数据结构是将传递给方法的参数类型和方法的返回类型。这个服务定义接口保存在扩展名为`.proto`的文本文件中。服务接口中的方法是 gRPC 服务器将公开给 gRPC 客户机调用的方法。

gRPC 有三个组成部分:

1.  托管方法实现并监听来自客户端的请求
2.  `protocol buffer`保存数据结构和方法的消息格式，包括它们的参数和返回类型
3.  `client`调用服务器托管的方法。客户端从`proto`文件中的服务定义接口了解方法及其返回和参数类型

使用这个服务接口，gRPC 服务器设置它的服务器代码，实现服务接口中的方法。它设置自己并监听来自客户端的请求(方法调用)。

客户端使用服务定义接口来生成客户端存根。这个客户机 sub 是调用服务器中的方法的地方。gRPC 客户端应用程序可以向服务器应用程序发出直接请求。客户机和服务器都包含一个公共接口，就像一个契约，在这个契约中，它决定了每个操作将具有的方法、类型和返回。

## 协议缓冲区如何工作

gRPC 最吸引人的地方是它对协议缓冲区的使用，这使得协议可以是平台不可知论的和多语言的。这意味着服务器可以用给定的语言编写，而客户机可以用另一种语言开发。协议缓冲区使这成为可能，因为它有编译器，可以从定义中的数据结构生成语言源代码。

例如，假设服务器是用 JavaScript 编写的。我们将使用 proto 编译器从`.proto`文件中的定义生成 JavaScript 源代码。然后，服务器可以使用 JavaScript 代码访问和操作数据结构和方法。

对于客户端，我们希望用 Java 开发，所以我们将从定义中生成 Java 源代码。然后，客户端可以调用这些方法，并使用 Java 代码访问数据结构。这就是我们说 gRPC 是多语言和平台不可知的意思。

请注意，协议缓冲区不仅由 gRPC 使用。它们也可以用于序列化。它通常用于通过流发送数据，因此您可以在没有任何开销损失的情况下读写数据结构。

## 在 Dart 中构建 gRPC 服务器

现在我们已经了解了 gRPC 和协议缓冲区的基础知识，是时候在 [Dart](https://blog.logrocket.com/introduction-to-using-dart-in-flutter/) 中构建我们的 gRPC 服务器了。

在我们开始之前，请确保您的机器上安装了 [Dart SDK](https://dart.dev/) 。Dart 可执行文件必须在您的系统中全局可用。运行以下命令进行检查:

```
➜  grpc-dart dart --version
Dart SDK version: 2.10.5 (stable) (Tue Jan 19 13:05:37 2021 +0100) on "macos_x64"

```

我们还需要一些协议工具。因为我们正在用 Dart 开发 gRPC 服务器，所以我们必须为 Dart 语言安装 proto 编译器。这个编译器将从`.proto`文件中的服务定义生成 Dart 源代码。

[p](https://developers.google.com/protocol-buffers)rotocol buffer 编译器是一个命令行工具，用于编译`.proto`文件中的 IDL 代码，并为其生成指定的语言源代码。有关安装说明，请参见 [gRPC 文档](https://grpc.io/docs/protoc-installation/)。确保下载[版本 3](https://developers.google.com/protocol-buffers/docs/proto3) 。

最后，协议编译器的 [Dart 插件从。`proto`文件。](https://github.com/dart-lang/protobuf/tree/master/protoc_plugin#how-to-build-and-use)

对于 Mac 用户，通过运行以下命令安装 Dart 协议插件:

```
dart pub global activate protoc_plugin

```

这将在您的机器中全局安装`protoc_plugin`。

接下来，更新`$PATH`，这样`protoc`就会看到我们的插件:

```
export PATH="$PATH:$HOME/.pub-cache/bin"

```

现在是创建服务器的时候了。在我们的演示中，我们将创建一个管理图书服务的 gRPC 服务器。该服务将公开用于以下目的的方法:

*   获取所有书籍(`GetAllBooks`)
*   通过 ID ( `GetBook`)从服务器获取图书
*   删除一本书(`DeleteBook`
*   编辑一本书(`EditBook`
*   创建一本书(`CreateBook`)

我们的 Dart 项目将是一个`console-simple`项目。运行以下命令来搭建 Dart 项目:

```
dart create --template=console-simple dart_grpc

```

`create`子命令告诉 Dart 可执行文件我们希望创建一个 Dart 项目。`--template=console-simple`告诉 Dart exe 我们希望 Dart 项目是一个简单的控制台应用程序。

输出如下所示:

```
Creating /Users/.../dart_grpc using template console-simple...

  .gitignore
  CHANGELOG.md
  README.md
  analysis_options.yaml
  bin/dart_grpc.dart
  pubspec.yaml

Running pub get...                     10.2s
  Resolving dependencies...
  Downloading pedantic 1.9.2...
  Downloading meta 1.2.4...
  Changed 2 dependencies!

Created project dart_grpc! In order to get started, type:

  cd dart_grpc

➜

```

我们的项目将驻留在`dart_grpc`文件夹中。

打开`pubspec.yaml`文件。这是我们在 Dart 应用程序上设置配置和依赖关系的地方。我们想要安装`grpc`和`protobuf`依赖项。在`pubspec.yaml`文件中添加以下行并保存:

```
dependencies:
  grpc:
  protobuf:

```

现在，在您的控制台中运行`pub get`,以便安装依赖项。

## 正在写入服务定义

我们在一个`.proto`文件中定义我们的服务定义。所以让我们创建一个`book.proto`文件。

```
touch book.proto

```

在`book.proto`文件中添加下面的`Protobuf`代码:

```
syntax = "proto3";

service BookMethods {
    rpc CreateBook(Book) returns (Book);
    rpc GetAllBooks(Empty) returns (Books);
    rpc GetBook(BookId) returns (Book);
    rpc DeleteBook(BookId) returns (Empty) {};
    rpc EditBook(Book) returns (Book) {};
}

message Empty {}

message BookId {
    int32 id = 1;
}

message Book {
    int32 id = 1;
    string title = 2;
}

message Books {
    repeated Book books = 1;
}

```

代码太多了。让我们一行一行地过一遍。

```
syntax = "proto3";

```

这里，我们告诉协议缓冲编译器，我们将使用协议缓冲语言的第 3 版。

```
service BookMethods {
    rpc CreateBook(Book) returns (Book);
    rpc GetAllBooks(Empty) returns (Books);
    rpc GetBook(BookId) returns (Book);
    rpc DeleteBook(BookId) returns (Empty) {};
    rpc EditBook(Book) returns (Book) {};
}

```

在这里，我们声明了这些方法和它们将被应用的服务。关键字`service`表示 gRPC 中的单个服务，因此我们创建了服务`BookMethods`。若要调用方法，该方法必须由其服务引用。这类似于`class`和`methods`；`methods`通过它们的类实例被调用。我们可以在一个原型中定义几个服务。

每个服务内部的方法都用关键字`rpc`来表示。`rpc`告诉编译器该方法是一个`rpc`端点，将被客户端远程公开和调用。在我们的定义中，我们在`BookMethods`服务中有五个方法:`CreateBook`、`GetAllBooks`、`GetBook`、`DeleteBook`和`EditBook`。

*   `CreateBook`将一个`Book`数据类型作为 arg，返回一个`Book`类型。这个方法实现将创建一本新书
*   `GetAllBooks`接受一个`Empty`类型作为参数，返回一个`Books`类型。它的实现将返回所有的书
*   `GetBook`方法接受类型为`BookId`的输入参数，并返回一个`Book`。它的实现将返回一个特定的书
*   `DeleteBook`接受一个`BookId`类型作为输入参数，并返回一个`Empty`类型。它的实现将从集合中删除一个图书条目
*   `EditBook`接受一个`Book`类型作为参数，返回一个`Book`类型。它的实现将修改集合中的一本书

从这一点开始的所有其他数据代表数据或消息类型。我们有:

```
message Empty {}

```

`message`关键字表示消息类型。每种消息类型都有字段，每个字段都有一个编号，以便在消息类型中唯一地标识它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`Empty`表示空的数据结构。当我们不想给`rpc`方法发送任何参数，或者当方法不返回值时，就会用到这个函数。与 C/C++中的`void`相同。

```
message BookId {
    int32 id = 1;
}

```

这个数据结构表示一个图书 ID 消息对象。`id`字段将保存一个前面带有`int32`关键字的整数。`id`字段将保存一本书的 ID。

```
message Book {
    int32 id = 1;
    string title = 2;
}

```

这个数据结构代表一本书。`id`字段保存图书的唯一 ID，而`title`保存图书的标题。`title`字段将是一个前面带有`string`关键字的字符串。

```
message Books {
    repeated Book books = 1;
}

```

这代表一系列书籍。`books`字段是一个保存书籍的数组。`repeated`表示将是列表或数组的字段。字段名前的`Book`表示数组将是`Book`类型。

现在我们已经完成了服务定义的编写，让我们编译`book.proto`文件。

## **编译原型**

协议工具用于编译我们的`.proto`文件。确保协议工具在您的系统中全局可用:

```
protoc --version
libprotoc 3.15.8

```

这是我写这篇文章时的协议工具的版本。你的版本可能会不同，没关系。

现在，确保您的终端在`dart_grpc`根文件夹中打开。运行以下命令编译`book.proto`文件:

```
protoc -I=. --dart_out=grpc:. book.proto

```

`I=.`告诉编译器我们试图编译哪个`proto`字段的源文件夹。

`dart_out=grpc:.`子命令告诉协议编译器，我们正在从`book.proto`定义中生成 Dart 源代码，并将其用于 gRPC `=grpc:`。`.`告诉编译器在我们操作的根文件夹中写入 dart 文件。

此命令将生成以下文件:

*   `book.pb.dart`
*   `book.pbenum.dart`
*   `book.pbgrpc.dart`
*   `book.pbjson.dart`

最重要的文件是`book.pb.dart`，它包含了`book.proto`文件中消息数据结构的 Dart 源代码。它还包含了`Empty`、`BookId`、`Book`和`Books`的 Dart 类。由此，我们创建它们的实例，并在调用`rpc`方法时使用它们。

`book.grpc.dart`文件包含类`BookMethodClient`，我们将使用它来创建调用`rpc`方法和接口`BookMethodsServiceBase`的实例。该接口将由服务器实现，以添加方法的实现。

接下来，我们将编写我们的服务器代码。

## **创建 gRPC 服务器**

我们将在`dart_grpc.dart`文件中编写 gRPC 服务器代码。打开文件并粘贴以下代码:

```
import 'package:grpc/grpc.dart';
import 'package:grpc/src/server/call.dart';
import './../book.pb.dart';
import './../book.pbgrpc.dart';

class BookMethodsService extends BookMethodsServiceBase {
  Books books = Books();

  @override
  Future<Book> createBook(ServiceCall call, Book request) async {
    var book = Book();
    book.title = request.title;
    book.id = request.id;
    books.books.add(book);
    return book;
  }

  @override
  Future<Books> getAllBooks(ServiceCall call, Empty request) async {
    return books;
  }

  @override
  Future<Book> getBook(ServiceCall call, BookId request) async {
    var book = books.books.firstWhere((book) => book.id == request.id);
    return book;
  }

  @override
  Future<Empty> deleteBook(ServiceCall call, BookId request) async {
    books.books.removeWhere((book) => book.id == request.id);
    return Empty();
  }

  @override
  Future<Book> editBook(ServiceCall call, Book request) async {
    var book = books.books.firstWhere((book) => book.id == request.id);
    book.title = request.title;
    return book;
  }
}

Future<void> main(List<String> args) async {
  final server = Server(
    [BookMethodsService()],
    const <Interceptor>[],
    CodecRegistry(codecs: const [GzipCodec(), IdentityCodec()]),
  );
  await server.serve(port: 50051);
  print('Server listening on port ${server.port}...');
}

```

好大一段代码啊！这看起来令人望而生畏，但比你想象的要简单。

第一部分导入所需的文件。我们导入了`grpc`代码和`grpc`镖代码。我们导入了`book.pb.dart`和`book.pbgrpc.dart`文件，因为我们需要其中的类。

下面，我们扩展了`BookMethodsService`中的`BookMethodsServiceBase`接口，为`BookMethods`服务中的所有方法提供实现。

在`BookMethodsService`类中，我们覆盖所有的方法来提供它们的实现。注意方法中的两个参数。第一个参数`ServiceCall call`包含请求的元信息。第二个参数保存发送的信息，这是`rpc`方法接受作为参数的数据类型。

```
  Books books = Books();

```

上面的命令设置了一个`books`数组。

在`createBook`方法中，我们创建了一个新的`Book`，设置了`id`、`title`，并将其添加到`books`变量中的`books`数组中。

在`getAllBooks`方法中，我们只是返回了`books`变量。

在`getBook`方法中，我们从`BookId request`对象中获取 ID，并用它通过`List#firstWhere`方法从`books`数组中获取图书并返回。

在`deleteBook`中，我们从`BookId request`中提取 bookID，并使用它作为光标，使用`List#removeWhere`方法从`books`数组中移除图书。

在`editBook`方法中，`request`参数包含了`Book`信息。我们从`books`数组中检索这本书，并将它的`title`属性值编辑为在`request`参数中发送的值。

最后，我们在`main`函数中设置服务器。我们将数组中的`BookMethodsService`实例传递给了`Server`构造函数。然后，我们调用`serve`方法在端口`50051`启动服务器。

现在让我们构建客户端。

## 构建 gRPC 客户端

在`bin`文件夹中创建一个`client.dart`文件:

```
touch bin/client.dart

```

打开它并粘贴以下代码:

```
import 'package:grpc/grpc.dart';
import './../book.pb.dart';
import './../book.pbgrpc.dart';

class Client {
  ClientChannel channel;
  BookMethodsClient stub;

  Future<void> main(List<String> args) async {
    channel = ClientChannel('localhost',
        port: 50051,
        options: // No credentials in this example
            const ChannelOptions(credentials: ChannelCredentials.insecure()));
    stub = BookMethodsClient(channel,
        options: CallOptions(timeout: Duration(seconds: 30)));
    try {
      //...
      var bookToAdd1 = Book();
      bookToAdd1.id = 1;
      bookToAdd1.title = "Things Fall Apart";
      var addedBook1 = await stub.createBook(bookToAdd1);
      print("Added a book: " + addedBook1.toString());

      var bookToAdd2 = Book();
      bookToAdd2.id = 2;
      bookToAdd2.title = "No Longer at Ease";
      var addedBook2 = await stub.createBook(bookToAdd2);
      print("Added a book: " + addedBook2.toString());

      var allBooks = await stub.getAllBooks(Empty());
      print(allBooks.books.toString());

      var bookToDel = BookId();
      bookToDel.id = 2;
      await stub.deleteBook(bookToDel);
      print("Deleted Book with ID: " + 2.toString());

      var allBooks2 = await stub.getAllBooks(Empty());
      print(allBooks2.books);

      var bookToEdit = Book();
      bookToEdit.id = 1;
      bookToEdit.title = "Beware Soul Brother";
      await stub.editBook(bookToEdit);

      var bookToGet = BookId();
      bookToGet.id = 1;
      var bookGotten = await stub.getBook(bookToGet);
      print("Book Id 1 gotten: " + bookGotten.toString());
    } catch (e) {
      print(e);
    }
    await channel.shutdown();
  }
}

main() {
  var client = Client();
  client.main([]);
}

```

我们导入了`grpc.dart`包以及`book.pb.dart`和`book.pbgrpc.dart`文件。我们创建了一个类`Client`类。我们有一个`BookMethodsClient stub`；`stub`将保存`BookMethodsClient`实例，在这里我们可以调用`BookMethods`服务方法来在服务器中调用它们。

在`main`方法中，我们创建了一个`ClientChannel`实例和一个`BookMethodsClient`实例，并将`ClientChannel`实例传递给它的构造函数。`BookMethodsClient`使用实例获取配置—例如，gRPC 服务器将到达的端口。在我们的例子中，是`50051`和超时时间。

在`try`语句体中，我们调用我们的 gPRC 方法。首先，我们创建了一本书，书名为“事物分崩离析”，并给它分配了一个 ID`1`。我们在`stub`中调用了`createBook`方法，将`Book`实例`bookToAdd1`作为参数传递给该方法。这将使用`addToAdd1`对象调用服务器中的`createBook`方法。

接下来，我们创建一个新的 book 实例，“不再自在”，ID 为`2`，调用`createBook`方法，传入 book 实例。这远程调用了 gRPC 服务器中的`createBook`方法，并且创建了一本新书。

我们调用了`getAllBooks`方法来获取服务器上的所有书籍。

接下来，我们设置一个`BooKId`对象，将其 id 设置为`2`。然后，我们调用了`deleteBook`方法，
传入了`BookId`对象。这将从服务器上删除 id 为`2`(“不再安心”)的书。

注意我们在哪里编辑一本书。我们创建了一个 ID 设置为`1`并且标题设置为“当心灵魂兄弟”的`BookId`实例。我们想编辑 ID 为`1`的书名，说“当心灵魂兄弟”而不是“事情分崩离析”所以我们调用了`editBook`方法，传入了`BookId`实例。

最后，我们使用 ID 检索了一本书。我们创建了一个`BookId`实例，它的`id`设置为`1`。这意味着我们想要得到 ID 为`1`的书，它代表“当心灵魂兄弟”这本书。因此，我们调用了`getBook`方法，传递了`BookId`实例。返回的应该是一个标题为“当心灵魂兄弟”的`Book`对象。

在所有这些之后，通过从其`channel`实例调用`ClientChannel`中的`shutdown`方法来关闭通道。

## 测试服务器

现在是测试一切的时候了。首先，运行服务器:

```
➜  dart_grpc dart bin/dart_grpc.dart
Server listening on port 50051...

```

打开另一个终端并运行客户端:

```
➜  dart_grpc dart bin/client.dart
Added a book: id: 1
title: Things Fall Apart

Added a book: id: 2
title: No Longer at Ease

[id: 1
title: Things Fall Apart
, id: 2
title: No Longer at Ease
]
Deleted Book with ID: 2
[id: 1
title: Things Fall Apart
]
Book Id 1 gotten: id: 1
title: Beware Soul Brother

➜  dart_grpc

```

就是这样，我们的 gRPC 服务器正在按预期工作！

这个例子的完整源代码可以在 [GitHub](https://github.com/grpc-prj/grpc-dart) 上找到。

## 结论

我们在本教程中讨论了很多。我们首先介绍了 gRPC，并解释了它是如何从协议缓冲区到客户端工作的。

接下来，我们演示了如何为协议缓冲编译器安装工具和插件。这些用于从原型定义生成 Dart 源代码。之后，我们在 Dart 中创建了一个实际的 gRPC 服务，构建了一个 gRPC 客户端，并从客户端调用了这些方法。最后，我们测试了所有东西，发现它工作得很好。

gRPC 非常强大，您可以通过自己使用它来发现更多的东西。本教程中的例子将为您打下坚实的基础。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)