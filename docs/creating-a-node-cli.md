# 使用 Enquirer 创建节点 CLI

> 原文：<https://blog.logrocket.com/creating-a-node-cli/>

虽然图形用户界面(GUI)更经常被最终用户看到，但命令行界面(CLI)通常是对开发人员工具和快速项目的一个非常有价值的补充。

有时，CLI 提供了比 GUI 更好的体验。

在本帖中，我们将介绍如何创建一个节点 CLI 来使用来自[ESPN.com](https://www.espn.com)的数据查看体育新闻。我们将了解一些工具和开源库，它们有助于创建一个 CLI 来执行诸如着色、加载微调器和围绕输出绘制框等功能。我们还将使用一个 async/await 构造来等待用户响应我们在 CLI 中创建的提示。让我们开始吧！

## 设置项目

> **注意**:如果你想跳过大部分设置，你可以[克隆这个 repo](https://github.com/bradydowling/node-cli-tutorial) ，运行`npm install`，并开始向`index.js`添加代码。

要开始我们的项目，让我们继续创建一个新目录，初始化一个 npm 包，并创建一个`index.js`文件，我们将在其中编写所有代码:

```
mkdir my-cli
cd my-cli
npm init -y
touch index.js

```

现在，让我们安装将在 CLI 中使用的软件包:

```
npm install --save enquirer boxen ora chalk node-localstorage @rwxdev/espn

```

因为我们将在脚本中使用`import`语句，所以我们需要确保将我们的包标记为`module`。将下面一行添加到您的`package.json`文件中:

```
"type": "module",

```

首先，我们要建立`index.js`文件。我们将设置一个 main 函数，在这里我们最终可以运行一些异步代码和其余的 CLI。我们可以在使用过程中导入每个依赖项:

```
// index.js

const runCli = async () => {
  // Welcome user
  console.log("Thanks for consuming sports headlines responsibly!");

  console.log("Thanks for using the ESPN cli!");
  return;
}

runCli();

```

您可以使用`node ./index.js`运行 CLI 来查看我们刚刚添加的日志。

## 获取显示数据

现在我们已经布置好了我们的框架，让我们添加一些功能。

我们将导入一个库来获取 ESPN 标题，然后进行初始调用来获取它们。因为这是一个获取数据的异步调用，所以用户必须等到调用结束才能看到任何信息。向用户表明 CLI 正在加载会很有帮助。

为此，我们将使用一个名为 [ora](https://github.com/sindresorhus/ora) 的库来显示一个动画加载指示器，如下所示:

![Ora Library Animated Loading Indicator](img/dd329504228b92a91601041c3fb8806e.png)

An example of the ora loading spinner

在我们进行异步调用之前，我们将启动 spinner。调用完成后，我们可以停止微调器:

```
import ora from "ora";
import {
  getArticleText,
  getHeadlines,
  getPageContents,
  getSports,
} from "@rwxdev/espn";

const homepageUrl = "https://espn.com/";

const runCli = async () => {
  console.log("Thanks for consuming sports headlines responsibly!");
  const spinner = ora("Getting headlines...").start();
  const $homepage = await getPageContents(homepageUrl);
  spinner.succeed("ESPN headlines received");
...

```

现在用`node ./index.js`运行 CLI 会在获取数据时显示一个简短的 spinner/loader。一旦我们有了数据，我们会看到一个复选标记。注意，文本变成了我们传递给`.succeed`函数的内容。相当酷！

使用我们刚刚获得的`$homepage`数据，我们将获取稍后需要向用户显示的所有标题。同样，我们将在这里使用我们的助手函数:

```
...
  spinner.succeed("ESPN headlines received");
  const homepageHeadlines = getHeadlines($homepage);
  const sports = getSports($homepage);
  const headlinesBySport = {};
  for (let sport of sports) {
    getPageContents(sport.href).then(($sportPage) => {
      const headlines = getHeadlines($sportPage);
      headlinesBySport[sport.title] = headlines;
    }).catch((e) => {
      console.log("there was an issue getting headlines for a certain sport", e);
    });
  }
...

```

现在，我们已经收集并存储了我们想要向用户显示的所有信息:体育和可供选择的大量标题。我们以后会用到这些。

## 声明 CLI 选项

在我们的 CLI 中，我们将为用户提供几个不同的选项类型供其选择:一篇阅读的文章、一个查看标题的特定运动，以及一个查看特定运动的更多标题的`MORE`类型。

这可能是使用 TypeScript 的一个很好的选择，因为选择将根据它们的类型(例如，headline、sport、exit)表现不同。我们只是在本教程中使用普通的 JavaScript，所以我们将自己做一些次要的类型处理。

让我们声明变量，这样我们就可以根据选择类型不同地处理用户输入。我们还将提供一些通用选项供用户选择，不管在任何给定的一天有什么标题。

我们将在之前添加的`for`循环之后插入以下代码:

```
...
  const selectionTypes = {
    HEADLINE: "headline",
    SPORT: "sport",
    MORE: "more"
  };
  const genericOptions = {
    HOMEPAGE_HEADLINES: { title: "see homepage headlines" },
    LIST_SPORTS: { title: "see headlines for specific sports", type: selectionTypes.MORE },
    OTHER_SPORTS: { title: "see headlines for other sports", type: selectionTypes.MORE },
    EXIT: { title: "exit" },
  };
...

```

因为这是一个 CLI，我们将反复提示用户输入信息，直到他们选择退出。这非常适合一个`while`循环，在这个循环中我们可以指定用户所做的选择。我们将存储选择和选择标题:

```
...
  let selection;
  let selectionTitle;
  let articleText;
  let currentPrompt;
  let exit = false;
  while(!exit) {
    // Where we'll handle the user's selection
  }
  console.log("Thanks for using the ESPN cli!");
...

```

每次用户做出选择，我们将存储选择，通过循环返回，然后处理之前做出的选择。有几个不同的案例需要处理，所以让我们一个接一个地处理。

如果您现在运行 CLI，`while`循环将是无限的，因为我们既不告诉它退出，也不允许用户输入。如果你遇到这种情况，按下 **ctrl + c** 或者关闭你的终端窗口。在此之后，让我们进行第一次提示。

## 使用询问器创建提示

我们将使用 [Enquirer](https://github.com/enquirer/enquirer) ，这个库允许我们在控制台中显示一些内容，然后等待用户的输入。正如我们将要看到的，它有一个非常简单的 API。

让我们从将它导入到文件的顶部开始:

```
import ora from "ora";
import enquirer from "enquirer";
...

```

我们可以在我们的`while`循环中使用它，方法是创建第一个提示来显示给用户。它会问用户他们想阅读主页上的哪个故事，然后给他们几个选项:

*   阅读列表中的一个主页标题
*   列出不同的运动项目，以查看更多标题
*   退出应用程序

```
...
      while(!exit) {
        if (!selection || selection.title === genericOptions.HOMEPAGE*HEADLINES.title) {
          currentPrompt = new enquirer.Select({
            name: "homepage",
            message: "What story shall we read?",
            choices: [...homepageHeadlines.map(item => item.title), genericOptions.LIST*SPORTS.title, genericOptions.EXIT.title]
          });
        }
        selectionTitle = await currentPrompt.run();
      }
    ...
```

对于`choices`值，我们提供了一个字符串数组供用户选择。在这个`if`块下面是我们实际运行提示并将用户的选择存储为`selectionTitle`的地方。我们需要更多的信息，而不仅仅是标题，所以让我们声明几个变量，这些变量将根据选择标题找到选择:

```
...
    selectionTitle = await currentPrompt.run();
    const combinedSportHeadlines = Object.values(headlinesBySport).reduce((accumulator, item) => {
      return [...accumulator, ...item];
    }, [])
    const allOptions = [...Object.values(genericOptions), ...homepageHeadlines, ...sports, ...combinedSportHeadlines];
    selection = allOptions.find(item => item.title === selectionTitle);
  }
  console.log("Thanks for using the ESPN cli!");
...

```

这些变量基本上将我们所有不同的选项类型组合成一个大的选择阵列。从这个`allOptions`数组中，我们可以找到标题与用户选择相匹配的那个。

现在我们只处理一种类型的选择，我们的 CLI 将显示原始主页标题。这是朝着正确方向迈出的一步！然而，如果你现在运行它，一旦你做出第一个选择，你就会陷入一个无限循环中，这一点都不好玩。

## 退出 CLI

现在让我们处理另一种选择类型:退出。我们可以检查用户是否选择了退出`selection?.title === genericOptions.EXIT.title`，但是把它作为我们的`if...else`块的最后一位也是有帮助的，这样我们就可以在处理所有其他选择类型之前摆脱无限循环。我们将在`while`循环的末尾添加出口块:

```
...
  while(!exit) {
    if (!selection || selection.title === genericOptions.HOMEPAGE_HEADLINES.title) {
      currentPrompt = new enquirer.Select({
        name: "homepage",
        message: "What story shall we read?",
        choices: [...homepageHeadlines.map(item => item.title), genericOptions.LIST_SPORTS.title, genericOptions.EXIT.title]
      });
    }
    else {
      exit = true;
      break;
    }
...

```

现在，如果用户选择退出，或者如果他们选择了尚未处理的选项，CLI 将立即退出。不再有无限循环！

## 处理其他用户选择

我们已经显示了主页标题，所以现在我们也可以显示其他运动的标题，如果用户选择的话。

让我们添加另一个块来处理这个问题:

```
...
    else if (selection.type === selectionTypes.MORE) {
      currentPrompt = new enquirer.Select({
        name: "sports",
        message: "Which sport would you like headlines for?",
        choices: sports.map(choice => choice.title)
      });
    }
    else {
      exit = true;
      break;
    }
...

```

一旦用户选择了一项特定的运动，我们希望他们能够看到该项运动的标题。因此，我们将做一些与显示主页标题类似的事情，但是这一次，我们只需要它们来显示所选的运动。我们可以在退出 CLI 的`else`块之前插入另一个`if else`块:

```
...
    else if (selection.type === selectionTypes.SPORT) {
      const sportHeadlines = headlinesBySport[selection.title];
      const sportChoices = sportHeadlines.map(option => option.title);
      currentPrompt = new enquirer.Select({
        name: "sportHeadlines",
        message: `Select a ${selection.title} headline to get article text`,
        choices: [...sportChoices, genericOptions.HOMEPAGE_HEADLINES.title, genericOptions.OTHER_SPORTS.title, genericOptions.EXIT.title]
      });
    }
    else {
      exit = true;
      break;
    }
...

```

## 使用 boxen 设计日志

最后，如果用户选择标题阅读，我们实际上可以显示文章文本。因为这是一篇文章，我们可以给它添加一些样式，这样它就不仅仅是简单的旧文本了。

首先，在文件顶部导入一个名为 [boxen](https://github.com/sindresorhus/boxen) 的库:

```
import ora from "ora";
import enquirer from "enquirer";
import boxen from "boxen";
...

```

这将允许我们在显示的文章文本周围画一个框。在最后一个`else`模块前再插入一个`if else`。在显示另一个提示之前，我们将使用 boxen 的样式注销一些文本:

```
    else if (selection.type === selectionTypes.HEADLINE) {
      articleText = await getArticleText(selection.href);
      console.log(boxen(selection.href, { borderStyle: 'bold'}));
      console.log(boxen(articleText, { borderStyle: 'singleDouble'}));
      currentPrompt = new enquirer.Select({
        name: "article",
        message: "Done reading? What next?",
        choices: [genericOptions.HOMEPAGE_HEADLINES.title, genericOptions.LIST_SPORTS.title, genericOptions.EXIT.title]
      });
      articleText = "";
    }
    else {
...

```

现在，CLI 允许用户查看首页标题、体育项目列表、特定体育项目的标题，以及阅读标题文章。这看起来很不错！在我们完成之前，我们还有两个收尾工作要做。

## 清算查询提示

如果您多次运行 CLI，您会注意到提示一个接一个地堆积起来。它们留在控制台中，但不会提供太多价值。因为我们每次都存储提示，我们可以使用 Enquirer 清除它。在我们的`while`循环的开始，我们可以在`currentPrompt`上调用`.clear()`。我们将使用可选的链接，这样在第一次执行时就不会出错:

```
...
  while(!exit) {
    currentPrompt?.clear();
...

```

我们将在 CLI 中添加的最后一件事是在开始时记录日志，告诉用户他们一天使用 CLI 的次数。这将是有帮助的，因为它通常不是一个有效的 CLI，所以这将有助于您了解自己有多分心。也许你会意识到你应该更专注于你的工作。🤷‍♂️

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于更有帮助的 CLI，像开发人员工具一样，这种特性也可以向您展示该工具是多么有价值或不可或缺。然后，当然，如果你意识到你一天运行相同的脚本 100 次，这可能意味着你应该把它的功能合并到一个完全不需要人工干预就能独立运行的东西中。

## 向控制台日志添加颜色

为了完成这一部分，我们将使用一个`[node-localstorage](https://github.com/lmaccherone/node-localstorage)`模块存储每天执行的次数，我们在这里导入这个模块。这就像`[Window.localstorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)`一样，所以我们可以使用类似的 API。如果需要的话，我们也可以在这里使用数据库，但是这样应该就可以了。

根据我们运行 CLI 的次数，我们希望用不同的颜色显示我们的日志，因此我们将导入最后一个库， [C](https://github.com/chalk/chalk) [halk](https://github.com/chalk/chalk) ，以更改我们的文本颜色:

```
import ora from "ora";
import enquirer from "enquirer";
import boxen from "boxen";
import { LocalStorage } from "node-localstorage";
const localStorage = new LocalStorage("./scratch"); // scratch is the name of the directory where local storage is saved, this can be change to whatever you'd like
import chalk from "chalk";
...

```

现在，我们可以创建一个新的函数来处理所有的日常使用逻辑，使用粉笔来改变日志颜色，使用本地存储来计算日常使用。我们将在主函数开始时调用它:

```
...
const showTodaysUsage = () => {
  const dateOptions = { year: "numeric", month: "numeric", day: "numeric" };
  const now = new Date();
  const dateString = now.toLocaleString("en-US", dateOptions);
  const todaysRuns = parseInt(localStorage.getItem(dateString)) || 0;
  const chalkColor = todaysRuns < 5 ? "green" : todaysRuns > 10 ? "red" : "yellow";
  console.log(chalk\[chalkColor\](`Times you've checked ESPN today: ${todaysRuns}`));
  localStorage.setItem(dateString, todaysRuns + 1);
}

const runCli = async () => {
  showTodaysUsage();
  console.log("Thanks for consuming sports headlines responsibly!");
...

```

## 审查最终产品

现在，我们使用我们安装的所有库来处理所有案例。您的最终脚本应该如下所示:

```
import ora from "ora";
import enquirer from "enquirer";
import boxen from "boxen";
import { LocalStorage } from "node-localstorage";
const localStorage = new LocalStorage("./scratch");
import chalk from "chalk";
import {
  getArticleText,
  getHeadlines,
  getPageContents,
  getSports,
} from "@rwxdev/espn";
const homepageUrl = "https://espn.com/";
const showTodaysUsage = () => {
  const dateOptions = { year: "numeric", month: "numeric", day: "numeric" };
  const now = new Date();
  const dateString = now.toLocaleString("en-US", dateOptions);
  const todaysRuns = parseInt(localStorage.getItem(dateString)) || 0;
  const chalkColor = todaysRuns < 5 ? "green" : todaysRuns > 10 ? "red" : "yellow";
  console.log(chalk\[chalkColor\](`Times you've checked ESPN today: ${todaysRuns}`));
  localStorage.setItem(dateString, todaysRuns + 1);
}
const runCli = async () => {
  showTodaysUsage();
  console.log("Thanks for consuming sports headlines responsibly!");
  const spinner = ora("Getting headlines...").start();
  const $homepage = await getPageContents(homepageUrl);
  spinner.succeed("ESPN headlines received");
  const homepageHeadlines = getHeadlines($homepage);
  const sports = getSports($homepage);
  const headlinesBySport = {};
  for (let sport of sports) {
    getPageContents(sport.href).then(($sportPage) => {
      const headlines = getHeadlines($sportPage);
      headlinesBySport[sport.title] = headlines;
    }).catch((e) => {
      console.log("there was an issue getting headlines for a certain sport", e);
    });
  }

  const selectionTypes = {
    HEADLINE: "headline",
    SPORT: "sport",
    MORE: "more"
  };
  const genericOptions = {
    HOMEPAGE_HEADLINES: { title: "see homepage headlines" },
    LIST_SPORTS: { title: "see headlines for specific sports", type: selectionTypes.MORE },
    OTHER_SPORTS: { title: "see headlines for other sports", type: selectionTypes.MORE },
    EXIT: { title: "exit" },
  };

  let selection;
  let selectionTitle;
  let articleText;
  let currentPrompt;
  let exit = false;
  while(!exit) {
    currentPrompt?.clear();
    if (!selection || selection.title === genericOptions.HOMEPAGE_HEADLINES.title) {
      currentPrompt = new enquirer.Select({
        name: "homepage",
        message: "What story shall we read?",
        choices: [...homepageHeadlines.map(item => item.title), genericOptions.LIST_SPORTS.title, genericOptions.EXIT.title]
      });
    }
    else if (selection.type === selectionTypes.MORE) {
      currentPrompt = new enquirer.Select({
        name: "sports",
        message: "Which sport would you like headlines for?",
        choices: sports.map(choice => choice.title)
      });
    }
    else if (selection.type === selectionTypes.SPORT) {
      const sportHeadlines = headlinesBySport[selection.title];
      const sportChoices = sportHeadlines.map(option => option.title);
      currentPrompt = new enquirer.Select({
        name: "sportHeadlines",
        message: `Select a ${selection.title} headline to get article text`,
        choices: [...sportChoices, genericOptions.HOMEPAGE_HEADLINES.title, genericOptions.OTHER_SPORTS.title, genericOptions.EXIT.title]
      });
    }
    else if (selection.type === selectionTypes.HEADLINE) {
      articleText = await getArticleText(selection.href);
      console.log(boxen(selection.href, { borderStyle: 'bold'}));
      console.log(boxen(articleText, { borderStyle: 'singleDouble'}));
      currentPrompt = new enquirer.Select({
        name: "article",
        message: "Done reading? What next?",
        choices: [genericOptions.HOMEPAGE_HEADLINES.title, genericOptions.LIST_SPORTS.title, genericOptions.EXIT.title]
      });
      articleText = "";
    }
    else {
      exit = true;
      break;
    }

    selectionTitle = await currentPrompt.run();
    const combinedSportHeadlines = Object.values(headlinesBySport).reduce((accumulator, item) => {
      return [...accumulator, ...item];
    }, [])
    const allOptions = [...Object.values(genericOptions), ...homepageHeadlines, ...sports, ...combinedSportHeadlines];
    selection = allOptions.find(item => item.title === selectionTitle);
  }
  console.log("Thanks for using the ESPN cli!");
  return;
}

runCli();

```

你也可以在这里找到代码应该是什么样子的。

## 结论

我们都准备好了！我们的 CLI 将显示我们的 ESPN 主页标题，列出可用的运动，并让我们阅读文章。我们为用户提供了提示，在加载过程中显示了动画微调器，对控制台输出进行了着色，并在一些控制台文本周围绘制了方框。

我们已经熟悉了一些不同的节点 CLI 实用程序，但是还有[几个](https://github.com/sindresorhus/awesome-nodejs#command-line-utilities)可供您探索和使用。命令行界面对生产力有极大的帮助，而且很有趣！尝试一下我们在本教程中所做的内容，看看如何将它变成对你有帮助的东西！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.