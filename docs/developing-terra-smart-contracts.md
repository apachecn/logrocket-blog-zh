# 开发 Terra 智能合同- LogRocket 博客

> 原文：<https://blog.logrocket.com/developing-terra-smart-contracts/>

自 Web 3 诞生以来，构建自己的智能合约就一直存在，它允许人们构建部署到区块链的程序。

部署智能合约区块链从以太坊到比特币等等。此外，智能合约共同组成了分散的应用程序，这些应用程序可以在 Truffle、Hardhat 和 Embark 等框架上开发。

在本文中，我们将研究如何开发智能合约，并将其部署到类似以太坊的特拉区块链网络中。

## Terra 的基本概述

在继续阅读之前，需要注意一些关于 Terra 的事情:

*   部署到 Terra 区块链[的智能合约是用铁锈](https://blog.logrocket.com/why-is-rust-popular/)写的
*   我们的本地测试网是 [LocalTerra](https://github.com/terra-money/LocalTerra)
*   [Terra.js](https://terra-money.github.io/terra.js/) 和 [Terra SDK](https://terra-money.github.io/terra.py/) 是用于与 Terra 区块链交互的两个可用库
*   Terra 是由 Terraform 实验室创造的

此外，请注意，Terra 的共识是使用新 BFT 的利害关系证明算法。这允许持有者将他们的代币作为抵押品来验证交易。之后会根据下注的代币数量给予奖励。

同样重要的是要知道，月神是 Terra 的加密货币，被用来为区块链提供能量。这种区块链通过算法扩展和减少供应来提供价格稳定性。

要了解更多，我建议你[阅读这里的文档](https://docs.terra.money/Concepts/Protocol.html#terra-and-luna)。本文的主要焦点是我们如何将我们的智能合约部署到这个区块链协议。

最后，Terra 区块链拥有强大而活跃的共识机制，可以在几秒钟内完成批量交易，比比特币和以太坊快得多。

### 需求和开发基础

让我们看看使用 Terra 协议构建和部署智能契约所需的需求:

*   Rust 编程语言知识
*   熟悉地球生态系统
*   Docker 安装在您的计算机上
*   核心地球
*   本地人

但是，如果您没有上述要求，请不要担心，我们会满足您的所有要求。然而，Terra 团队认为，最后一个必要条件是，想要破坏/扰乱传统金融。

## 环境设置

在开始之前，我们需要安装一些东西。这个安装将帮助我们在编写合同时连接到 Terra 的本地 testnet，并提供最新版本的`terrad`。`terrad`与[地核](https://github.com/terra-money/core/)一起工作。

如果你还没有安装 Rust，你也需要安装它。

首先，让我们安装 Terra Core，这需要我们先安装 Go。为此，[使用此链接](https://go.dev/dl/go1.17.5.darwin-amd64.pkg)下载 Go 并验证:

```
➜  ~ go version
go version go1.17 darwin/amd64

```

Go 版本 1.17+，使用 Terra Core 需要。

### 安装 Terra Core

通过从 GitHub 克隆存储库来安装 Terra Core。然后，检查拥有最新版本的主要分支:

```
$ git clone https://github.com/terra-money/core terra-core
$ cd terra-core
$ git checkout main

```

接下来，安装 Terra Core 来获取`terrad`，它将作为可执行文件与 Terra 节点交互:

```
$ make install

```

然后，验证是否已成功安装:

```
$ terrad version --long

```

您的输出应该类似于以下内容:

```
name: terra
server_name: terrad
version: 0.5.12-1-gd411ae7
commit: d411ae7a276e7eaada72973a640dcab69825163f
build_tags: netgo,ledger
go: go version go1.17 darwin/amd64

```

### 安装 LocalTerra

LocalTerra 将成为我们的测试网，在开发过程中测试我们的智能合同。我们的本地测试网由[web assembly 集成](https://blog.logrocket.com/getting-started-with-webassembly-and-rust/)组成。要启动 LocalTerra，您需要设置 [Docker 和`docker-compose`T4，因为 LocalTerra 是集装箱化的:](https://blog.logrocket.com/node-js-docker-improve-dx/)

```
$ git clone --depth 1 https://www.github.com/terra-money/LocalTerra
$ cd LocalTerra

```

在后台运行 Docker 的情况下，运行以下命令:

```
$ docker-compose up

```

您应该会得到下面的响应，它们是日志:

```
 11:25PM INF Timed out dur=4955.7669 height=5 module=consensus round=0 step=1
terrad_1         | 11:25PM INF received proposal module=consensus proposal={"Type":32,"block_id":{"hash":"54D9C757E9AA84E0F5AAA736E6EED3D83F364A3A62FDC625970539CA81DFA86E","parts":{"hash":"2517579A126AC2BF6EB9EB6274FAE6748D14115C91FC59FE3A2AF5F061A12740","total":1}},"height":5,"pol_round":-1,"round":0,"signature":"AMxXngubsUHyterTZuZsiLgY0olPDpdpgjMIRZ9L59UR9+JngC93xO63yTxwE0kQLp2HdZ99G8M4ATchS7d1CA==","timestamp":"2021-12-16T23:25:00.8000592Z"}
terrad_1         | 11:25PM INF received complete proposal block hash=54D9C757E9AA84E0F5AAA736E6EED3D83F364A3A62FDC625970539CA81DFA86E height=5 module=consensus
terrad_1         | 11:25PM INF finalizing commit of block hash=54D9C757E9AA84E0F5AAA736E6EED3D83F364A3A62FDC625970539CA81DFA86E height=5 module=consensus num_txs=0 root=84C2F2EF6B7FC8B3ACED8B2B0D2921D649F13CE54C5AB5B032DE988D1392E0FD
terrad_1         | 11:25PM INF minted coins from module account amount=226569846uluna from=mint module=x/bank
terrad_1         | 11:25PM INF executed block height=5 module=state num_invalid_txs=0 num_valid_txs=0
terrad_1         | 11:25PM INF commit synced commit=436F6D6D697449447B5B32382031303020373220323137203234312038352031363320313520313530203137382031353820323235203133312032343620313538203235322031333420313238203134392031383220323033203131372039382031333420312035382032333720323120333620313534203136203134335D3A357D
terrad_1         | 11:25PM INF committed state app_hash=1C6448D9F155A30F96B29EE183F69EFC868095B6CB756286013AED15249A108F height=5 module=state num_txs=0
terrad_1         | 11:25PM INF indexed block height=5 module=txindex
terrad_1         | 11:25PM INF Ensure peers module=pex numDialing=0 numInPeers=0 numOutPeers=0 numToDial=10
terrad_1         | 11:25PM INF No addresses to dial. Falling back to seeds module=pex
terrad_1         | 11:25PM INF Timed out dur=4975.4085 height=6 module=consensus round=0 step=1
terrad_1         | 11:25PM INF received proposal module=consensus proposal={"Type":32,"block_id":{"hash":"5FE8526C43C0B32BEF011299D67FDA44DBD625E0B69836D175C25C1F914DD06E","parts":{"hash":"BE583EC25B30F52E652FA28DEAB869D98602B3FB82CD0D9C80ADF96A210CC8D4","total":1}},"height":6,"pol_round":-1,"round":0,"signature":"Bx3WaDl3hhR9IkDjXRa+dXkSIK0Tezl07gZhDm4RXyJyHq0oriAkQD23Q9+ly1+cFhGIdKF3hyvH3GcjCNLvAQ==","timestamp":"2021-12-16T23:25:05.823444Z"}

```

现在，我们连上了本地网络。以下是要连接的端口号:

### 安装铁锈

Rust 是 Terra 选择使用和编写智能合同的工具，因为 Rust 可以编译成 WebAssembly，而 WebAssembly 工具非常成熟，是为 Terra 构建的。

要在 MacOS 或任何类似 Linux 的操作系统上安装 Rust，请运行以下命令:

```
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

```

[如果您使用的是 Windows，请使用此链接](https://forge.rust-lang.org/infra/other-installation-methods.html)。

一旦成功安装，我们必须添加`wasm32-unknown-unknown`目标进行编译:

```
$ rustup target add wasm32-unknown-unknown

```

最后，让我们安装`cargo-generate`来搭建一个 CosmWasm 智能合同模板，并安装`cargo-run-script`来优化我们的智能合同:

```
$ cargo install cargo-generate --features vendored-openssl
$ cargo install cargo-run-script

```

我们终于完成了安装🙂！

## 在 Terra 中编写和探索智能合同

随着我们的 LocalTerra 网络运行并等待我们，我们准备好编写一个小的智能合同，部署它们，我们今天就完成了！

自从我们安装了`cargo-generate`，我们可以快速搭建一个工作项目。这将有助于我们用文件夹结构来写合同。为此，请使用以下命令:

```
$ cargo generate --git https://github.com/CosmWasm/cw-template.git --name PROJECT_NAME

```

对于`PROJECT_NAME`，你应该将其命名为你的项目名称。以下是运行前面的命令后应该得到的结果:

```
Vectormikes-MacBook-Pro:Projects macbookpro$ cargo generate --git https://github.com/CosmWasm/cw-template.git --name terra-demo
🔧   Generating template ...
[ 1/34]   Done: .cargo/config
[ 2/34]   Done: .cargo
[ 3/34]   Skipped: .circleci/config.yml
[ 4/34]   Done: .circleci
[ 1/34]   Done: .cargo/config
[ 2/34]   Done: .cargo
[ 3/34]   Skipped: .circleci/config.yml
[ 4/34]   Done: .circleci
[ 5/34]   Done: .editorconfig
[ 6/34]   Done: .github/workflows/Basic.yml
[ 7/34]   Done: .github/workflows
[ 8/34]   Done: .github
[ 9/34]   Done: .gitignore
[10/34]   Done: .gitpod.Dockerfile
[11/34]   Done: .gitpod.yml
[ 1/34]   Done: .cargo/config
[ 2/34]   Done: .cargo
[ 3/34]   Skipped: .circleci/config.yml
[ 4/34]   Done: .circleci
[ 5/34]   Done: .editorconfig
[ 6/34]   Done: .github/workflows/Basic.yml
[ 7/34]   Done: .github/workflows
[ 8/34]   Done: .github
[ 9/34]   Done: .gitignore
[10/34]   Done: .gitpod.Dockerfile
[11/34]   Done: .gitpod.yml
[12/34]   Done: Cargo.lock
[13/34]   Done: Cargo.toml
[14/34]   Done: Developing.md
[15/34]   Done: Importing.md
[16/34]   Done: LICENSE
[17/34]   Done: NOTICE
[18/34]   Done: Publishing.md
[19/34]   Done: README.md
[20/34]   Done: examples/schema.rs
[21/34]   Done: examples
[22/34]   Done: rustfmt.toml
[23/34]   Done: schema/count_response.json
[24/34]   Done: schema/execute_msg.json
[25/34]   Done: schema/instantiate_msg.json
[26/34]   Done: schema/query_msg.json
[27/34]   Done: schema/state.json
[28/34]   Done: schema
[29/34]   Done: src/contract.rs
[30/34]   Done: src/error.rs
[31/34]   Done: src/lib.rs
[32/34]   Done: src/msg.rs
[33/34]   Done: src/state.rs
[34/34]   Done: src
🔧   Moving generated files into: `/Users/macbookpro/Desktop/Projects/terra-demo`...
✨   Done! New project created /Users/macbookpro/Desktop/Projects/terra-demo

```

查看`src/msg.rs`文件，我们可以看到可以发送到智能合约的三种消息。首先，这包括`InstantiateMsg`，它设置智能合约中的状态，这意味着当智能合约启动时，必须给它一个初始状态。

第二，`ExecuteMsg`是对状态改变执行动作的消息，例如向区块链发布消息。最后，`QueryMsg`就像它听起来的那样:它作为一个对链的查询，从中获取数据。

让我们看看如何在代码中使用这些:

```
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub count: i32,
}
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Increment {},
    Reset { count: i32 },
}
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    // GetCount returns the current count as a json-encoded number
    GetCount {},
}
// We define a custom struct for each query response
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct CountResponse {
    pub count: i32,
}

```

在我们进入契约之前，让我们看看在`src/state.rs`文件中的`State`中有什么接口:

```
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};
use cosmwasm_std::Addr;
use cw_storage_plus::Item;
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct State {
    pub count: i32,
    pub owner: Addr,
}
pub const STATE: Item<State> = Item::new("state");

```

我们的结构`State`，应该包含一个`i32`的`count`和`Addr`的`owner`。根据 Terra 的说法，我们的状态是持久的，因为 Terra 的活动 [LevelDB 是一个键值存储](https://blog.logrocket.com/11-database-drivers-and-orms-for-rust-that-are-ready-for-production/#leveldbhttpskadegithubioleveldbleveldb)。

考虑到这一点，我们的合同放在`src/contract.rs`文件中:

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    _env: Env,
    info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {
    let state = State {
        count: msg.count,
        owner: info.sender.clone(),
    };
    set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;
    STATE.save(deps.storage, &state)?;
    Ok(Response::new()
        .add_attribute("method", "instantiate")
        .add_attribute("owner", info.sender)
        .add_attribute("count", msg.count.to_string()))
}

```

`instantiate`方法需要四个参数，`deps`、_ `env`、`info`和`msg`，以及它们的支持接口。然后，我们期待一个结果，要么是我们预期的`Response`要么是`ContractError`。

这里，我们在`src/error.rs`文件中定义了我们的`ContractError`:

```
use cosmwasm_std::StdError;
use thiserror::Error;
#[derive(Error, Debug)]
pub enum ContractError {
    #[error("{0}")]
    Std(#[from] StdError),
    #[error("Unauthorized")]
    Unauthorized {},
    // Add any other custom errors you like here.
    // Look at https://docs.rs/thiserror/1.0.21/thiserror/ for details.
}

```

其他一些接口如`Response`也是从`cosmwasm_std`中导入的:

```
#[cfg(not(feature = "library"))]
use cosmwasm_std::entry_point;
use cosmwasm_std::{to_binary, Binary, Deps, DepsMut, Env, MessageInfo, Response, StdResult};
use cw2::set_contract_version;

```

接下来，对于方法，我们的契约中也有`execute`和`query`:

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(
    deps: DepsMut,
    _env: Env,
    info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response, ContractError> {
    match msg {
        ExecuteMsg::Increment {} => try_increment(deps),
        ExecuteMsg::Reset { count } => try_reset(deps, info, count),
    }
}

pub fn try_increment(deps: DepsMut) -> Result<Response, ContractError> {
    STATE.update(deps.storage, |mut state| -> Result<_, ContractError> {
        state.count += 1;
        Ok(state)
    })?;
    Ok(Response::new().add_attribute("method", "try_increment"))
}
pub fn try_reset(deps: DepsMut, info: MessageInfo, count: i32) -> Result<Response, ContractError> {
    STATE.update(deps.storage, |mut state| -> Result<_, ContractError> {
        if info.sender != state.owner {
            return Err(ContractError::Unauthorized {});
        }
        state.count = count;
        Ok(state)
    })?;
    Ok(Response::new().add_attribute("method", "reset"))
}

```

这里，将计数状态增加`1`的`try_increment`和重置计数状态的`try_reset`是在`execute`功能中使用的功能。

最后，从存储器中为我们获取状态或信息的`query`计数可以在下面看到:

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::GetCount {} => to_binary(&query_count(deps)?),
    }
}
fn query_count(deps: Deps) -> StdResult<CountResponse> {
    let state = STATE.load(deps.storage)?;
    Ok(CountResponse { count: state.count })
}

```

## 将智能合同上传到 LocalTerra

我们现在可以构建我们的智能契约来检查编译期间的错误，以便我们可以修复它。为此，我们运行以下命令:

```
$ cargo wasm

```

正如我们安装`cargo-run-script`来帮助优化我们的构建一样，我们现在必须使用它:

```
$ cargo run-script optimize

```

在项目目录中，我们应该会看到`artifacts/terra_demo.wasm`中的代码，我们很快会将它上传到 LocalTerra。我们现在可以创建一个本地 testnet 名称和节点名字:

```
$ terrad init --chain-id=<testnet_name> <node_moniker>

```

这将提示您输入助记符:

```
 $ terrad keys add <account_name>

```

根据 Terra 的说法，具有以下助记符的帐户是网络上唯一的验证者:

```
satisfy adjust timber high purchase tuition stool faith fine install that you unaware feed domain license impose boss human eager hat rent enjoy dawn

```

接下来，我们可以将代码上传到 Terra 网络:

```
terrad tx wasm store artifacts/terra_demo.wasm --from demo --chain-id=localterra --gas=auto --fees=100000uluna --broadcast-mode=block

```

这将要求您键入“yes”以允许它推送至 LocalTerra。如果成功的话，你的合同现在会被广播到本地网络。

## 结论

我们刚刚开始触及 Terra 中智能合约的表面，这应该只是给出了如何在 Terra 协议上构建 dApp 的概述。

强烈建议基于此协议，因为其活跃的用户群不断增长，包括 LUNA 在内的 Terra stablecoins 正在被添加到支付解决方案中，这是一个好消息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

它还具有快速和高效的共识，在未来的版本中会有更高的效率。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。