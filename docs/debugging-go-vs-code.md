# 调试与 VS 代码同行-日志火箭博客

> 原文：<https://blog.logrocket.com/debugging-go-vs-code/>

调试用于检测和修复程序中的错误，防止它们在部署到生产环境后不正确地执行。当有几个紧密连接的模块时，调试变得更加复杂，因为在一个模块中所做的每一个更改都可能导致另一个模块出错。

开发人员可以使用 Visual Studio 代码编辑器调试 Go 应用程序。通过所需的调试扩展，VS 代码编辑器为调试 Go 程序提供了出色的工具。在本文中，我们将学习如何使用 VS 代码编辑器调试 Go 应用程序:

### 先决条件

要完成本教程，您需要以下内容:

*   安装在您的系统上
*   对 Go 编程语言的基本理解
*   安装在计算机上的 VS 代码 v1.63
*   在你的 VS 代码编辑器中[运行](https://marketplace.visualstudio.com/items?itemName=golang.go)和 [Delve 扩展安装](https://blog.logrocket.com/comparing-go-debugging-tools/#delve)

## 创建示例应用程序

为了更好地理解 VS 代码调试器是如何工作的，让我们创建一个基本的 Go 应用程序，它从一个数组生成 JSON 输出。要创建新的 Go 程序，请打开您的终端并运行以下命令:

```
mkdir go-debugging
cd go-debugging
go mod init github.com/USERNAME/go-debugging
touch cmd/go-debugging/main.go

```

在上面的命令中，将`USERNAME`改为您个人的 GitHub 用户名。打开`main.go`文件，使用 VS 代码编辑器添加以下代码:

```
package main

import (
   "encoding/json"
   "fmt"
   "log"
)

type user struct {
   FullName string `json:"full_name"`
   Email string `json:"email"`
   Gender   string `json:"gender"`
   Status   string `json:"status"`
   RegDate   string `json:"Reg_date"`
}

func main() {
   userinfos := []user{
       {
           FullName: "blessing james",
           Email: "[email protected]",
           Gender:   "Male",
           Status:   "active",
           RegDate:"20-01-2021",
       },
       {
           FullName: "matt john",
           Email: "[email protected]",
           Gender:   "Male",
           Status:   "active",
           RegDate:"20-01-2021",
       },
       {
           FullName: "john peace",
           Email: "[email protected]",
           Gender:   "Midgard",
           Status:   "active",
           RegDate:"20-01-2021",
       },
   }

   jsonBytes, err := json.Marshal(userinfos)
   if err != nil {
       log.Fatalln(err)
   }
   fmt.Println(string(jsonBytes))
}

```

上面的代码将以 JSON 格式打印数组`userinfos`。您可以使用下面的命令执行该应用程序:

```
go run main.go

```

上面命令的输出是 JSON 格式的，如下所示:

```
[{"full_name":"blessing james","email":"[email protected]","gender":"Male","status":"active","Reg_date":"20-01-2021"},{"full_name":"matt john","email":"[email protected]","gender":"Male","status":"active","Reg_date":"20-01-2021"},{"full_name":"john peace","email":"[email protected]","gender":"Midgard","status":"active","Reg_date":"20-01-2021"}]

```

## 在 VS 代码中设置调试会话

在 Go 中设置调试配置非常简单明了。从你的 VS 代码的工具条菜单中，点击**运行和调试**按钮，然后点击**创建一个`launch.json`文件**:

![Set Up Debugging Sessions VS Code](img/80e57005fd958234e2eb2f917d4f2217.png)

您将看到一个下拉菜单，您可以在其中选择您的`workspace folder`。然后，为环境语言选择 **Go** 。最后选择**启动包**进行调试配置。该配置将创建`launch.json`文件，该文件将包含以下代码:

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Package",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${fileDirname}"
        } 
    ]
}

