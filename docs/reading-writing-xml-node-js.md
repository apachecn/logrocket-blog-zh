# 在 Node.js - LogRocket 博客中读写 XML

> 原文：<https://blog.logrocket.com/reading-writing-xml-node-js/>

当您想到 Node.js 时，首先想到的可能不是 XML。但是在某些情况下，您可能会发现需要从 Node.js 应用程序中读取或写入 XML。在 npm 上搜索“XML”会返回 3400 多个结果，这绝非巧合；有相当多的与 XML 相关的包，它们专门以不同的方式处理 XML。

在本文中，我们将使用一些最流行的可用 npm 包来探索一些真实的 XML 用例，包括:

请注意，本文中的代码示例是用于演示目的。详细的工作示例代码可在[briandesousa/node-XML-demo](https://github.com/briandesousa/node-xml-demo)获得。

如果您想遵循本文中的说明，您可能想从使用 [express-generator](https://expressjs.com/en/starter/generator.html) 生成一个 Express 应用程序开始。选择 Pug 视图引擎(视图代码示例被写成 Pug 模板)。

## 通过 HTTP 接收 XML

XML-RPC 和 SOAP web 服务曾经是应用程序之间交换数据的标准，但是 JSON APIs(即 REST、GraphQL)已经[取代了 XML 服务](https://blog.logrocket.com/the-quiet-revolution-how-json-displaced-xml-1e1f3e8552f7/)。

尽管如此，仍然存在这样的情况，您可能需要公开一个基于 XML 的 API，以允许其他应用程序将 XML 数据输入到您的应用程序中。幸运的是，npm 上有大量的包，使得消费 XML 数据变得很容易。

比较流行的 XML 包之一是 [xml2js](https://www.npmjs.com/package/xml2js) ，Node.js 比较流行的应用框架之一是 Express。自然，有一个快速中间件包， [express-xml-bodyparser](https://www.npmjs.com/package/express-xml-bodyparser) ，将这两者联系在一起。让我们构建一个可以接收 XML 的快速路由。

首先，我们需要安装 express-xml-bodyparser 包:

```
npm install express-xml-bodyparser

```

然后将 express-xml-bodyparser 中间件添加到 express 应用程序中。默认情况下，中间件将解析任何传入的请求，如果请求的`Content-Type`头被设置为`text/xml`。

```
// app.js
var express = require('express');
var xmlparser = require('express-xml-bodyparser');

var app = express();
app.use(xmlparser());
// other Express middleware and configurations

```

添加一个接收 XML 请求的`/xml2js/customer`路由:

```
// routes/xml2js.js 
router.post('/xml2js/customer', function(req, res, next) {
  console.log('Raw XML: ' + req.rawBody);
  console.log('Parsed XML: ' + JSON.stringify(req.body));
  if (req.body.retrievecustomer) {
    var id = req.body.retrievecustomer.id;
    res.send(`<customer><id>${id}</id><fullName>Bob Smith</fullName></customer>`);
  } else {
    res.status(400).send('Unexpected XML received, missing <retrieveCustomer>');
  }
});

```

为了测试我们的路由，我们可以编写一些发送 XML 请求的客户端 JavaScript:

```
// views/xml2js.pug
fetch('/xml2js/customer', {
  method: 'POST',
  headers: {
    'Content-Type': 'text/xml'
  },
  body: '<retrieveCustomer><id>39399444</id></retrieveCustomer>'
})
.then(response => {
  console.log('Response status: ' + response.status);
  return response.text();
})
.then(responseText => console.log('Response text: ' + responseText)
.catch(error => console.log('Error caught: ' + error));

```

该路由返回 XML 响应。如果您看一下服务器控制台的输出，您可以看到服务器收到的请求正文:

```
Raw XML: <retrieveCustomer><id>39399444</id></retrieveCustomer>
Parsed XML: {"retrievecustomer":{"id":["39399444"]}}

```

等等——您是否注意到收到的原始 XML 和 express-xml-bodyparser 中间件返回的对象之间有什么不同？

原始 XML 的`retrieveCustomer` XML 标记是 camel case，但是 JSON 对象上的`retrievecustomer`键是小写的。这是因为 express-xml-bodyparser 中间件正在配置 xml2js 解析器，包括设置一个将所有 xml 标记转换为小写的选项。

我们希望 JSON 对象的属性与原始请求中的 XML 标记完全匹配。幸运的是，我们可以指定自己的 xml2js 选项，并覆盖中间件提供的默认值。

修改之前添加到 Express 应用程序中的`xmlparser`中间件，以包含一个配置对象，并将`normalizeTags`选项设置为`false`:

```
// app.js
app.use(xmlparser({
  normalizeTags: false
}));

```

重新运行客户端代码并查看服务器控制台日志。原始 XML 和解析的 JSON 对象之间的标记名现在应该匹配了:

```
Raw XML: <retrieveCustomer><id>39399444</id></retrieveCustomer>
Parsed XML: {"retrieveCustomer":{"id":["39399444"]}}

```

xml2js 包公开了其他几个选项，允许您自定义 xml 的解析方式。请参见 npm 上的 [xml2js 以获得选项的完整列表。](https://www.npmjs.com/package/xml2js)

## 用模式验证 XML

XML 的另一个常见用途是作为不同应用程序之间(有时是不同组织之间)交换数据的格式。通常，XML 模式(XSD)用于定义每个应用程序应该发送和接收的 XML 消息结构。每个应用程序都根据模式验证传入的 XML 数据。

XML 数据可以以各种方式在应用程序之间传输。例如，应用程序可以通过 HTTP 连接接收 XML，或者通过 SFTP 连接接收保存到文件系统的平面文件。

虽然有相当多的 npm 包可用于处理 XML，但是当您还需要 XML 模式验证时，选择就比较有限了。让我们看一下 [libxmljs2](https://www.npmjs.com/package/libxmljs2) 包，它支持 XML 模式验证。我们将编写一些代码来从服务器的文件系统加载 XML 模式，并使用它来验证一些传入的 XML 数据。

首先在应用程序根目录下的`schemas`目录中创建 XML 模式:

```
<!-- schemas/session-info.xsd -->
<xs:schema attributeFormDefault="unqualified" elementFormDefault="qualified" xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="sessionInfo">
    <xs:complexType>
      <xs:sequence>
        <xs:element type="xs:int" name="customerId"/>
        <xs:element type="xs:string" name="customerName"/>
        <xs:element type="xs:string" name="token"/>
      </xs:sequence>
      <xs:attribute type="xs:long" name="id"/>
    </xs:complexType>
  </xs:element>
</xs:schema>

```

安装 libxmljs2 包:

```
npm install libxmljs2

```

创建新的`/libxmljs2/validateSessionXml`路线:

```
// routes/libxmljs2.js
var express = require('express');
var router = express.Router();
var libxmljs = require('libxmljs2');
var fs = require('fs');
var path = require('path');

var router = express.Router();

router.post('/libxmljs2/validateSessionXml', (req, res, next) => {
  var xmlData = req.body;

  // parse incoming XML data
  var xmlDoc = libxmljs.parseXml(xmlData);  

  // load XML schema from file system
  var xmlSchemaDoc = loadXmlSchema('session-info.xsd');

  // validate XML data against schema
  var validationResult = xmlDoc.validate(xmlSchemaDoc);

  // return success or failure with validation errors
  if (validationResult) {
    res.status(200).send('validation successful');
  } else {
    res.status(400).send(`${xmlDoc.validationErrors}`);
  }  
});

function loadXmlSchema(filename) {
  var schemaPath = path.join(__dirname, '..', 'schemas', filename);
  var schemaText = fs.readFileSync(schemaPath, 'utf8');
  return libxmljs.parseXml(xmlSchema); 
}

```

> **提示**:如果您仍然使用上一个示例中的 express-xml-bodyparser 中间件，您可能需要修改第 2 行，使用`req.rawBody`而不是`req.body`来绕过`xlm2js`并访问原始请求字符串。

在第 14 行，libxmljs2 的`parseXml()`函数解析请求中的 XML。它返回一个`libxmljs.Document`对象，该对象公开了一个接受另一个包含 XML 模式的`libxmljs.Document`的`validate()`函数。validate 函数将返回`true`或一个包含验证错误列表的字符串。在第 23–27 行，我们根据验证结果返回适当的响应。

第 30 行的`loadXmlSchema()`函数使用标准的 Node.js `path`和`fs`模块从服务器的文件系统加载一个 XML 模式。我们再次使用`parseXml()`函数将模式文件的内容解析为`libxmljs.Document`对象。XML 模式归根结底只是 XML 文档。

现在我们已经实现了一个路由，我们可以编写一些简单的客户端 JavaScript 来用一个有效的 XML 请求测试我们的路由:

```
// views/libxmljs2.pug
fetch('/libxmljs2/validateSessionXml', {
  method: 'POST',
  headers: { 'Content-Type': 'text/xml' },
  body: `<?xml version="1.0"?>
<sessionInfo id="45664434343">
  <customerId>39399444</customerId>
  <customerName>Bob Smith</customerName>
  <token>343ldf0bk343bz43lddd</token>
</sessionInfo>`
})
.then(response => response.text())
.then(response => console.log(response))
.catch(error => console.log('Error caught: ' + error));
// console output: validation successful

```

我们还可以发送一个无效的 XML 请求，并观察返回的验证错误:

```
// views/libxmljs2.pug
fetch('/libxmljs2/validateSessionXml', {
  method: 'POST',
  headers: { 'Content-Type': 'text/xml' },
  body: `<?xml version="1.0"?>
<sessionInfo id="45664434343a">
  <customerName>Bob Smith</customerName>
  <token>343ldf0bk343bz43lddd</token>
</sessionInfo>`
})
.then(response => response.text())
.then(response => console.log(response))
.catch(error => console.log('Error caught: ' + error));
// console output: Error: Element 'customerName': This element is not expected. Expected is ( customerId ).

```

libxmljs2 的`validate()`函数返回的错误消息相当详细；但是，错误消息格式使得很难解析单个错误消息并将它们映射到最终用户友好的文本。除此之外，根据模式验证 XML 只需要很少的代码。

## 操作 HTML 内容

如果你的应用程序需要操作 HTML 怎么办？与我们到目前为止讨论的例子不同，HTML 在技术上不符合 XML 规范。

有几个 npm 包专门处理 HTML 与 XML 之间的细微差别。其中之一是 [Cheerio](https://www.npmjs.com/package/cheerio) 。让我们看看如何使用 Cheerio 来读取、操作和返回 HTML 片段。

从安装 Cheerio 包开始:

```
npm install cheerio

```

创建新的`/cheerio/highlightTable`路线:

```
// routes/cheerio.js
var cheerio = require('cheerio');
var express = require('express');
var router = express.Router();

router.post('/cheerio/highlightTable', (req, res, next) => {
  // decode HTML framgent in request body
  var decodedHtml = decodeURI(req.body.encodedHtml);

  try {
    // parse HTML fragment
    var $ = cheerio.load(decodedHtml);

    // use the cheerio selector to locate all table cells in the HTML
    $('td').each(function() {
      tableCellText = $(this).text();
      tableCellNumber = parseFloat(tableCellText);
      if (tableCellNumber) {
        // highlight cells based on their numeric value
        if (tableCellNumber >= 0) {
          $(this).prop('style', 'background-color: #90ee90');
        } else {
          $(this).prop('style', 'background-color: #fa8072');
        }
      }
  } catch(err) {
    return res.status(500).send({ error: err});
  }

  // only return the HTML fragment that was received in the request
  updatedHtml = $('body').html();
  res.status(200).send({ encodedHtml: encodeURI(updatedHtml) });
});

```

在第 8 行，使用内置的`decodeURI`函数对`encodedHtml`请求属性进行解码。当在 JSON 请求中发送和接收 HTML 字符串时，我们必须对其进行编码，以避免特殊字符(如双引号)与 JSON 语法冲突。

在第 12 行，Cheerio 的`load()`函数用于解析 HTML 片段。该函数返回一个选择器对象，其 API 与 jQuery 核心 API 几乎相同。

然后在第 15–25 行使用选择器来定位和提取 HTML 片段中所有表格单元格内的文本。选择器提供的`prop()`函数用在第 21 行和第 23 行，通过添加新的`style`属性来修改 HTML 片段。

在第 31 行，选择器用于从 HTML 片段中提取`body`元素，并将其作为 HTML 字符串返回。即使请求中传递的 HTML 片段不包含外部的`<html>`或`<body>`标签，Cheerio 也会自动将 HTML 片段包装成结构正确的 HTML 文档。调用`load()`函数时会发生这种情况。

最后，HTML 字符串被编码并在第 32 行发送回客户机。

让我们编写一些客户端 JavaScript 来测试路由:

```
// views/cheerio.pug
var sampleHtml = 
  '<table border="1" cellpadding="5px" cellspacing="0">\n' +
  '  <tr>\n' +
  '    <td>Sammy Steakhouse Inc.</td>\n' +
  '    <td>-130.33</td>\n' +
  '  </tr>\n' +
  '  <tr>\n' +
  '    <td>ATM deposit</td>\n' +
  '    <td>500.00</td>\n' +
  '  </tr>\n' +
  '  <tr>\n' +
  '    <td>Government cheque deposit</td>\n' +
  '    <td>150.00</td>\n' +
  '  </tr>\n' +
  '</table>';

fetch('/cheerio/highlightTable', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    encodedHtml: encodeURI(sampleHtml)
  })
})
.then(response => response.json())
.then(response => {
  if (response.error) {
    console.log('Error received: ' + response.error);
  } else {
    decodedHtml = decodeURI(response.encodedHtml);
    console.log('Received HTML: ' + decodedHtml);
    // console output:
    // <table border="1" cellpadding="5px" cellspacing="0">
    //  <tbody><tr>
    //    <td>Sammy Steakhouse Inc.</td>
    //    <td style="background-color: #fa8072">-130.33</td>
    //  </tr>
    //  <tr>
    //    <td>ATM deposit</td>
    //    <td style="background-color: #90ee90">500.00</td>
    //  </tr>
    //  <tr>
    //    <td>Government cheque deposit</td>
    //    <td style="background-color: #90ee90">150.00</td>
    //  </tr>
    // </tbody></table>
  }
})
.catch(error => console.log('Error caught: ' + ${error})); 

```

在第 22 行，HTML 片段使用浏览器内置的`encodeURI`函数进行编码，类似于我们在前面的例子中在服务器端解码的方式。

您将注意到正在发送的原始 HTML 片段(第 3–16 行)和返回的修改后的 HTML 片段(在第 32–46 行的注释中演示)之间的一些差异:

*   一些额外的标签如`<tbody>`被添加到返回的 HTML 中。这些标签是由 Cheerio `load()`函数自动添加的。Cheerio 在加载 HTML 时非常宽容:它会添加额外的标签来确保 HTML 是一个符合标准的 HTML 文档
*   `style`属性按预期添加到每个`<td>`标签

## 生成 SVG 图像

与其他例子相比，这个例子更加直观和有趣。让我们通过修改它们的 XML 源代码来操作一些 SVG 图像。

首先，快速入门可伸缩矢量图形(SVG)。SVG 是所有主流浏览器都支持的基于 XML 的图像格式。SVG XML 由一系列定义不同形状类型的元素组成。 [CSS 样式可以包含在每个形状](https://blog.logrocket.com/animating-svg-with-css-83e8e27d739c/)中，以定义其外观。通常，您会使用工具来生成 SVG XML，而不是手工编码，但是鉴于 SVG 图像只是 XML，通过 JavaScript 进行图像操作是可能的。

我们将创建一个接受三种颜色的路由，从服务器的文件系统加载一个 SVG 图像，将这些颜色应用于图像中的某些形状，并将其返回给客户端进行渲染。我们将使用 [svgson](https://www.npmjs.com/package/svgson) 包在 SVG XML 和 JSON 之间进行转换，以简化操作图像所需的代码。

首先安装 svgson 包:

```
npm install svgson

```

创建新的`/svgson/updateSVGImageColors`路线:

```
// routes/svgson.js
var express = require('express');
var fs = require('fs');
var path = require('path');
var { 
  parse: svgsonParse,
  stringify: svgsonStringify
} = require('svgson')

var router = express.Router();

router.post('/svgson/updateSVGImageColors', function(req, res, next) {
    // 3 colors provided to stylize the SVG image
    var { color1, color2, color3 } = req.body;

    // load the original SVG image from the server's file system
    var svgImageXML = loadSVGImageXML('paint.svg');

    // use svgson to convert the SVG XML to a JSON object
    svgsonParse(svgImageXML).then(json => {

      // get the shape container that contains the paths to be manipulated
      gElement = json.children.find(elem => elem.name == 'g'
        && elem.attributes.id == 'g1727');      

      // update styles on specific path shapes
      updatePathStyleById(gElement, 'path995', 'fill:#000000', 'fill:' + color1);
      updatePathStyleById(gElement, 'path996', 'fill:#ffffff', 'fill:' + color2);
      updatePathStyleById(gElement, 'path997', 'fill:#ffffff', 'fill:' + color3);

      // convert JSON object back to SVG XML
      svgImageXML = svgsonStringify(json);

      // return SVG XML
      res.status(200).send(svgImageXML);
    });
});

function updatePathStyleById(containerElem, pathId, oldStyle, newStyle) {
  pathElem = containerElem.children.find(elem => elem.attributes.id == pathId);
  pathElem.attributes.style = pathElem.attributes.style.replace(oldStyle, 
    newStyle);
}

function loadSVGImageXML(filename) {
  var svgImagePath = path.join(__dirname, '..', 'public', 'images', filename);
  return fs.readFileSync(svgImagePath, 'utf8');
}

```

这段代码中有相当多的内容。让我们把它分解一下，突出几个关键概念。

在第 5–8 行，`parse`和`stringify`模块是从 svgson 包中导入的。这些模块名非常通用，但是我们可以使用对象析构来给它们起一个更简洁的名字，比如`svgsonParse`和`svgsonStingify`。

在第 17 行，`loadSVGImageXML()`函数用于使用本地 Node.js 模块从服务器的文件系统加载预定义的 SVG 图像的内容。正在使用的图像是`[paint.svg](https://github.com/briandesousa/node-xml-demo/blob/main/public/images/paint.svg)`。开始时看起来是这样的:

![Black And White Image Of Paint And A Paintbrush](img/56dd2728ac4d41168a82583b0dc723be.png)

Original paint.svg image.

第 20–36 行是图像处理魔术发生的地方。SVG XML 被转换成 JSON 对象。标准 JavaScript 用于导航对象树以定位我们想要操作的三个路径形状。下面是 JSON 对象树(左侧)和 SVG XML(右侧)的比较，有助于将其可视化。请注意，为了简洁起见，已经删除了一些元素。

![Code Highlighting SVG JSON Object Tree Being Compared To SVG XML](img/70a4d06176e90450c0b5aef38045e508.png)

SVG JSON object tree compared to SVG XML.

第 27–29 行调用的`updatePathStyleById()`辅助函数通过其 ID 定位路径形状，并用使用请求中提供的颜色构建的新填充样式替换其填充样式。

SVG JSON 对象在第 32 行被转换回 SVG XML 字符串，并在第 35 行返回给客户机。

让我们编写一些客户端 JavaScript 来测试路由:

```
// views/svgson.pug
fetch('/svgson/updateSVGImageColors', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    color1: '#FF0000', 
    color2: '#00FF00',
    color3: '#0000FF'
  })
})
.then(response => response.text())
.then(svgImageXml => console.log(svgImageXml))
.catch(error => console.log('Error caught: ' + error));

```

如果我们要呈现这个返回的 SVG XML，它应该是这样的:

![Green, Blue, And Red Paint With A Paintbrush](img/74e27ef9484d652b56a01ff28949d854.png)

Updated paint.svg image.

## 总结:您已经拥有足够的 XML 了吗？

我们讨论了 XML 的一些常见用途，从枯燥的基于 XML 的数据交换到 SVG 图像处理。下面是我们所了解的 npm 包的总结，以及使它们与众不同的关键特性。

| npm 包 | 关键特征 |
| --- | --- |
| [xml2js](https://www.npmjs.com/package/xml2js) | 

*   可以在 XML 和 JavaScript 之间进行双向转换
*   公开几个可用于改变 XML 解析方式的选项
*   使用 express-xml-bodyparser 中间件与 Express 很好地配对

 |
| [libxmljs2](https://www.npmjs.com/package/libxmljs2) | 

*   可以根据 XML 模式解析和验证 XML

 |
| [再见](https://www.npmjs.com/package/cheerio) | 

*   专门解析和操作 HTML
*   可用于加载 HTML 或 XML 文档
*   有意宽恕；将添加缺少的 HTML 标记以确保 HTML 有效

 |
| [svgson](https://www.npmjs.com/package/svgson) | 

*   可以在 SVG 图像(XML)和 JSON 之间执行双向转换
*   使得用 JavaScript 操作 SVG 图像变得更加容易

 |

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.