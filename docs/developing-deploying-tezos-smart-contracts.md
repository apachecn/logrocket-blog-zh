# 开发和部署 Tezos 智能合约

> 原文：<https://blog.logrocket.com/developing-deploying-tezos-smart-contracts/>

Tezos 是最古老的智能合同区块链之一，以太坊是第一个。

虽然以太坊是开发人员开发和部署智能合约的热门选择，但由于高费用和缓慢的交易，其应用程序的可扩展性不是很好。相比之下，Tezos 应用程序非常高效，安装成本也很低。

在本指南中，您将了解如何使用 SmartPy CLI 在 Tezos 中开发和部署智能合同。我们将涵盖:

你可以在[这个 GitHub repo](https://github.com/TheGhoulRe/tezos-smart-contract) 中获得最终项目的代码。

## 先决条件

要阅读本文，您需要熟悉 Tezos 区块链。你的知识不一定要深入。

以太坊区块链的知识不是必需的，但它很有帮助。

## Tezos 是什么？

Tezos 是一个区块链网络和一个智能合同平台，通过其治理模式和自我升级能力来适应。

虽然 Tezos 和以太坊类似于支持智能合约和 DApp 开发的区块链，但它们在几个方面有所不同。下面的 Tezos vs. Ethereum 表显示了这两个网络之间的一些差异。

### Tezos vs .以太坊:对比图

| **泰佐斯** | **以太坊** |
| 开发人员提议升级网络上的区块链协议 | 开发者使用硬盘来升级区块链协议 |
| 利益攸关方通过投票接受或拒绝提案来控制对《区块链议定书》的升级 | 区块链网络的创建者负责管理区块链协议的升级 |
| 使用正式验证来执行智能合同 | 使用 EVM 在网络上存储和执行智能合同 |
| 参与者(节点)可以选择提供计算资源或委托他们的令牌来参与验证事务 | 所有参与者(节点)必须提供计算资源并投入他们的硬币来参与交易验证 |
| 汽油费很低，这使得它非常具有可扩展性 | 汽油费真的很高，这使得它无法扩展 |

## 什么是 SmartPy？

SmartPy 是一个工具，可以让您在 Tezos 区块链上轻松建立智能合同。

使用 Python 在 SmartPy 中编写合同。如果你已经熟悉 Python，你就不必学习新的[智能契约编程语言](https://blog.logrocket.com/smart-contract-programming-languages/)。

SmartPy 还有另外两种使用其他语法的变体:SmartTS 使用 TypeScript，SmartML 使用 OCaml。

SmartPy 将 Python 脚本编译成迈克尔逊。Michelson 是 Tezos 中智能合约的低级编程语言。SmartTS 和 SmartML 也可以使用 SmartPy 工具进行编译。

## 什么是智能合约？

智能合同是你可以在区块链网络上构建、部署和执行的程序。在部署智能合约之前，您不需要在网络上拥有一个节点。

借助智能合约，您可以构建所有设备都可以访问的应用程序，而无需拥有或维护服务器。这些完全分散的应用程序几乎不需要维护。

智能合约充分利用了区块链的潜力，允许你在链上执行程序，并与链本身进行交互。您可以持有或转让代币，并通过智能合约访问区块链上的专业功能。

## smarty cli 入门

要安装 SmartPy CLI，请运行以下命令:

```
$ bash <(curl -s https://smartpy.io/cli/install.sh)

```

该命令在`home`中创建一个`smartpy-cli`目录，树形结构如下:

```
smartpy-cli
├── browser.py
├── node_modules/
├── originator.js
├── package.json
├── package-lock.json
├── __pycache__/
├── smart.css
├── smart.js
├── smartpyc.js
├── smartpyc.py
├── smartpyio.py
├── smartpy.py
├── SmartPy.sh
├── smarttop/
├── smart-ts-cli.js
├── templates/
├── theme.js
└── typography.css

```

要确认安装，请运行以下命令:

```
$ ~/smartpy-cli/SmartPy.sh --version

```

作为使`SmartPy.sh`易于访问的一个额外步骤，通过将以下内容复制到您的`.bashrc`文件或相关配置文件中来创建一个别名:

```
$ alias smartpy="$HOME/smartpy-cli/SmartPy.sh"

```

现在您可以使用:

```
$ smartpy --version

```

而不是:

```
$ ~/smartpy-cli/SmartPy.sh --version

```

## 设置`tezos-client`

本指南使用`tezos-client`与已部署的智能合同进行交互。要安装`tezos-client,`,请运行以下命令之一，具体取决于您使用的是 Mac 还是 Linux 操作系统:

```
# Mac
$ brew tap serokell/tezos-packaging-stable https://github.com/serokell/tezos-packaging-stable.git
$ brew install tezos-client

# Linux
$ wget https://github.com/serokell/tezos-packaging/releases/latest/download/tezos-client
$ chmod +x tezos-client
$ mkdir -p $HOME/.local/bin
$ mv tezos-client $HOME/.local/bin
$ echo 'export PATH="$HOME/.local/bin:$PATH"' >> $HOME/.bashrc
$ source $HOME/.bashrc

```

注意，如果您使用的是 Windows，您需要[安装一个带有 wsl](https://docs.microsoft.com/en-us/windows/wsl/about) 的 Linux 发行版并运行 Linux 安装。

安装`tezos-client`后，需要进行设置。通过运行以下命令开始:

```
$ tezos-client --endpoint https://jakartanet.ecadinfra.com config update

```

本指南使用 Jarkatanet testnet 来部署智能合约并与之交互。上面的命令通过网络上的节点`[https://jakartanet.ecadinfra.com](https://jakartanet.ecadinfra.com)`将`tezos-client`连接到 Jakartanet testnet。

在使用`tezos-client`进行任何操作之前，您需要连接一个帐户。Tezos 提供水龙头账户，让你免费与网络互动。

你需要访问 [Jakartanet 水龙头网站](https://teztnets.xyz/jakartanet-faucet)并下载水龙头文件。完成后，打开终端中的下载文件夹并运行以下命令:

```
$ tezos-client activate account faucet with ./jakartanet.json

```

该命令激活您的`tezos-client`中的水龙头帐户，并给它一个别名`faucet`。要检查该帐户的余额，请打开`jakartanet.json`文件，复制`pkh`的值，然后运行以下命令:

```
$ tezos-client get balance for <address>

```

用您复制的`pkh`值替换`<address>`。

注意，Tezos 水龙头账户对所有人都是公开的，并且有限量的 Tez 令牌，所以你必须规范你的使用。

## 与 SmartPy 开发 Tezos 智能合约

要开始创建我们的示例智能合同，请创建一个新的`store_text.py`文件，并将以下内容复制到其中:

```
import smartpy as sp

class StoreText(sp.Contract):
    def __init__(self, value):
        self.init(text = value)

    @sp.entry_point
    def replace(self, params):
        self.data.text = params.text

    @sp.entry_point    # Note: the spaces before "@"
    def append(self, params):
        self.data.text += params.text

```

下面是上述合同的工作原理，一点一点来说:

首先，我们导入了`smartpy`库:

```
import smartpy as sp

```

然后，我们定义了一个扩展`sp.Contract`的类:

```
class StoreText(sp.Contract):

```

最后，我们在智能合约中定义了一些项目；首先，一个构造函数来初始化`text`:

```
    def __init__(self, value):    # Note: the spaces before "def"
        self.init(text = value)

```

第二，替换`text`值的入口点:

```
    @sp.entry_point    # Note: the spaces before "@"
    def replace(self, params):
        self.data.text = params.text

```

第三，向`text`追加字符串的入口点:

```
    @sp.entry_point    # Note: the spaces before "@"
    def append(self, params):
        self.data.text += params.text

```

接下来，我们来看看如何测试智能合约。

## 测试 Tezos 智能合约

部署到 Tezos 的智能合约不能更改或删除。这可能是一个问题，因为合同中可能会有错误，而错误可能会导致代价高昂的错误和资金损失。

SmartPy 使您能够在部署之前轻松测试您的合同。测试合同不需要任何令牌或钱包帐户来运行。你所需要做的就是打开`store_text.py`文件并复制下面的内容:

```
@sp.add_test(name = "StoreText")
def test():
  scenario = sp.test_scenario()
  contract = StoreText("Hello")
  scenario += contract

  scenario.verify(contract.data.text == "Hello")

  contract.replace(text = "Hi")
  contract.append(text = ", there!")
  scenario.verify(contract.data.text == "Hi, there!")

```

下面是代码片段的工作方式。首先，我们注册了一个`test`函数作为测试脚本:

```
@sp.add_test(name = "StoreText")

```

然后，我们定义了`test`函数:

```
def test():

```

在剩下的几行中，我们创建了一个测试场景:

```
  scenario = sp.test_scenario()

```

初始化与`"Hello"`的合同:

```
  contract = StoreText("Hello")

```

将合同实例添加到场景中:

```
  scenario += contract

```

已验证合同的`text`值为`"Hello"`:

```
 scenario.verify(contract.data.text == "Hello") 
```

称为`replace`和`append`入口点:

```
  contract.replace(text = "Hi")
  contract.append(text = ", there!")

```

最后，验证合同的`text`值现在是`"Hi, there"`:

```
  scenario.verify(contract.data.text == "Hi, there!")

```

添加测试后，保存文件并运行以下命令:

```
$ ~/smartpy-cli/SmartPy.sh test store_text.py ./test-output

```

如果测试成功，编译器不会抛出错误信息。

## 将智能合同编译给迈克尔逊

在部署您的智能合约之前，您需要将其编译到 Michelson。如前所述，Michelson 是一种低级编程语言，用于 Tezos 区块链上的智能合约。

要编译`store_text.py`，运行以下命令:

```
$ ~/smartpy-cli/SmartPy.sh compile message.py ./output

```

如果编译成功，您应该会看到如下所示的`output`文件夹:

```
output/
├── scenario.json
├── script_init.py
├── script_pure.py
└── storeMessage/
    ├── log.txt
    ├── step_000_cont_0_contract.json
    ├── step_000_cont_0_contract.py
    ├── step_000_cont_0_contract.tz
    ├── step_000_cont_0_sizes.csv
    ├── step_000_cont_0_storage.json
    ├── step_000_cont_0_storage.py
    ├── step_000_cont_0_storage.tz
    └── step_000_cont_0_types.py

```

`output`目录包含部署智能合同所需的所有文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 部署 Tezos 智能合约

要部署`store_text.py`，请在终端中打开`output/storeMessage`文件夹并运行以下命令:

```
$ ~/smartpy-cli/SmartPy.sh originate-contract --code step_000_cont_0_contract.json --storage step_000_cont_0_storage.json --rpc https://jakartanet.ecadinfra.com

[INFO] - Using RPC https://jakartanet.ecadinfra.com/...
[INFO] - Contract KT1………………CAjjW originated!!!

```

下面是该命令的工作方式:

*   `originate-contract`告诉`tezos-client`部署(“发起”)合同
*   `--code step_000_cont_0_contract.json`指向已编译的合同文件
*   `--storage step_000_cont_0_storage.json`指向编译后的存储文件
*   `--rpc [https://jakartanet.ecadinfra.com](https://jakartanet.ecadinfra.com)`指向您要部署到的网络上的 RPC 节点

Tezos 智能合同通常包括两个部分:存储和合同。存储保存协定存储的数据，协定保存智能协定的逻辑。

请注意，您可以使用任何协定或存储编译来部署协定。当您使用该命令时，它们必须是相同的文件扩展名。

默认情况下，如果您在 testnet 上部署，编译器将使用水龙头帐户。如果您正在部署到 mainnet 或者想要使用您的钱包帐户，请添加`--private-key`标志，后跟帐户的私钥。

## 与部署的智能合约交互

在与已部署的智能合约进行交互之前，您需要了解合约当前的样子。为此，[在浏览器](https://smartpy.io/explorer.html)中打开 SmartPy Explorer，并遵循以下步骤:

1.  导航到“备用节点”
2.  将合同的地址粘贴在“合同”输入中
3.  在“浏览特定节点”下，从`mainnet`切换到`jakartanet`
4.  将 RPC URL `[https://jakartanet.ecadinfra.com](https://jakartanet.ecadinfra.com)`粘贴到文本框中
5.  单击“在特定节点上浏览”

当合同数据出现时，文本存储器应该显示为“你好，这里！”

现在你知道了合同的文本，你可以把它改成“嗨，你好！”通过用这个命令调用`replace`入口点:

```
$ tezos-client transfer 0 from faucet to <contract-address> --entrypoint replace --arg '"Hi, There!"'

```

如果命令成功，当您刷新资源管理器时，存储现在应该显示“Hi，There！”

注意，在运行该命令之前，必须用已部署契约的地址替换`<contract-address>`。

## 结论

本文介绍了使用 SmartPy 在 Tezos 上构建智能合约的过程。构建智能合同允许您充分利用区块链网络来构建分散的应用程序和组织。

我希望这篇文章能帮助您理解在 Tezos 上构建智能合约的过程。如果你想了解更多关于智能合同的信息，看看这篇关于避免的[智能合同错误的文章。](https://blog.logrocket.com/smart-contract-development-common-mistakes-avoid/)

感谢阅读！祝你愉快。开发和部署 Tezos 智能合约

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。