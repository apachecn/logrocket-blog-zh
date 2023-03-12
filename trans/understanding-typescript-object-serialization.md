# 了解 TypeScript 对象序列化- LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-typescript-object-serialization/>

在本文中，我们将了解系统如何通过称为序列化和反序列化的过程无缝地相互通信，以及我们如何在 TypeScript 中应用它。由于可传输数据的处理方式，该过程是无缝的。

作为用户，当您访问任何应用程序时，您都是为了检索信息。这些信息由必须存储在某个地方的数据组成；这就是序列化的用武之地。

到本文结束时，您应该能够理解数据如何从一个位置传输到另一个位置，它以什么格式传输，以及存储数据流的可能选项。我们还将讨论在 TypeScript 中使用序列化时可能出现的技术问题。

## 什么是序列化与反序列化？

那么什么是序列化和反序列化呢？它只是允许无缝传输数据的过程。

![Serialization And Deserialization Diagram, Showing Going From Multidimensional Object To Byte Stream And Back](img/14d885dca4c4530a4f7408f8e35c8144.png)

缓存是序列化和反序列化的一个很好的现实例子，其中数据存储在内存中以便更快地检索。

缓存的数据以序列化的格式通过网络传输并存储在内存中，然后，每次请求数据时，数据被反序列化回对象。

在本帖中，我们还将看看需要序列化和反序列化过程的不同场景。通常，序列化和反序列化过程在您不知情的情况下进行。

## 序列化的意义

序列化主要用于存储对象，或者当您希望将对象从一个活动脚本转移到另一个脚本时。传输可以是从服务器到服务器，从服务器到客户端，或者从客户端到服务器。

不同的系统在接受特定的数据格式时必须相互通信。在这种情况下，您控制序列化和反序列化。序列化消除了网关接收这些不同数据格式的需要。

一旦序列化完成，字节流就可以很容易地跨多个平台共享。然后进行反序列化，将数据重新构造回其原始形式。

## 序列化和反序列化用例

下面是需要数据序列化和反序列化时不同用例的几个例子。

### HTTP 序列化

当通过 web 浏览器传输数据时，HTTP 使用序列化。例如，RESTFul 服务有一个带有消息体的 HTTP 请求，消息体的内容是对象的序列化形式。

### 会话数据序列化

会话数据以序列化格式通过通信协议在多个虚拟机之间传输。

这方面的一个例子是基于会话的认证。当在服务器端以序列化格式保存用户信息时，客户端可以向服务器发送请求以检索信息，服务器处理该请求，然后发回反序列化的响应。

### 重复序列

Redis 是一种数据库，它使用内存中的数据集来减少应用程序的加载时间。使用序列化，代码转换成持久的数据结构。

### 视频流

在涉及视频流的应用程序中，视频被分解成数据包，这些数据包是串行传输到客户端的字节集合。

## 序列化和反序列化的好处

除了一般的用例，序列化还提供了许多好处，包括帮助解决通信问题、深层复制和缓存。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

例如，通过允许不同的计算机设计同时共享对象，我们可以直接存储任何对象的状态，解决通信问题。这方面的一个例子是通过网络传输数据，如 web 和移动应用程序。

