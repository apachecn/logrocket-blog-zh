# 使用 JWT 认证创建全栈 MERN 应用程序:第 1 部分

> 原文：<https://blog.logrocket.com/mern-app-jwt-authentication-part-1/>

我的绿地项目需要通常的歌舞:认证。我们的客户想要时髦一点，并且发现认证中的趋势技术是 [JWT (JSON Web Token)](https://tools.ietf.org/html/rfc7519) ，并且在将它用于所有认证目的时，压力很快就增大了。

作为一个反应快的人，我不介意在这上面工作和学习一项新技术，但是，是的——这就是为什么我在这里写我的经验。

我们正在构建一个与[对话的 React 前端。NET Core API 服务器](https://dotnet.microsoft.com/)。身份验证机制建立在 JWT 上。项目的. NET 端。我正在使用 React 在前端实现 JWT 认证，现在开始。

另外，请注意，我没有添加任何 MongoDB 部分，但我称它为 [MERN 堆栈应用程序](https://blog.logrocket.com/mern-stack-a-to-z-part-2/)——抱歉。这不在本文的讨论范围之内。

## 什么是 JWT？

JSON Web 令牌是一种开放的、符合行业标准的 RFC 7519 方法，用于安全地表示双方之间的声明。一个 [JWT](https://blog.logrocket.com/jwt-authentication-best-practices/) 是一个 [JSON](https://www.json.org/) 响应，它在 RFC 7519 中被定义为一种在双方之间表示一组信息的安全方式。令牌由报头、有效载荷和签名组成。

简单地说，JWT 就是以下格式的字符串:

![JWT String Format](img/971cf6e0e4439d35271bc96857917b1d.png)

JWT string format.

应该注意的是，双引号引起的字符串实际上被认为是有效的 JSON 响应:

```
"header.payload.signature"
```

### JWT 流

由于我们在基于微服务的架构中工作，我们有不同的微服务，其中一个是身份验证服务。这是后端服务之一。NET Core API，它基于域 cookie 中的引用令牌检索用户声明，并为该声明生成 JWT。

如果返回有效的 JWT，则调用被转发到相应的服务，并且 JWT 作为 OAuth 承载令牌在请求头中传递，以供后台服务进一步授权。

![JWT Auth Sequence](img/0343d7cb070f1c3eee02785006b12b40.png)

JWT auth sequence represented in a flowchart.

使用下图可以简化上述顺序:

![Simplified JWT Auth Sequence](img/b14606ce85b5152f8c21a753ecf3340c.png)

Simplified representation of JWT auth sequence, courtesy of [TopTal](https://www.toptal.com/java/rest-security-with-jwt-spring-security-and-java%23jwt-authentication).

让我们现在开始构建类似的东西。

## 先决条件

正如我所说的，这是一个我们将要构建的 MERN 堆栈应用程序。显而易见的要求是:

我想现在够了。

## 创建 Express.js 服务器

下面是最简单的代码形式:`index.js`运行`node index`后，当你打开端口 3000 上的[本地主机时，浏览器上会显示`Hello World!`:](http://localhost:3000/)

```
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => res.send("Hello World!"));

app.listen(port, () => console.log(`Example app listening on port ${port}!`));
```

## 在服务器上生成 JWT

我们必须从基于 JWT 的认证开始，并尝试验证一些东西。jwt.io 给出了支持 jwt 不同特性的不同库的列表。我试图找到一个支持一切的，但在 JavaScript 中没有。😔

让我们想想这里需要什么。后端应该能够支持算法 HS512 和 RS512，因为这些是我们的一些银行客户推荐的。

### 选择正确的库！

我计划使用`[jsrsasign](https://www.npmjs.com/package/jsrsasign)`，因为这是最接近我的后端团队生成的，并且它支持所有的算法。让我们以这种方式包含它们，但是不要忘记在运行代码之前在本地安装软件包:

```
const JSRSASign = require("jsrsasign");
```

### 实现编码功能

JWT 有效载荷通常被称为声明——不知道为什么。所以让我们先创建一个`claims`:

```
const claims = {
  Username: "praveen",
  Age: 27,
  Fullname: "Praveen Kumar"
}
```

这就是我现在从 Users DB 中为我的应用程序所需要的一切。我在这里没有涉及任何数据库概念，所以我跳过了它们。接下来是私钥——顾名思义，让它保持私有，不要在任何客户端代码中使用它。

```
const key = "$PraveenIsAwesome!";
```

> **注意:**绝对保密这个东西！🤐

现在我们已经准备好了我们的`claims`和`key`。让我们开始签名并生成令牌。在签名之前，我们必须确定我们将使用哪种算法。这些细节将在标题中。所以，让我们创建一个标题。

```
const header = {
  alg: "HS512",
  typ: "JWT"
};
```

我们将使用 HS512 (HMAC-SHA512)算法对令牌进行签名，并且我们将生成一个 JWT。您可以生成的其他类型或现有标准有:

*   JSON 网络签名(JWS): [RFC7515](https://tools.ietf.org/html/rfc7515)
*   JSON 网页加密(JWE): [RFC7516](https://tools.ietf.org/html/rfc7516)
*   JSON Web Key (JWK): [RFC7517](https://tools.ietf.org/html/rfc7517)
*   JSON 网络算法(JWA)–[RFC 7518](https://tools.ietf.org/html/rfc7518)
*   JSON Web 令牌(JWT): [RFC7519](https://tools.ietf.org/html/rfc7519)
*   JSON Web Key (JWK)指纹: [RFC7638](https://tools.ietf.org/html/rfc7638)
*   JWS 未编码有效载荷选项: [RFC7797](https://tools.ietf.org/html/rfc7797)
*   CFRG 椭圆曲线签名(EdDSA): [RFC8037](https://tools.ietf.org/html/rfc8037)

让我们继续下一个生成令牌的主要过程。我们已经得到了我们所需要的一切:`header`、`payload` ( `claims`)和`key`。我们需要将上述对象字符串化并发送给生成器。

```
var sHeader = JSON.stringify(header);
var sPayload = JSON.stringify(claims);
```

`key`已经是一个字符串了，所以不要费心将其字符串化。要生成密钥，我们应该将三个字符串和算法一起传递给`JSRSASign.jws.JWS.sign()`函数，如下所示:

```
const sJWT = JSRSASign.jws.JWS.sign("HS512", sHeader, sPayload, key);
```

您得到的输出是一个 JWT！可以在 [jwt.io](https://jwt.io/) 验证正确性。最后，将字符串记录到控制台将得到输出:

```
console.log(sJWT);
```

当我执行上面的代码时，我得到了这个:

```
​​eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJBZ2UiOjI3LCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.Nut6I57FYUGP973LgfOUNUBjMlaIm6NKk8ffgX4BTdQ_Wc2ob8m6uOwWlgoNMxpuRQaOoeFQOHhrIOJ_V8E-YA
```

将它和我的签名一起放入在线验证器，我得到了相同的输出，其中包含一条经过签名验证的消息:

![Signature Verified](img/d4960a2fc34f576b5d23a11132ddc513.png)

Signature verified.

呜哇！你可以在这里查看[。](https://jwt.io%2F%23debugger-io%3Ftoken%3DeyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJQYXNzd29yZCI6IlByQHYzM24iLCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.nsE9yzUct5vdoNjFZ-2P48vFLGld5-Wr1tne8P3P9eZyCtrRPDSR7dJ8myg30sSV0kcRJVGogzTI92NpOo5pPw)

## 解码和验证 JWT

第一步是在解码和了解令牌中的内容之前验证令牌。只有在确保令牌未被篡改并且使用相同的私钥进行了签名时，才需要进行验证。**这不应该在客户端进行。**我们先通过实现验证函数来尝试解码 JSON Web Token，然后我们再解码。

### 实现验证功能

既然我们已经基于我的后端团队使用的相同算法成功地生成了令牌，那么让我们尝试验证它并验证它是否有效。为了验证令牌，我们只需要`algorithm`、`key`和生成的`token`。让我们继续干吧。

```
const token = "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJBZ2UiOjI3LCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.Nut6I57FYUGP973LgfOUNUBjMlaIm6NKk8ffgX4BTdQ_Wc2ob8m6uOwWlgoNMxpuRQaOoeFQOHhrIOJ_V8E-YA";
const algorithm = "HS512";
const key = "$PraveenIsAwesome!";
```

语法有点奇怪——原因是，验证器可以尝试在任何算法中进行验证，所以它需要一组算法。上述信息将被传递给`JSRSASign.jws.JWS.verifyJWT()`函数，该函数接受`token`、`key`和`{"alg": [ algorithm ]}`，语法如下:

```
console.log(
  JSRSASign.jws.JWS.verifyJWT(token, key, {
    alg: [algorithm]
  })
);
```

在控制台上，当我运行这个命令时，我得到了以下结果:

```
true
```

啊，那是一个甜蜜的成功。现在让我们继续解码令牌。解码部分真的很容易；甚至客户端的浏览器也可以做到这一点，这就是为什么我的客户更喜欢在 React 这样的单页面应用程序框架中安全可靠地传输声明对象。

### 实现解码功能

为了解码 JSON Web 令牌，我们将只传递令牌的第二部分。这包含有效载荷，或`claims`。我们还需要算法，可以从令牌的第一部分获取。

我们需要做的第一件事是分割`.`上的令牌，然后将它转换成一个数组，并获得`Header`和`Claim`:

```
const aJWT = sJWS.split(".");
const uHeader = JSRSASign.b64utos(aJWT[0]);
const uClaim = JSRSASign.b64utos(aJWT[1]);
```

让我们将它们设为`uHeader`和`uClaim`，因为此时它们是不可信的。现在让我们解析它们。`JSRSASign.b64utos()`将不受信任的 Base64 转换成字符串，由同一个库提供。我们现在将使用函数`JSRSASign.jws.JWS.readSafeJSONString()`，它类似于`JSON.parse()`，但是有更多的异常处理机制。

```
const pHeader = JSRSASign.jws.JWS.readSafeJSONString(uHeader);
const pClaim = JSRSASign.jws.JWS.readSafeJSONString(uClaim);
```

现在我们已经得到了解析后的头和声明。让我们尝试记录它们并查看输出。

```
console.log(pHeader);
console.log(pClaim);
```

呜哇！我们这里有解码版本。

```
{
  "alg": "HS512",
  "typ": "JWT"
}
{
  "Username": "praveen",
  "Age": 27,
  "Fullname": "Praveen Kumar"
}
```

开始了。现在我们可以用这种方式安全地访问有效载荷(至少不是明文形式的)!👍🏻

## 完整的代码和下一部分

下面是完整的代码，包括签名、生成、确认、验证和解码！😇

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
// Log it to the console.
console.log("JSON Web Token: ", sJWT);

const token =
  "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJBZ2UiOjI3LCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.Nut6I57FYUGP973LgfOUNUBjMlaIm6NKk8ffgX4BTdQ_Wc2ob8m6uOwWlgoNMxpuRQaOoeFQOHhrIOJ_V8E-YA";
const algorithm = "HS512";

// Log it to the console.
console.log(
  "Verification: ",
  // Validation
  JSRSASign.jws.JWS.verifyJWT(token, key, {
    alg: [algorithm]
  })
);

// Decoding
const sJWS = token;
const aJWT = sJWS.split(".");
const uHeader = JSRSASign.b64utos(aJWT[0]);
const uClaim = JSRSASign.b64utos(aJWT[1]);
const pHeader = JSRSASign.jws.JWS.readSafeJSONString(uHeader);
const pClaim = JSRSASign.jws.JWS.readSafeJSONString(uClaim);
// Decoded objects.
// Log it to the console.
console.log("Header: ", pHeader);
console.log("Claim: ", pClaim);
```

上面的代码会给出这样的输出:

```
➜  MockAPIServer node dec-enc.js
JSON Web Token:  eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJBZ2UiOjI3LCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.Nut6I57FYUGP973LgfOUNUBjMlaIm6NKk8ffgX4BTdQ_Wc2ob8m6uOwWlgoNMxpuRQaOoeFQOHhrIOJ_V8E-YA
Verification:  true
Header: { alg: 'HS512', typ: 'JWT' }
Claim: { Username: 'praveen',
  Age: 27,
  Fullname: 'Praveen Kumar' }
```

顺便说一下，我所做的只是一个服务器端生成组件，并不包括使用 POST 请求向您发送令牌的“web 服务器”。

现在我们已经完成了服务器端的工作，让我们开始[构建 REST API 端点，供客户端在第二部分中使用](https://blog.logrocket.com/mern-app-jwt-authentication-part-2/)！

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