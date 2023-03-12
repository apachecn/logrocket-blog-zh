# 为什么不应该将 Blazor 与其他 JavaScript SPA 框架进行比较

> 原文：<https://blog.logrocket.com/why-you-shouldnt-compare-blazor-to-other-javascript-spa-frameworks/>

当在多种事物之间做出决策时——从物理对象到软件——您需要确保这些对象都是相互关联的，或者有助于实现相似的目标，因此是可比较的。

在比较框架时尤其如此。要进行出色、公平的比较，需要比较同一类别、具有相同基本特征的事物。所有的 SPA 框架/库都有相同的目标， [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) 也不例外。

但是有一些差异可能会让您重新考虑将其与其他 JavaScript 框架进行比较。原因如下。

## 布拉索是什么？

Blazor 是一个 web 框架，用 C#和 Razor 构建现代的、交互式的基于 web 的 ui。Blazor 可以运行在 [WebAssembly](https://blog.logrocket.com/webassembly-how-and-why-559b7f96cd71/) 或者服务器上。它也是第一个也是唯一一个利用 WebAssembly 的商业支持前端框架。

通常，当我们谈到前端开发时，人们会立即想到 JavaScript，但有了 Blazor，事情就不同了。怎么会？因为:

1.  你用 C#和 [Razor](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-5.0) 编写你的应用程序
2.  。NET 编译器将您的代码编译成。NET [CIL](https://en.wikipedia.org/wiki/Common_Intermediate_Language) (通用中间语言)
3.  您的应用程序附带了[单声道 WASM 运行时(BlazorWASM)](https://github.com/mono/mono/blob/main/sdks/wasm/README.md)
4.  你的浏览器执行 Mono 的二进制文件和你的应用程序[程序集](https://docs.microsoft.com/en-us/dotnet/standard/assembly/)

我将在本文的后面介绍 BlazorServer，让我们先讨论 WebAssembly。

## 正在执行。Blazor 和 WebAssembly 中的. NET 代码

为了查看实际操作中的差异，我使用了默认的 Blazor 模板。我有[。NET 5](https://dotnet.microsoft.com/download) 安装在我的机器上。您可以通过下面的命令行来搭建项目:

```
dotnet new blazorwasm

```

![File Folder Structure of a Blazor Project](img/9adf2e784f38afea6e64721ddcbbedde.png)

File folder structure of a Blazor project.

要查看它如何编译项目，请执行以下命令:

```
dotnet publish -c Release -o ./output

```

它编译并整理。NET 输出程序集来减小应用程序的大小。在输出文件夹 **_framework 文件夹**中，您会看到所有的程序集。对于具有. NET 背景的人来说，大多数 DLL 文件都是熟悉的。

![Application Shipped .NET Runtime Assemblies](img/2d33bc42da909ec5e5503dce6fd83d4d.png)

The application shipped with .NET runtime assemblies.

你可能会问 WebAssembly 在哪里。注意，在这些 dll 的旁边，有一个大小为 2.65MB 的`dotnet.wasm`文件；那就是 dotnet 运行时本身。要了解该应用程序的外观和工作方式，请观看以下视频:

![Blazor in Action](img/0433a8ae96627fa00d17eb668eef06b3.png)

Blazor in action and just 512kb loaded!

它加载所有需要的东西并执行。网络代码在后面。

## 避免与其他 SPA 框架直接比较的原因

### 1.WASM

Blazor 的输出是 WASM，不像其他 SPA 框架/库(Angular，Vue，React 等。).WASM 是新的标准和未来。浏览器不是在浏览器 JS 引擎中运行你的代码，而是以[二进制格式](https://webassembly.github.io/spec/core/binary/index.html)执行你的应用程序。

。NET/Mono 运行时编译成 WASM，项目的其余部分仍然是。NET 程序集 DLL 文件。的。NET 团队和社区正在努力交付 WASM AOT，这样你就可以将整个项目编译成 WASM 字节码。

WASM 很快，而且已经编译好了。它不需要解析或解释，浏览器直接运行它。和原生代码一样快。它非常适合图像编辑、3D 渲染、视频游戏、加密等性能密集型应用。

### 2.的好处。NET 和 C#

英国丰富的图书馆。NET 生态系统:有大量[强大的库](https://www.nuget.org/)，开发者正在桌面或后端应用上使用。使用 Blazor，您也可以在前端应用程序中使用它们。

**C#，静态类型语言** : C#不言自明——我不需要推广。它很强大，由于它的本质和已经存在的工具，重构 C#和。NET 代码库非常容易，而且失败的几率很小。

**遵循设计模式架构和更简洁的代码**:已经在使用多个库来实现更好的应用结构和设计？需要[依赖注射](https://docs.microsoft.com/en-us/aspnet/core/blazor/fundamentals/dependency-injection?view=aspnetcore-5.0&pivots=webassembly)吗？反思？[联发科](https://github.com/jbogard/MediatR)模式怎么样？静态分析仪？一切都已经存在于布拉索。它成熟、久经考验，是行业标准。

**共享代码库**:使用的好处。WASM 在这里展示了它的价值。您的后端已经有了不同的层和服务。NET 应用程序。从后端到前端使用共享代码不会有任何问题。

例如，考虑这样一个场景，您已经为您的输入编写了一个验证和安全层，因为您已经为后端编写了它们。为什么要为你的前端重写它们呢？使用 Blazor，只需引用同一个项目，并像为前端编写一样使用它。

**消除前端和后端开发之间的差距**:本着共享代码库的心态，开发前端和后端应该没有什么不同——至少在理论上是这样。

**几乎没有 JavaScript** :你可以用同一种语言为后端和前端编写整个应用程序。您可能会想，“*好吧，但是对于节点应用程序来说这也是可能的；后端和前端都是用 ES/TS/JS 写的。浏览器本身理解 JS/ES，所以它们的后端和前端语言对于节点开发者来说是一样的。*

但与 Blazor 不同的是，你用 C#编写你的应用程序，它将作为 WASM 和。同样的代码，没有任何像 babel 和 webpack 那样的魔法。不使用 JS 可能对不熟悉前端开发的默认应用程序和开发人员有利，因为它几乎对所有事情都使用相同的语言。

我说“差不多”是有原因的。WASM 代码还不能直接访问 DOM，但是有一些变通方法可以实现。Blazor 支持 JS[interop](https://docs.microsoft.com/en-us/aspnet/core/blazor/call-javascript-from-dotnet?view=aspnetcore-5.0)；有了它，就很容易从。NET 在浏览器里面。

## 3.将传统或桌面应用程序移植到网络的可能性

有了 Blazor，一切都与 WASM 和. NET 有关。对于拥有重要应用程序但还不是 web 应用程序的公司和开发人员来说，他们可以通过重新设计 GUI 部分，将其应用程序移植到 web 上！

甚至在 C++、Rust 等其他语言中。，有对 WASM 本土的支持。

## 4.BlazorServer

[BlazorServer 不同于服务器端渲染。](https://docs.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-5.0)您可以在服务器模式的帮助下在您的 BlazorWasm 项目上激活它，这意味着所有的逻辑将在服务器上执行，并且更改将反映在前端。

![How Blazor Server Works](img/0498706fc53ddd33da1eaf8c9ce33b43.png)

How Blazor server works – [Microsoft Docs](https://docs.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-5.0#blazor-server).

我不太喜欢这个功能，但它对许多情况有很大的帮助。它更安全，因为一切都在服务器上运行，但有延迟。

这个特性可以帮助开发者和公司节省一些应用开发的时间。他们同时开发前端和后端，它可以托管在 Docker 容器上。

如果您想了解更多信息，请阅读来自的文档。网队。

## 结论

WASM 和。NET 是 Blazor 的两个功能，这使得它很难与其他 SPA 框架和库进行比较。每个工具都有缺点和缺点，但最终，他们试图交付一个高质量的工具集，使开发人员能够做出优秀的产品。

在我看来，Blazor 非常出色，尤其是对于那些想要将他们的遗留应用程序迁移到 web 的公司来说。它为开发人员提供了利用。NET 来创建健壮的应用程序。

使用 Blazor 与使用其他框架没有什么不同，但是请记住，使用 Blazor，您将使用 C#进行开发，并且在项目中使用一些 JavaScript 库时可能会遇到问题。

希望创建了优秀 Blazor 组件的社区和公司将继续添加健壮的工具。web 开发的前景是光明的。不管是 WASM 还是现代的 JS/ES web 应用程序。结果是您和您的团队开发出一个健壮的应用程序。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。