在[深度复制](https://en.wikipedia.org/wiki/Object_copying#:~:text=Deep%20copy%20is,deep%20copy()%E2%80%9D%20function.)的情况下，通过将对象序列化为字节数组并反序列化，克隆过程变得简单而准确。

最后，对于缓存，构建一个对象所需的时间比反序列化一个对象所需的时间要多，这意味着序列化通过兑现这个巨大的对象来节省时间。

在 TypeScript 中，由于这是一个简单的场景，您可以调用 JavaScript 函数`JSON.stringify`将对象序列化为 JSON 字符串，然后`JSON.parse`将 JSON 字符串反序列化为对象。下面是一个分别使用`JSON.stringify`和`JSON.parse` 序列化和反序列化`Person`对象的例子。

JSON 是一种广泛使用的数据格式，因为它将数据表示为键值对，并且不包括类元数据(对象的类定义，如属性、方法和事件),因为它是结构化数据:

```
class Person{
 name: string;
 constructor() {
   this.name = 'Serializer';
 }
}

const human = new Person();
Output1: Person { name: 'Serialer' }
const serialized_data = JSON.stringify(human)
Output1:// {"name":"Serializer"}
const deserialized_data = JSON.parse(serialized_data)
Output:// { name: 'Serializer }
```

查看官方文档，了解更多关于使用`JSON.stringify`和 `JSON.parse.`的不同数据类型的例子

## 在 TypeScript 中序列化/反序列化对象时的技术问题

### 丢失类型信息

看看上面的`Person`类例子，如果你想将一个 JSON 对象反序列化回一个类实例，使用`JSON.parse`是不够的**。**

`JSON.parse`方法返回一个普通对象，而不是一个类对象。幸运的是，像`Class-transformer` 这样的库可以处理 TypeScript 类的序列化和反序列化，而不会丢失类型信息。

它允许您使用 decorators 将普通对象转换为类对象。查看[官方文档，了解如何使用它提供的方法轻松序列化和反序列化类](https://github.com/typestack/class-transformer)。

### 安全存储序列化数据

上面的`Output1`字符串现在既可以传输到其他机器，也可以存储起来。为了安全地存储序列化数据，可以对数据进行加密。在 TypeScript 中，可以使用一个名为`crypto-js`的库来加密序列化的对象。

使用该工具的要求是安装了 [Node.js 和任意节点包管理器](https://blog.logrocket.com/node-js-best-practices-and-performance-analytics-in-2021/)。在本例中，我们将使用 nmp。

要安装`crypto-js,`,运行以下命令:

```
>npm install crypto-js
Usage: 
//Import it in your TS file
const CryptoJS require("crypto-js");
const AES = require("crypto-js/aes");
const serialized_data_encrytedData = CryptoJS.AES.encrypt(
                                    serialized_data,
                                    'my serialized data'
                                  ).toString()
```

存储数据流的不同方式

我们有不同的方法来序列化和存储对象。这样做时要考虑的因素是可读性、数据复杂性、速度和空间限制。

这些数据流可以存储在数据库、内存或文件中。在我们的例子中，我们将使用 YAML。

存储这些数据时，请确保数据安全，以避免可能的篡改。安全反序列化的关键要素之一是知道数据源是什么，以及数据是安全的。

### 使用 YAML 文件的优势

YAML 是一种数据序列化格式，代表“YAML 不是标记语言”

在创建特定于领域的语言(DSL)时使用 YAML 是一个额外的优势，因为它旨在提供可读性。YAML 文件中的字符串不需要加引号，因此提高了可读性。

由于 YAML 处理复杂数据类型的特性，使用 YAML 是小型数据集的理想选择，有助于减少与性能相关的问题。

如何在 YAML 文件中存储数据的一个简单例子是使用`js-yaml`模块。要使用它，您必须安装`js-yaml`并初始化它和文件流:

```
$ npm install js-yaml
const yaml = require('js-yaml');
const fs   = require('fs');

const object = {
   name: "Serialization",
   category: "db"
}

const serialized_object = JSON.stringify(object);

const yaml_formatted_serialized_object = yaml.dump(serealized_object);

console.log(yaml_formatted_serialized_object);
```

### 使用数据库的优势

当以下实例适用于您正在序列化或反序列化的数据类型时，数据库是首选。

首先，当应用程序的代码不需要经常更改时，使用数据库。例如，包含人口数据的仪表板很少需要精确的洞察力。这里的目标是报告数据。

数据只读取一次，因此性能不是优先考虑的因素；在这种情况下使用数据库是理想的。

其次，如果不需要在反序列化的数据结构中选择字段，使用数据库是理想的。然而，这样做的缺点是几乎不可能知道将来需要什么类型的查询。

如果您将`LocalStorage`数据存储用作数据库，您可以通过以下方式实现它:

```
const object = {
 name: "Serialization",
 category: "db"
}
serialized_object = JSON.stringify(object);
// '{"name":"Serialization","category":"db"}'
localStorage.setItem("serialized object", serealized_object)
localStorage.getItem('serialized object')
// '{"name":"Serialization","category":"db"}'
localStorage.setItem("serialized object", serealized_object)

localStorage.getItem('serialized object')
```

您可以看到，使用`JSON.parse()`方法反序列化数据会返回一个普通对象，而不是一个类对象。因此，在序列化类对象时使用`JSON.stringify`和`JSON.parse`是不够的，因为你会丢失类型信息。

幸运的是，像`Class-transformer`这样的库可以让你将普通对象转换成类对象。

### 将数据流存储在内存中的优势

当下列情况适用时，在内存中存储序列化数据是理想的。

第一种情况是需要实时数据或者需要更快的方法来访问数据。例如，在医疗保健行业，即时访问准确的患者信息对于挽救生命至关重要。

当您处理时间敏感的数据时，将数据流存储在内存中可以使瞬间决策变得更加容易，例如处理股票交易数据。您获得数据更改的速度决定了您的成败。

最后，当减少应用程序的加载时间时，数据检索的最快方法是将其存储在内存中。例如， [Redis 使用内存中的数据集，这使得检索数据更快](https://blog.logrocket.com/how-to-use-redis-as-a-database-with-go-redis/)，因为它在存储数据时不使用硬盘。

下面是一个如何使用`SET`和`GET`命令在 Redis 中存储序列化数据的例子，假设您已经安装并运行了 Redis。

要读取数据，使用`GET`命令:

```
127.0.0.1:6379>  SET serializedData '{"name":"Serialization","category":"db"}'
output: OK

127.0.0.1:6379> GET serializedData
"{\"name\":\"Serialization\",\"category\":\"db\"}"
```

## 序列化和反序列化的利弊

正如我们在本文中提到的，序列化和反序列化在许多用例中非常有用。但是，使用序列化和反序列化有什么好处和坏处呢？

### 序列化和反序列化的优点

因为 JavaScript 中的简单序列化不需要任何第三方库，而第三方库通常会过时并导致未来的问题，所以它避免了减轻使用外部依赖带来的安全相关风险的需要。

序列化和反序列化也不依赖于任何特定的平台。这意味着您可以在一个平台上序列化数据，在另一个平台上反序列化数据，当许多当前技术依赖于序列化并需要这种灵活性时，这很有用。

序列化的数据流也支持加密，这导致一个平稳、安全的过程。加密序列化数据的能力确保了它的安全性和可靠性。

### 序列化和反序列化的缺点

虽然序列化和反序列化有许多好处，但在使用序列化和反序列化时，也有一些缺点需要考虑减轻。

虽然序列化和反序列化是有用的机制，但该过程是脆弱的，会导致安全问题，并增加复杂性，如果使用不当，很容易导致安全漏洞。

根据经验，您编写的防御性代码越多，您的应用程序就越安全。

反序列化还保存分配给对象的所有变量、属性和特性，这使得该过程很危险或容易受到攻击。编写特定于类的序列化方法是很重要的，这些方法不会暴露关于数据流的敏感信息。

确保反序列化过程没有任何副作用。

而且，正如我们之前看到的，序列化和反序列化涉及到需要增加应用程序内存需求的数据流。这是数据流的对象和字符串的所有内存分配的结果，很容易导致内存泄漏。

## 结论

到目前为止，我们了解了序列化和反序列化允许对象的可移植性，并通过示例概述了如何存储序列化的数据流。归根结底，选择如何序列化和反序列化数据完全取决于您的应用程序需求。

快乐学习！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.