```

将上面 JSON 设置中的`program`值更改为应用程序文件名，在我们的例子中为`main.go`:

```
"program": "main.go"
```

保存`launch.json`配置后，编辑器窗口底部的`DEBUG CONSOLE`将显示项目的输出。调试工具栏将出现在屏幕顶部，允许您单步执行代码、暂停脚本或结束会话。

要调试应用程序，点击`RUN AND DEBUG`附近的**播放图标**，将在`DEBUG CONSOLE`窗口显示程序输出:

![Debug Console Window Output](img/6e969fde773d865be1e646ebc16e865c.png)

如果您第一次运行 delve 调试器扩展，您可能会得到一个错误，如下所示:

![VS Code Debugger Error](img/b65a5ed3b81a24d473a8b126b191cdba.png)

要解决此错误，请在您的终端中输入以下命令，然后再次单击运行和调试图标:

```
Install -v githup.com/go-delve/cmd/[email protected]
```

## 使用断点调试

断点允许您通过暂停代码的执行来检查代码行。几乎可以在 VS 代码中的任何地方设置断点，包括变量声明、表达式、注释和空行，但函数声明语句除外。

让我们给第`26`、`29`和`35`行添加断点。只需点击行号的左侧，您会看到一个红点出现:

![Debbugging Breakpoint](img/f9cee2f948cebb8f2bb99aa73a20c294.png)

当您调试上面的程序时，执行将在每个断点处暂停。首先，程序会自动暂停在第`26`行。点击调试工具栏上的**继续** **按钮** `F8`，程序将继续执行，直到到达第`29`行的下一个断点，然后是第`35`行。

在 **VARIABLES** 面板下，我们可以通过将鼠标悬停在用黄色标记的当前断点行上来检查每个标识符的当前范围。

### 使用条件断点

在 VS 代码中，你可以通过给断点一个表达式来修改断点，通常是一个布尔表达式，允许你在某些表达式为`true`或`false`时检查你的程序。

例如，我们可以添加一个条件断点，它只在表达式为`true`时出现，如在`user[2].email == "[[email protected]](/cdn-cgi/l/email-protection)"`中。为此，右击断点并选择**条件断点**:

![Conditional Breakpoint VS Code](img/e4ff5dac5270289f85cae92815975bfd.png)

### 使用 logpoint

与暂停代码执行和中断调试器不同，logpoint 是一种断点，它将消息或值记录到控制台，这对调试工作流非常重要。

要在不改变代码的情况下添加和删除`log()`语句，右键单击 gutter 并选择 **Add Logpoint。** 代替红色圆圈的是一个红色的菱形图标。在终端中，你会看到一个文本输入框；要记录表达式或变量值，请将其放在花括号中:

![Add Logpoint Debug Vscode](img/f83864785c05c4d03d457b018fe7e223.png)

## 检查我们的代码执行

在 VS 代码编辑器的顶部，您会看到调试工具栏，它包含有效导航调试器的方向。让我们逐一回顾一下:

![Debug Toolbar VSCode](img/d832349c60c5d9b383ec1a3bc62aefba.png)

### 继续`F8`

当程序在断点处暂停时，您可以使用继续`F8`按钮来恢复程序的执行。在 VS 代码中调试 Go 程序时，可以添加任意多的断点。

### 跨过`F10`

单步执行命令`F10` r 在移动到下一行之前，运行当前突出显示的代码行。 你可以使用“单步执行”命令向下执行一个功能，完全理解它是如何执行的。

如果您在调用函数的行上使用 step over 命令，它将执行整个函数，在函数下面的第一行暂停。

### 步入`F11`

像单步执行命令一样，我们可以使用单步执行命令来逐行调试程序。但是，如果“单步执行”命令遇到函数，调试器将进入被调用的函数，从那里继续逐行调试。

### 步出`Shift+F11`

“跳出”命令继续当前函数的执行，在最后一行暂停。例如，如果您错误地键入了一个与您试图解决的问题无关的函数，您可以使用“跳出”命令快速退出该函数并返回到代码库的相关部分。

### 重启`Ctrl+Shift+F5`

每当您希望重新开始调试遇到断点的程序时，可以使用 restart 命令从头开始调试程序，而不是终止并重新启动调试器。

### 停止`Shift+F5`

完成程序调试后，使用 stop 命令退出调试会话。当您连接到外部 Node.js 进程时，将会出现一个断开图标。

## `VARIABLES`面板

既然我们已经回顾了调试工具栏中可用的功能，那么让我们回顾一下 VS 代码编辑器中的附加选项卡。在`VARIABLES`面板中，您可以看到在断点处评估的变量和表达式的值。

此外，通过右击**上下文菜单**中的任何值，可以为变量设置`Value`、`Copy Value`或`Add to Watch`。

## `WATCH`面板

当代码暂停时，您可以在`WATCH`面板中查看想要监控的值。不必每次想检查一个值时都通过`VARIABLES`面板，您可以向`WATCH`面板添加一个深度嵌套的属性，以便于访问。

这对于一次找到多个变量的值特别有用，因为它们在执行过程中会立即重新计算。

## 使用单元测试进行调试

我们也可以用单元测试来调试 Go 应用；单元测试有助于确保应用程序的每个组件都正确执行其预期的功能。让我们看看如何在 Visual Studio 中使用单元测试来调试 Gol 应用程序。

创建一个名为`main_test.go`的测试文件，并将以下代码添加到该文件中:

```
package main
import "testing"

func average(score1, score2, score3 int) int {
    return ((score1 + score2 + score3) / 3)
}
func Test_arerage(t *testing.T) {
    score1, score2, score3 := 10, 18, 41

    averageScore := average(score1, score2, score3)
    if averageScore == 0 {
        t.Fail()
    }

}

```

上面的两个函数使我们能够计算三个数字的平均值。要测试的函数(`Test_average`)前面有`Test_`关键字。要运行单元测试，请输入以下命令:

```
 go test

```

现在，让我们通过向程序添加一个断点来调试我们的测试文件，如下所示:

![Golang Debug Unity Test](img/1be6e4c48e8ab847049429f25e8e549c.png)

现在，您可以启动调试会话，然后使用调试工具逐步检查每个变量，并在 variables 部分下更改它们的值。

![Golang Unit Testing with the Debug Tool](img/46ea147bc258b041cc47bf17af766e58.png)

## 结论

在本文中，我们介绍了用 Visual Studio 代码调试 Go 应用程序的一些基础知识。VS 代码编辑器提供了有用的插件，使调试变得容易。

我们可以添加断点、条件断点和日志点来暂停代码执行，从而允许我们深入检查哪里出错了。我们还探索了调试工具栏中的一些快捷方式，这些快捷方式允许我们在调试过程中浏览代码。最后，我们介绍了`VARIABLES`面板和`WATCH`面板中的一些功能。

要了解更多信息，请务必查看[在线文档](https://code.visualstudio.com/Docs/editor/debugging)。我希望你喜欢这个教程！如果你有任何问题，请留下评论。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)