# 用颤振和坚固性建造你的第一个 DApp

> 原文：<https://blog.logrocket.com/building-dapp-flutter-solidity/>

区块链技术和一般的加密空间是当今世界的主要趋势之一。诸如 NFT、ETF、DApps 和 DeFi 等越来越多的流行词汇是这一论断不可否认的标志。

即使在移动和网络技术领域，分散式应用和分散式网络的概念现在也是一个东西，任何想要保持领先和跟上时代的人都在追赶当前的潮流。

在我们继续之前，让我们简要定义一些我们将在本文中遇到的术语。

## 什么是智能合同？

我们都知道合同是两个人或两方之间的协议。这将使智能合同成为用代码编写并保存在区块链网络上的可验证、不可逆和自动执行的协议的扩展。

据 Investopedia 称，另一方面，区块链网络是一个分布式数据库，由计算机网络的节点共享。作为一个数据库，区块链以数字格式存储电子信息。

在本文中，我将带您了解:

*   如何通过使用[S](https://blog.logrocket.com/smart-contract-programming-languages/)[solidity 编程语言](https://blog.logrocket.com/smart-contract-programming-languages/)构建一个简单的智能契约来实现分散应用程序的概念
*   以及使用 Flutter 将其连接到应用程序客户端的步骤

## 入门指南

我们要构建的是一个简单的智能契约，用于在 alpha 和 beta 两个值之间进行投票。就本教程的范围而言，我们不会深入任何复杂的内容，比如验证用户和用户可以投票的次数；我们将专注于在 Solidity 中创建一个简单的智能契约，并使用 Flutter 在我们的应用程序中部署它。 [Flutter 是一个 UI 工具包，使用户能够快速创建 UI，并增加了灵活性](https://blog.logrocket.com/complete-guide-flutter-architecture/)。

首先，我们需要下载并使用 MetaMask。因此，[点击这个链接将 MetaMask 扩展添加到你的浏览器](https://metamask.io)(最好是 Chrome)。

完成后，创建并设置您的加密钱包。由于这是为了学习，将你的网络从**以太坊主网**改为**林克比测试网络**，如下图所示。

一旦你解决了这个问题，你将需要一些测试乙醚来处理天然气费时，进行某些交易与我们的智能合同。你可以做的一个方法是利用水龙头。请点击链接获取详细说明。

我们要做的下一件事是用 Solidity 编写我们的智能合同。我们将使用 Remix，这是一个用于 Solidity 的在线集成开发环境(IDE)，类似于 Dart 的 DartPad。

[Remix IDE 的链接可以在这里找到](http://remix.ethereum.org/#optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.7+commit.e28d00a7.js)。

如果您在 ide 左侧找到的文件夹令人困惑，您可以删除并创建一个新文件。我们将创建一个名为`voting.sol`的新文件。

`sol`扩展名代表可靠性，就像编写 Dart 和 Flutter 类时用于创建文件的`.dart`扩展名一样:

```
pragma solidity 0.6.6;

contract Voting{

int alpha;
int beta;

constructor() public {
   alpha  = 0;
    beta = 0;
}

function getTotalVotesAlpha() view public returns(int) {
    return alpha;
}

function getTotalVotesBeta() view public returns(int){
    return beta;
}

function voteAlpha () public{
    alpha = alpha+1;
}

function voteBeta () public{
    beta = beta+1;
}
}

```

现在，让我们来了解一下这个简单智能合约的结构:

```
pragma solidity 0.6.6;

```

第一行是我们将使用的 Solidity 版本的声明，在我们的例子中是版本 0.6.6。

我们代码的第 3 行是如何声明契约，类似于其他编程语言中的类声明:

```
int alpha;
int beta;

```

我们还有两个整型变量声明，分别是`alpha`和`beta`:

```
constructor() public {
   alpha  = 0;
    beta = 0;
}

```

接下来是我们的构造函数声明，每当我们创建这个类的实例时，我们都希望使用构造函数来初始化我们的字段:

```
function getTotalVotesAlpha() view public returns(int) {
    return alpha;
}

function getTotalVotesBeta() view public returns(int){
    return beta;
}

```

接下来我们有上面这两个函数，它们做类似的事情。第一个函数返回`alpha`的值，第二个函数返回`beta`的值。如果你仔细观察，你会注意到添加了`view`属性。这意味着这个函数是一个只读函数，不修改我们合同中的任何字段。

在 Solidity 中，我们所有的函数都标有访问修饰符，它们决定了函数或字段的范围。在我们的例子中，我们有一个`public`访问修饰符，这意味着这个函数可以从我们的契约外部调用。

最后，`return`类型是在花括号之前指定的。

我们函数的其余部分只是增加字段的值`1`。

现在点击 **Compile** ，它位于我们 IDE 的左侧窗格，如下图所示。

一旦您的智能合同编译成功，单击 **Deploy** 并运行选项卡，如下所示。把你的环境从 **JavaScript VM** 换成 **Injected Web3** 。

一旦你完成了这些，你可以再次点击**编译**标签，从智能契约中选择一个或多个你想要测试的函数，并在终端中查看结果。

在成功编写智能合同之后，我们需要一个区块链服务器来运行。有很多方法可以做到这一点——其中之一就是使用 [Ganache](https://www.trufflesuite.com/ganachehttps://www.trufflesuite.com/ganache) 来运行个人区块链。但是为了简单起见，我们将使用 [Infura](https://infura.io/) ，这是一个为我们提供区块链访问以太坊的平台。

只需创建一个帐户，并在平台上创建一个项目，你就会得到一个网址。记下并确保将创建的项目的网络从 **Mainnet** 更改为 **Rinkeby** 。

在网络下方，你可以看到我们提供了两个链接。你可以选择其中的任何一个，然后你就可以开始了。

接下来我们要做的是创建一个新的 Flutter 项目，并在我们的`pubspec`文件中添加以下依赖项:

```
web3dart: ^2.3.3
http: ^0.13.4

```

我们的屏幕将是一个简单的屏幕，有两个按钮来投票`alpha`或`beta`，我们将在按钮上方显示总得分。

我们从创建这些变量开始:

```
late Client httpClient;

late Web3Client ethClient;

//Ethereum address
final String myAddress = "0x8fF1b659bDC9D6eF5d99823B155cfdf47eF2944d";

//url from Infura
final String blockchainUrl = "https://rinkeby.infura.io/v3/4e577288c5b24f17a04beab17cf9c959";

//strore the value of alpha and beta
var totalVotesA;
var totalVotesB;

```

在我们的`initState`回调中，我们将初始化我们的`httpClient`和`Web3client`，并调用一个方法从我们的智能契约中获取`alpha`和`beta`的值:

```
@override
void initState() {
  httpClient = Client();
  ethClient = Web3Client(
      blockchainUrl,
      httpClient);
  getTotalVotes();
  super.initState();
}

```

为了理解`getTotalVotes`应该如何工作，让我们来看看下面的函数，并对它们进行分解。

在此之前，我们将返回到 Remix IDE，在 **Solidity 编译器**选项卡上，我们将复制智能合约的 ABI，如上图所示，将其带到我们的 Flutter IDE，并创建一个新文件。在我们的例子中，我们将其命名为`contract.json`，它将位于我们的 assets 文件夹中。之后，我们继续将它添加到我们的`pubspec file`并运行`flutter pub get`命令:

```
# To add assets to your application, add an assets section, like this:
assets:
  - assets/contract.json

Future<DeployedContract> getContract() async {
//obtain our smart contract using rootbundle to access our json file
  String abiFile = await rootBundle.loadString("assets/contract.json");

  String contractAddress = "0xee3F5a4361ec47C57394Fc028C3fBCCd0e9f1B5d";

  final contract = DeployedContract(ContractAbi.fromJson(abiFile, "Voting"),
      EthereumAddress.fromHex(contractAddress));

  return contract;
}

Future<List<dynamic>> callFunction(String name) async {
  final contract = await getContract();
  final function = contract.function(name);
  final result = await ethClient
      .call(contract: contract, function: function, params: []);
  return result;
}

Future<void> getTotalVotes() async {
  List<dynamic> resultsA = await callFunction("getTotalVotesAlpha");
  List<dynamic> resultsB = await callFunction("getTotalVotesBeta");
  totalVotesA = resultsA[0];
  totalVotesB = resultsB[0];

  setState(() {});
}

```

使用我们的`web3dart`包中的`DeployedContract`类构造一个契约，它接受 ABI 文件、我们的智能契约的名称(在我们的例子中是`Voting`)和契约地址，并从我们的函数返回它。

注意，要获取合同地址，请返回到您的 Remix IDE，单击**部署并运行**选项卡，然后单击**部署** **合同**下面的复制图标，如下图所示。

下一个函数将用于调用我们的智能合约内部的函数:

```
final result = await ethClient
      .call(contract: contract, function: function, params: []);

```

上面一行是我们如何用来自`web3dart` `EthereumClient`类的调用扩展连接到我们的智能契约。该操作的结果是该函数返回的列表:

```
Future<List<dynamic>> callFunction(String name) async {
  final contract = await getContract();
  final function = contract.function(name);
  final result = await ethClient
      .call(contract: contract, function: function, params: []);
  return result;
}

```

最后，在`getTotalVotes`方法中，我们将使用这个`callFunction`来调用像`getTotalVotesAlpha`和`getTotalVotesBeta`这样的函数，它们是我们在智能契约中创建的。请确保您传递的名称与您部署的契约上的名称完全匹配。

一旦我们完成了接收投票值的函数，下一个动作就是投票。与我们所做的类似，我们将使用 ABI 文件通过调用其中的函数来构造一个请求。

投票操作的不同之处在于，我们做的是`write`请求，而不是`read`请求。因此，我们将利用元掩码中的私钥。或者您可以使用任何私钥；它会通过，因为这是一个测试帐户。

此外，我们不使用`call`，而是使用`sendTransaction`并传递我们的私有密钥，一个事务对象，并指定`chainId`，在我们的例子中(Rinkeby)是`4`:

```
Future<void> vote(bool voteAlpha) async {
  snackBar(label: "Recording vote");
  //obtain private key for write operation
  Credentials key = EthPrivateKey.fromHex(
      "f6417d3d4c5cc294ace85aa196fcde0ca792550e085f65fff459423e597ff306");

  //obtain our contract from abi in json file
  final contract = await getContract();

  // extract function from json file
  final function = contract.function(
    voteAlpha ? "voteAlpha" : "voteBeta",
  );

  //send transaction using the our private key, function and contract
  await ethClient.sendTransaction(
      key,
      Transaction.callContract(
          contract: contract, function: function, parameters: []),
      chainId: 4);
  ScaffoldMessenger.of(context).removeCurrentSnackBar();
  snackBar(label: "verifying vote");
  //set a 20 seconds delay to allow the transaction to be verified before trying to retrieve the balance
  Future.delayed(const Duration(seconds: 20), () {
    ScaffoldMessenger.of(context).removeCurrentSnackBar();
    snackBar(label: "retrieving votes");
    getTotalVotes();

    ScaffoldMessenger.of(context).clearSnackBars();
  });
}

```

之后，我们设置了一个`20`秒的延迟，因为在再次检索我们的余额之前，交易需要一段时间才能得到验证。

如果一切都设置好了，当您运行您的项目时，您将拥有这个。

## 最后的想法

智能契约的这个简单实现应该让您开始探索和学习更多内容。您可以决定添加一些有趣的特性，比如身份验证，确保一个用户不会投票超过一次，并对您的应用程序应用良好的状态管理方法。

要了解更多关于可靠性的知识，你可以[点击这里](https://docs.soliditylang.org/en/v0.8.10/)查看他们的文档。

要访问该项目的存储库，请查看下面的链接:

[https://github . com/ekeminie/block chain-evolved](https://github.com/Ekeminie/blockchain-evoting)

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。