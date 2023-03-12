# 将 Web3 集成到 Vue 应用程序中

> 原文：<https://blog.logrocket.com/integrate-web3-into-vue/>

有了 Web3，就有可能在区块链网络上构建分散的应用程序，让用户完全拥有自己的数据。区块链网络使用智能合约来处理分散应用程序的所有后端逻辑。

作为最受欢迎的，[可编程区块链网络，以太坊](https://blog.logrocket.com/deploy-ethereum-smart-contracts-cosmos-ecosystem/)允许开发者构建和操作去中心化应用(DApps)、去中心化金融(DeFi)、智能合约和不可替代令牌(NFT)。

在本文中，我们将介绍在 Vue 应用程序中使用 Web3 的基础知识，包括安装过程和与智能合约的交互。要阅读本文，您需要以下内容:

*   了解 Vue CLI
*   对 Web3 的基本理解
*   熟悉 Solidity 智能合同并了解如何部署它们

## 目录

## 什么是 Web3？

Web3 是基于区块链技术的新一代互联网，提倡去中心化和基于令牌的经济。调用智能合约和发送交易构成了区块链网络上的大部分交互。

## 什么是智能合约？

智能合同是一种自动执行的计算机软件，它位于区块链网络上。区块链一部署就执行智能合同。当契约被执行时，它创建接口，DApps 使用这些接口来扩展它们的功能。

考虑以下简单的 Solidity smart 契约:

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.0;

contract Contract {
    string private text;

    function speak() public view returns (string memory) {
        return text;
    }

    function changeText(string memory newText) public {
        text = newText;
    }
}

```

当您将上面的契约部署到区块链网络时，它创建了两个接口方法，`speak`，它返回一个文本字符串，和`changeText`，它改变`speak`返回的字符串。

## 项目设置

如果您已经有一个准备集成的 Vue 项目，您可以跳过这一部分。如果没有，请使用以下命令为 npx 包管理器创建项目:

```
npx vue create web3-project

```

如果使用纱线卷装管理器，请使用以下命令:

```
yarn vue create web3-project

```

在菜单提示中，选择 **Vue3** 或 **Vue2** 。你选择哪一个都没关系，因为你可以用同样的方式将 Web3 集成到两者中。创建项目后，在您的`App.vue`文件中编写以下代码:

```
<template>
  <div>
    <!-- connect-wallet button is visible if the wallet is not connected -->
  <button v-if="!connected">Connect wallet</button>
    <!-- call-contract button is visible if the wallet is connected -->
    <button v-if="connected">Call contract</button>
    {{ contractResult }}
  </div>
</template>

<script>

export default {
  name: 'App',

  data() {
    return {
      connected: false,
      contractResult: '',
    }
  },
}
</script>

```

在`App.vue`中，我们有两个按钮；第一个用于连接钱包，第二个用于调用智能契约方法。

在用户将钱包连接到网络应用程序之前，`call contract`按钮是隐藏的。我们的应用程序使用`connected`状态变量来保存钱包连接状态。

## 设置网站 3

在您的项目目录中，运行以下命令以使用 npm 安装 Web3 包:

```
npm install web3

```

如果您使用 Yarn，请改为运行以下命令:

```
yarn add web3

```

安装包之后，我们将 Web3 导入到`App.vue`文件中，并运行项目来测试它的兼容性:

```
...
<script>
import Web3 from 'web3'
export default {
  name: 'App',
…

```

如果除了`Web3 is imported but never used`之外，你没有收到任何错误消息，那么 Web3 与你的框架是兼容的。但是，如果框架产生其他错误，您必须切换到旧版本的 Vue。Vue 3 使用的 Webpack 5 模块出现了兼容性问题。

## 连接我们的钱包

MetaMask 是一个应用程序，[允许用户存储并将他们的钱包](https://blog.logrocket.com/understanding-resolving-metamask-error-codes/)连接到 Web3 应用程序。Metamask 可以安装在所有主流浏览器中。通过将钱包连接到网站，用户可以执行以下操作:

*   在网站上买卖代币
*   管理区块链网络上的数字资产
*   在网站上向帐户进行交易
*   在区块链网络上与智能合约交互

当您在浏览器中安装元掩码扩展时，该扩展会创建一个`window.ethereum`对象。Web3 应用程序使用`window.ethereum`对象访问用户的钱包并连接到以太坊网络。

`window.ethereum.request()`方法提示用户连接他们的元掩码钱包:

```
ethereum.request({ method: 'eth_requestAccounts' })

```

我们可以使用这个提示将钱包连接事件注册到我们的`connect wallet`按钮。将以下代码添加到您的`App.vue`文件中:

```
<template>
  <div>
    <!-- "connect" click event is registered -->
>    <button v-if="!connected" @click="connect">Connect wallet</button>
    <button v-if="connected">Call contract</button>
    {{ contractResult }}
  </div>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      connected: false,
      contractResult: '',
    }
  },

