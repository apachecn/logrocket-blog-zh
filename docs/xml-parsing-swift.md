# Swift 中的 XML 解析:示例教程

> 原文：<https://blog.logrocket.com/xml-parsing-swift/>

尽管与 JSON 文件格式相比，XML 文件格式听起来已经过时了，但是 XML 仍然非常活跃。JSON 在 web 开发中可能更受欢迎，但是 XML 有许多 JSON 所没有的特性，比如名称空间支持，它有助于避免元素和属性的名称冲突。XML 还有一种模式，可以强制 XML 文档遵守某种标准。

您不需要寻找第三方库来使用 Swift 操作 XML 文件。Swift 标准库拥有满足您所有 XML 需求的`XMLParser`。

向前跳:

## 设置`XMLParser`和`XMLParserDelegate`

在 XCode 中创建一个游乐场项目，命名为`XMLParsingPlayground`。参考[这篇文章](https://blog.logrocket.com/getting-started-regexbuilder-swift/#setting-up-swift-playground-xcode)了解更多关于这个步骤的信息。

为了解析 XML 数据，您需要将 XML 字符串转换为`Data`。添加以下代码:

```
let xmlContent =
"""
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <article>
        <title>Getting Started with Swift</title>
    </article>
    <article>
        <title>How to Parse XML with Rust</title>
    </article>
</root>
""";

let xmlData = Data(xmlContent.utf8)

```

在上面的代码中，您用一个 XML 字符串初始化了一个`Data`实例。现在，您需要用这个`Data`实例初始化一个`XMLParser`实例:

```
let xmlParser = XMLParser(data: xmlData)

```

`XMLParser`将解析逻辑委托给它的委托类。你需要实现这个继承了`NSObject`和`XMLParserDelegate`的类。添加以下代码以创建类及其实例:

```
class Parser1 : NSObject, XMLParserDelegate {

    func parserDidStartDocument(_ parser: XMLParser) {
        print("Start of the document")
        print("Line number: \(parser.lineNumber)")
    }

    func parserDidEndDocument(_ parser: XMLParser) {
        print("End of the document")
        print("Line number: \(parser.lineNumber)")
    }

}
let parser1 = Parser1()

```

在委托类中，当您在解析过程中点击文档的开头和结尾时，您设置了一个逻辑来做一些事情。如果您遇到这些问题，您将在解析器当前所在的位置打印一些字符串和一个行号。两种方法都在参数中接受一个`XMLParser`实例。委托类中的许多方法在其参数中接受一个`XMLParser`实例。

一旦完成了解析器委托实例，就必须将其设置为`XMLParser`实例的委托类:

```
xmlParser.delegate = parser1

```

最后一步是解析它:

```
xmlParser.parse()

```

如果您编译并运行了该程序，您应该得到以下输出:

```
Start of the document
Line number: 1
End of the document
Line number: 9

```

上面的代码意味着当解析器遇到`start of the document`的事件时，它在第 1 行。然后是文件的内容。它穿越了元素，但是因为你没有实现方法来处理它，所以什么也没发生。最后，当它遇到`end of the document`的事件时，它通过您已经实现的`parserDidEndDocument`方法通知我们。

## 处理 XML 数据的元素

解析 XML 时，需要实现处理感兴趣的节点或事件的方法。如果您对注释感兴趣，那么当解析器遇到 XML 数据中的注释时，您需要实现一个方法。

现在，您希望实现一些方法，用于解析器何时遇到像`article`和`title`这样的元素。您需要实现的方法是带有以下签名的`parser`方法:

```
func parser(
        _ parser: XMLParser,
        didStartElement elementName: String,
        namespaceURI: String?,
        qualifiedName qName: String?,
        attributes attributeDict: [String : String] = [:]
    )

```

创建新的解析器委托类:

```
class Parser2 : NSObject, XMLParserDelegate {

    func parser(
        _ parser: XMLParser,
        didStartElement elementName: String,
        namespaceURI: String?,
        qualifiedName qName: String?,
        attributes attributeDict: [String : String] = [:]
    ) {
        print(elementName)
    }

}

```

在新的委托类中，您没有实现`parserDidStartDocument`和`parserDidEndDocument`，因为您只关注解析元素。

像往常一样，您需要设置一个新的`XMLParser`实例的委托类实例，并再次调用`parse`方法:

```
let parser2 = Parser2()
let xmlParser2 = XMLParser(data: xmlData)
xmlParser2.delegate = parser2

xmlParser2.parse()

```

编译并运行程序，它将打印元素的名称:

```
root
article
title
article
title

```

如你所见，有五个元素。如果你想区分`title`元素，因为它们有两个，你需要添加更多的逻辑。其中一个例子是跟踪解析遇到了多少个`article`元素。

创建另一个委托类:

```
class Parser3 : NSObject, XMLParserDelegate {

    var articleNth = 0

    func parser(
        _ parser: XMLParser,
        didStartElement elementName: String,
        namespaceURI: String?,
        qualifiedName qName: String?,
        attributes attributeDict: [String : String] = [:]
    ) {
        if (elementName=="article") {
            articleNth += 1
        } else if (elementName=="title") {
            print("'\(elementName)' in the article element number \(articleNth)")
        }
    }

}

```

这一次，你追踪了你遇到了多少个`article`元素，这样当你遇到`title`元素时，你就知道你在哪个`article`元素中了。

创建一个新的`XMLParser`实例，并将委托类设置为`Parser3`:

```
let parser3 = Parser3()
let xmlParser3 = XMLParser(data: xmlData)
xmlParser3.delegate = parser3

xmlParser3.parse()

```

如果您编译并运行该程序，您应该会看到以下输出:

```
'title' in the article element number 1
'title' in the article element number 2

```

## 处理 XML 数据元素的属性

XML 数据中的元素也可以有属性。您可能想要查询属性。您最近执行的方法有另一个引用属性的参数。但是首先，您必须创建其他 XML 数据，因为这些数据现在还没有属性:

```
let xmlContent_attributes =
"""
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <article id="1" tag="swift">
        <title>Getting Started with Swift</title>
    </article>
    <article id="2" tag="rust">
        <title>How to Parse XML with Rust</title>
    </article>
</root>
""";
let xmlData_attributes = Data(xmlContent_attributes.utf8)

```

这一次，您在`article`元素上添加了属性。属性是`id`和`tag`。

现在，您必须创建委托类来处理属性:

```
class Parser4 : NSObject, XMLParserDelegate {

    func parser(
        _ parser: XMLParser,
        didStartElement elementName: String,
        namespaceURI: String?,
        qualifiedName qName: String?,
        attributes attributeDict: [String : String] = [:]
    ) {
        for (attr_key, attr_val) in attributeDict {
            print("Key: \(attr_key), value: \(attr_val)")
        }
    }

}

```

该方法与您之前实现的方法相同。但是这一次，你没有忽略`attributeDict`这个论点。

我们还没完呢！现在，您必须创建一个使用这个委托类实例的新的`XMLParser`实例:

```
let parser4 = Parser4()
let xmlParser4 = XMLParser(data: xmlData_attributes)
xmlParser4.delegate = parser4

xmlParser4.parse()

```

如果您编译并运行该程序，您会看到以下属性:

```
Key: id, value: 1
Key: tag, value: swift
Key: id, value: 2
Key: tag, value: rust

```

## 处理 XML 数据中的命名空间

现在，在`parser`方法中还有两个参数没有处理。他们是`namespaceURI`和`qName`，两者是有联系的。

首先，需要将名称空间添加到 XML 数据中:

```
let xmlContent_namespace =
"""
<?xml version="1.0" encoding="UTF-8"?>
<root xmlns:t="http://logrocket.com/tech" xmlns:m="http://logrocket.com/marketing">
    <t:article>
        <t:title>Getting Started with Swift</t:title>
    </t:article>
    <m:article>
        <m:title>How to Parse XML with Rust</m:title>
    </m:article>
</root>
""";

let xmlData_namespace = Data(xmlContent_namespace.utf8)

```

这个 XML 数据中有两个名称空间:`[http://logrocket.com/tech](http://logrocket.com/tech)`和`[http://logrocket.com/marketing](http://logrocket.com/marketing)`。根元素中的元素使用名称空间。已经不是`article`了，而是`t:article`或者`m:article`。

`t`是指`xmlns:t`，其值为`[http://logrocket.com/tech](http://logrocket.com/tech)`。`m`是指`xmlns:m`，其值为`[http://logrocket.com/marketing](http://logrocket.com/marketing)`。

然后，您需要创建委托类来处理名称空间:

```
class Parser5 : NSObject, XMLParserDelegate {

    func parser(
        _ parser: XMLParser,
        didStartElement elementName: String,
        namespaceURI: String?,
        qualifiedName qName: String?,
        attributes attributeDict: [String : String] = [:]
    ) {
        print("Namespace URI: \(namespaceURI!), qualified name: \(qName!)")
    }

}

```

要获得名称空间 URI，例如，`[http://logrocket.com/tech](http://logrocket.com/tech)`，可以使用`namespaceURI`参数。要获得限定名，例如，`t:article`，可以使用`qName`参数。

下一步很重要。创建一个新的`XMLParser`实例来处理名称空间:

```
let parser5 = Parser5()
let xmlParser5 = XMLParser(data: xmlData_namespace)
xmlParser5.delegate = parser5
xmlParser5.shouldProcessNamespaces = true

xmlParser5.parse()

```

在编译和运行程序之前，注意有一行告诉解析器处理名称空间。要在 XML 数据中启用名称空间，需要将解析器的`shouldProcessNamespaces`属性设置为`true`。否则，命名空间将被忽略。

编译并运行程序。然后，您将获得名称空间 URIs 和限定名:

```
Namespace URI: , qualified name: root
Namespace URI: http://logrocket.com/tech, qualified name: t:article
Namespace URI: http://logrocket.com/tech, qualified name: t:title
Namespace URI: http://logrocket.com/marketing, qualified name: m:article
Namespace URI: http://logrocket.com/marketing, qualified name: m:title

```

## 解析 XML 数据时获取文本内容

现在，是时候从 XML 数据中获取文本内容了。您需要实现的方法是带有以下签名的`parser`方法:

```
    func parser(
        _ parser: XMLParser,
        foundCharacters string: String
    )

```

添加以下 XML 数据:

```
let xmlContent_text =
"""
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <article>
        <title>Getting Started with Swift</title>
        <published>true</published>
    </article>
    <article>
        <title>How to Parse XML with Rust</title>
        <published>false</published>
    </article>
</root>
""";
let xmlData_text = Data(xmlContent_text.utf8)

```

然后，您需要实现具有处理文本内容的方法的委托类:

```
class Parser6 : NSObject, XMLParserDelegate {

    func parser(
        _ parser: XMLParser,
        foundCharacters string: String
    ) {
        if (string.trimmingCharacters(in: .whitespacesAndNewlines) != "") {
            print(string)
        }
    }

}

```

您只对包含一些字符的文本内容感兴趣。如果没有实现过滤器，就会产生大量空白文本内容。文本上下文被定义为元素节点之间的文本。这也包括`article`元素和`title`元素之间的空白文本:

```
    <article>
        <title>How to Parse XML with Rust</title>

```

不仅仅是空格，在`article`和`title`之间还有一条新的线。这就是为什么你先修剪它，然后检查它是否是一个空字符串。

最后，创建一个`XMLParser`实例来处理文本内容:

```
let parser6 = Parser6()
let xmlParser6 = XMLParser(data: xmlData_text)
xmlParser6.delegate = parser6

xmlParser6.parse()

```

如果您编译并运行了该程序，您应该得到以下文本内容:

```
Getting Started with Swift
true
How to Parse XML with Rust
false

```

## 解析 XML 数据时处理错误

生活并不理想。有时您会得到不完美的 XML 数据，并且数据被损坏。但是不用担心！您可以使用带有以下签名的`parser`方法来处理错误:

```
    func parser(
        _ parser: XMLParser,
        parseErrorOccurred parseError: Error
    )

```

但是首先，您需要编写损坏的 XML 数据:

```
let xmlContent_corrupted =
"""
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <article>
        <title>Getting Started with Swift</title>
    </article>
    <article>
        <title>How to Parse XML with Rust
    </article>
</root>
""";
let xmlData_corrupted = Data(xmlContent_corrupted.utf8)

```

请注意，这里缺少一个结束标签`title`。

然后，创建一个委托类来处理错误:

```
class Parser7 : NSObject, XMLParserDelegate {

    func parser(
        _ parser: XMLParser,
        parseErrorOccurred parseError: Error
    ) {
        print(parseError)
    }

}

```

这个方法给出了包含错误信息的参数`parseError`。在这种情况下，您打印了错误本身。

错误看起来像什么？您需要创建`XMLParser`实例来获取这个委托类:

```
let parser7 = Parser7()
let xmlParser7 = XMLParser(data: xmlData_corrupted)
xmlParser7.delegate = parser7

xmlParser7.parse()

```

如果您编译并运行了该程序，您应该会看到以下错误:

```
Error Domain=NSXMLParserErrorDomain Code=76 "(null)" UserInfo={NSXMLParserErrorColumn=15, NSXMLParserErrorLineNumber=8, NSXMLParserErrorMessage=Opening and ending tag mismatch: title line 7 and article
}

```

您得到了发生错误的行号和列号。您还收到了错误消息，告诉您开始和结束标记不匹配。

## 结论

在本文中，您了解了如何解析 XML 数据。您从创建`XMLParser`实例开始。然后您创建了一个委托的`XMLParserDelegate`类来处理解析过程的逻辑。在那里，您处理了元素、属性和文本内容。您还管理了解析过程中的一个错误，并将解析器配置为处理名称空间。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

本文只是触及了 Swift 中 XML 解析的皮毛。您可以在这里的文档中了解更多关于 XML 解析 API 的信息。本文的代码可以在这个 [GitHub 仓库中找到。](https://github.com/arjunaskykok/XML-parsing-in-swift)