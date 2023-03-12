# 了解和解决元掩码错误代码

> 原文：<https://blog.logrocket.com/understanding-resolving-metamask-error-codes/>

MetaMask 使我们能够访问分散式应用程序(dApps ),同时提供一种使用 MetaMask wallet 的身份验证形式。它提供了一键式安全登录流程，允许我们在前端使用 [ethers](https://docs.ethers.io/) 访问区块链资源。元掩码抽象了一些微妙的过程，比如在与区块链交互时签署事务，并向应用程序提供元掩码的公共地址。

因此，作为开发人员，在构建这些 dApps 时，肯定会有错误，这些错误应该得到适当的处理，这样开发人员和用户都可以知道哪里出了问题。因为 MetaMask 文档对于使用 MetaMask 时可能出现的许多类型的错误没有全面而清晰的指导，所以我在这里编译了一个最常见的错误及其含义的列表。

您可以通过以下链接跳转到本文概述的错误代码:

## 4001

当试图连接到钱包时，如果用户在该界面的任何一点点击“取消”并终止该过程，它将返回一个`4001`错误。

![Cancel button metamask error](img/09578be0f0ba11aeecf08087980d9d1e.png)

下面是错误的 JSON 结构:

```
'4001': {
  standard: 'EIP-1193',
  message: 'User rejected the request.',
},

```

## 4100

当 dApp 想要对未经授权的帐户采取措施时，会出现此错误。

例如，要做某些动作，比如签署消息，您需要首先使用`eth_requestAccounts`方法从 MetaMask 获得帐户权限:

```
import { ethers } from "ethers";

const accounts = await ethereum.request({
  method: "eth_requestAccounts",
});

const address = accounts[0];
const provider = new ethers.providers.Web3Provider(ethereum);
const signer = provider.getSigner();
const signature = await signer.signMessage(address);
console.log(signer);

```

类似地，要切换钱包的链，需要使用`wallet_switchEthereumChain`方法，该方法反过来通过元掩码扩展请求许可:

```
// switching the chain for MetaMask wallet

await provider.request({
  method: "wallet_switchEthereumChain",
  params: [{ chainId: "0x89" }],
});

```

如果权限被拒绝，wallet 将返回以下错误:

```
'4100': {
  standard: 'EIP-1193',
  message: 'The requested account and/or method has not been authorized by the user.',
},

```

## 4200

元掩码支持两种方法:受限和非受限。这些方法允许 dApp 采取诸如连接到钱包、签署交易以及添加或切换网络之类的动作。

元掩码不支持的方法会返回此错误:

```
'4200': {
  standard: 'EIP-1193',
  message: 'The requested method is not supported by this Ethereum provider.',
},

```

你可以在这里找到现有方法的链接。

## 4900

当用户的元掩码 wallet 未连接到任何链时，会返回此错误:

```
ethereum.on('disconnect', (error) => console.log(error));

```

跟`disconnect`事件有关。当 wallet 断开连接并且无法向链提交请求时，将触发此事件。除了断开连接之外，网络连接问题也可能导致这种情况。

一旦我们发出`disconnect`，提供者将不会接受任何新的请求，直到链的连接被重新建立，这需要重新加载页面。您还可以使用`ethereum.isConnected()` [方法](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-isconnected)来确定提供商是否断开连接。

以下是错误的 JSON 响应:

```
'4900': {
  standard: 'EIP-1193',
  message: 'The provider is disconnected from all chains.',
},

```

## 4901

此错误意味着用户没有连接到该事务的适当链。例如，如果交易要求用户在多边形链上，而他们在[和谐或以太坊](https://blog.logrocket.com/ethereum-vs-harmony-which-blockchain-right-for-you/)区块链。

需要注意的一点是，元掩码提供程序允许我们监听`chainChanged`事件，该事件在当前连接的链发生变化时进行监听和操作:

```
ethereum.on('chainChanged', (chainId) => {console.log(chainId)});

```

我们[将 RPC 请求](https://blog.logrocket.com/leverage-ethereum-blockchain-data-json-rpc/)提交给当前连接的链，这使得通过监听变化来跟踪当前链 ID 变得非常重要。每条链都有自己独特的链号，你可以在[链表中找到。](https://chainlist.org/)

如果它变得无法将 RPC 请求提交到该特定链，它将返回错误:

```
'4901': {
  standard: 'EIP-1193',
  message: 'The provider is disconnected from the specified chain.',
}

```

## 32700

如果用户向协定发送不完整的请求对象，将返回此错误。如果发送到协定的对象不包含它需要的所有数据，就会发生这种情况。

以下是 JSON 的回应:

```
 '-32700': {
    standard: 'JSON RPC 2.0',
    message: 'Invalid JSON was received by the server. An error occurred on the server while parsing the JSON text.',
  },

```

## 32600

这里，对象是有效的，但是结构或属性不正确。它有点类似于 32700，但在这种情况下，它的内部结构是不正确的。

以下是 JSON 的回应:

```
  '-32600': {
    standard: 'JSON RPC 2.0',
    message: 'The JSON sent is not a valid Request object.',
  },

```

## 32601

如果指定的方法根本不存在，则会返回此错误:

```
'-32601': {
    standard: 'JSON RPC 2.0',
    message: 'The method does not exist / is not available.',
  },

```

你可以在这里找到现有方法的链接。

## 32602

如果传递给一个 [RPC 方法](https://docs.metamask.io/guide/rpc-api.html#ethereum-json-rpc-methods)的参数不正确，我们就会得到这个错误。例如，在定义`transactionParameters`时，`from`属性引用了`accounts[0]`。

通常，`accounts[0]`应该是用户的钱包地址，但是在这种情况下，我们将它分配给一个空数组:

```
 let accounts = [];
  const amountEth = 1
   const paymentAddress = '0x71C7656EC7ab88b098defB751B7401B5f6d8976F'
  const transactionParameters = { from: accounts[0], to: paymentAddress, value: web3.toWei(amountEth, 'ether') };

  function Mint(){
  ethereum.request({ method: 'eth_sendTransaction',  params: [transactionParameters] });

  }

```

`Mint`函数将返回这个特定的错误，因为参数显然是无效的:

```
'-32602': {
    standard: 'JSON RPC 2.0',
    message: 'Invalid method parameter(s).',
  },

```

## 32603

这是一个由几个因素引起的普遍错误。这可能是因为你试图添加一个新的链到你的钱包(手动或通过 dApp)，但添加了错误的链数据。或者，您试图完成一项交易，但您没有足够的代币来支付汽油费。例如，如果你在以太坊主网上交易，无论你是否使用其他代币交易，你都必须在以太网支付汽油费。

最后，它可能像没有最新版本的元掩码一样微不足道。

以下是错误 JSON 响应:

```
'-32603': {
    standard: 'JSON RPC 2.0',
    message: 'Internal JSON-RPC error.',
  },

```

## 32000

当生产中使用的协定地址是您部署到 testnet 的协定时，可能会触发此错误。这是一个我们可以纠正的简单错误。

类似于 Web2，我们有一个开发环境和一个生产环境，当构建 dApps 时，我们使用 testnet 来部署我们的合同，因此我们可以在构建时测试它，而无需在 mainnet 上使用真正的 ETH。因此，部署在 testnet 和 mainnet 上的相同契约将具有不同的地址。如果您对您所在的链使用了错误的地址，则会返回此错误。

一般来说，在处理智能合同时，您需要合同地址、ABI 文件和您的签名者等参数:

```
const provider = new ethers.providers.Web3Provider(ethereum);
const signer = provider.getSigner();

const countContract = new ethers.Contract(contractAddress, contractABI, signer);

/*
 * Call the getAllCount method from your Smart Contract
 */
const count = await countContract.getAllCount()

```

如果这些参数中的任何一个是错误的，您很可能会得到他的错误，也称为“用户输入错误”:

```
  '-32000': {
    standard: 'EIP-1474',
    message: 'Invalid input.',
  },

```

## 32001

在这种情况下，我们请求的资源在区块链上不存在。这可能是客户端的打印错误。

假设您试图获取有关 ETH 链上不存在的块号的信息:

```
import Web3 from 'web3';

const web3 = new Web3(web3Provider);
var block = await web3.eth.getBlock({invalid block number})

```

您将得到以下 JSON 响应:

```
'-32001': {
    standard: 'EIP-1474',
    message: 'Resource not found.',
  },

```

## 32002

此错误意味着请求的资源确实存在，但在请求时当前不可用。当我们试图访问当前正在使用的资源时，就会发生这种情况。当您试图使用一个特定的资源/方法时，它可能发生在元掩码扩展上，就像当元掩码当前正在做同样的事情时切换链一样。

当构建你的 dApp 时，你应该学会在方法成功启动后禁用你的按钮，这样用户就不会连续点击。

下面是这个错误的 JSON 响应:

```
  '-32002': {
    standard: 'EIP-1474',
    message: 'Resource unavailable.',
  },

```

## 32003

这个错误可能是由多种原因造成的。可能是因为汇款人地址不存在，资金不足，账户被锁定，或者我们无法签署交易。要解决这个问题，请确保您交易中的所有内容都是正确的。

如果不满足完成交易所需的条件，可以拒绝交易:

```
  '-32003': {
    standard: 'EIP-1474',
    message: 'Transaction rejected.',
  },

```

## 32004

根本不支持该方法。可能，它不存在，或者只是一个印刷错误:

```
'-32004': {
    standard: 'EIP-1474',
    message: 'Method not supported.',
  },

```

## 32005

此错误意味着 RPC 提供程序的速率限制已被超过。如果 RPC 提供程序终结点对请求数有速率限制，则可能会出现此问题:

```
  '-32005': {
    standard: 'EIP-1474',
    message: 'Request limit exceeded.',
  },

```

此外，还有一个相当常见的错误，错误消息“内在气体太低。”原因很简单，因为启动交易时分配的汽油限额低于要求的限额。

这种情况最常发生在交易复杂的情况下，这可能会使燃气费不可预测:

```
ethereum
.request({
  method: 'eth_sendTransaction',
  params: [
    {
      from: accounts[0],
      to: '0xbCfDCCDbE7B3D681A1144D25a31e0D4BE869079a',
      value: '0x293434x341af62c0000',
      gasPrice: '0x09184e242',
      gas: '0x2709',
      gasLimit: '0x2723'
    },
  ],
})
.then((txHash) => console.log(txHash))
.catch((error) => console.error);

```

## 结论

如果你坚持到了最后，恭喜你！我们讨论了一些可能的错误以及如何处理它们。现在，当构建您的下一个 dApp 时，您可以轻松地解释这些错误并修复它们，而不会对最初导致它们的原因感到困惑。这有助于改善开发人员处理这些 Web3 技术的体验。

你可以从官方的[资源](https://eips.ethereum.org/EIPS/eip-1474)中查看更多关于元掩码错误的信息。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。