>  methods: {

>    connect: function () {
>        // this connects to the wallet
>      
>      if (window.ethereum) { // first we check if metamask is installed
>        window.ethereum.request({ method: 'eth_requestAccounts' })
>          .then(() => {
>            this.connected = true; // If users successfully connected their wallet
>          });
>      }
>    }
>  }
}
</script>

```

当用户点击`connect wallet`按钮时，会出现元掩码提示，允许用户选择他们想要连接的帐户。用户连接钱包后，`App.vue`文件中的`connected`状态变量变成了`true`。

## 与部署的智能合同交互

部署的智能合约创建 Web3 应用程序与之交互的接口。要访问这些接口，我们需要向 Web3 提供 ABI、智能合约接口的描述以及合约地址，即合约在以太网上的位置。

在写这篇文章之前，我在 [Rinkeby 测试网络](https://www.rinkeby.io/#stats)上部署了一个智能合约。在下面的示例中，我将使用它的 ABI 和地址，但是如果您已经部署了一个智能契约，则可以使用该契约的地址和 ABI，而不是这里包含的地址和地址。

为了与部署的契约进行交互，使用`window.ethereum`对象创建一个 Web3 的实例:

```
let web3 = new Web3(window.ethereum);

```

然后，使用其 ABI 和地址创建对已部署合同的引用:

```
let contract = new web3.eth.Contract(abi, contractAddress)

```

初始化契约后，就可以与之进行交互了。如果契约有一个名为`greet`的接口方法，我们使用下面的代码调用它:

```
contract.methods.greet().call()

```

现在，我们将修改`call contract`按钮，用您部署的契约的`greet`方法调用一个契约:

```
<!-- vue -->
<button v-if="connected" @click="callContract">Call contract</button>

```

接下来，在 Vue 对象中创建一个`call contract`按钮调用的`callContract`方法:

```
callContract: function () {
  // method for calling the contract method
  let web3 = new Web3(window.ethereum);
  let contractAddress = '0xC0B2D76aB95B7E31E241ce713ea1C72d0a50588e';

  let abi = JSON.parse(`[{"inputs": [],"stateMutability": "nonpayable","type": "constructor"},{"inputs": [],"name": "greet","outputs": [{"internalType": "string","name": "","type": "string"}],"stateMutability": "view","type": "function"}]`);

  let contract = new web3.eth.Contract(abi, contractAddress);

  contract.methods.greet().call()
    .then(result => this.contractResult = result);
}

```

`callContract`方法将被注册为按钮的 click 事件，并将调用已部署的智能合约中的`greet`方法:

```
<template>
  <div>
    <button v-if="!connected" @click="connect">Connect wallet</button>

    <!-- "callContract" event handler is added -->
>    <button v-if="connected" @click="callContract">Call contract</button>

    <!-- displays the result of the contract -->
    {{ contractResult }}
  </div>
</template>

<script>
import Web3 from 'web3'

export default {
  name: 'App',

  data() {
    return {
      connected: false,
      contractResult: '',
    }
  },

  methods: {

    connect: function () {
      let ethereum = window.ethereum;
      if (ethereum) {
      ethereum.request({ method: 'eth_requestAccounts' })
       .then(() => {
            this.connected = true;
          });
      }
    },

>    callContract: function () {
>        // method for calling the contract method
>      let web3 = new Web3(window.ethereum);
>      let contractAddress = '0xC0B2D76aB95B7E31E241ce713ea1C72d0a50588e';

>      let abi = JSON.parse(`[{"inputs": [],"stateMutability": "nonpayable","type": "constructor"},{"inputs": [],"name": "greet","outputs": [{"internalType": "string","name": "","type": "string"}],"stateMutability": "view","type": "function"}]`);

>      let contract = new web3.eth.Contract(abi, contractAddress);

>      contract.methods.greet().call()
>        .then(result => this.contractResult = result);
>    }

  }
}
</script>

```

在用户连接他们的钱包并调用合同后，应用程序应该类似于下图:

![User Connect Wallet Output](img/3039e2e2a6b3ca0a7bc2023230305559.png)

## 结论

在本文中，我们学习了如何使用 Vue 通过以太坊区块链网络创建 Web3 应用程序、连接 MetaMask 钱包以及与智能合约交互。正如你所看到的，只要你熟悉 Solidity，将 Web3 集成到 Vue 应用程序中是相当简单的。Web3 为隐私和数据所有权提供了主要的好处，我们探索了组成这一过程的一些工具和概念，如智能合同。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。