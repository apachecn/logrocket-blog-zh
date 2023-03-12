# 使用 Cobra 构建 CLI 会计应用程序- LogRocket Blog

> 原文：<https://blog.logrocket.com/using-cobra-build-cli-accounting-app/>

当开发人员不在他们的 IDE 文本编辑器中时，他们通常在终端中。作为一名开发人员，您很有可能在项目中使用过命令行界面(CLI)。

大多数开发人员工具运行在命令行上的主要原因是:易于配置。CLI 应用程序提供了在图形用户界面(GUI)应用程序中不容易找到的自由度。

Cobra 是一个用于构建 CLI 应用程序的 Go 库。它非常受欢迎，并在许多流行的开发工具中使用，如 Github CLI、Hugo 等等。

在本教程中，我们将通过构建一个简单的会计 CLI 应用程序来了解 Cobra，该应用程序为用户开账单，将信息存储在 JSON 文件中，记录收据，并跟踪用户的总余额。

## 安装 Cobra

创建 Cobra 应用程序有两种方法:

1.  [安装眼镜蛇发电机](https://github.com/spf13/cobra/tree/master/cobra)
2.  手动将 Cobra 添加到[Go 应用程序](https://blog.logrocket.com/tag/go/)

在本教程中，我们将安装 Cobra 生成器。这提供了一种简单的方法来生成赋予应用程序生命力的命令。

首先，运行以下命令来安装 Cobra 生成器:

```
go get github.com/spf13/cobra/cobra

```

这将在`GOPATH`目录中安装 Cobra，然后生成 Cobra 应用程序。

## 了解 Cobra CLI 命令和标志

在开始构建我们的应用程序之前，我们必须了解 CLI 应用程序的主要组件。

当使用 Git 克隆一个项目时，我们通常运行以下命令:

```
git clone <url.to.project>

```

这包括:

*   `git`，应用名称
*   `clone`，命令
*   `url.to.project`，参数传递给命令和我们想要的项目`git`给`clone`

CLI 应用程序通常由应用程序的名称、命令、标志和参数组成。

考虑这个例子:

```
npm install --save-dev nodemon

```

这里，`npm`是正在运行的应用程序，`install`是命令。`--save-dev`是传递给`install`命令的标志，而`nodemon`是传递给命令的参数。

Cobra 允许我们非常容易地创建命令和添加标志。对于我们的应用程序，我们将创建两个命令:`credit`和`debit` **。**并且，使用各种标志，我们可以指定诸如进行交易的用户、交易的金额和交易的叙述等项目。

## 创建 Cobra 应用

要创建新的 Cobra 应用程序，请运行以下命令:

```
cobra init --pkg-name github.com/<username>/accountant accountant

```

该命令创建一个新文件夹`accountant`，并创建一个`main.go`文件、`LICENSE`文件和一个带有`root.go`文件的`cmd`文件夹。

注意，这个命令不会创建一个`go.mod`文件，所以我们必须自己初始化`go`模块:

```
go mod init github.com/<username>/accountant
go mod tidy

```

我们现在可以像运行任何普通的 Go 应用程序一样运行它:

```
go run .

```

但是，我们也可以通过以下方式构建应用程序:

```
go build .

```

并通过以下方式运行应用程序:

```
./accountant

```

## Cobra 应用入口点

我们的 Cobra 应用程序的入口点是`main.go`，保持主包精简是很重要的，这样我们就可以将应用程序的不同方面分开。查看 Cobra 生成的`main.go`文件，我们发现 main 函数只有一个功能:执行 root 命令:

```
cmd.Execute()

```

根命令`cmd/root.go,`包含以下内容:

*   `rootCmd`结构，它是`cobraCommand`的一种类型
*   在`main.go`中调用的`Execute`函数
*   `init`函数，初始化配置并设置根标志
*   `initConfig`功能，初始化任何设定的配置

目前，运行该应用程序包含大量 Cobra 生成的文本。让我们通过将`cmd\root.go`修改为以下内容来改变这一点，这样我们就可以解释我们的应用程序的用途了:

```
package cmd
import (
  "github.com/spf13/cobra"
)
// rootCmd represents the base command when called without any subcommands
var rootCmd = &cobra.Command{
  Use:   "accountant",
  Short: "An application that helps manage accounts of users",
  Long: `
This is a CLI that enables users to manage their accounts.
You would be able to add credit transactions and debit transactions to various users.
  `,
  // Uncomment the following line if your bare application
  // has an action associated with it:
  // Run: func(cmd *cobra.Command, args []string) { },
}
// Execute adds all child commands to the root command and sets flags appropriately.
// This is called by main.main(). It only needs to happen once to the rootCmd.
func Execute() {
  cobra.CheckErr(rootCmd.Execute())
}

```

运行应用程序现在应该会给出以下响应:

```
This is a CLI that enables users to manage their accounts.
You would be able to add credit transactions and debit transactions to various users.

Usage:
  accountant [command]

```

这里，我们删除了 Cobra 生成的`init`和`initConfig`函数。这是因为我们不需要这个应用程序的任何环境变量，并且 root 命令没有做太多事情。相反，应用程序的所有功能都是通过特定的命令来实现的。

## 在 Cobra 中创建命令

我们的应用程序应该能够处理两个主要特性:借记和贷记用户。因此，我们必须创建两个命令:`debit`和`credit`。

运行以下命令来生成这些命令:

```
cobra add credit
cobra add debit

```

这将在`/cmd`目录中创建两个新文件:`debit.go`和`credit.go`。

检查完新创建的文件后，将以下内容添加到`init`功能中:

```
rootCmd.AddCommand(debitCmd)

```

这行代码将新创建的命令添加到根命令中；现在，应用程序知道了新命令。

要运行`debitCmd`命令，我们必须通过`go build .`构建应用程序，并像这样运行应用程序:

```
./accountant debit

```

## 添加 JSON 存储层

对于这个应用程序，我们将使用一个非常简单的存储层。在这种情况下，我们将[将我们的数据存储在一个 JSON 文件](https://blog.logrocket.com/working-with-json-in-go/)中，并通过一个`go`模块访问它们。

在根目录下，创建一个`database`文件夹，然后创建一个`db.go`文件和一个`db.json`文件。

将以下内容添加到`db.go`以与数据库交互:

```
package database
import (
  "encoding/json"
  "os"
  "strings"
)
type User struct {
  Username     string        `json:"username"`
  Balance      int64         `json:"balance"`
  Transactions []Transaction `json:"transactions"`
}
type Transaction struct {
  Amount    int64  `json:"amount"`
  Type      string `json:"string"`
  Narration string `json:"narration"`
}
func getUsers() ([]User, error) {
  data, err := os.ReadFile("database/db.json")
  var users []User
  if err == nil {
    json.Unmarshal(data, &users)
  }
  return users, err
}
func updateDB(data []User) {
  bytes, err := json.Marshal(data)
  if err == nil {
    os.WriteFile("database/db.json", bytes, 0644)
  } else {
    panic(err)
  }
}
func FindUser(username string) (*User, error) {
  users, err := getUsers()
  if err == nil {
    for index := 0; index < len(users); index++ {
      user := users[index]
      if strings.EqualFold(user.Username, username) {
        return &user, nil
      }
    }
  }
  return nil, err
}
func FindOrCreateUser(username string) (*User, error) {
  user, err := FindUser(username)
  if user == nil {
    var newUser User
    newUser.Username = strings.ToLower(username)
    newUser.Balance = 0
    newUser.Transactions = []Transaction{}
    users, err := getUsers()
    if err == nil {
      users = append(users, newUser)
      updateDB(users)
    }
    return &newUser, err
  }
  return user, err
}
func UpdateUser(user *User) {
  // Update the json with this modified user information
  users, err := getUsers()
  if err == nil {
    for index := 0; index < len(users); index++ {
      if strings.EqualFold(users[index].Username, user.Username) {
        // Update the user details
        users[index] = *user
      }
    }
    // update database
    updateDB(users)
  }
}

```

这里我们定义两个结构:`User`和`Transaction`。`User`结构定义了如何存储和访问用户信息，如`username`、`balance`和`transactions`。`Transaction`结构存储事务，包括`amount`、`type`和`narration`。

我们还有两个写入数据库的函数。`getUsers`加载数据库文件并返回存储的用户数据，同时`updateDB`将更新的数据写入数据库。

这些函数是这个包的私有函数，需要公共函数来与命令交互。

`FindUser`在数据库中找到一个有用户名的用户，并返回该用户。如果没有找到用户，则返回`nil`。`FindOrCreateUser`检查是否有用户名的用户并返回；如果没有用户，它将使用该用户名创建一个新用户并返回它。

`UpdateUser`接收用户数据并更新数据库中的相应条目。

这三个函数被导出，以便在命令中对用户进行贷记和借记时使用。

## 使用 Cobra 实施信用交易

用以下内容修改`credit`命令，为该命令创建一个适当的描述，并在详细描述中添加一个用法部分:

```
// cmd/credit.go
var creditCmd = &cobra.Command{
  Use:   "credit",
  Short: "Create a credit transaction",
  Long: `
This command creates a credit transaction for a particular user.
Usage: accountant credit <username> --amount=<amount> --narration=<narration>.`,
  Run: func(cmd *cobra.Command, args []string) {
  },
}

```

当用户试图获取该命令的帮助时，会出现详细描述。

![Long Description Appears In Terminal When Users Need Help](img/0563b18ebfe01a25581cfe1fb7ab3341.png)

接下来，我们必须为`credit`命令添加必要的标志:`amount`和`narration`。

在`creditCmd`定义后添加以下内容:

```
var creditNarration string
var creditAmount int64

func init() {
  rootCmd.AddCommand(creditCmd)
  creditCmd.Flags().StringVarP(&creditNarration, "narration", "n", "", "Narration for this credit transaction")
  creditCmd.Flags().Int64VarP(&creditAmount, "amount", "a", 0, "Amount to be credited")
  creditCmd.MarkFlagRequired("narration")
  creditCmd.MarkFlagRequired("amount")
}

```

在`init`方法中，我们通过`rootCmd.AddCommand`将`creditCmd`命令附加到`root`命令上。

接下来，我们必须使用`StringVarP`方法创建一个字符串标志`narration`。该方法接收五个参数:

*   指向存储标志值的变量的指针
*   旗帜的名称
*   旗帜的简称
*   标志的默认值
*   当用户通过`--help`标志寻求帮助时，会提供一条帮助消息

同样，我们必须通过`Int64VarP`方法创建一个新的标志`amount`。这个方法类似于`StringVarP`，但是创建了一个 64 位整数标志。

之后，我们必须根据需要设置两个标志。通过这样做，只要在没有这些标志的情况下调用命令，Cobra 就会输出一个错误，指出这些标志是必需的。

完成 credit 命令后，我们使用数据库函数创建事务并将它们添加到用户中。

为此，修改`run`函数，如下所示:

```
var creditCmd = &cobra.Command{
  ...
  Run: func(cmd *cobra.Command, args []string) {
    if len(args) < 1 {
      log.Fatal("Username not specified")
    }
    username := args[0]
    user, err := database.FindOrCreateUser(username)
    if err != nil {
      log.Fatal(err)
    }
    user.Balance = user.Balance + creditAmount
    creditTransaction := database.Transaction{Amount: creditAmount, Type: "credit", Narration: creditNarration}
    user.Transactions = append(user.Transactions, creditTransaction)
    database.UpdateUser(user)
    fmt.Println("Transaction created successfully")
  },
}

```

`run`函数是命令中最重要的部分，因为它处理命令的主要动作。

因此，我们希望该命令具有以下签名:

```
./accountant credit <username> --amount=<amount> --narration<narration>

```

这里发送给命令的参数是`username`，更确切地说，是`args`数组中的第一项。这确保了至少有一个参数传递给该命令。

得到用户名后，我们可以使用数据库包中的`FindOrCreateUser`方法，用那个用户名得到对应的用户信息。

如果操作成功，我们将增加用户的余额，并添加一个包含金额和叙述的新交易。然后，我们用新的用户数据更新数据库。

将所有内容放在一起，credit 命令应该如下所示:

```
package cmd
import (
  "fmt"
  "log"
  "github.com/jameesjohn/accountant/database"
  "github.com/spf13/cobra"
)
// creditCmd represents the credit command
var creditCmd = &cobra.Command{
  Use:   "credit",
  Short: "Create a credit transaction",
  Long: `
This command creates a credit transaction for a particular user.
Usage: accountant credit <username> --amount=<amount> --narration=<narration>.`,
  Run: func(cmd *cobra.Command, args []string) {
    if len(args) < 1 {
      log.Fatal("Username not specified")
    }
    username := args[0]
    user, err := database.FindOrCreateUser(username)
    if err != nil {
      log.Fatal(err)
    }
    user.Balance = user.Balance + creditAmount
    creditTransaction := database.Transaction{Amount: creditAmount, Type: "credit", Narration: creditNarration}
    user.Transactions = append(user.Transactions, creditTransaction)
    database.UpdateUser(user)
    fmt.Println("Transaction created successfully")
  },
}
var creditNarration string
var creditAmount int64
func init() {
  rootCmd.AddCommand(creditCmd)
  creditCmd.Flags().StringVarP(&creditNarration, "narration", "n", "", "Narration for this credit transaction")
  creditCmd.Flags().Int64VarP(&creditAmount, "amount", "a", 0, "Amount to be credited")
  creditCmd.MarkFlagRequired("narration")
  creditCmd.MarkFlagRequired("amount")
}

```

这样，我们就成功地实现了`credit`命令。

## 使用 Cobra 实施借记交易

`debit`命令看起来类似于`credit`命令。唯一的区别是`run`功能。`Debit`减少用户余额，而`credit`增加用户余额。

`debit`命令应该如下所示:

```
./accountant debit <username> --amount=<amount> --narration=<narration>

```

`debit`的`run`函数的不同之处在于检查用户的余额是否大于借记的金额；我们不希望数据库中出现负余额。

为此，修改`debit.go`如下所示:

```
package cmd
import (
  "fmt"
  "log"
  "github.com/jameesjohn/accountant/database"
  "github.com/spf13/cobra"
)
// debitCmd represents the debit command
var debitCmd = &cobra.Command{
  Use:   "debit",
  Short: "Create a debit transaction",
  Long: `
This command creates a debit transaction for a particular user.
Usage: accountant debit <username> --amount=<amount> --narration=<narration>.`,
  Run: func(cmd *cobra.Command, args []string) {
    if len(args) < 1 {
      log.Fatal("Username not specified")
    }
    username := args[0]
    user, err := database.FindOrCreateUser(username)
    if err != nil {
      log.Fatal(err)
    }
    if user.Balance > debitAmount {
      user.Balance = user.Balance - debitAmount
      debitTransaction := database.Transaction{Amount: debitAmount, Type: "debit", Narration: debitNarration}
      user.Transactions = append(user.Transactions, debitTransaction)
      database.UpdateUser(user)
      fmt.Println("Transaction created successfully")
    } else {
      fmt.Println("Insufficient funds!")
    }
  },
}

var debitNarration string
var debitAmount int64

func init() {
  rootCmd.AddCommand(debitCmd)
  debitCmd.Flags().StringVarP(&debitNarration, "narration", "n", "", "Narration for this debit transaction")
  debitCmd.Flags().Int64VarP(&debitAmount, "amount", "a", 0, "Amount to be debited")
  debitCmd.MarkFlagRequired("narration")
  debitCmd.MarkFlagRequired("amount")
}

```

如果用户有足够的余额来执行交易，我们从他们的余额中减去借记的金额，创建一个新的借记交易，并将该交易添加到用户。最后，我们用更新的用户更新数据库。

如果用户没有足够的余额，我们会输出一条错误消息，指出他们的余额不足。

我们现在可以使用`accountant`来借记用户:

```
./accountant debit henry --amount=40 --narration="Paid James"

```

现在可以通过运行`go build`来构建应用程序。

## 结论

我们刚刚学习了如何使用 Cobra 构建 CLI 应用程序！考虑到 Cobra 为我们做的大量工作，不难理解为什么流行的开源应用程序和工具将它用于它们的 CLI 应用程序。

[这个项目可以在这里找到](https://github.com/jameesjohn/accountant)。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)