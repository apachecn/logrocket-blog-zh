# 如何在 Node.js 中创建和读取二维码

> 原文：<https://blog.logrocket.com/create-read-qr-codes-node-js/>

二维码是一种便捷高效的信息存储和共享方式。使用流行的 JavaScript 运行时环境 Node.js 可以轻松读写二维码。在本文中，我们将使用三个流行的库来详细介绍在 Node 中读取和写入二维码的步骤:读取二维码的 [qrcode-reader](https://github.com/edi9999/jsqrcode) 和 [jimp](https://github.com/oliver-moran/jimp) ，以及生成二维码的 [qrcode](https://github.com/soldair/node-qrcode) 。

向前跳:

## 在节点中写入 QR 码

qrcode 库为编写 QR 码提供了一个简单的 API。

### 安装二维码库

要安装 qrcode 库，您需要在系统上安装 Node.js 和 NPM(node . js 包管理器)。如果没有安装这些工具，可以从 [Node.js](https://nodejs.org/en/) 网站下载安装。

一旦安装了 Node.js 和 npm，就可以使用 npm 安装 qrcode 库及其依赖项。

打开终端窗口并运行以下命令:

```
npm install qrcode
```

### 生成 QR 码图像

要生成 QR 码图像，您可以使用 QR 码模块中的`toFile`方法。此方法将文件路径、要在 QR 码中编码的文本和 options 对象作为参数，并在指定的文件路径生成 QR 码图像文件。

以下是如何使用`toFile`方法生成 QR 码图像文件的示例:

```
// __ Importing qrcode __ \\
const QRCode = require('qrcode');

QRCode.toFile('/output-file-path/file.png', 'Encode this text in QR code', {
  errorCorrectionLevel: 'H'
}, function(err) {
  if (err) throw err;
  console.log('QR code saved!');
});
```

此代码将生成一个具有指定文本和纠错级别的 QR 码图像文件，并将其保存到指定的输出文件中。

纠错级别指定 QR 码中编码的冗余量，以允许在扫描码时纠正错误。

除了生成二维码图像文件，二维码库还允许您生成其他格式的二维码图像，如 JPEG 或 SVG。为此，您可以使用`toString`方法，该方法返回一个包含指定格式的 QR 码图像数据的字符串。

以下是如何使用`toString`方法生成 SVG 格式的 QR 码图像的示例:

```
// __ Importing qrcode __ \\
const QRCode = require('qrcode');

QRCode.toString('Encode this text in QR code', {
  errorCorrectionLevel: 'H',
  type: 'svg'
}, function(err, data) {
  if (err) throw err;
  console.log(data);
});
```

这段代码将生成一个具有指定文本和纠错级别的 QR 码图像，并将其作为一个 SVG 数据字符串返回。然后，您可以使用这些数据在浏览器或其他应用程序中显示 QR 码图像。

您可以扫描下面的二维码，并在上面的代码片段中看到您作为`toString`方法的输入给出的字符串。

这是输出:

![QR Code Output](img/3d195f9fbbdccfae6a0964e5f6974c50.png)

## 读取节点中的二维码

在本节中，您将阅读在上一节中生成并保存的 QR 码文件。

要在 Node 中读取二维码，您将使用二维码阅读器和 jimp 库。这些库提供了简单的 API，用于从图像和视频源解析和读取 QR 码。

### 安装二维码阅读器和 jimp 库

要安装 qrcode-reader 和 jimp 库，您将再次需要安装读取 qrcode 所需的 npm 包。

打开终端窗口并运行以下命令:

```
npm install qrcode-reader jimp
```

### 从图像文件中读取 QR 码

要从图像文件中读取 QR 码，您可以使用 QR 码阅读器模块的`decode`方法和 jimp 模块的`read`方法。jimp `read`方法解析图像，图像将由二维码阅读器模块的`decode`方法解码。

以下是如何使用`read`和`decode`方法从我们之前保存的图像中读取二维码的示例:

```
// __ Importing jimp __ \\
const Jimp = require("jimp");

// __ Importing filesystem = __ \\
const fs = require('fs')

// __ Importing qrcode-reader __ \\
const qrCodeReader = require('qrcode-reader');

// __ Read the image and create a buffer __ \\
const buffer = fs.readFileSync('/output-file-path/file.png');

// __ Parse the image using Jimp.read() __ \\
Jimp.read(buffer, function(err, image) {
    if (err) {
        console.error(err);
    }
// __ Creating an instance of qrcode-reader __ \\

    const qrCodeInstance = new qrCodeReader();

    qrCodeInstance.callback = function(err, value) {
        if (err) {
            console.error(err);
        }
// __ Printing the decrypted value __ \\
        console.log(value.result);
    };

// __ Decoding the QR code __ \\
    qrCodeInstance.decode(image.bitmap);
});
```

该代码将从图像文件中读取 QR 码，并将结果记录到控制台。如果 QR 码被成功解码，结果将是一个包含 QR 码文本的字符串。如果二维码无法解码，结果将为空。

在下面的输出中，您可以看到我们使用 qrcode 库编码的字符串。

这是输出:

```
Encode this text in QR code
```

可以看到二维码库非常有用，可以配合不同的参数来创建二维码。这个库最好的一点是它可以与 React.js 应用程序和 Node.js 服务器一起工作。

您可能想知道在 Node.js 后端服务中使用二维码的潜在用例是什么。让我们演示一个示例，创建一个简单的登录路径，并根据来自前端的 QR 码对用户进行身份验证。

## 在节点中创建后端

首先，运行以下代码:

```
mkdir my-server
```

然后，我们初始化后端服务器:

```
npm init
```

接下来，我们必须创建`controller`和`route`文件:

```
touch controller.js route.js
```

现在，安装节点包:

```
npm install express jsonwebtoken nodemon
```

这个库用于创建一个服务器并监听 Node.js 中的一个端口

这个库被用来创建一个 JWT 并发送给客户端

这个库用于检测服务器代码的实时变化

这将提示您输入一些元数据并创建一个`package-lock.json`文件。

初始化后端后，下一步是安装必要的依赖项:

```
Npm install express jsonwebtoken qrcode
```

当我们的服务器配置好后，让我们为登录创建一个控制器函数。将以下代码复制并粘贴到 controller.js 文件中:

```
// __ controller.js __\\

exports.login = async (req, res) => {
  try {

// __ Extract the secret from the request body. __ \\
    const secret = req.body.secret;
// __ Validate the secret. __ \\

    if (secret === VALID_SECRET) {
// __ Generate a JWT token. __ \\
      const token = jwt.sign({ user: 'example' }, JWT_SECRET, { expiresIn: '1h' });

// __ Send the JWT token to the client. __ \\
      res.json({ token });
    } else {

// __ If the secret is invalid, return an error. __ \\
      res.status(401).json({ error: 'Invalid QR code' });
    }
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: 'Server error' });
  }
};

exports.test = async (req, res) => {

console.log("Hitting the test route");
return res.status(200).json( {message: "Hitting the test route"} );
}
```

在上面的代码中，我们创建了一个登录控制器，它从请求体中提取秘密并验证它。如果验证成功，它会创建一个 JWT 令牌，用这个秘密对其进行签名，然后将其发送回客户端。如果验证失败，它用状态代码 401 **响应。**

*注意*，我们在文件中添加了一个测试函数来检查我们服务器的活性。

控制器完成后，我们现在将创建一个登录路由来公开我们的资源。将以下代码复制并粘贴到 route.js 文件中:

```
// __ route.js __ \\

const app = require("express");
const router = app.Router();

const authController = require("./controller");
router.post("/login", authController.login);
router.get("/test", authController.test);

module.exports = router;
```

最后一步是为 index.js 编写代码，我们的服务器将启动并运行:

```
// __ index.js __ \\
const express = require("express");
const app = express();

const auth = require("./route");

app.use("/auth", auth);

const port = process.env.PORT || 3001;
app.listen(port, () => {
console.log(`Listening to port ${port}...`);
});
```

## 运行我们的服务器

要运行服务器，首先在 package.json 中的脚本对象中添加以下代码:

```
"start": "nodemon index.js",
```

现在，运行:

```
npm start
```

您的输出应该如下所示:

![Output After Running The Server](img/c2ba865aac7a28c22b6a60f9553af5f0.png)

让我们通过点击之前配置的测试路径来测试我们的服务器。我们可以用很多方法测试，比如用 Postman 或者 Thunder 客户端，但是我会用一个 curl 请求。

打开您的终端并运行以下命令:

```
curl -I http://localhost:3001/auth/test
```

输出应该如下所示:

![Output After Using Curl Request](img/534f77a7655783469c2e4ebcd1c6ba23.png)

现在我们已经测试了服务器的活性，我们可以从构建的前端向登录路由发送请求，并对用户进行身份验证。

## 结论

在本教程中，我们学习了如何使用二维码以方便高效的方式存储和共享信息。无论我们需要为 web 或移动应用程序编码数据，都可以使用节点运行时环境轻松实现。我们还学习了如何在 QR 码中以不同形式编码数据，例如 SVG 文件。最后，我们创建了一个 Node.js 服务器，通过使用来自前端的 QR 码秘密对用户进行签名，使用 JWT 令牌对用户进行身份验证。

要进一步探索节点模块，请访问它们的[文档页面](https://nodejs.org/api/packages.html)。

编码快乐！😇

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.