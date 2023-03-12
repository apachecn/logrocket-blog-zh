# Deno 是什么，和 Node.js 有什么不同？

> 原文：<https://blog.logrocket.com/what-is-deno/>

***编者按**:本指南最后一次更新是在 2021 年 3 月，以反映最新 Deno 版本 [Deno v1.7.0](https://deno.land/posts/v1.7) 中引入的变化，并包括更新的 Deno 与 Node.js 性能比较。*

Ryan Dahl， [Node.js](https://blog.logrocket.com/use-cases-for-node-workers/) 的创作者，于 2018 年 5 月正式发布了 [Deno](https://deno.land/) 。这个新的 JavaScript 运行时旨在修复 Node.js 中的一长串固有问题。

不要误解我的意思:Node 本身就是一个很棒的服务器端 JavaScript 运行时，这主要归功于它庞大的生态系统和 JavaScript 的使用。然而，Dahl 在 2018 JSConf EU 上承认，有几件事他应该考虑得更多——安全性、模块和依赖性，等等。

[https://www.youtube.com/embed/M3BM9TB-8yA](https://www.youtube.com/embed/M3BM9TB-8yA)

视频

在他的辩护中，他无法想象这个平台会在如此短的时间内增长多少。此外，早在 2009 年，JavaScript 还是这种人人取笑的奇怪的小语言，它的许多功能还没有出现。

## 什么是德诺？

[Deno](https://github.com/denoland/deno) 是一个安全的 [TypeScript](https://blog.logrocket.com/writing-idiomatic-typescript/) 运行时，构建于 V8 之上，V8 是用于 JavaScript 的 Google 运行时引擎。

Deno 由以下材料制成:

*   Rust (Deno 的内核用 Rust 编写，Node 的用 C++)
*   Tokio(用 Rust 编写的事件循环)
*   TypeScript (Deno 支持 JavaScript 和现成的 TypeScript)
*   V8(谷歌在 Chrome 和 Node 等平台上使用的 JavaScript 运行时)

 [https://www.youtube.com/embed/AoAXcW2-LNA?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/AoAXcW2-LNA?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 为什么用 Deno？

Deno 的特性旨在改进 Node.js 的功能。让我们仔细看看使 Deno 成为 Node 的一个有吸引力的替代方案的一些主要特性。

### Deno 中的安全性(权限)

Deno 最重要的特性之一是它对安全性的关注。

与 Node.js 相反，Deno 默认情况下在沙箱中执行代码，这意味着运行时无权访问:

*   文件系统
*   网络
*   其他脚本的执行
*   环境变量

让我们来看看许可系统是如何工作的。

```
(async () => {
 const encoder = new TextEncoder();
 const data = encoder.encode('Hello world\n');

 await Deno.writeFile('hello.txt', data);
 await Deno.writeFile('hello2.txt', data);
})();
```

该脚本创建了两个名为`hello.txt`和`hello2.txt`的文本文件，其中包含一条`Hello world`消息。代码是在沙箱中执行的，所以它不能访问文件系统。

还要注意，我们使用的是 Deno 名称空间，而不是 fs 模块，就像在 Node.js 中一样。通过使用名称空间，我们失去了浏览器兼容性，这将在后面讨论。

当我们通过执行以下命令来运行它时:

```
deno run write-hello.ts
```

系统会提示我们以下信息:

```
⚠️Deno requests write access to "/Users/user/folder/hello.txt". Grant? [a/y/n/d (a = allow always, y = allow once, n = deny once, d = deny always)]
```

我们实际上被提示了两次，因为来自沙箱的每个调用都必须请求许可。当然，如果我们选择了`allow always`选项，我们只会被询问一次。

如果我们选择`deny`选项，将会抛出`PermissionDenied`错误，并且由于我们没有任何错误处理逻辑，流程将会终止。

如果我们使用以下命令执行脚本:

```
deno run --allow-write write-hello.ts
```

没有提示，两个文件都被创建。

除了文件系统的`--allow-write`标志之外，还有`--allow-net`、`--allow-env`和`--allow-run`标志，分别用于启用网络请求、访问环境和运行子进程。

### Deno 模块

Deno 就像浏览器一样，通过 URL 加载模块。当许多人在服务器端看到带有 URL 的 import 语句时，他们一开始会感到困惑，但是它实际上是有意义的——请耐心听我说:

```
import { assertEquals } from "https://deno.land/std/testing/asserts.ts";
```

你可能会问，通过 URL 导入包有什么大不了的？答案很简单:通过使用 URL，Deno 包可以在没有像`npm`这样的集中注册中心的情况下分发，最近这个注册中心出现了很多问题，所有问题都在这里[解释](https://www.youtube.com/watch?v=MO8hZlgK5zc)。

通过 URL 导入代码，我们使得包创建者可以在他们认为合适的地方托管他们的代码——这是去中心化的最好体现。不再有`package.json`和`node_modules`。

当我们启动应用程序时，Deno 下载所有导入的模块并缓存它们。一旦它们被缓存，Deno 将不会再次下载它们，直到我们用`--reload`标志明确要求它。

这里有几个重要的问题要问:

#### 如果一个网站瘫痪了怎么办？

由于它不是一个集中的注册中心，托管该模块的网站可能会因为许多原因而关闭。依赖于它在开发期间——或者更糟，在生产期间——是有风险的。

正如我们之前提到的，Deno 缓存下载的模块。由于缓存存储在我们的本地磁盘上，Deno 的创建者建议在我们的版本控制系统(即 g it)中检查它，并将其保存在存储库中。这样，即使当网站关闭时，所有的开发者仍然可以访问下载的版本。

Deno 将缓存存储在环境变量`$DENO_DIR`下指定的目录中。如果我们不自己设置变量，它将被设置为系统默认的缓存目录。我们可以在本地存储库中的某个地方设置`$DENO_DIR`,并将其签入版本控制系统。

#### 我必须一直通过网址导入吗？

不断地输入 URL 会非常乏味。谢天谢地，Deno 为我们提供了两个选择来避免这样做。

第一个选项是从本地文件重新导出导入的模块，如下所示:

```
export { test, assertEquals } from "https://deno.land/std/testing/mod.ts";
```

假设上面的文件叫做`local-test-utils.ts`。现在，如果我们想再次使用`test`或`assertEquals`函数，我们可以像这样引用它:

```
import { test, assertEquals } from './local-test-utils.ts';
```

所以它是否从 URL 加载并不重要。

第二个选项是创建一个导入映射，我们在一个 JSON 文件中指定它:

```
{
   "imports": {
      "http/": "https://deno.land/std/http/"
   }
}
```

然后像这样导入它:

```
import { serve } from "http/server.ts";
```

为了让它工作，我们必须通过包含`--importmap`标志来告诉 Deno 关于导入映射的信息:

```
deno run --importmap=import_map.json hello_server.ts
```

#### Deno 中的包版本控制

版本控制必须得到包提供者的支持，但是从客户端来看，它只是在 URL 中设置版本号，如下所示:

```
https://unpkg.com/[email protected]/dist/liltest.js
```

### Deno 浏览器兼容性

Deno 的目标是兼容浏览器。从技术上讲，当使用 ES 模块时，我们不必使用任何像 webpack 这样的构建工具来使我们的应用程序准备好在浏览器中使用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然而，像 Babel 这样的工具会将代码转换到 JavaScript 的 ES5 版本，因此，代码甚至可以在不支持该语言所有最新特性的旧浏览器中运行。但这也是以在最终文件中包含大量不必要的代码和输出文件膨胀为代价的。

由我们来决定我们的主要目标是什么，并做出相应的选择。

### Deno 支持现成的类型脚本

Deno 使得使用 TypeScript 变得容易，不需要任何配置文件。尽管如此，用普通的 JavaScript 编写程序并毫无困难地用 Deno 执行它们是可能的。

## Deno 生产就绪了吗？

一段时间以来，Deno 一直在稳步增长。Deno v1.0 于 5 月 13 日大张旗鼓地正式发布，此后一直稳步增长。

撰写本文时最新的稳定版本是 2021 年 1 月 19 日发布的 [Deno v1.7.0](https://github.com/denoland/deno/releases) 。Deno 的最新版本具有改进的编译和数据 URL 功能。新版本的亮点包括使用`deno compile`从稳定的、受支持的架构(包括 Windows x64、MacOS x64 和 Linux x64)中交叉编译的能力。此外，`deno compile`现在生成的二进制文件比 Deno v. 1.6 生成的要小 40–60%，并且二进制文件具有内置的 CA 证书、自定义 V8 标志、锁定权限等等。

Deno v.1.7 中其他值得注意的特性包括支持数据 URL，支持用`deno fmt`格式化 Markdown 文件，以及用`--location`标志为脚本设置文档位置。了解更多关于[Deno](https://www.infoworld.com/article/3604740/deno-17-brings-compilation-improvements.html)的最新更新。

通过这种去中心化的方法，Deno 采取了必要的步骤，将 JavaScript 生态系统从集中的包注册中心(npm)中解放出来。

## Deno 与 Node.js:性能

由于 Deno 相对年轻，因此很难比较 Node.js 和 Deno 的性能。大多数可用的基准测试都非常简单，比如将消息记录到控制台，因此我们不能仅仅假设性能会随着应用程序的增长而保持不变。

我们可以肯定的一点是，Node.js 和 Deno 都使用相同的 JavaScript 引擎，谷歌的 V8，所以在运行 JavaScript 本身时，性能不会有任何差异。

唯一可能影响性能的区别是 Deno 是基于 Rust 构建的，而 Node.js 是基于 C++构建的。Deno 的团队为每个版本发布了一组基准测试,展示 Deno 的性能，在许多情况下与 Node 进行比较。截至 2021 年 3 月，似乎 Node.js 在 HTTP 吞吐量方面表现更好，而 Deno 在更低的延迟方面表现更好。总的来说，表现很像。

关于 Deno 的一级类型脚本支持的一个常见误解是，由于类型检查，它会以某种方式影响运行时性能。我找不到这种误解的来源，但是，任何了解 TypeScript 如何工作的人都会告诉你，TypeScript 只在翻译过程中检查类型。因此，它不可能对任何类型的运行时性能产生负面影响。一旦我们使用了 [bundle 命令](https://deno.land/manual/tools/bundler)，输出就是一个 JavaScript 文件。

总而言之，这两个运行时都非常快，并利用大量优化来提供最佳性能。我不认为 Deno 或 Node.js 能够明显胜过对方。

## 应该用 Deno 而不是 Node.js 吗？

Deno 的目标不是做节点替代者，而是替代者。我要说的是，大多数开发人员对 Node.js 的发展方式感到满意，并没有真的想改变现状。

Deno 对安全性的关注以及将整个代码库捆绑到单个文件中的可能性为 Deno 提供了一个很好的机会，使其成为 JavaScript 开发人员在创建实用程序脚本时的首选工具，否则这些脚本可能是用 Bash 或 Python 等语言编写的。

如果你想决定选择哪一个，我会选择 Node。它是成熟和稳定的，因此不会有任何可能对应用程序的开发产生负面影响的意外行为。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.