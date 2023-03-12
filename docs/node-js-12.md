# Node.js 12:服务器端 JavaScript 的未来

> 原文：<https://blog.logrocket.com/node-js-12/>

## 介绍

自从 2009 年在[发布以来，Node.js](https://en.wikipedia.org/wiki/Node.js) 已经成为一项改变游戏规则的技术。简而言之，它允许开发人员使用 JavaScript 在服务器端运行脚本，在页面被发送到用户的 web 浏览器之前生成动态 web 内容。因此，Node.js 代表了一种“JavaScript 无处不在”的范式，围绕一种编程语言统一了 web 应用程序开发，而不需要服务器端和客户端脚本使用不同的语言。

如果你像我一样是 JavaScript 和 Node.js 的粉丝，你会很兴奋地知道它会变得更好。

## 节点 12 新增和改进

为什么 JavaScript 会变得更好？Node.js 12 几个月前刚掉了。

2019 年 4 月 23 日， [Node.js 12 正式上线](https://medium.com/@nodejs/introducing-node-js-12-76c41a1b3f3f)，各地 JavaScript 爱好者欢欣鼓舞。让我们明确一点，这不仅仅是一次常规的旧版本更新，这是一次带有一些重大升级的大检修，让我们来看看亮点列表。

## V8 JavaScript 引擎升级

除了每一个新版本的 JavaScript V8 引擎带来的[预期的性能调整和改进](https://v8.dev/blog/v8-release-74)之外，这次还有一些真正值得注意的升级。其中包括:

*   **[零成本异步堆栈跟踪](https://v8.dev/blog/v8-release-72#async-stack-traces)**——这将有助于用异步调用帧丰富`error.stack`属性，而不会给 V8 引擎增加额外的运行时间
*   **[参数不匹配的更快调用](https://v8.dev/blog/v8-release-74#faster-calls-with-arguments-mismatch)**–过去，V8 必须以相同的方式处理所有参数过多或过少的函数调用，这是以性能为代价的。现在，它足够聪明，知道什么时候可以跳过这一步，从而减少高达 60%的呼叫开销
*   **[更快的异步函数和承诺](https://v8.dev/blog/v8-release-73#faster-await)**——的确，使用异步实际上比现在的承诺快了两个额外的*微信号*，如果除了 async / await 为不熟悉承诺的开发人员提供的更同步风格的语法之外，你还需要一个理由
*   **[更快的 JavaScript 解析](https://v8.dev/blog/v8-release-72#javascript-parsing)**——在网页启动时，不到 10%的 V8 时间花在解析 JS 上。最新发布的 JavaScript 解析器将桌面解析速度提高了 30%

## TLS 1.3 带来更高的安全性

TLS 代表传输层安全性，是节点处理加密流通信的方式。

随着 Node.js 12 的发布， [TLS 获得了 1.3 版](https://developer.ibm.com/blogs/tls13-is-coming-to-nodejs/)的升级，这听起来微不足道，但实际上是一次重大更新，具有众多的性能和安全增强。虽然乍听起来有些违反直觉，但 TLS 1.3 实际上是一个比 TLS 1.2 更容易实现的协议，它更安全，更容易配置，并且可以更快地在应用程序之间协商会话。

通过使用 TLS 1.3，节点应用将增加最终用户隐私，同时通过减少 HTTPS 握手所需的时间来提高请求的性能。

底线:为使用它的每个人提供更好的安全性，并减少通信服务之间的延迟。这对我来说是一个重大的胜利。

## 正确配置的默认堆限制

现在，让我们谈谈一些较低层次的改进。到目前为止，JavaScript 堆大小默认为 V8 为浏览器设置的最大堆大小，除非手动配置。随着 Node.js 12 的发布，js 堆大小将基于可用内存进行配置，这确保了 Node 不会试图使用比可用内存更多的内存，并在内存耗尽时终止进程。

在处理大量数据时，告别内存不足错误——至少有时是这样。如果需要，旧的`--max-old-space-size`标志仍可用于设置不同的限制，但希望该功能将减少设置标志的需要。

## 默认的 http 解析器变成了 llhttp

许多人(包括我自己)不知道的是，Node 中使用的当前的`http_parser`库很难维护和改进，这就是 [llhttp](https://github.com/nodejs/llhttp#readme) 诞生的原因。该项目是 http_parser 到 TypeScript 的一个端口，然后通过 llparse 运行，生成 C 或 bitcode 输出。

结果是， [llhttp 比 http_parser 快 156%](https://github.com/nodejs/llhttp#performance) ，它用更少的代码行编写，所有性能优化都是自动生成的，这与 http_parser 手工优化的代码相反。

在 Node.js 12 中，他们决定第一次将默认解析器切换到 llhttp，并更彻底地对其进行测试。让我们希望，当有许多不同需求的不同应用程序正在试用它时，它会继续表现良好。

## 按需诊断报告

将话题转到调试上来，Node.js 12 中有一个新的实验性特性，允许用户按需或在某些触发事件发生时生成报告。

这种实时报告有助于诊断生产中的问题，包括崩溃、性能低下、内存泄漏、高 CPU 使用率、意外错误等。–这种东西通常需要几个小时甚至几天来调试、诊断和修复。

## 集成堆转储

这个版本中关于堆的另一个特性是集成的堆转储，它是 Node.js 12 附带的，已经内置，肯定会加快调试过程。

现在不需要安装新的模块来调查内存问题——只需通过命令行或 API 调用告诉 Node 您想要哪种 JSON 格式的诊断摘要，并解析您可以处理的所有信息。

## Node.js 中的本机模块变得更容易

从低级的改进退一步说，对于节点生态系统中的开发者和模块制造商来说，还有一些很酷的东西。

为 Node 制作和构建本机模块的工作在继续改进，包括更好地支持与工作线程结合的本机模块，以及 N-API 的第 4 版，这使得为本机异步功能配置自己的线程变得更加容易。

总而言之，这意味着特定于节点的模块的创建者和维护者维护这些模块几乎和纯 JavaScript 模块创建者一样容易。维护人员需要为他们希望他们的模块支持的每个 Node.js 版本重新构建分布式二进制文件，这增加了复杂性，现在 N-API 很大程度上抽象掉了这种复杂性。

## 工作线程即将到来–实验标志已被移除

工作线程，虽然从 Node 10 开始就已经存在，但不再需要启用标志——它们正在走出实验阶段。在 Node.js 11.7.0 之前，您不能访问工作线程模块，除非您在命令行中使用`--experimental-worker`标志启动`node`。

```
$ node -e "require('worker_threads'); console.log('success');"
internal/modules/cjs/loader.js:605
    throw err;
    ^
Error: Cannot find module 'worker_threads'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:603:15)
    at Function.Module._load (internal/modules/cjs/loader.js:529:25)
    at Module.require (internal/modules/cjs/loader.js:657:17)
    at require (internal/modules/cjs/helpers.js:22:18)
    at [eval]:1:1
    at Script.runInThisContext (vm.js:123:20)
    at Object.runInThisContext (vm.js:312:38)
    at Object. ([eval]-wrapper:6:22)
    at Module._compile (internal/modules/cjs/loader.js:721:30)
    at evalScript (internal/bootstrap/node.js:720:27)
$
$ node --experimental-worker -e "require('worker_threads'); console.log('success');"
success
$
```

工作人员在执行 CPU 密集型 JavaScript 操作时确实很出色，他们不会对 I/O 密集型工作有太大帮助。Node 的内置异步 I/O 操作比 Workers 更高效。

## 启动时间改进

Node.js 11 通过使用内置代码缓存支持，将[工作线程的启动时间减少了近 60%](https://github.com/nodejs/node/pull/24950) 。

Node 12 基于这一想法，在构建时提前为内置库**生成代码缓存，允许主线程使用代码缓存启动任何用 JavaScript 编写的内置库的初始加载。**

[最终结果是主线程的启动时间又加快了 30%](https://github.com/nodejs/node/pull/27161) ,你的应用程序将比以往任何时候都更快地为用户加载。

## ES6 模块支持，快到了🙌

我把最好的留到了最后。对我来说，最令人兴奋的功能之一是 [ES6 模块支持](https://medium.com/@nodejs/announcing-a-new-experimental-modules-1be8d2d6c2ff)——这是我们许多人一直在等待的。这个功能仍然是试验性的，[节点团队正在寻找试用者的反馈](https://github.com/nodejs/modules),但是想象一下能够从前端无缝过渡到后端 JavaScript 而不用担心这个世界。

以下是最新版本的`-–experimental-modules`包含的最佳内容:

*   ES2015 import 语句使用相对 URL`./examples.js`、绝对 URL`file:///opt.app/examples.js`、包名`example-package`或包内路径`example-package/lib/examples.js`引用 JavaScript 文件。

```
// relative urls
‘./examples.js’

// absolute URLs
‘file:///opt.app/examples.js’

// package names
‘example-package’

// paths within packages
example-package/lib/examples.js
```

*   `.js`文件中的导入和导出语法有效。最后，开发者可以指定默认导出`import test from` `'./examples'`、命名导出`import {example1, example2} from './examples'`和名称空间导出`import * as samples from './examples'`，就像自从 ES6 出现以来我们在传统 JavaScript 中所做的一样。

```
// default imports / exports
import test from ‘./examples’

// named imports / exports
import {example1, example2} from ‘./examples’

// namespace exports
 import * as samples from ‘./examples’
```

*   将`"type": "module"`添加到项目的`package.json`中，Node.js 会将项目中的所有`.js`文件视为 es 模块。这种方法允许 Node 使用`package.json`进行包级元数据和配置，类似于 Babel 和其他捆绑和配置工具已经使用的方式。
*   文件的显式扩展名将被视为以`.mjs`结尾的模块，而文件将被视为以`.cjs`结尾的普通文件。这些文件仍然使用`require`和`module.exports`类型的语法。

哈利路亚！我真的很期待这一天的到来。

## 节点 12 的新编译器和平台最低标准

最后但同样重要的是，对运行节点本身有了新的要求。

随着 Node.js 通过内部改进和升级到 [V8 引擎](https://blog.logrocket.com/how-javascript-works-optimizing-the-v8-compiler-for-efficiency/)的 C++来实现新的功能，Node.js 12 也有了新的最低要求。在 macOS 和 Windows 之外的平台上，代码库现在至少需要 GCC 6 和 glibc 2.17。发布的二进制文件使用这个新的工具链最小值，并包括新的编译时性能和安全性增强。

如果你使用 Mac 或 Windows 机器，你应该没问题:运行 Node.js 11 的 Windows 最低要求是一样的，Mac 用户至少需要 [Xcode 8](https://developer.apple.com/xcode/) 和最低[10.10](https://support.apple.com/downloads/OS-X-Yosemite)“Yosemite”的 macOS。来自 nodejs.org 的 Linux 兼容二进制文件将支持 [Enterprise Linux 7](https://developers.redhat.com/products/rhel/download) 、 [Debian 8](https://www.debian.org/releases/jessie/debian-installer/) 和 [Ubuntu 14.04](http://releases.ubuntu.com/14.04/) ，但是在原生不支持 GCC 6 的系统上定制工具链可能是必要的。我相信你会很快找到需要什么的。

## 结论

是的，Node.js 只有 10 年的历史，是的，它是单线程的，是的，它不像其他一些编程语言那样被广泛采用和利用，但 Node 拥有其他编程语言无法拥有的东西:[它是用 JavaScript 构建的，可以在客户端和服务器端运行](https://en.wikipedia.org/wiki/Node.js)。

致力于支持和改进 Node [的团队和公司都是业内最优秀、最聪明的](https://foundation.nodejs.org/about/members)。Node 继续从核心 JavaScript 和其他语言中学习，挑选合适的部分整合到自身中，成为开发人员和应用程序等越来越好的平台。

Node.js 12 带来了一些非常令人兴奋的改进，比如 ES6 模块支持、更好的应用程序安全性和更快的启动时间。虽然它要到 2019 年 10 月才会进入 [LTS(长期支持)模式](https://github.com/nodejs/release#release-schedule)，但我很想挖掘这些新功能，看看团队还能想出什么来继续让这个平台成为一个伟大的服务器端解决方案。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.