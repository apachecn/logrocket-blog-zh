# 使用 Hyperledger Fabric 和 Go 进行 KYC 验证

> 原文：<https://blog.logrocket.com/streamline-kyc-verification-hyperledger-fabric-go/>

“了解您的客户”( KYC)流程是各行各业的公司用来识别和验证客户的解决方案，目的是验证他们是否享有特定的特权。KYC 验证是组织的一个痛点，因为它通常成本高昂、效率低下，并且是客户感到沮丧的一个原因。

在本文中，我们将展示如何使用 Hyperledger Fabric 区块链框架以及其他区块链解决方案来简化金融机构使用自主身份系统的客户验证流程。

Hyperledger Fabric 是反洗钱(AML)和 KYC 流程的良好解决方案，因为它获得了许可，以企业为中心，并且能够进行身份管理。

本文将涵盖以下内容:

## Hyperledger 项目概览

构建和部署 Hyperledger Fabric dApp 不需要对 Hyperledger 生态系统中的所有工具有广泛的了解。因此，出于本文的目的，我只提供了一个简单的介绍。

Hyperledger 项目是指 Linux 基金会与其他组织合作提供的越来越多的开源工具和框架。Hyperledger Fabric 项目提供了支持企业消费并由社区人才管理的许可私有区块链。

除了开源之外，Hyperledger 项目还具有以下特性:

*   由开放社区建造
*   建造和利用经济
*   轻松快速的采用
*   由于对项目技术变更的持续社区审查、更新和批准流程，因此安全可靠

