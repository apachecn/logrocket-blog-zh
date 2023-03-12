# 使用 JSON Hero - LogRocket 博客轻松阅读 JSON

> 原文：<https://blog.logrocket.com/make-reading-json-easy-json-hero/>

JSON 是现代编程中一种重要的数据交换机制。它基于文本的格式和类似对象的语法使它在许多编程语言中使用起来非常方便和容易。例如，JavaScript 和 PHP 都有处理 JSON 数据的内置函数。

尽管它很有用，但是处理原始的、冗长的 JSON 文件和浏览大量的文本信息可能会很困难、枯燥和耗时。

还有，要看到实际数据在动作，必须先在你的 app 里渲染；在此之前，你不能确定数据(图像、颜色、日期等)是如何。)看起来或者它是否存在。

幸运的是，有工具可以帮助解决这个问题。在本文中，我们将探索一个叫做 JSON Hero 的工具，一个拥有超能力的 JSON 查看器。我们将涵盖:

![Json Hero Homepage Showing Feature Highlight And Json Data Entry Fields](img/e04ac969d4555ce5032738bf7a76dde6.png)

首先，让我们探索一下 JSON 是什么，以及为什么我们可能需要像 JSON Hero 这样的查看器。

## JSON 是什么？

[JSON 代表 JavaScript 对象符号](https://www.json.org/json-en.html)。这是一种基于文本的数据交换格式，顾名思义，它基于 JavaScript 对象文字。JSON 可以用来在不同的平台、计算机、系统等之间交换数据。

我们可以很容易地将来自服务器的 JSON 数据转换成 JavaScript 对象，反之亦然。它是现代编程中首选的数据交换格式。让我们回顾一下使 JSON 如此有用和流行的主要特性。

JSON 是轻量级的。它被设计得尽可能简单明了，这样程序员就可以轻松高效地读写 JSON 数据结构。

JSON 是基于文本的。这使得它完全独立于语言。所有主要的编程语言都有某种处理 JSON 数据的功能，包括 Go 、 [Rust 和其他语言](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)。

JSON 是可移植的。这使它成为一种跨平台的文件格式。使用 JSON，我们可以以一种兼容所有平台和设备的方式在不同平台和设备之间传输数据。

JSON 数据结构很容易被机器和人类阅读。

JSON 很简单。与 XML 等其他数据交换格式相比，它的语法非常简单。

### XML 与 JSON

正如上一节提到的，XML 是另一种流行的数据交换格式，您可能已经听说过。但是随着时间的推移， [JSON 已经取代了 XML](https://blog.logrocket.com/the-quiet-revolution-how-json-displaced-xml-1e1f3e8552f7/) ,因为它是一种更强大的替代品。

下面是程序员更喜欢 JSON 而不是 XML 的一些其他原因:

*   JSON 不使用复杂的语法，比如标签
*   JSON 更短，读写速度更快
*   JSON 不太冗长，这更适合数据传输
*   JSON 可以用标准编程语言函数解析(要解析 XML，我们必须使用 XML 解析器)

### JSON 中的数据结构

JSON 支持两种主要的数据结构:对象和数组。两者可以结合使用来构建复杂的数据表示。

这是一个 JSON 对象:

```
{
  "name":"David", 
  "age":33, 
  "hobby":null
}

```

如您所见，JSON 对象是名称和值对的集合。

这是一个 JSON 数组:

```
["Tennis", "Karate", "Dancing"]

```

JSON 数组是有序的值列表。

我们也可以一起使用它们:

```
{
  "name":"David", 
  "age":33, 
  "hobbies":[
    "Tennis", 
    "Karate", 
    "Dancing"
  ]
}

```

JSON 值必须是以下数据类型:

*   空
*   布尔代数学体系的
*   数字
*   线
*   排列
*   目标

注意，在 JavaScript 中，值也可以是有效的 JavaScript 表达式，比如函数或日期。

许多编程语言都提供了读取和生成 JSON 的函数。例如，JavaScript 内置了将 JSON 字符串转换成 JavaScript 对象(`JSON.parse()`)和将对象转换成 JSON 字符串(`JSON.stringify()`)的函数。

### 什么是 JSON 模式？

在我们继续讨论 JSON Hero 之前，还需要提到最后一件事。[叫做 JSON Schema](https://github.com/json-schema-org) 。

JSON 模式是验证特定 JSON 文件的一种方式。通过检查以下内容，可以根据 JSON 模式验证任何 JSON 文件:

*   值的数据类型是否正确。如果我们期望一个字符串，这个值必须是`string`
*   如果所需的数据存在
*   值和数据的正确格式，如范围或最小值和最大值

换句话说，JSON 模式的目的是确保数据符合一组已定义的规则。

注意，JSON 模式不用于语法验证——我们可以使用 JSON 验证器来验证语法——而是用于验证数据本身。

## JSON 英雄是什么？

我们已经看到了 JSON 是多么有用。然而，在使用 JSON 时，我们可能会遇到一些困难。例如，很难:

*   导航到大型 JSON 文件
*   想象一下，在我们以某种方式实际呈现数据之前，数据会是什么样子
*   探索相关价值

JSON Hero viewer 解决了上述所有问题。它使得使用 JSON 变得容易，因为它提供了:

*   “抽象”原始 JSON 数据的具体和可视化表示
*   选定字段的相关值
*   轻松浏览大型复杂 JSON 文件的不同方法
*   JSON 模式，以便您可以验证您的 JSON 数据

## JSON Hero 入门

您可以在浏览器中立即开始使用 JSON Hero。你不需要安装任何东西。只需进入 app 网站，输入一个 JSON URL 或上传一个 JSON 文件，如下图所示:

![Section Of Json Hero Homepage With Fields To Enter Json Url, Paste Json Data, Or Upload Json File](img/1dfc3093de2aafc7be8aec8064634f04.png)

查看器将加载 JSON 数据，您可以立即开始浏览文档。

为了让您的工作流程更加简单方便，您可以为 VS 代码安装 [JSON Hero 扩展。](https://marketplace.visualstudio.com/items?itemName=JSONHero.jsonhero-vscode)

一旦安装了扩展，就可以直接从 VS 代码编辑器加载 JSON 文件。打开您想要浏览的 JSON 文件，然后从命令面板运行`View in jsonhero.io`命令:

![Opened Json File In Json Hero With View Command Being Run](img/2700de48fec2bf5d59b08c78f7397263.png)

该文件将通过使用您的默认浏览器自动加载到 JSON 查看器中。

## 探索 JSON Hero 特性

在这一节中，我们将探索 JSON Hero 的所有特性。

### JSON 数据的实时预览

JSON Hero 的第一个也是最具表现力的特性是能够预览 JSON 文档中任何字段的数据——如果可以生成预览的话。这种实时预览功能支持多种类型的数据，包括图像、视频、URL、颜色和日期。

让我们看一些实时预览功能的例子。下面的第一个例子展示了 JSON Hero 如何生成图像预览:

![Json Hero Image Preview Example Showing Image Source And Preview Of Image](img/850165cc157bb5f986a519c506462bc4.png)

这非常有用，因为你可以看到实际图像渲染后的样子。

下一个示例展示了 JSON Hero 如何生成颜色预览:

![Json Hero Color Preview Showing Visual Example Of Color, Various Color Modes, And Other Info](img/5e942c6f6e899e096a70ab195cb78689.png)

你可以立即看到颜色的样子。此外，您可以在“属性”部分获得关于颜色的附加信息，包括其他颜色模式、亮度和对比度。

最后一个例子展示了 JSON Hero 如何生成日期预览:

![Json Hero Date Preview With Date Represented In Various Ways, Including Calendar View](img/4501732589807fd6566108795226be87.png)

这里，我们得到了所选日期字段的一个易于阅读的日历视图。我们还可以看到其他有用的信息，同样可以在“属性”部分找到。

除了这些很酷的预览，JSON Hero 通过为每个字段预先添加一个合适的图标，使得查看预期的数据变得很容易。

如您所见，JSON Hero 提供了更丰富的数据信息，而不仅仅是原始的文本块。

### 导航 JSON 文档

JSON Hero 提供了三种数据视图，使得浏览 JSON 文档变得很容易。

默认情况下，JSON Hero 显示分栏视图，这与 MacOS Finder 的导航方式非常相似:

![Json Hero Default Column View Displaying Document Data](img/e480ff7d923eb61829f42f72652b8e3a.png)

第二个视图是原始的 JSON 视图:

![Json Hero Raw Json View Showing Raw Json Data](img/1b81c60de56fbe8f4a3d603987e1238b.png)

在这个视图中，您可以选择单独的行并获得相应字段的信息。

第三个视图是树形视图:

![Json Hero Tree View With Vertical Expansion And Collapse Options For Field Groups](img/b9a6058c70315eafb083403a8916a466.png)

在这个视图中，您可以通过展开或折叠任何字段组，在垂直方向上浏览 JSON 文档。您还可以使用键盘上的箭头键在各个字段中上下移动。

### 搜索 JSON 文档

在一个很长的 JSON 文件中，很难找到您想要的一个或多个字段。JSON Hero 通过模糊匹配提供了完整的文档搜索功能，因此您可以在一秒钟内找到任何字段。

看看下面这个搜索功能的运行情况:

![Json Hero Search Function In Popup Modal](img/9e2c2fead2bb5abfa2cdc47155ad1f7f.png)

键盘快捷键也是可用的，这使得搜索 JSON 文档更加容易。

另一个有用的特性是 JSON Hero 显示了所选字段的相关值。

假设您的 JSON 文件在其记录中有一个名为`username`的字段类型。当您选择一个字段时，JSON Hero 会列出所有同名字段的值，从而可以轻松提取所需的信息:

![Json Hero Displaying Selected Field And Related Values](img/322816c7db81db454df919a3012b4e5f.png)

在上图中，您可以看到我们如何自动获取与`username`字段相关的所有值。

### 自动生成 JSON 模式

另一个 gem JSON Hero 为我们提供的是，它自动为正在查看的 JSON 文档生成 JSON 模式，如下图所示:

![Automatically Generated Json Schema Displaying File Structure](img/04209bd687bb9049b4955907cf4d9998.png)

这个特性使我们能够准确地看到 JSON 文件是如何构造的。

### 共享 JSON 文件

我们将讨论的最后一个 JSON Hero 特性是，当您加载文件时，只需复制生成的链接，就可以与任何人共享您的 JSON 文件。要获取链接，请点击“共享”按钮并复制链接。

![Json Hero Share Feature Displaying Popup Modal On Click Of Share Button With Options To Copy Link Or Include Path To Exact Location](img/1968f80d0f0e0ae7527fc1c949b9b38f.png)

如果您想要在当前路径下打开文档，请选中“链接包括路径”栏。

## 结论

记住它的名字——JSON Hero——我们可以把使用这个 JSON 查看器比作阅读一本漫画书，而不是普通的基于文本的书。

JSON 是一种有用且灵活的数据格式，但是阅读它会遇到一些挑战。使用 JSON Hero，您可以以一种更加可视化和更具上下文的方式探索和共享您的 JSON 文件。所以，拿起你的 JSON，和 JSON Hero 一起享受阅读吧。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.