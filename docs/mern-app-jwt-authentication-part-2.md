# 使用 JWT 认证创建全栈 MERN 应用程序:第 2 部分

> 原文：<https://blog.logrocket.com/mern-app-jwt-authentication-part-2/>

到目前为止，我们已经在本系列的第一部分中介绍了 JWT 认证的核心功能:“[使用 JWT 认证创建全栈 MERN 应用程序:第 1 部分](https://blog.logrocket.com/mern-app-jwt-authentication-part-1/)”

逻辑运行在服务器端，我们需要一个接口来帮助我们使用逻辑。所以我们将创建一个基于 REST API 的 Express.js 服务器。服务器将公开几个端点来登录和验证用户。

步骤很简单。我们需要首先导出函数，以便可以将它们导入到我们的主 Express.js 服务器脚本中。然后，我们需要公开几个接受 HTTP `GET`和`POST`请求的 REST 端点，最好一个用于登录，一个用于获取现有令牌，一个用于注销。我们还将公开另一个端点来利用承载认证头。

## 承载认证

术语承载认证，有时称为令牌认证，是一种 [HTTP 认证方案](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)，涉及一些称为承载令牌的秘密字符串或安全令牌。“载体认证”这个名字可以被理解为“给予这个令牌的载体访问权”

承载令牌是加密生成的字符串，通常由服务器在客户端发出登录请求时生成。每当客户端试图访问需要认证的资源时，它必须在`Authorization`报头中发送这个令牌:

```
Authorization: Bearer <token>
```

该方案最初是作为 [RFC 6750](https://tools.ietf.org/html/rfc6750) 中 [OAuth 2.0](https://oauth.net/2/) 的一部分创建的。像[基本认证](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)一样，承载认证应该只在 HTTPS (SSL)上使用。

### 在`<token>`前使用`Bearer`

任何人想到的最常见的问题是在`<token>`前面使用`Bearer`背后的原因。为什么不简单地:

```
Authorization: <token>
```

对于大多数开发人员来说，这绝对是一个合理的问题。`Authorization: <type> <credentials>`模式是由 W3C 在 [HTTP 1.0](https://tools.ietf.org/html/rfc1945) 中引入的，此后在很多地方都得到了重用。许多 web 服务器支持多种授权方法。在这些情况下，仅仅发送令牌是不够的。早在无记名授权之前，这个报头就被用于[基本认证](https://www.httpwatch.com/httpgallery/authentication/)。

为了实现互操作性，这些头文件的使用是由 W3C 规范管理的，所以即使您正在读取和编写头文件，也应该遵循它们。不记名区分你使用的授权类型，所以这很重要。Google 将其描述为在每个 inline action HTTP 请求的`Authorization`头中设置一个`Bearer Token`。例如:

```
POST /rsvp?eventId=123 HTTP/1.1
Host: praveen.science
Authorization: Bearer Prav33nIsGr3a7JK
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/1.0 (KHTML, like Gecko; Gmail Actions)

rsvpStatus=YES
```

上例中的字符串`Prav33nIsGr3a7JK`是不记名授权令牌。这是由我们的应用程序生成的加密令牌。

### 不记名令牌的验证

如果使用不记名令牌，请验证请求是否来自受信任的域(例如，您的客户端应用程序位置)并且是针对发送方域的。如果令牌没有通过验证，服务应该用 HTTP 响应代码`401 (Unauthorized)`来响应请求。

## 导出到 REST 端点

由于我们有了`dec-enc.js`中的代码，我们需要导出正确的函数。在文件的最后，让我们添加一些导出语句，并对它与 REST 端点的工作方式做一些更改。我们将用这种方法做的几件事是将`claims`、`key`和`header`转换成编码函数的参数。

### 核心功能的变化

在本节中，我们将只处理`dec-enc.js`。我们有许多`console.log()`,我们需要不惜一切代价删除它们，因为它们可能会将一些相当敏感的数据泄露给服务器日志记录器(也就是说，如果有人获得了对它的访问权)。接下来的事情是将代码分割成不同的单元和功能，然后一个接一个地导出所有内容。

### 移除日志

这个过程应该相当简单——只需找到所有的`console.log`语句并删除它们。我们之前添加它们只是为了调试的目的，而不是为了别的。修改后的代码如下所示。

```
const JSRSASign = require("jsrsasign");

// Generation
const claims = {
  Username: "praveen",
  Age: 27,
  Fullname: "Praveen Kumar"
};
const key = "$PraveenIsAwesome!";
const header = {
  alg: "HS512",
  typ: "JWT"
};

const sHeader = JSON.stringify(header);
const sPayload = JSON.stringify(claims);
// Generate the JWT
const sJWT = JSRSASign.jws.JWS.sign("HS512", sHeader, sPayload, key);

const token = "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJBZ2UiOjI3LCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.Nut6I57FYUGP973LgfOUNUBjMlaIm6NKk8ffgX4BTdQ_Wc2ob8m6uOwWlgoNMxpuRQaOoeFQOHhrIOJ_V8E-YA";
const algorithm = "HS512";

// Decoding
const sJWS = token;
const aJWT = sJWS.split(".");
const uHeader = JSRSASign.b64utos(aJWT[0]);
const uClaim = JSRSASign.b64utos(aJWT[1]);
const pHeader = JSRSASign.jws.JWS.readSafeJSONString(uHeader);
const pClaim = JSRSASign.jws.JWS.readSafeJSONString(uClaim);
```

由于我们从用户或服务器获取一切，我们必须将上述内容转换成可调用的单元或函数。这里需要两个函数，一个用于生成 JWT，另一个用于验证 JWT。这使得我们最后只导出两个函数，我们将使用`module.exports.Variable`导出，使用`require`导入。

### 生成、验证和确认 JWT 的函数

我们将编写的第一个函数接受几个参数，如`claims`、`key`和`header`，并返回我们一个 JWT。我们需要明确的一件事是在哪里定义什么。例如，`key`和`header`这样的常数会去哪里？`dec-enc.js`的作用是提供一个生成 JWT 和验证 JWT 的通用函数。

所以让我们保持它的通用性——不要硬编码上面的内容。让我们为他们创建三个函数。因为我们使用 Node.js 作为后端，所以让我们使用 ES6 胖箭头函数，因为它们在某些情况下比传统函数更有效和有用。但在这之前，我们先从导入所需的`jsrsasign`(即 JS RSA Sign)库开始。

```
const JSRSASign = require("jsrsasign");

const GenerateJWT = () => {};

const DecodeJWT = () => {};

const ValidateJWT = () => {};

module.exports = {
  GenerateJWT,
  DecodeJWT,
  ValidateJWT
};
```

在上面的代码中，有些人可能会对导出部分感到困惑。以`module.exports`开始的最后一段代码可以这样解释:`module`是一个用于描述模块的内置对象，它有一个参数`exports`，可以用我们想要导出的命名变量多次赋值。

这个物体看起来也有点不同。它利用了对象文字属性值速记。这是 ES6 中的另一个语法糖。考虑下面的 ES5 片段:

```
{ GenerateJWT: GenerateJWT, DecodeJWT: DecodeJWT, ValidateJWT: ValidateJWT }
```

有了新的简写形式，这可以改写如下:

```
{ GenerateJWT, DecodeJWT, ValidateJWT }
```

如您所见，这是可行的，因为属性值与属性标识符同名。这是最新 [ECMAScript 6 草案 Rev 13](http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts) 中*对象初始化器* ( [第 12.1.5 节](http://teramako.github.io/ECMAScript/ecma6th_syntax.html#12.1.5))语法的新增内容。是的，就像 ECMAScript 3 中设置的限制一样，您不能使用保留字作为您的属性名。

如上所述，我们将根据之前所做的编写两个函数的主体，在这种情况下，函数参数将成为输入。让我们在这里开始编写函数:

```
const JSRSASign = require("jsrsasign");

const GenerateJWT = (header, claims, key) => {
  // Let's convert everything into strings.
  const sHeader  = JSON.stringify(header);
  const sPayload = JSON.stringify(claims);
  // Generate the JWT and return it to the caller.
  const sJWT = JSRSASign.jws.JWS.sign("HS512", sHeader, sPayload, key);
  return sJWT;
};

const DecodeJWT = sJWS => {
  const aJWT = sJWS.split(".");
  const uHeader = JSRSASign.b64utos(aJWT[0]);
  const uClaim  = JSRSASign.b64utos(aJWT[1]);
  const pHeader = JSRSASign.jws.JWS.readSafeJSONString(uHeader);
  const pClaim  = JSRSASign.jws.JWS.readSafeJSONString(uClaim);
  return pClaim;
};

const ValidateJWT = (header, token, key) => {
  return JSRSASign.jws.JWS.verifyJWT(token, key, header);
};

module.exports = {
  GenerateJWT,
  DecodeJWT,
  ValidateJWT
};
```

### 导出和导入函数

`module.exports`或`exports`是一个特殊的对象，应该出现在需要导出 Node.js 应用程序中的资源的 JavaScript 文件中。`module`是代表当前模块的变量，`exports`是将被公开为模块的对象。所以，你分配给`module.exports`或`exports`的任何东西都将作为一个模块暴露出来。

```
module.exports = {
  GenerateJWT,
  DecodeJWT,
  ValidateJWT
};
```

在上面的代码中，我们从这个文件中导出了`GenerateJWT`、`DecodeJWT`和`ValidateJWT`。我们将能够通过使用以下代码来导入函数:

```
const DecEnc = require("./dec-enc.js");
```

我们可以用几种不同的方式来使用它们。一种常见的方法是使用父对象，如下所示:

```
DecEnc.GenerateJWT();
DecEnc.DecodeJWT();
DecEnc.ValidateJWT();
```

另一种方法是使用命名导出和对象[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)，析构如下:

```
const { GenerateJWT, DecodeJWT, ValidateJWT } = require("./dec-enc.js");
```

上面的方法会更容易，因为我们知道函数的名字，并且它们是我们将要使用的唯一的函数。

## 创建 REST API 端点

让我们首先创建一个基本的 Express.js 服务器，`app.js`。我们可以从[快速表达“Hello World”的例子](https://expressjs.com/en/starter/hello-world.html)开始。

从请求`express`并定义应用程序监听的端口地址开始，我们通过调用`express()`并在名为`app`的变量中存储返回值来创建 Express.js 服务器的实例。然后，我们逐一定义路线:

```
const express = require('express');
const app = express();
const port = process.env.PORT || 3100;

app.get('/', (req, res) => res.send('Hello World!'));

app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

当你运行`node app`时，默认的路线会在你打开`[http://localhost:3100](http://localhost:3100)`网页时在你的浏览器上显示给我们`Hello World!`，或者按照`PORT`环境变量中的定义。

如果该变量未定义，服务器应用程序通过使用由`||`表示的`OR`条件退回到端口`3100`，并存储在`port`常量中。最后，我们让服务器监听最后一行中的端口。

完成以上工作后，让我们也包含来自我们的`dec-enc.js`的三个函数。让我们将所有的`require`语句放在一起，将库`require`放在最上面，后面是我们的自定义`require` s。将这些添加到上面的代码中，我们的`app.js`将如下所示:

```
const express = require('express');
const { GenerateJWT, DecodeJWT, ValidateJWT } = require("./dec-enc.js");

const app = express();
const port = process.env.PORT || 3100;

app.get('/', (req, res) => res.send('Hello World!'));

app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

让我们继续为 API 调用创建路由。

### 创造正确的路线

对于连接到 React 应用程序的 REST 端点，最好对所有调用使用一个公共的路由前缀`/api/`。由于调用既不是幂等运算符，也不包含不敏感的数据，所以在这里使用`POST`方法总是更好。这不会通过查询字符串将数据传递给服务器，而是通过内容体。

Express.js 不能处理 POST 数据中的内容。为了解析或读取 POST 数据，我们需要使用 Node.js 主体解析中间件。`[express.json()](https://expressjs.com/en/api.html#express.json)`是 Express 中内置的中间件功能。它使用 JSON 有效负载解析传入的请求，并基于`[body-parser](https://expressjs.com/en/resources/middleware/body-parser.html)`。在定义了`app`之后，使用下面的新代码行激活这个中间件。

同样，让我们用一条适当的消息来更新默认的 home 根(`/`)，解释他们如何正确地使用它。出于说明的目的，下面的代码没有 API 路由的实现。

```
const express = require("express");
const { GenerateJWT, DecodeJWT, ValidateJWT } = require("./dec-enc.js");

const app = express();
app.use(express.json());
const port = process.env.PORT || 3100;

const welcomeMessage =
  "Welcome to the API Home Page. Please look at the documentation to learn how to use this web service.";

app.get("/", (req, res) => res.send(welcomeMessage));

app.post("/api/GenerateJWT", (req, res) => res.send(""));
app.post("/api/DecodeJWT", (req, res) => res.send(""));
app.post("/api/ValidateJWT", (req, res) => res.send(""));

app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

`app.use(express.json())`返回只解析 JSON 的中间件，并且只查看`Content-Type`头匹配`type`选项的请求。这个解析器接受正文的任何 Unicode 编码，并支持自动扩展`gzip`和`deflate`编码。

包含解析数据的新的`body`对象被填充到中间件(即`req.body`)之后的`request`对象上，或者如果没有要解析的主体、`Content-Type`不匹配或发生错误，则填充一个空对象(`{}`)。

### 实施路线

我们现在需要做的就是从用户的请求对象中用正确的参数调用这三个方法。request ( `req`)对象将包含客户端浏览器或用户发送到服务器的信息，如果我们需要向客户端浏览器或用户发回一些东西，我们将使用 response ( `res`)对象。`req.body`将拥有每次通话所需的所有信息。

```
const express = require("express");
const { GenerateJWT, DecodeJWT, ValidateJWT } = require("./dec-enc.js");

const app = express();
app.use(express.json());
const port = process.env.PORT || 3100;

const welcomeMessage =
  "Welcome to the API Home Page. Please look at the documentation to learn how to use this web service.";

app.get("/", (req, res) => res.send(welcomeMessage));

app.post("/api/GenerateJWT", (req, res) =>
  res.json(GenerateJWT(req.body.header, req.body.claims, req.body.key))
);
app.post("/api/DecodeJWT", (req, res) => 
  res.json(DecodeJWT(req.body.sJWS))
);
app.post("/api/ValidateJWT", (req, res) =>
  res.json(ValidateJWT(req.body.header, req.body.token, req.body.key))
);

app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

## 测试 API 端点

三个函数`GenerateJWT`、`DecodeJWT`和`ValidateJWT`都返回一个有效的 JavaScript 对象。因此，我们可以通过调用`res.json()`并传递函数的返回值，将响应盲目地发送成 JSON 格式。这将被格式化成 JSON 并发送给浏览器。这可以被任何接受`Content-type: application/json`响应的客户端使用，比如 React 应用程序。

为了确保这些工作正常，我们可以使用一个名为 [Postman](https://www.getpostman.com/) 的免费工具，这是一个完整的 API 开发环境。这个工具帮助我们测试 API 端点和检查响应。

![API Requests Using Postman](img/80a212ca2d4f03b6b0b8682328a1604d.png)

API requests using Postman.

使用 Postman，我们可以添加 URL，设置 HTTP 请求方法，添加头，并执行请求以找到要显示和验证的正确输出。现在我们已经完成了服务器端的工作，生成令牌，通过 REST 端点向客户端发送数据，让我们在第三部分中[构建客户端来获取令牌并解码它](https://blog.logrocket.com/mern-app-jwt-authentication-part-3/)！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。