在撰写本文时，有 17 个 [Hyperledger 项目](https://landscape.hyperledger.org/projects)——一些正在进行，另一些处于酝酿阶段。这些项目可分类如下:

*   分布式分类帐框架
*   图书馆
    *   白羊星座
    *   被子
    *   乌尔萨
    *   交易
*   工具
    *   卡钳
    *   大提琴
    *   探险家
    *   阿瓦隆
    *   斜角规
    *   萤火虫
*   特定领域的项目

在本文中，我们将重点关注 Hyperledger Fabric 分布式分类帐技术。使用 Hyperledger Fabric 构建的区块链应用程序由共识层、智能合同层、通信层和数据持久层组成。根据应用，也可以有附加层。

### 作为企业解决方案的 Hyperledger 结构区块链

Hyperledger 项目是在区块链提供企业解决方案的第一个项目系列，这意味着它们通过使用智能合同来自动化业务流程。区块链企业解决方案有几个使用案例。在本文中，我们将探讨公司和政府对客户凭证的数字化。

[Hyperledger Fabric 是 Hyperledger 生态系统中最广泛采用的项目](https://www.hyperledger.org/learn/blockchain-showcase)。这种私有的、经许可的基于企业的开源区块链技术支持使用用 Go、Node.js 和 Java 等语言编写的 chaincodes (Hyperledger 的智能合同术语)创建和管理多分类帐、企业级区块链解决方案。在本文中，我们将使用 Go 进行 KYC 验证过程的演示。

Hyperledger Fabric 技术提供了以下功能:

*   **身份管理:** Hyperledger Fabric 的[会员服务提供商(MSP)](https://hyperledger-fabric.readthedocs.io/en/release-2.2/msp.html) 为身份管理提供支持
*   **隐私和保密性:** Hyperledger Fabric 提供未经许可禁止同行或成员进入的私人通道。这使得渠道成员之间能够安全、私密地共享数据。
*   **效率:** Hyperledger Fabric 的并发和并行功能通过加快事务的线程化和处理速度来提高其性能
*   **链码执行:**链码是光纤网络上的业务逻辑。他们负责在链上执行事务。链码逻辑是用通用语言编写的，这使得开发人员可以用他们最熟悉的语言来构建链码
*   **治理:** Hyperledger Fabric 利用治理策略来识别可以部署链代码或向渠道添加 MSP 的对等方
*   **模块化架构:** Hyperledger Fabric 的模块化架构使其能够灵活应对变化。这可能是为什么这种区块链被如此广泛的行业(金融、银行、医疗保健、制造、技术、物联网、供应链)的公司采用的原因

## KYC 验证流程概述

“了解您的客户”验证是使用身份验证系统来验证客户(金融机构)或公民(政府)的身份和真实性的过程。

在银行业，KYC 验证流程的特点是在个人客户和打算合作的金融机构之间交换文档。该流程包括收集和存储客户的基本身份信息，例如:

*   身份证明(POI):护照、驾照、选民身份证或健康卡
*   地址证明(POA):银行对账单、公用事业账单、政府出具的居住证明或身份证
*   生物特征数据:面部照片或视频、指纹、录音、虹膜识别或视网膜扫描

对于金融机构来说，KYC 验证过程通常是一项繁琐、不安全且成本高昂的任务。这个过程非常依赖于身份和访问管理；所有金融机构反洗钱工作的支柱。

## 区块链如何帮助 KYC 核查？

区块链技术为简化 KYC 验证和打破传统身份管理解决方案提供了多种机会:

*   **增加安全性**:将客户身份文件作为加密文件存储在链上比传统方法更安全
*   **提高技术效率**:负责管理数字证书的 KYC 智能合同将具备 CRUD(创建、读取、更新和删除)能力
*   **不变性:**加密散列使区块链分类账成为永久记录，不能被更改或撤销
*   **增加隐私:**使用区块链，金融机构只验证用户，无权访问凭证文档中包含的敏感信息
*   **提高准确性:**合同代码代替人工管理流程
*   **增加透明度**:金融机构之间可以安全地共享验证信息
*   **降低成本:**一旦客户开立账户并向一家金融机构提供凭据，智能合同就会对包含凭据的文档进行归档和哈希处理。然后，其他机构可以访问该智能合同，以验证客户的身份

在许可的区块链(如 Hyperledger Fabric)上简化 KYC 验证流程的一个重要好处是，只有授权和许可的链成员才能添加或交换客户数据。

这与无许可的公共区块链(如以太坊)有很大的不同，在以太坊中，网络上的任何成员都可以建立一个节点，只需一个以太坊地址就可以加入系统，无需证明身份。

## 使用 Hyperledger Fabric 和 Go 的 KYC 验证工作流演示

让我们使用 Hyperledger Fabric 区块链完成三步 KYC 验证流程。

### 先决条件

对于我们的 KYC 验证工作流程演示，您需要以下内容:

*   安装在您的机器上
*   一个代码编辑器，比如 VS 代码

### 步骤 1:加密身份凭证

第一步，收集客户的身份凭证作为文档，并使用区块链散列算法进行加密。文档的加密值存储在链上，并且生成令牌并交给客户保管。当客户在未来被验证时，他们将提供这个令牌，以便它可以与存储在链上的加密值进行比较。

让我们假设客户的详细信息保存在一个`credential.csv`文件中，如下所示:

```
Name,ID,Phone
John, 10121,199823458

```

Hyperledger Fabric chaincode 负责加密文档。它的逻辑中有一个哈希算法，如下所示:

```
package main

import (
    "crypto/sha256"
    "fmt"
    "io/ioutil"
    "log"
    "os"
)

func showFileToCustomer() {
    fmt.Println("Your document is: " + os.Args[0] + " credential.csv")
}

func checkArgs() string {
    if len(os.Args) < 2 {
        showFileToCustomer()
        os.Exit(1)
    }
    return os.Args[1]
}

func main() {
    credential := checkArgs()
    // Get bytes from the credential document
    data, err := ioutil.ReadFile(credential)
    if err != nil {
        log.Fatal(err)
    }

    // encrypt the file and display to the customer
    fmt.Printf("The encrypted file is: %x\n\n", sha256.Sum256(data))
}

```

如果我们将这个文件保存在与`credential.csv`文件相同的目录中，然后运行它，结果输出将表明它已经被散列。

### 步骤 2:在链上存储加密的凭据

接下来，客户的`credential.csv`文件的加密版本将被转换成字符串。然后，它将被存储在区块链上，在没有链上的管理对等体的许可的情况下，它不能被编辑。

证书的加密版本充当被验证文档的真实性的收据。新的金融机构将需要请求客户的许可来查看通过链码存储在链上的收据。该请求将使用类似如下的逻辑:

```
package shim

import (
    "bytes"
    "os"
    "strconv"
    "strings"
    "testing"
    "github.com/hyperledger/fabric/common/flogging"
    mockpeer "github.com/hyperledger/fabric/common/mocks/peer"
    "github.com/hyperledger/fabric/common/util"
    lproto "github.com/hyperledger/fabric/protos/ledger/queryresult"
    protobuf "github.com/hyperledger/fabric/protos/peer"
    "github.com/hyperledger/fabric/protos/utils"
)

err = stub.PutState(A, []byte(strconv.Itoa(data)))
if err != nil {
    return Error(err.Error())
    }

```

### 步骤 3:检索和查询身份凭据

客户通过提供他们的散列 ID 用于交叉检查或查询来同意验证请求。这由链码使用如下逻辑进行检索和查询:

```
func (t *shimTestCC) logsQ(stub ChaincodeStubInterface, args []string) protobuf.Response {
    if len(args) < 1 {
        return Error("Incomplete args. Needs 1")
    }

    credentials := args[0]
    iterateResult, err := stub.GetKeyHistory(id)

    if err != nil {
        return Error(err.Error())
    }

    defer iterateResult.Close()
    var buffer bytes.Buffer
    buffer.WriteString("[")
    WriteArrayMem := false
    for resultsIterator.HasNext() {
        response, err := resultsIterator.Next()
        if err != nil {
            return Error(err.Error())
        }

        // Add a comma before array members, suppress it for the first array member
        if WriteArrayMem == true {
            buffer.WriteString(",")
        }

        buffer.WriteString("{\"TransactionId\":")
        buffer.WriteString("\"")
        buffer.WriteString(response.TransactionId)
        buffer.WriteString("\"")
        buffer.WriteString(", \"Value\":")

        if response.IsDelete {
            buffer.WriteString("null")
        } else {
            buffer.WriteString(string(response.Value))
        }

        buffer.WriteString(", \"IsDelete\":")
        buffer.WriteString("\"")
        buffer.WriteString(strconv.FormatBool(response.IsDelete))
        buffer.WriteString("\"")
        buffer.WriteString("}")
        bArrayMemberAlreadyWritten = true
    }

    buffer.WriteString("]")
    return Success(buffer.Bytes())
}

```

一旦有匹配，KYC 验证过程就完成了。

出于安全目的，链码监控整个过程并记录金融机构的名称。

## 结论

在本文中，我们回顾了 Hyperledger 项目工具和框架的介绍。我们还探索了超分类帐结构区块链和 KYC 进程。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们演示了如何使用 Hyperledger Fabric 区块链框架和 Go 编程语言将 KYC 验证简化为三步流程:加密、存储和查询/验证。

Hyperledger Fabric 有几种不同的存储和查询算法实现，比如 [GenesisKYC](https://github.com/bairathirahul/GenesisKYC) 。当您练习编写 Hyperledger Fabric chaincode 时，您应该能够重新构建代码以满足您的特定需求。有关 Hyperledger Fabric 的更多信息，请参见 [GitHub](https://github.com/hyperledger/fabric) 。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。