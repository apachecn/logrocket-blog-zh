# 为什么你应该使用电子博客

> 原文：<https://blog.logrocket.com/why-use-electron-alternative/>

在 Electron 项目之前，跨平台桌面应用程序开发并不是特别容易，因为开发人员经常必须为每个操作系统维护单独的代码库。即使有一些通用的 API 来构建跨平台的应用程序，创建定制的 GUI 元素也很困难。

Electron 引入了一种新的方法，用一个代码库和 web 技术(HTML、JavaScript 和 CSS)来开发跨平台的桌面应用程序。开发人员可以用他们喜欢的前端框架构建高度灵活和用户友好的 GUI。此外，他们可以用 HTML 和 CSS 轻松地定制 GUI 控件。此外，Electron 有一个全功能的 API 用于本机系统操作。

然而，电子应用程序在用户计算机上消耗的资源高于平均水平，这意味着许多开发人员正在寻找轻量级的电子替代品。在本文中，我将讨论电子的两种替代方案:Tauri 和 Neutralino.js。

## 什么是电子？

[Electron](https://www.electronjs.org/) (原名 Atom Shell)是 GitHub 打造的跨平台应用开发框架。Electron 将 Chromium 项目和 Node.js 集成到一个包中，并公开了一个通用的 JavaScript API，用于显示对话框、发送通知、创建通知和托盘图标等本地特性。

开发人员可以使用他们喜欢的前端框架和 web UI 工具包来创建应用程序的 GUI。例如，我们可以使用 React 和 Material UI 来构建应用程序的前端。像文件处理这样的原生功能可以在 Node.js 的帮助下完成。正如前面提到的，我们可以使用 Electron 的 JavaScript API 来处理所需的操作系统功能。

如果我们需要显示一个消息框，我们可以使用电子 API 中的`dialog.showMessageBox`方法。我们不需要担心特定于平台的代码，因为 Electron 有针对 Linux、macOS 和 Windows 的对话框实现。

## 电子的优势

如今，大多数开发人员使用 Electron 开发跨平台应用程序，因为它有以下令人印象深刻的优势:

### 单一代码库

电子为本地操作系统特性提供了灵活的抽象。因此，开发人员可以为他们的跨平台应用程序维护一个单一的代码库，该应用程序可以在大多数流行的平台上运行。

### 快速功能交付

在电子版中用 HTML 和 CSS 创建用户界面是小菜一碟；这些 web 技术让您可以自由地创建任何定制的 GUI 元素。此外，Node.js 有一个庞大的库生态系统，因此您可以非常快速地添加类似本机的功能。

### 成熟的框架

Electron 最初是在大约八年前发布的，因此它拥有强大的用户基础和社区。还有一些有益的内置功能，比如自动更新。像微软这样的知名公司选择 Electron 来构建他们的跨平台应用程序。例如，Visual Studio 代码、团队和 Skype 都是在 Electron 的基础上构建的。

## 电子中隐藏的问题

虽然总体上令人印象深刻，但电子框架有几个关键的性能问题。

Electron 将 Chromium 和 Node.js 捆绑到最终的应用程序包中，所以即使你是通过精心选择前端库和框架来编写一个简单轻量级的应用程序，你的应用程序也会变得臃肿。

Chromium 和 Node.js 是复杂的项目，这些模块将消耗您计算机上高于平均水平的资源。换句话说，用 Electron 构建的应用程序将占用大量的物理内存和磁盘空间。

此外，由于高资源消耗，电子应用程序会很快耗尽笔记本电脑的电池。由于上面提到的 Electron 的关键性能问题，用 Electron 开发的跨平台应用程序经常变成臃肿的软件。

强大的硬件可以对普通用户隐藏这个缺点。然而，一旦用户开始运行多个电子应用程序，就很容易感觉到这种隐藏的性能问题。

## 电子替代品

一段时间以前，几个替代方案如[electronino](https://github.com/pojala/electrino)和 [DeskGap](https://deskgap.com/) 作为电子性能问题的解决方案出现了。两个项目都试图通过使用操作系统的 webview 而不是 Chromium 来减少最终的包大小。不幸的是，这两个项目无法在成熟的电子框架下完成。

然而，有两个趋势轻量级的电子替代品: [Tauri](https://tauri.studio/en/) 和 [Neutralino.js](https://neutralino.js.org/) 。这两个项目都试图通过用更好的轻量级替代方案替换 Chromium 和 Node 来解决 Electron 的性能问题。

两个项目都使用众所周知的 [webview](https://github.com/webview/webview) 库来呈现 HTML 和 CSS，而不是 Chromium。webview 库使用现有的 web 浏览器组件进行渲染。例如，它将在基于 Linux 的平台上使用 gtk-webkit2，在 macOS 上使用 Cocoa Webkit，在 Windows 上使用 Edge/MSHTML。

## Tauri 是什么？

Tauri 是一个用 Rust 编写的轻量级、跨平台的桌面应用程序开发框架。开发者可以通过使用他们喜欢的前端框架来制作 Tauri 应用程序的前端。

我们可以将 Tauri 的 JavaScript API 用于本地平台特性，比如文件处理和显示对话框。Tauri 的另一个优点是，我们可以在 Rust 中实现自己的本机 API，并将其作为 JavaScript API 公开给 webview。

让我们用 Tauri 编写一个简单的跨平台应用程序。

### 装置

Tauri 应用程序可以构建在任何流行的平台上。在这个演示中，我构建了 Linux。

首先，我们需要通过在终端中输入以下命令来安装所需的库:

```
$ sudo apt update && sudo apt install libwebkit2gtk-4.0-dev \
    build-essential \
    curl \
    wget \
    libssl-dev \
    appmenu-gtk3-module \
    libgtk-3-dev \
    squashfs-tools

```

之后，我们需要安装 Rust 编译器和货物包管理器:

```
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

```

最后，我们需要安装 Tauri CLI(确保您已经有 Node.js 和包管理器):

```
$ npm install -g tauri

```

### 发展

通过输入以下命令，可以创建一个全新的 Tauri 应用程序:

```
$ mkdir tauri-app && cd tauri-app
$ tauri create

```

输入`tauri create`命令后，CLI 将询问一系列配置应用程序的问题。在本例中，使用了默认值。应用名称为`tauri-app`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`tauri-app/src-tauri`目录由应用程序的后端代码组成。你可以把你的前端代码放到`tauri-app/dist`目录中。现在，我创建了`tauri-app/dist/index.html`，并输入了以下 HTML:

```
<h1 style="padding-top: 45vh; text-align: center;" >Hello Tauri!</h1>

```

我们可以通过简单地输入`tauri dev`来启动我们的应用程序。因为我没有使用开发服务器，所以我必须将`devPath`设置为`tauri.conf.json`文件中的`../dist`目录。

![A sample application created with Tauri](img/ed8b2ab76d1b881475fb78484b46fe15.png)

### 发布 Tauri 应用程序

Tauri 允许您为每个平台创建一个应用程序的二进制文件。只需输入`tauri build`命令即可。但是，如果您需要为 Linux 生成二进制文件，您必须从 Linux 计算机上运行这个命令。

## 什么是 Neutralino.js？

Neutralino.js 是一个用 C/C++编写的轻量级跨平台桌面应用开发框架。与 Tauri 类似，Neutralino 鼓励开发人员使用任何前端框架来构建应用程序的 GUI。它还为类似于 Electron 的本地操作提供了 JavaScript API。

让我们用 Neutralino 编写一个简单的跨平台应用程序。

### 装置

Neutralino 不需要任何额外的应用程序开发库。您需要做的就是在任何操作系统上安装它的 CLI:

```
$ npm install -g @neutralinojs/neu

```

### 发展

使用以下命令可以创建一个全新的 Neutralino 应用程序:

```
$ neu create neutralino-app

```

上面的命令将通过下载预构建的 JavaScript 模板来创建一个新项目。它还会为每个操作系统下载最新的预构建的 Neutralinojs 二进制文件。该示例应用程序的主视图(`app/index.html`)包含以下内容:

```
<h1 style="padding-top: 45vh; text-align: center;" >Hello Neutralinojs!</h1>

```

只需输入`neu run`命令即可启动应用程序。

![A sample application created with Neutralinojs](img/ba52c7f036554b21c05eead4c97c480a.png)

### 发布 Neutralino.js 应用程序

我们可以通过输入`neu build`命令为其他人发布我们的应用程序。该命令将在`dist`目录中生成二进制文件。

Neutralino 不像 Tauri 那样提供单一二进制创建支持。它将始终创建一个单独的资源文件以及特定于平台的二进制文件。

## 比较

同一个应用程序在 Electron、Tauri 和 Neutralino.js 上使用了以下资源。测量是在 Linux 平台上进行的:

| 比较因素 | 电子 | 托里 | 中性。js |
| --- | --- | --- | --- |
| 包大小(未压缩) | 200mb | 8mb | 2mb |
| 物理内存使用 | 大约 100mb | 大约 50mb | 大约 50mb |

## 结论

Electron、Tauri 和 Neutralino.js 将在 web 浏览器中呈现整个应用程序。然而，web 浏览器的呈现过程是复杂的。在基于 web 的 GUI 元素的绘制过程之前有几个步骤。

首先，将解析 HTML、CSS 内容。之后，浏览器将生成带有解析结果的 DOM 树。最后，web 浏览器将绘制由样式规则和 DOM 树组合而成的渲染树。

因此，这些框架不适合非常大的应用程序。在这种情况下，进行本地化或使用真正的本地框架(如 Flutter)可能是明智的选择。

根据以上对比，Tauri 和 Neutralino.js 的表现优于 Electron。电子应用程序消耗大量磁盘空间，因为每个应用程序都与 Chromium 和 Node 捆绑在一起。另一方面，Tauri 和 Neutralino.js 具有令人惊讶的轻量级包大小，因为这些框架重用了用户操作系统的系统 web 浏览器库。

电子版可以让你轻松地运行桌面应用程序，但是它会产生严重的性能问题，让用户感到沮丧。因此，是时候寻找替代方案了。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)