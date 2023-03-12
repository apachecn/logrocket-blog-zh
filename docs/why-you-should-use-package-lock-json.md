# 为什么应该使用 package-lock . JSON-log rocket Blog

> 原文：<https://blog.logrocket.com/why-you-should-use-package-lock-json/>

在本文中，我们将了解`package-lock.json`，为什么它如此重要，以及如何在您的日常生活中最好地与 NPM CLI 配合使用。

## 历史

NPM 版本 5 引入了`package-lock.json`作为一种机制来捕获在任何时间点安装的精确依赖树。

这有助于跨不同环境的协作，在这种环境中，您希望每个人为项目的特定版本获取依赖项，以获取相同的树。

`package.json`使用[语义版本化](https://semver.org/)定义所需的依赖关系及其各自的版本。然而，语义版本控制可能很棘手。

考虑一个被称为`"express": "^4.16.4"`的依赖关系。

这个模块的发行者(没有使用`package-lock.json`)会安装`express`版本 4.16.4，因为他们安装了最新版本。

如果在我下载这个模块并试图在其上安装依赖项时，`express`已经发布了一个新版本，我可以下载最新版本。

`caret`符号[确切地告诉我们](https://stackoverflow.com/a/22345808/1955940)。

上面的问题是，如果版本 4.17.x 包含一个 bug，我的本地安装将失败，但发行商的将继续在以前的版本上正常工作。

同样的事情可能发生在生产环境中，而您不知道它为什么会失败。

在 NPM 版本 5 之前，您将使用`shrinkwrap`。它与`package-lock.json`不同，因为它允许在 NPM 注册中心与你的模块一起发布，而`package-lock.json`则不允许。

如果所有成员都可以使用 NPM+5，那么对于未发布的项目，最好使用`package-lock.json`。

但是，如果您正在开发一个模块并打算发布它，您可能需要考虑是否希望客户端安装您指定的确切的依赖树，或者是否希望在这方面更加灵活。这里有一个关于这个主题的更详细的版本。

因此，`package-lock.json`将描述当前安装的确切依赖树。格式在 [NPM 文档 pag](https://docs.npmjs.com/files/package-lock.json#dependencies-1) e 中描述

通过将它提交到您的 VCS——这是您绝对应该做的——您可以回到历史中，从那时开始复制精确的依赖树。

确保总是将`package-lock.json`提交给你的 VCS，以便在任何给定的时间跟踪精确的依赖树。

这将确保下载您的项目并尝试安装依赖项的所有客户端都将获得完全相同的依赖树。此外，它将确保您能够检查以前的提交并复制每个提交的依赖状态。

## `package.json`对`package-lock.json`

确保不要直接改`package-lock.json`。这是由 NPM 自动处理的。它反映了对`package.json`到`package-lock.json`的更改，并使其保持最新。

但是，这只有在您使用 NPMs 的 CLI 进行更改时才会发生。如果手动更改`package.json`，就不要指望`package-lock.json`会更新。始终使用 CLI 命令，如`install`、`uninstall`等。

## 如何使用 NPM 命令行界面

当你第一次在一个新项目中使用它时，NPM 会自动生成一个`package-lock.json`。

然后，你可以像平常一样使用 NPM。

### npm 安装(使用特定模块作为参数)

`install`可以与要安装的模块的名称一起用作参数，这将改变`package.json`和`package-lock.json`，因为依赖关系树将会改变。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

考虑下面的例子:

```
npm install express body-parser cors
```

### npm 安装(无参数)

`install`将尝试安装与`package-lock.json`相关的所有依赖项。

这里的一个关键点是，`install`可以改变`package-lock.json`,如果它注册它是过时的。

例如，如果有人手动更改`package.json`——比方说，他们删除了一个包，因为这只是删除一行——下次有人运行`npm install`时，它将更改`package-lock.json`以反映前一个包的删除。

这可能很棘手。想象一下，提取项目的最新版本，运行`npm install`来更新，却发现你的树中立即有一堆毫无意义的变更。

也很有可能你的树中的变化对审查你的变化的人没有意义。

### npm 卸载

类似于`install`,但是要移除的模块名称作为参数。这将改变`package.json`和`package-lock.json`。

### npm 更新

`update`将读取`package.json`以找到任何可以更新的依赖项。随后，它将构建一个新的依赖树并更新`package-lock.json`。

还记得语义版本吗？假设我们的`package.json`中有一个依赖项，表示为`^1.4.5`。

`^`字符告诉 NPM 检查在`1.X.X`作用域下是否有更新的版本，如果有，就安装它。同样的，`~`角色只会升级到热补丁，或`1.4.X`。

您也可以省略特殊字符，保留一个固定的版本，这使得`package-lock.json`不那么有用(但不是没有用)。

### npm ci

`ci`将安装与`package-lock.json`相关的所有依赖项，类似于`install`。这里的关键区别在于，它在任何情况下都不会改变`package-lock.json`。

其目的是供环境使用，例如构建服务器，其中安装以自动方式进行。

## 结论

使用`package-lock.json`时，请记住以下要点:

不要使用不带参数的`npm install`来获取依赖关系——为此使用`npm ci`。您可以使用`npm install`来安装特定的依赖项。

当您只想使用本地依赖关系树时，可以在任何地方使用`npm ci`——甚至在您的本地开发环境中。

做一个重复的任务，比如一个月一次，来更新你的依赖。(或者，您可以使用类似于[dependent bot](https://dependabot.com/)的服务，但要确保您有良好的测试覆盖率)。

这样，您可以确保您的依赖项是最新的，并避免产生技术债务。

## 更多参考

插头

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)