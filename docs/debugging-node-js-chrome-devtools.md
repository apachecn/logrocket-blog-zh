# 用 Chrome DevTools 调试 Node.js

> 原文：<https://blog.logrocket.com/debugging-node-js-chrome-devtools/>

在开发软件时，调试是最重要的步骤之一。代码测试不仅确保了稳定性，还保证了高质量的代码和更好的用户体验。

通常，开发人员在他们的代码库中使用`console.log`语句来检查问题。例如，看看下面的 JavaScript 代码:

```
console.log("This code will be printed in the console");
const i = 10;
console.log("The value of i is", i);

```

这将产生以下输出:

![Console.log output](img/60dd3fd671bf257b9ff316f61d9a3a38.png)

尽管这是一种有效的测试方法，但是有一个小问题:这是一种测试代码的笨拙方法。这有几个原因:

*   您无法检查应用程序的内存使用情况。在优化性能时，跟踪 RAM 的使用情况至关重要
*   没有断点:这意味着您无法在运行时跟踪它们的变量值。作为一种变通方法，您可以使用`console.log`语句，但是对于大型项目来说这可能是一个问题，因为您必须检查日志语句的页面

![Debugging output.](img/cf640ed1ab51e9d2aebd906a8f79088e.png)

这就是 Chrome 的开发者工具发挥作用的地方。Chrome DevTools 帮助程序员用最少的努力测试他们的 web 应用程序和调试 Node.js 控制台项目。

Chrome DevTools 支持以下功能:

*   断点:让用户在运行时跟踪他们的变量值
*   内存和 CPU 分析:顾名思义，这个特性可以帮助我们跟踪应用程序的内存和 CPU 使用情况
*   源代码映射:将目标代码映射到源代码。这有助于我们在源代码中读取和调试编译后的代码

在本文中，您将了解如何使用 Chrome 的内置开发工具调试 Node.js 应用程序。以下是我们要学习的内容:

## 创建项目

作为第一步，首先使用以下终端命令初始化一个黑色控制台项目:

```
mkdir debugger-tools #create project directory
cd debugger-tools 
npm init -y #initialize project.
touch index.js #create a file called index.js

```

在本文中，为了展示调试器的功能，我们将构建一个基本的 Express 服务器。为此，请按如下方式安装`express`模块:

```
npm install express

```

### 运行检查器

在这一节中，你将学习如何开始使用 Chrome DevTools 菜单来调试你的项目。

首先，导航到`index.js`。首先编写以下代码:

```
//file: index.js
const express = require("express");
const app = express();
const port = 3000;
app.get("/", async (req, res) => { //when the user is on the home directory,
  console.log("User is on home page"); //log out this message to the console
  res.send("Hello World!"); //send response to client to prevent timeout errors
});
app.listen(port, () => { //run the server
  console.log(`Example app listening on port ${port}`);
});

```

下一步，我们必须运行调试器:

```
npx nodemon --inspect index.js

```

这告诉编译器附加一个调试器守护进程，这样我们就可以使用 Chrome 的 DevTools 来检查我们的程序。

![Debugger daemon.](img/af941636670576736547ee9d35b196a7.png)

既然守护程序正在运行，请按照以下步骤打开调试器:

1.  在一个空的浏览器窗口中，点击右键，然后点击**检查**打开 Chrome 开发者工具
2.  单击绿色的 Node.js 图标。这将告诉浏览器启动 Node.js 调试器

以下是 GIF 形式的步骤:

![opening debugger step 1](img/d2c7f4f9d38ad79ef4982ae84f4a0da9.png)

现在让我们测试我们的代码！转到`localhost:3000`:

![opening debugger step 2](img/165d77baf8acac89d292db1ce8d4192a.png)

如您所见，每次用户刷新页面时，我们都会在 DevTools 的**控制台**视图中获得控制台日志。这表明 Node.js 已经成功地为我们的程序附加了一个调试器。

### 在 Node.js 中插入断点

断点功能可以说是调试器工具集中最重要的功能。顾名思义，每当代码执行到某一点时，它们就暂停代码执行。在下列情况下，这可能很有用:

*   当在代码执行期间跟踪变量值时
*   检查调用堆栈。这在您想要检查项目是否在应用程序的生命周期中实际调用了关键函数的情况下是至关重要的

以下代码将帮助我们理解调试器:

```
let count = 0; //initialize a count variable.
app.get("/", async (req, res) => {
  console.log("User is on home page");
  count++; //when the user visits this page, increment the count variable
  console.log("User visit count", count); //log out its value
  res.send("Hello World!");
});

```

要启用断点，请在 DevTools 窗口中执行以下步骤:

1.  选择**信号源**
2.  双击要设置断点的行；在我们这里是`line 9`。这告诉调试器我们希望断点跟踪变量`count`
    ![Enable breakpoints.](img/21fb3057a71928bfbc5c56f4995afe3d.png)的值

当我们运行代码时，我们可以看到调试器在每次到达`line 9`时暂停代码执行。

![the debugger pauses code execution every time it reaches line 9](img/7f9aee85f82145b8f81ff4015d61b499.png)

感谢调试器服务，我们现在可以在程序运行时跟踪`count`的值。

### 使用`Debugger` **关键字**

除了通过 DevTools 启用断点，我们甚至可以在代码中使用`debugger`关键字。这对于我们不想通过 GUI 手动激活断点的情况非常有用。

这段代码演示了如何使用`debugger`关键字:

```
const count = 0;
const myArray = []; //create an empty array
app.get("/", async (req, res) => {
  console.log("User is on home page");
  count++;
  myArray.push(count); //push element into this array
  debugger; //activate a breakpoint. 
  //All of the variables before this line will be tracked
  console.log("User visit count", count);
  res.send("Hello World!");
});

```

这将是程序的输出:

![Debugger program output](img/aed821cf919b889adb0a2d21ff33388b.png)

### 使用内存分析器

内存分析器帮助开发人员跟踪他们的应用程序的内存使用情况。当程序员想要跟踪内存泄漏时，这很有用。因此，这有助于优化项目的性能。

要使用内存分析器，请按照下列步骤操作:

1.  转到**存储器**
2.  然后，确保选择了**堆快照**
3.  之后，向下滚动并点击**记录**
    ![Memory profiler record. ](img/bd3588de02501d8f39f06f2639659ae5.png)

在这个选项中，您可以看到您的程序总共消耗了多少内存。

此外，如果我们扩展每个选项，我们可以更深入地了解我们的代码使用了多少内存。因此，这让我们可以监控项目的内存泄漏。

![How much memory your program consumes](img/19ca4cd93d0932e6aa5769d0767dbe11.png)

## 结论

如前所述，谷歌的 Chrome DevTools 是应用开发过程中最重要的软件之一。它不仅能帮助程序员消灭 bug，还能帮助他们提升应用程序的用户体验。最终，这会让用户和客户更加满意。非常感谢您的阅读！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.