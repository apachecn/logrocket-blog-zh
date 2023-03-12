# 如何在 Corda - LogRocket 博客上写 DApps

> 原文：<https://blog.logrocket.com/how-to-write-dapps-corda/>

比特币于 2009 年 1 月 3 日推出，推动了数字资产和数字货币等趋势。从那以后，更多的区块链像以太坊和索拉纳被创造出来。尽管有不同的功能和使用案例，这些区块链有一个共同点:它们被设计为在没有监管机构的情况下民主地运行。因此，这种模式不适合受监管的行业，在这些行业中，数据必须保密，并在受信任的各方之间共享。因为这个原因，私人区块链存在。

私有区块链是一个许可的区块链，包含称为网络运营商的实体。这些节点控制网络，并能够配置权限和访问其他节点的控件。为了维护隐私和信任，只有参与交易的实体才知道它。

利用[私有区块链的数字平台的几个例子包括 Hyperledger Fabric](https://blog.logrocket.com/hyperledger-fabric-develop-blockchain-smart-contracts/) 、Ripple 和 [R3 的 Corda](https://www.r3.com/trust-technology/) 。在本文中，我们将探索 Corda，学习如何创建 CorDapps。我们开始吧！

## Corda 简介

Corda 是一种经许可的点对点(P2P)分布式账本技术(DLT ),有助于在受监管的市场中开发应用。有了 Corda，各方可以在一个单一、开放的网络中自由地相互发现和交易，同时对网络参与者的身份具有信心。

从长远来看，Corda 希望成为一个共享的全球分布式分类帐。为此，许多使用 Corda 软件的解决方案必须遵守一套通用标准和准则。其中一些标准通常被称为最终状态原则，具体如下:

*   **确定身份**:各方对网络参与者的身份有信心。Corda 中的身份由相关机构颁发的证书代表
*   **隐私**:只有参与交易的人和需要核实交易来源的人才有权访问交易信息
*   互操作性:Corda 旨在允许众多应用程序在同一网络上共存和互操作；提供了一组定义好的契约接口，以优化各种提供者的互操作性

## CorDapps 的关键组件

CorDapp 是 Corda 分布式应用程序的简称。CorDapps 是在 Corda 节点上运行的分布式应用程序。以下是 CorDapp 的主要组成部分:

*   **状态**:状态跟踪事务之间的数据，并且是不可变的，这意味着它们一旦形成就不能被改变。任何修改都必须导致产生一个新的继承国
*   **契约**:契约定义了将应用于事务输入和输出的验证标准。契约保证事务状态是有效的，并且避免无效的事务。一个 CorDapp 中可能存在一个或多个契约，每个契约为一个或多个状态提供规则
*   **流**:流是你的 CorDapp 可能在网络上进行的活动，它们构成了你的 CorDapp 的业务逻辑。流程允许各方在不使用中央控制器的情况下协调他们的操作
*   **交易**:交易是更新分类账的请求。它消耗当前的输入状态并输出新的状态来更新分类帐。在交易被节点接受之前，交易必须是唯一的、有效的，并且由适当的一方签名
*   **公证人**:公证人是一种 Corda 网络服务，有助于防止网络资产的重复支出。它通过保证每个事务都包括前一个事务未使用的唯一输入状态来实现这一点。公证人签字后，交易即被视为完成
*   **共识**:在 Corda 中，你可以通过证明一个交易既有效又独特来建立共识。共识是一种允许节点就网络的当前状态达成一致的方法。在提议的交易被记入分类账之前，双方必须同意它是合法的

## Corda 入门

我们将创建一个 CorDapp 来模拟 Corda 区块链上的代币发行。我们的 CorDapp 将跟踪代币的发行者、持有者和发行的数量。

CorDapp 开发需要四个软件:

*   Java 8 JDK
*   智能理念
*   饭桶
*   Gradle，5.1 到 5.6.4 之间的任何版本

### 安装

要设置我们的 CorDapp，首先，[从他们的 GitHub repo 中克隆 Corda Java 模板](https://github.com/corda/cordapp-template-java)。在您选择的任何 IDE 中打开`cordapp-template-java`。我将使用 IntelliJ IDE。

### 创建国家

重申一下，状态是不可变的，并且在事务之间跟踪数据。我们的 CorDapp 将有一组属性，我们将把它们存储在我们的状态中，比如`issuer`、`holder`和`amount`。

导航到`/contracts/src/main/java/com/template/states`并创建一个名为`TokenState`的新 Java 类:

```
package com.template.states

import com.template.contracts.TokenContract;
import net.corda.core.identity.AbstractParty;
import net.corda.core.contracts.BelongsToContract;
import net.corda.core.contracts.ContractState;
import net.corda.core.identity.Party;
import org.jetbrains.annotations.NotNull;
import java.util.Arrays;
import java.util.List;

@BelongsToContract(TokenContract.class)
public class TokenState implements ContractState {
    private Party issuer;
    private Party holder;
    private int amount;

    public TokenState(Party issuer, Party holder, int amount){
        this.issuer = issuer;
        this.holder = holder;
        this.amount = amount;
    }

    public Party getHolder() {
        return holder;
    }

    public Party getIssuer() {
        return issuer;
    }

    public int getAmount() {
        return amount;
    }

    @NotNull
    @Override
    public List<AbstractParty> getParticipants() {
        return Arrays.asList(issuer, holder);
    }
}

```

在上面的代码中，我们创建了一个名为`TokenState`的类，它继承了`ContractState`类。`ContractState`类告诉 Corda 我们正在实现一个状态。

接下来，我们添加了`@BelongsToContract`注释，它建立了状态和契约之间的关系。没有这个，你所在的州就不知道用哪个契约来验证。添加这个注释会在 IntelliJ 中触发一个错误，因为我们还没有创建我们的`TokenContract`。

然后，我们创建三个属性，`issuer`、`holder`和`amount`。`issuer`和`holder`属性被赋予一种类型`Party`，因为它们都表示节点上的实体。

接下来，我们为每个属性创建三个 getter 方法。最后，我们创建了一个`getParticipants`方法，它定义了哪一方应该知道这个事务。在我们的例子中，我们只希望`issuer`和`holder`知道这个事务。

### 创建合同

重申一下，契约定义了国家如何发展的规则。他们在交易成功之前进行某些验证。因此，每个州都与一个合同相关联。

导航到`/contracts/src/main/java/com/template/contracts`并创建一个名为`TokenContract`的新 Java 类:

```
package com.template.contracts

import net.corda.core.contracts.CommandData;
import net.corda.core.contracts.Contract;
import net.corda.core.transactions.LedgerTransaction;

import org.jetbrains.annotations.NotNull;
import com.template.states.TokenState;

public class TokenContract implements Contract {
    public static final String ID = "contracts.TokenContract";

    @Override
    public void verify(@NotNull LedgerTransaction tx) {

        if(tx.getCommands().size() != 1) {
            throw new IllegalArgumentException("expects only one command: ISSUE");
        }
        if(tx.getCommand(0).getValue() instanceof Commands.Issue){
            throw new IllegalArgumentException("issue command expected");
        }
        TokenState state = (TokenState) tx.getOutput(0);
        int amountIssued = state.getAmount();
        if (amountIssued <= 0){
            throw new IllegalArgumentException("amount must be greater than zero");
        }
        if(! (tx.getCommand(0).getSigners().contains(state.getIssuer().getOwningKey()))){
            throw new IllegalArgumentException("transaction must be signed by issuer");
        }
    }
    // Used to indicate the transaction's intent.
    public interface Commands extends CommandData {
        class Issue implements Commands {}
    }
}

```

在上面的代码中，我们创建了一个名为`TokenContract`的类，它继承了`Contract`类。`Contract`类告诉 Corda 我们正在实现一个契约。

首先，我们创建一个名为`ID`的属性，它将在测试环境中构建我们的事务时标识我们的契约。`ID`属性完全是可选的。

我们继承的`Contract`类给我们的方法之一是`verify`方法，我们必须覆盖它。`verify`方法将事务作为输入，并根据定义的规则对它们进行评估。如果`verify`方法不抛出异常，则事务有效。

使用上面的代码，我们的契约执行以下检查:

*   交易必须由发行人签名
*   金额必须大于零
*   只能使用`issue`命令
*   零初始输入状态，因为它是一个发行

最后，因为我们打算将令牌发给另一方，所以我们创建了一个名为`Issue`的类，它继承了`Command`类。`Command`类用于指示正在执行的动作的类型。

### 编写启动流程

重申一下，流包含了我们 CorDapp 的业务逻辑。发起者流由发起事务的节点运行，在我们的例子中是`issuer`。

导航到`workflows/src/main/java/com/template/flows`并创建一个名为`FlowInitiator`的新 Java 类:

```
package com.template.flows;

import co.paralleluniverse.fibers.Suspendable;
import com.bootcamp.contracts.TokenContract;
import com.bootcamp.states.TokenState;
import net.corda.core.flows.*;
import net.corda.core.identity.CordaX500Name;
import net.corda.core.identity.Party;
import net.corda.core.transactions.SignedTransaction;
import net.corda.core.transactions.TransactionBuilder;
import net.corda.core.utilities.ProgressTracker;
import net.corda.core.contracts.CommandData;

import static java.util.Collections.singletonList;

    @InitiatingFlow
    @StartableByRPC
    public static class TokenFlowInitiator extends FlowLogic<SignedTransaction> {
        private final Party owner;
        private final int amount;

        public TokenFlowInitiator(Party owner, int amount) {
            this.owner = owner;
            this.amount = amount;
        }

        private final ProgressTracker progressTracker = new ProgressTracker();

        @Override
        public ProgressTracker getProgressTracker() {
            return progressTracker;
        }

        @Suspendable
        @Override
        public SignedTransaction call() throws FlowException {

            /** Explicit selection of notary by CordaX500Name - argument can by coded in flows or parsed from config (Preferred)*/
            final Party notary = getServiceHub().getNetworkMapCache().getNotary(CordaX500Name.parse("O=Notary,L=London,C=GB"));
            // We get a reference to our own identity.
            Party issuer = getOurIdentity();

            /* ============================================================================
             *         TODO 1 - Create our TokenState to represent on-ledger tokens!
             * ===========================================================================*/
            // We create our new TokenState.
            TokenState tokenState = new TokenState(issuer, owner, amount);

            /* ============================================================================
             *      TODO 3 - Build our token issuance transaction to update the ledger!
             * ===========================================================================*/
            // We build our transaction.
            TransactionBuilder transactionBuilder = new TransactionBuilder.setNotary(notary).addOutputState(tokenState).addCommand(new TokenContract.Commands.Issue(), Arrays.asList(issuer.getOwningKey(), owner.getOwningKey()));

            /* ============================================================================
             *          TODO 2 - Write our TokenContract to control token issuance!
             * ===========================================================================*/
            // We check our transaction is valid based on its contracts.
            transactionBuilder.verify(getServiceHub());

            FlowSession session = initiateFlow(owner);

            // We sign the transaction with our private key, making it immutable.
            SignedTransaction signedTransaction = getServiceHub().signInitialTransaction(transactionBuilder);

            // The counterparty signs the transaction
            SignedTransaction fullySignedTransaction = subFlow(new CollectSignaturesFlow(signedTransaction, singletonList(session)));

            // We get the transaction notarised and recorded automatically by the platform.
            return subFlow(new FinalityFlow(fullySignedTransaction, singletonList(session)));
        }
    }

```

在上面的代码中，我们创建了一个名为`TokenFlowInitiator`的类，它继承了`FlowLogic`类。`FlowLogic`类告诉 Corda 我们正在创建一个流。

然后，我们添加两个注释，`@InitiatingFlow`和`@StartableByRPC`。`@InitiatingFlow`注释表明这个流是启动流。另一方面，`@StartableByRPC`注释允许 RPC 启动流。

接下来，我们创建一个名为`progressTracker`的变量，它检查流的每个阶段，并在代码中到达每个检查点时输出指定的消息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，我们创建了`call`方法，当流程开始时，Corda 会调用这个方法。在`call`方法中，我们首先创建一个公证人，并将其存储在一个名为`notary`的变量中。由于我们与多方打交道，我们需要公证服务来达成各方之间的共识。

我们获取自己的身份，并将其存储在一个名为`issuer`的变量中。接下来，我们通过创建一个新的`TokenState`实例来创建令牌，并将`issuer`、`owner`和`amount`作为参数传递。

然后，我们通过创建一个新的`TransactionBuilder`实例来构建我们的事务提议。我们链接不同的方法来设置公证人、添加命令和签署交易。

最后，我们使用`InitiateFlow`方法开始与交易对手的`FlowSession`。这个过程使我们能够将状态发送给交易对手。然后我们调用`CollectSignaturesFlow`子流来收集签名。

### 编写响应器流

响应方流程由交易方运行。它接收并记录交易，然后如果交易成功，通过发回确认来响应发行者的流程。

导航到`workflows/src/main/java/com/template/flows`并创建一个名为`TokenFlowResponder`的新 Java 类:

```
package com.template.flows;

import co.paralleluniverse.fibers.Suspendable;
import net.corda.core.flows.*;
import net.corda.core.identity.Party;
import net.corda.core.transactions.SignedTransaction;

@InitiatedBy(TokenFlowInitiator.class)
    public static class TokenFlowResponder extends FlowLogic<Void>{
        //private variable
        private FlowSession counterpartySession;

        //Constructor
        public TokenFlowResponder(FlowSession counterpartySession) {
            this.counterpartySession = counterpartySession;
        }

        @Suspendable
        @Override
        public Void call() throws FlowException {
            SignedTransaction signedTransaction = subFlow(new SignTransactionFlow(counterpartySession) {});

            //Stored the transaction into data base.
            subFlow(new ReceiveFinalityFlow(counterpartySession, signedTransaction.getId()));
            return null;
        }
    }

```

在上面的代码中，我们创建了一个名为`TokenFlowResponder`的类，它继承了`FlowLogic`类。然后我们添加了`@InitiatedBy`注释，并将`TokenFlowInitiator`类作为参数传递，告诉 Corda 是谁发起了这个流。

然后，我们创建带有`subFlow`的`call`方法，该方法将验证事务及其从流发起者处接收到的签名。可选地，为了安全起见，我们可以按照惯例创建一个名为`checkTransaction`的新方法来执行一系列测试。

## 运行我们的 CorDapp

要启动我们的 CorDapp，我们必须首先通过导航到项目的根目录并运行以下命令来部署它:

```
#Mac or Linux
./gradlew clean deployNodes

#Windows
gradlew.bat clean deployNodes

```

如果我们的 CorDapp 构建成功，它将生成三个安装了 CorDapp 的节点。这些可以在`build/nodes`文件夹中找到。

### 启动示例 CorDapp

要启动节点和我们的 CorDapp，请从我们的根目录运行以下命令:

```
#Mac or Linux
./build/nodes/runnodes

#Windows
.\build\nodes\runnodes.bat

```

上面的代码将为每个节点启动一个新的终端窗口。给每个终端一些启动时间，一旦节点准备就绪，您将在终端上收到一条欢迎消息。

## 与我们的 CorDapp 互动

为了检查我们的 CorDapp 是否成功工作，我们可以通过运行以下命令来尝试启动一个流:

```
flow start TokenFlowInitiator owner: PartyB, amount: 100

```

如果成功，您将收到一条确认消息。

## 结论

区块链是一种改变游戏规则的技术，由于系统使用私有区块链，受监管的企业不会被排除在这种变化之外。像 Corda 这样的区块链项目为企业提供了所需的灵活性，同时还保持了数据的私密性。在本文中，我们探索了如何使用 Corda，学习如何制作 CorDapps。

我希望你喜欢这个教程，如果你有任何问题，请随时发表评论。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。