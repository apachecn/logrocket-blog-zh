# 使用 Web3 钱包改进用户认证

> 原文：<https://blog.logrocket.com/improve-user-authentication-web3-wallets/>

## 摘要

本文将展示一个软件钱包，一个标准的 Web3 技术，如何将复杂的用户注册和认证转换成一个简单的过程。

术语“钱包”在以太坊中有两种不同的用法。一种是提供访问以太坊的软件，比如 [MetaMask](https://metamask.io) 或者 [Rabby](https://rabby.io) 。另一个是衍生用户帐户的密码短语。每个帐户都是一个秘密私钥和一个公钥的组合，公钥也是帐户地址。

在本文中，我们将讨论以下内容:

## 先决条件

要运行本文中使用的示例，您需要以下内容:

*   安装了 Git
*   [Node.js](https://nodejs.org/en/) 已安装
*   [纱线](https://classic.yarnpkg.com/lang/en/docs/getting-started/)安装完毕
*   安装在浏览器上的钱包。如果你还没有 Web3 钱包，你可以按照网站上的说明从 [MetaMask](https://metamask.io/) 安装一个

对于本文中使用的例子，我使用 [AngularJS](https://docs.angularjs.org/guide) 来管理 JavaScript。您不需要成为角度专家来理解代码，我会一路解释任何相关的细节。

## 用户注册和认证的挑战

没有简单的方式来说，注册和认证用户可能是一件痛苦的事情。用户经常忘记他们的密码，无法访问他们的登录电子邮件地址，并要求朋友或家人帮助他们猜测密码重置问题的答案。然而，在 Web3 中，用户的浏览器不仅仅是互联网的窗口，它还包含 DeFi 工具，如钱包。这是一个游戏改变者，因为这些钱包可以为 web 应用程序提供强用户识别。

用户有很大的动机来保护他们的 Web3 钱包，因为它与资产相关联。如果另一方控制了用户的钱包(例如，通过使用他们的恢复短语)，他们就可以窃取其资产。类似地，如果用户丢失了他们的钱包访问或恢复信息，与钱包相关联的任何资产实际上都将永远丢失(或锁定)。

## 保持密钥安全的重要性

当您生成一个新密钥时，您会得到一个代码字列表(通常是 12 个，但也可能是 15 个、18 个或更多)。这份名单是你的钥匙。保持它的安全是至关重要的，通常在你电脑的软件钱包里有一个密码，在其他地方有一份清单的纸质副本(我把我的放在保险箱里)。

*   如果您丢失了密钥，您将无法再访问受密钥保护的资产或帐户
*   如果其他人可以访问您的密钥，那么这个人现在就是您的了。应用程序或区块链本身没有办法区分您和拥有您的密钥的另一个人

钱包不会过期。你不需要定期改变你的单词表。但是如果你认为它可能已经被公开了，创建一个新的并把你能得到的一切转移到新的钱包中是非常重要的。

## 客户端用户识别

以太坊钱包的标准是[在`window.ethereum`的网络浏览器上公开一个全局 API](https://docs.metamask.io/guide/ethereum-provider.html) 。此时，我们只关心与 API 相关的两个项目:

1.  它是否存在。如果没有`window.ethereum`，则用户没有安装钱包软件
2.  这个称呼`window.ethereum.request({method: 'eth_requestAccounts'})`。这个调用指示钱包请求用户允许识别他们自己(作为一个或多个地址)

让我们看看如何使用客户端代码获取用户信息:

Angular 使用一个名为 `$scope`的变量来保存应用程序的状态。初始化它的代码如下:

```
myApp.controller("myCtrl", function($scope, $http) {

```

`.addr`和`.error`字段保存我们从钱包中获得的信息。第一个是地址，以防用户同意向我们表明身份。第二个是出错时的错误(例如，如果用户拒绝识别请求)。

```
    $scope.addr = "" 
    $scope.error = ""

```

以下函数通过验证是否定义了`window.ethereum`来检查钱包的存在。出于安全原因，如果 HTML 是从本地文件而不是服务器读取的，那么`window.ethereum`将是未定义的。为了调试它，甚至是 UI，我们需要从某个地方提供服务。

```
    $scope.isWallet = () => typeof window.ethereum !== 'undefined'

```

下一个函数试图将我们连接到钱包。我们请求钱包使用`eth_requestAccounts`方法通知我们可用的账户。`window.ethereum.request`函数返回一个`Promise`对象。

```
    $scope.connect = () => {
        window.ethereum.request({method: 'eth_requestAccounts'})

```

如果呼叫成功，我们将获得用户允许我们查看的地址列表。如果我们只需要一个地址作为身份标识，我们可以使用列表中的第一个地址。此外，我们希望清除先前交互中可能存在的任何错误。

```
        .then(arr => {
            $scope.addr = arr[0]
            $scope.error = ""

```

当范围变量从一个`Promise`中更新时，AngularJS 有时看不到它们已经被更新。调用`$scope.$digest()`告诉它处理新值:

```
            $scope.$digest()

```

在下面的代码中，我们指定如果有错误，只显示错误消息。用堆栈跟踪显示整个错误会让用户感到困惑。

```
        },    // success
        err => {
            $scope.error = err.message
            $scope.$digest()

```

现在，让我们看看 Angular 是如何处理条件视图的。`ng-if`属性指定了一个条件，HTML 标签只在条件为真时显示。在下面的代码中，我们检查`error`不是一个假值，比如空字符串。

```
<div ng-if="error">

```

这与下面的 JavaScript 代码形成对比，其中`$scope.`被自动添加到变量名中:

```
        })   // error
    }    // $scope.connect

```

在 Angular 中，我们使用`{{<expression>}}`在 HTML 中显示数值。

```
    <h2>Error: {{error}}</h2>

```

在下面的代码中，我们显示了钱包和地址都存在的任何实例的地址。在本文的后面，我们将演示如何将这些信息安全地发送到服务器。

```
</div>
<div ng-if="isWallet()">
    <h2 ng-if="addr>Address: {{addr}}</h2>

```

属性指定了点击按钮时要执行的 JavaScript 代码。下面，JavaScript 代码嵌入在我们的 HTML 中:

```
    <button ng-if="!addr" ng-click="connect()">
    Connect to Ethereum
    </button>

```

如果地址为空，我们会要求用户安装 MetaMask 或另一个以太坊钱包以便连接:

```
</div>    <!-- ng-if="isWallet()" -->
<div ng-if="!isWallet()">
    To use this application, please install 
    <a href="https://metamask.io">MetaMask</a> or
    some other Ethereum wallet.
</div>

```

## 服务器端用户识别

在浏览器中有用户标识是很好的，但是通常我们希望服务器能够识别用户。依赖代码来共享用户身份可能是危险的，因为恶意应用程序可能会提供虚假的用户身份。

更安全的解决方案是让用户[用会话 ID](https://en.wikipedia.org/wiki/Digital_signature) 签署消息。这要求用户拥有与地址对应的私钥。只有合法用户拥有这个密钥。

让我们按照下面的步骤来看一个服务器端用户识别的实际例子:

从 GitHub 下载源代码库:

```
    git clone https://github.com/qbzzt/qbzzt.github.io/ \ 
    demos

```

下载代码包:

```
    cd demos/LogRocket/20220228-user-identity
    yarn

```

运行服务器:

```
    node server.js

```

在浏览器中导航到 [http://localhost:8000](http://localhost:8000) ，选择**服务器端用户认证**。

在这里，我们可以选择表明自己的身份。要测试安全性，请尝试三个选项。首先，提供一个实际地址。现在，试着用一个假地址。接下来，尝试输入一个随机签名。

新功能主要在`$scope.sign`函数中，所以让我们仔细看看:

```
$scope.sign = async addr => {
    const httpResult = await $scope.$http.get("/session")

```

在本教程中，我们将使用 [Express.js](https://www.npmjs.com/package/express) web 服务器和 [express-session](https://www.npmjs.com/package/express-session) 中间件进行会话管理。由于这个包不允许 JavaScript 读取会话 ID，我们将使用 Angular 内部的 HTTP 客户端，`[$http](https://docs.angularjs.org/api/ng/service/%24http)`。

让我们转到服务器代码，看看对这个请求的响应:

```
app.get("/session", (req, res) => {

```

上面的代码显示了如何在 Express 中处理 HTTP GET 请求。路径是`/session`，被调用的函数有两个参数:请求对象(`req`)和响应对象(`res`)。

接下来，下面的函数从请求中读取会话 ID(`req.sessionID`)，然后将其作为响应发送(`res.send`):

```
  res.send(req.sessionID)
})

```

回到浏览器，`httpResult`不仅包含服务器发送的数据，还包含头字段。我们只想要数据:

```
    $scope.sessionId = httpResult.data

```

现在，我们将使用`[personal_sign](https://docs.metamask.io/guide/signing-data.html#signing-data-with-metamask)`方法访问钱包。这个方法需要三个参数。第一个参数是被签名的字符串。第二个参数是签名的地址(与钱包关联的地址有私钥)。第三个参数是密码(在本例中是空字符串)。

```
    $scope.walletResp = await window.ethereum.request(
    {
        method: "personal_sign",
        params: [`My session ID: ${$scope.sessionId}`,
                          $scope.addr, ""]
    })

```

我们将向服务器发送两个参数:签名的响应和我们的地址。让我们看看服务器代码是如何处理这些值的:

```
    const sigUrl = `/signature?sig=${$scope.walletResp}&addr=${addr}`
    $scope.authResult = (await $scope.$http.get(sigUrl)).data

```

`personal_sign`方法实际上是一个添加到数据中的常量前缀，因此它不能模仿或模仿事务。这防止了潜在的滥用。

```
app.get("/signature", (req, res) => {
  let error = "", realAddr = ""
  const expectedMsg = `My session ID: ${req.sessionID}`
  const hash = ethers.utils.id(
`\x19Ethereum Signed Message:\n${expectedMsg.length}${expectedMsg}`)

```

本例中的查询参数`addr`和`sig`位于`req.query`下:

```
  const claimedAddr = req.query.addr

```

我们通过尝试[恢复创建签名的地址](https://docs.ethers.io/v5/api/utils/address/#utils-recoverAddress)来验证以太坊中的签名:

```
  try {
    realAddr = ethers.utils.recoverAddress(hash, req.query.sig)

```

在随机签名的情况下，有可能得到一个随机地址或者一个错误。这两种情况我们都要报道。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
  } catch (err) {
    error = err.reason
  }

```

ethers 包给我们[一个校验和地址](https://docs.ethers.io/v5/api/utils/address/#address)，其中字母数字(a-f)是地址的校验和。然而，MetaMask 给了我们一个全小写的地址。为了检查两个地址是否相同，我们将所有的字母转换成小写。这一步很重要，因为可能还有其他全大写地址的 Web3 钱包，我们也想使用这些钱包。

```
  if (error)  {
    res.send(`ERROR: ${error}`)
  } else {
    if (realAddr.toLowerCase() === claimedAddr.toLowerCase())

```

回到客户端，响应存储在`$scope.authResult`中。一旦我们连接到以太坊，就会显示给用户:

```
      res.send(`Legitimate, welcome ${realAddr}`)
    else
      res.send(`Fraud!!! You are not ${claimedAddr}, you are ${realAddr}!`)
  } // if (error) else

})    // app.get("signature")

```

函数`$scope.invalidSignature()`类似于`$scope.sign()`，除了它不是创建合法的签名，而是发出 130 个随机字节。由于签名来自任何来源，无论是可信的还是不可信的，因此正确处理它们非常重要。

```
    $scope.$digest()
}    // $scope.sign

```

## 结论

软件钱包使得用户认证变得简单。钱包为我们处理一切；我们只需要验证一个签名。没有要管理的凭据或要跟踪的恢复问题。

在本文中，我们讨论了与用户注册和身份验证相关的挑战，以及保证密钥安全的重要性。我们还浏览了关于客户端和服务器端用户识别的教程。

既然您已经看到了使用钱包处理用户认证是多么容易，我希望您将在 web 应用程序中使用钱包，并鼓励用户安装它们。Web3 钱包改善了用户认证，让每个人的生活变得更加简单。

本文中引用的[客户端用户标识](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220228-user-identity/server.js)和[服务器端用户标识](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220228-user-identity/public/02_server_side.html)示例可在 GitHub 上获得。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。