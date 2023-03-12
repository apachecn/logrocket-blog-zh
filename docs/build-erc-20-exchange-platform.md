# 如何建立 ERC-20 交流平台

> 原文：<https://blog.logrocket.com/build-erc-20-exchange-platform/>

令牌和 NFT 的应用每天都在增加。他们带来了许多新的可能性。从在游戏中使用它们到为某项活动创造你自己的虚拟货币，你可以使用 [Web3](https://blog.logrocket.com/best-practices-web3-providers/) 技术做很多事情。

今天我们要做一个代币兑换平台，用户可以在这里买卖特定的代币。这里我们将使用的令牌类型是 ERC-20，我们将在多边形孟买网络上部署它们。

我们将遵循的方法是首先创建一个智能合同，这将铸造我们的代币，然后创建另一个智能合同来促进代币的买卖。然后，我们将在 Next.js 应用程序中使用这两个智能合约，以便让用户能够进行买卖。

### 先决条件

*   React 的工作知识
*   Next.js 的工作知识
*   [坚实度](https://blog.logrocket.com/develop-solidity-smart-contracts-hardhat/)的工作知识
*   Node.js 已安装
*   代码编辑器——我更喜欢 Visual Studio 代码
*   至少安装了一个 wallet 的元掩码扩展
*   连接到多边形孟买测试网的元掩码

如果你觉得自己被困在教程中，请随意参考 [GitHub 库](https://github.com/atharvadeosthale/erc20-exchange-logrocket)。

### 内容

## 创建令牌智能合约

代币智能合约将帮助我们铸造代币。打开 Remix IDE，在`contracts`文件夹下新建一个名为`TestToken.sol`的文件。在文件中使用以下代码:

```
// SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.4;
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contract TestToken is ERC20 {
    constructor() ERC20("TestToken", "TEST"){
        _mint(msg.sender, 10000 * 10 ** 18);
    }
}

```

在上面的代码中，我们使用 OpenZeppelin 契约模板来创建一个 ERC-20 令牌。这些模板为我们提供了 ERC-20 令牌所需的最低限度的功能，并且是安全的和优化的，因此您不必担心使用收紧您的令牌的安全性。在契约的构造函数中，我们指定了令牌的名称和符号，并在契约创建者的钱包中铸造了 10，000 个令牌。

现在，通过按下 **Ctrl + S** (或者 Mac 的 **Cmd + S** )来编译合同。确保已将 MetaMask 连接到多边形 Mumbai。进入侧边栏的 **Deploy** 选项卡，将环境设置为 **Injected Web3** ，这样 Remix IDE 就可以使用 MetaMask 来部署你的契约了。

确保您的其他设置与此类似:

![metamask settings](img/4e5b87a3b17b4d66d8be9ca770485548.png)

验证所有设置后，点击**部署**。这将打开元掩码授权弹出窗口。点击**确认**。

如果你的 Polygon Mumbai 钱包里没有资金，你可以通过一个[水龙头](https://faucet.polygon.technology/)得到一些。确认交易后，等待几秒钟，直到您看到您的合同部署在左侧栏上。

复制合同地址，因为我们在配置供应商合同时需要它。

## 创建供应商合同

在`contracts`文件夹下创建一个名为`TestTokenVendor.sol`的新文件。对合同使用以下代码:

```
// SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.4;
import "./TestToken.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
contract Vendor is Ownable {
  TestToken yourToken;
  uint256 public tokensPerMatic = 100;
  event BuyTokens(address buyer, uint256 amountOfMATIC, uint256 amountOfTokens);
  constructor(address tokenAddress) {
    yourToken = TestToken(tokenAddress);
  }

  function buyTokens() public payable returns (uint256 tokenAmount) {
    require(msg.value > 0, "You need to send some MATIC to proceed");
    uint256 amountToBuy = msg.value * tokensPerMatic;

    uint256 vendorBalance = yourToken.balanceOf(address(this));
    require(vendorBalance >= amountToBuy, "Vendor has insufficient tokens");

    (bool sent) = yourToken.transfer(msg.sender, amountToBuy);
    require(sent, "Failed to transfer token to user");

    emit BuyTokens(msg.sender, msg.value, amountToBuy);
    return amountToBuy;
  }
  function sellTokens(uint256 tokenAmountToSell) public {

    require(tokenAmountToSell > 0, "Specify an amount of token greater than zero");

    uint256 userBalance = yourToken.balanceOf(msg.sender);
    require(userBalance >= tokenAmountToSell, "You have insufficient tokens");

    uint256 amountOfMATICToTransfer = tokenAmountToSell / tokensPerMatic;
    uint256 ownerMATICBalance = address(this).balance;
    require(ownerMATICBalance >= amountOfMATICToTransfer, "Vendor has insufficient funds");
    (bool sent) = yourToken.transferFrom(msg.sender, address(this), tokenAmountToSell);
    require(sent, "Failed to transfer tokens from user to vendor");

    (sent,) = msg.sender.call{value: amountOfMATICToTransfer}("");
    require(sent, "Failed to send MATIC to the user");
  }

  function withdraw() public onlyOwner {
    uint256 ownerBalance = address(this).balance;
    require(ownerBalance > 0, "No MATIC present in Vendor");
    (bool sent,) = msg.sender.call{value: address(this).balance}("");
    require(sent, "Failed to withdraw");
  }
}

```

在上面的代码中，我们正在创建一个`Ownable`契约，这是一个 OpenZeppelin 模板。这意味着，如果我们愿意，我们可以将该合同的所有权转让给其他钱包。

我们正在导入令牌契约，并通过构造函数向它提供地址(在部署契约时，我们将提供地址作为参数)。我们还在`tokensPerMatic`变量中设置了令牌的一般价格。该值将用于根据发送给合同的 MATIC 数量计算要购买的令牌数量。

在`buyTokens()`函数中，我们首先检查是否发送了任何 MATIC，以及供应商合同中是否有足够的令牌。然后我们做`yourToken.transfer()`将我们的令牌从供应商契约发送到发送契约调用的钱包。最后，我们将发出一个事件，并返回购买的令牌数。

`sellTokens()`函数不像`buyTokens()`函数那样简单。我们检查用户是否拥有指定数量的令牌，计算要发送回用户的 MATIC 数量，并检查供应商合同是否有足够的 MATIC 余额。

然而，有趣的部分来了:我们不能只是从用户的钱包转移令牌到我们的供应商合同的余额。我们首先需要获得用户的批准，让我们管理他们的令牌，并将这些令牌发送回我们。这种方法是安全的，因为我们需要设置请求批准的令牌数量的限制。授权基于元掩码，用户可以清楚地看到合同允许处理多少令牌。

这个批准过程发生在前端(在本例中是 Next.js 应用程序的前端)。获得批准后，我们执行`transferFrom()`功能将用户的资金转移到供应商合同的钱包中。最后，我们将 MATIC 发送给用户。

`withdraw()`功能只能由合同所有者运行。此功能允许您将智能合约中存储的所有 MATIC 发送到所有者的钱包中。

最后，部署契约，并将令牌契约的地址作为参数传递。

## 配置供应商智能合同

既然我们已经部署了供应商智能合约，我们需要向它发送一些令牌来工作。

在**部署**选项卡中打开令牌合同。在**转账**部分，粘贴卖家合同地址和代币总数(点击`totalSupply`可获得)。

点击**交易**以批准 MetaMask 交易，所有代币将从您的钱包发送到卖方合同余额。现在，您的供应商合同能够促进代币的买卖。

现在，转到 **compile** 选项卡，复制两个契约的 ABI(以及令牌和供应商地址)，因为我们将在 Next.js 文件中需要它们。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 设置我们的 Next.js 应用程序

导航到一个安全目录，并在终端中运行以下命令来创建 Next.js 应用程序:

```
npx create-next-app erc20-exchange-platform

```

如果您希望使用`npm`安装依赖项，请在末尾使用`--use-npm`(`create-next-app`最近默认为 yarn)。导航到项目文件夹并运行以下命令来安装一些必需的依赖项:

```
#npm
npm install @thirdweb-dev/sdk @thirdweb-dev/react web3
#yarn
yarn add @thirdweb-dev/sdk @thirdweb-dev/react web3

```

我们正在安装`@thirdweb-dev/sdk`来帮助认证，并将我们的应用程序连接到 MetaMask。这样，我们就不会在认证上花费太多时间。

我们还安装了`@thirdweb-dev/react`来为我们提供钩子，这些钩子将与`@thirdweb-dev/sdk`一起为我们提供关于用户状态的有用信息。我们正在安装`web3`来与我们的令牌和供应商智能合同进行交互。

因为我们不会在本教程中讨论样式，所以我就把它留在这里，不再过多关注细节。打开`styles`文件夹下的`globals.css`，使用以下样式:

```
html,
body {
  padding: 0;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
    Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
}
a {
  color: inherit;
  text-decoration: none;
}
* {
  box-sizing: border-box;
}
.home__container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}
.home__button {
  background-color: #2ecc71;
  border: none;
  border-radius: 5px;
  font-size: 20px;
  padding: 15px;
  margin: 10px;
  cursor: pointer;
}
.exchange__container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}
.exchange__textBox {
  width: 300px;
  height: 50px;
  border: 1px solid #c4c4c4;
  border-radius: 5px;
  font-size: 20px;
  padding: 15px;
  margin: 10px;
  cursor: pointer;
}
.exchange__button {
  width: 300px;
  height: 50px;
  border: 1px solid #2ecc71;
  background-color: #2ecc71;
  border-radius: 5px;
  font-size: 20px;
  padding: 15px;
  margin: 10px;
  cursor: pointer;
}

```

## 设置环境变量和其他数据

在项目目录中创建一个名为`.env.local`的新文件，它将保存我们的合同地址:

```
NEXT_PUBLIC_TOKEN_CONTRACT_ADDRESS=(token address here)
NEXT_PUBLIC_VENDOR_CONTRACT_ADDRESS=(vendor address here)

```

现在，在项目目录中创建一个名为`contracts.js`的新文件。在此文件中，我们将保存并导出两个合同的合同 ABI:

```
export const tokenABI = [
  {
    inputs: [],
    stateMutability: "nonpayable",
    type: "constructor",
  },
  {
    anonymous: false,
    inputs: [
      {
        indexed: true,
        internalType: "address",
        name: "owner",
        type: "address",
      },
      {
        indexed: true,
        internalType: "address",
        name: "spender",
        type: "address",
      },
      {
        indexed: false,
        internalType: "uint256",
        name: "value",
        type: "uint256",
      },
    ],
    name: "Approval",
    type: "event",
  },
  {
    anonymous: false,
    inputs: [
      {
        indexed: true,
        internalType: "address",
        name: "from",
        type: "address",
      },
      {
        indexed: true,
        internalType: "address",
        name: "to",
        type: "address",
      },
      {
        indexed: false,
        internalType: "uint256",
        name: "value",
        type: "uint256",
      },
    ],
    name: "Transfer",
    type: "event",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "owner",
        type: "address",
      },
      {
        internalType: "address",
        name: "spender",
        type: "address",
      },
    ],
    name: "allowance",
    outputs: [
      {
        internalType: "uint256",
        name: "",
        type: "uint256",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "spender",
        type: "address",
      },
      {
        internalType: "uint256",
        name: "amount",
        type: "uint256",
      },
    ],
    name: "approve",
    outputs: [
      {
        internalType: "bool",
        name: "",
        type: "bool",
      },
    ],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "account",
        type: "address",
      },
    ],
    name: "balanceOf",
    outputs: [
      {
        internalType: "uint256",
        name: "",
        type: "uint256",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [],
    name: "decimals",
    outputs: [
      {
        internalType: "uint8",
        name: "",
        type: "uint8",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "spender",
        type: "address",
      },
      {
        internalType: "uint256",
        name: "subtractedValue",
        type: "uint256",
      },
    ],
    name: "decreaseAllowance",
    outputs: [
      {
        internalType: "bool",
        name: "",
        type: "bool",
      },
    ],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "spender",
        type: "address",
      },
      {
        internalType: "uint256",
        name: "addedValue",
        type: "uint256",
      },
    ],
    name: "increaseAllowance",
    outputs: [
      {
        internalType: "bool",
        name: "",
        type: "bool",
      },
    ],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [],
    name: "name",
    outputs: [
      {
        internalType: "string",
        name: "",
        type: "string",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [],
    name: "symbol",
    outputs: [
      {
        internalType: "string",
        name: "",
        type: "string",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [],
    name: "totalSupply",
    outputs: [
      {
        internalType: "uint256",
        name: "",
        type: "uint256",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "to",
        type: "address",
      },
      {
        internalType: "uint256",
        name: "amount",
        type: "uint256",
      },
    ],
    name: "transfer",
    outputs: [
      {
        internalType: "bool",
        name: "",
        type: "bool",
      },
    ],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "from",
        type: "address",
      },
      {
        internalType: "address",
        name: "to",
        type: "address",
      },
      {
        internalType: "uint256",
        name: "amount",
        type: "uint256",
      },
    ],
    name: "transferFrom",
    outputs: [
      {
        internalType: "bool",
        name: "",
        type: "bool",
      },
    ],
    stateMutability: "nonpayable",
    type: "function",
  },
];
export const vendorABI = [
  {
    inputs: [
      {
        internalType: "address",
        name: "tokenAddress",
        type: "address",
      },
    ],
    stateMutability: "nonpayable",
    type: "constructor",
  },
  {
    anonymous: false,
    inputs: [
      {
        indexed: false,
        internalType: "address",
        name: "buyer",
        type: "address",
      },
      {
        indexed: false,
        internalType: "uint256",
        name: "amountOfETH",
        type: "uint256",
      },
      {
        indexed: false,
        internalType: "uint256",
        name: "amountOfTokens",
        type: "uint256",
      },
    ],
    name: "BuyTokens",
    type: "event",
  },
  {
    anonymous: false,
    inputs: [
      {
        indexed: true,
        internalType: "address",
        name: "previousOwner",
        type: "address",
      },
      {
        indexed: true,
        internalType: "address",
        name: "newOwner",
        type: "address",
      },
    ],
    name: "OwnershipTransferred",
    type: "event",
  },
  {
    inputs: [],
    name: "buyTokens",
    outputs: [
      {
        internalType: "uint256",
        name: "tokenAmount",
        type: "uint256",
      },
    ],
    stateMutability: "payable",
    type: "function",
  },
  {
    inputs: [],
    name: "owner",
    outputs: [
      {
        internalType: "address",
        name: "",
        type: "address",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [],
    name: "renounceOwnership",
    outputs: [],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "uint256",
        name: "tokenAmountToSell",
        type: "uint256",
      },
    ],
    name: "sellTokens",
    outputs: [],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [],
    name: "tokensPerEth",
    outputs: [
      {
        internalType: "uint256",
        name: "",
        type: "uint256",
      },
    ],
    stateMutability: "view",
    type: "function",
  },
  {
    inputs: [
      {
        internalType: "address",
        name: "newOwner",
        type: "address",
      },
    ],
    name: "transferOwnership",
    outputs: [],
    stateMutability: "nonpayable",
    type: "function",
  },
  {
    inputs: [],
    name: "withdraw",
    outputs: [],
    stateMutability: "nonpayable",
    type: "function",
  },
];

```

## 致力于认证

转到`pages`文件夹下的`_app.js`文件。在这里，我们将用`ThirdwebProvider`将我们的应用程序括起来，这将帮助我们实现身份验证。

您的`_app.js`应该是这样的:

```
import { ThirdwebProvider, ChainId } from "@thirdweb-dev/react";
import "../styles/globals.css";
function MyApp({ Component, pageProps }) {
  return (
    <ThirdwebProvider desiredChainId={ChainId.Mumbai}>
      <Component {...pageProps} />
    </ThirdwebProvider>
  );
}
export default MyApp;

```

这里，我们提供`desiredChainId`作为多边形孟买的链 ID。现在转到`index.js`并在其中复制以下代码:

```
import { useAddress, useMetamask } from "@thirdweb-dev/react";
import Head from "next/head";
import Image from "next/image";
import styles from "../styles/Home.module.css";
import { useEffect } from "react";
import { useRouter } from "next/router";
export default function Home() {
  const connectWithMetamask = useMetamask();
  const router = useRouter();
  const address = useAddress();
  useEffect(() => {
    if (address) router.replace("/exchange");
  }, [address]);
  return (
    <div>
      <Head>
        <title>Exchange TEST tokens</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className="home__container">
        <h1>Sign in to exchange</h1>
        <button className="home__button" onClick={connectWithMetamask}>
          Sign in using MetaMask
        </button>
      </div>
    </div>
  );
}

```

上图中，我们使用第三个网站的钩子，通过一个按钮来执行认证，并跟踪用户钱包的地址。我们还检查用户是否在`useEffect()`中被认证，并相应地重定向用户。

现在在`pages`目录中创建一个名为`exchange.js`的新文件。文件中有以下布局:

```
import { useAddress } from "@thirdweb-dev/react";
import Head from "next/head";
import { useRouter } from "next/router";
import React, { useEffect, useState } from "react";
import Web3 from "web3";
import { tokenABI, vendorABI } from "../contracts";
const web3 = new Web3(Web3.givenProvider);
function Exchange() {
  const [tokens, setTokens] = useState();
  const [matic, setMatic] = useState(0);
  const address = useAddress();
  const router = useRouter();
  const purchase = async () => {};
  const sell = async () => {};
  useEffect(() => {
    if (!address) router.replace("/");
  }, [address]);
  useEffect(() => {
    setMatic(tokens / 100);
  }, [tokens]);
  return (
    <div>
      <Head>
        <title>Exchange TEST tokens</title>
      </Head>
      <div className="exchange__container">
        <h1>Purchase TEST Tokens</h1>
        <input
          type="number"
          placeholder="Amount of tokens"
          className="exchange__textBox"
          value={tokens}
          onChange={(e) => setTokens(e.target.value)}
        />
        <div>MATIC equivalent: {matic}</div>
        <button className="exchange__button" onClick={purchase}>
          Purchase
        </button>
        <button className="exchange__button" onClick={sell}>
          Sell
        </button>
      </div>
    </div>
  );
}
export default Exchange;

```

上面的代码检查用户是否经过身份验证，并计算购买令牌需要多少 MATIC。代码应提供如下结果:

![Purchase test tokens screen](img/726236c4bb2b82e68d7edf9dd3b7f014.png)

当您键入代币数量时，所需的 MATIC 数量将会更新。我们还在组件之前初始化了`web3`包，所以我们现在可以使用基于契约的操作。

## 实施采购和销售功能

现在，让我们研究购买和销售的实际功能。在上面的代码中，我们没有`purchase()`函数的代码。下面是它的代码:

```
const purchase = async () => {
  try {
    const accounts = await web3.eth.getAccounts();
    const vendor = new web3.eth.Contract(
      vendorABI,
      process.env.NEXT_PUBLIC_VENDOR_CONTRACT_ADDRESS
    );
    const request = await vendor.methods.buyTokens().send({
      from: accounts[0],
      value: web3.utils.toWei(matic.toString(), "ether"),
    });
    alert("You have successfully purchased TEST tokens!");
    console.log(request);
  } catch (err) {
    console.error(err);
    alert("Error purchasing tokens");
  }
};

```

在上面的代码中，我们首先获得与 MetaMask 连接的帐户。然后，我们通过在构造函数的参数中提供契约 ABI 和地址来初始化我们的供应商契约。

然后，我们调用合同的`buyTokens()`函数，并随请求一起发送所需数量的 MATIC。现在，当您尝试购买令牌时，元掩码授权应该会弹出，接受后，您应该会很快在元掩码资产中看到令牌:

![number of test tokens](img/9aec54f8801dcf591bfbf97da3d2fa57.png)

`sell()`的代码如下:

```
const sell = async () => {
  try {
    const accounts = await web3.eth.getAccounts();
    const tokenContract = new web3.eth.Contract(
      tokenABI,
      process.env.NEXT_PUBLIC_TOKEN_CONTRACT_ADDRESS
    );
    // Approve the contract to spend the tokens
    let request = await tokenContract.methods
      .approve(
        process.env.NEXT_PUBLIC_VENDOR_CONTRACT_ADDRESS,
        web3.utils.toWei(tokens, "ether")
      )
      .send({
        from: accounts[0],
      });
    // Trigger the selling of tokens
    const vendor = new web3.eth.Contract(
      vendorABI,
      process.env.NEXT_PUBLIC_VENDOR_CONTRACT_ADDRESS
    );
    request = await vendor.methods
      .sellTokens(web3.utils.toWei(tokens, "ether"))
      .send({
        from: accounts[0],
      });
    alert("You have successfully sold TEST tokens!");
    console.log(request);
  } catch (err) {
    console.error(err);
    alert("Error selling tokens");
  }
};

```

在上面的代码中，我们联系代币合同，以批准代表我们花费所需代币的供应商合同。然后，我们联系我们的供应商合同，启动销售流程。然后，令牌将被转移到供应商，而 MATIC 将被发送到用户的钱包。

## 结论

恭喜你！您已经成功创建了自己的 ERC 20 代币兑换平台。如果你觉得自己被困在了教程的某个地方，这里有 [GitHub 库](https://github.com/atharvadeosthale/erc20-exchange-logrocket)。

我建议尝试一些新的东西，增加一些功能，使平台更具互动性，这将有助于你更好地学习坚实的概念。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。