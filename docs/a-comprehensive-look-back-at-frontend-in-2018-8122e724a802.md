# 2018 年前端全面回眸——log rocket 博客

> 原文：<https://blog.logrocket.com/a-comprehensive-look-back-at-frontend-in-2018-8122e724a802/>

Web 开发一直是一个快速发展的领域——很难跟上所有浏览器的变化、库的发布和新的编程趋势，它们会在一年中淹没你的大脑。

这个行业每年都在变大，这使得一般的开发者很难跟上。所以让我们退一步，回顾一下 2018 年 web 开发社区发生了什么变化。

在过去的几年里，我们见证了 JavaScript 的爆炸性发展。随着互联网对全球经济变得更加重要，像谷歌和微软这样强大的商业利益相关者意识到他们需要更好的工具来创建下一代网络应用程序。

这导致了 JavaScript 自诞生以来最大的一波变化，从 ECMAScript 2015(又名 ES6)开始。现在每年发布的版本都给我们带来了令人兴奋的新特性，比如类、生成器、迭代器、承诺、全新的模块系统等等。

这开启了网络发展的黄金时代。许多当今最流行的工具、库和框架都是在 ES2015 发布后开始流行的。甚至在主流浏览器供应商支持新标准的一半之前， [Babel](https://babeljs.io/) 编译器项目就已经允许数千名开发者抢先一步，亲自体验新特性。

前端开发者第一次不再受制于他们公司支持的最老的浏览器，而是可以按照自己的节奏自由创新。三年过去了，ECMAScript 已经有了三个版本，这个 web 开发的新时代丝毫没有放缓的迹象。

## 新的 JS 语言特性

与之前的版本相比，ECMAScript 2018 在功能方面相当轻便，仅添加了[对象休息/传播属性](https://github.com/tc39/proposal-object-rest-spread)、[异步迭代](https://github.com/tc39/proposal-async-iteration)和 [Promise.finally](https://github.com/tc39/proposal-promise-finally) ，这些都已经被 Babel 和 [core-js](https://github.com/zloirock/core-js#stage-3-proposals) 支持了一段时间。[大多数浏览器](http://kangax.github.io/compat-table/es2016plus/#test-Asynchronous_Iterators)和 [Node.js](https://node.green/) 支持除 Edge 之外的所有 ES2018，Edge 只支持 Promise.finally .对于许多开发者来说，这意味着他们需要的所有语言功能都在他们支持的所有浏览器中得到支持——有些人想知道 Babel 是否真的有必要了。

### 新的正则表达式功能

JavaScript 一直缺少一些更高级的正则表达式特性，而 Python 等其他语言却有——直到现在。ES2018 增加了四项新功能:

*   [后视断言](https://github.com/tc39/proposal-regexp-lookbehind)，为早在 1999 年就出现在语言中的前视断言提供了缺失的补充。
*   [s (dotAll)标志](https://github.com/tc39/proposal-regexp-dotall-flag)，匹配除行结束符以外的任何单个字符。
*   [命名的捕获组](https://github.com/tc39/proposal-regexp-named-groups)，通过允许基于属性的捕获组查找，使得使用正则表达式更加容易。
*   [Unicode 属性转义](https://github.com/tc39/proposal-regexp-unicode-property-escapes)，这使得编写能够识别 Unicode 的正则表达式成为可能。

尽管这些特性中的许多已经有了解决方法和替代库，但没有一个能比得上本机实现提供的速度。

## 新的浏览器功能

今年发布了数量惊人的新 JavaScript 浏览器 API。几乎所有方面都有所改善，比如网络安全、高性能计算和动画。让我们按领域对它们进行分解，以便更好地了解它们的影响。

### web 程序集

尽管 WebAssembly v1 支持在去年被添加到了主流浏览器中，但它尚未被开发人员社区广泛采用。WebAssembly 组有一个[雄心勃勃的特性路线图](https://webassembly.org/docs/future-features/)，包括[垃圾收集](https://github.com/WebAssembly/gc)，ECMAScript 模块集成，以及[线程](https://developers.google.com/web/updates/2018/10/wasm-threads)等特性。也许有了这些特性，我们将开始看到 web 应用程序的广泛采用。

部分问题是 WebAssembly 需要大量的设置才能开始，并且许多习惯于 JavaScript 的开发人员不熟悉使用传统的编译语言。Firefox 推出了一个名为 [WebAssembly Studio](https://hacks.mozilla.org/2018/04/sneak-peek-at-webassembly-studio/) 的在线 IDE，让 WebAssembly 的入门变得尽可能简单。如果你想把它集成到现有的应用程序中，现在有很多工具可供选择。Webpack v4 增加了实验性的[内置支持](https://github.com/webpack/webpack/releases/tag/v4.0.0)，用于紧密集成到构建和模块系统中的 WebAssembly 模块，并具有源代码映射支持。

Rust 已经成为编译成 WebAssembly 的最受欢迎的语言。它提供了一个健壮的包生态系统，有 [cargo](https://github.com/rust-lang/cargo) ，可靠的性能，和一个[易学的](https://doc.rust-lang.org/book/)语法。已经有一个新兴的工具生态系统将 Rust 与 JavaScript 集成在一起。你可以使用 [wasm-pack](https://github.com/rustwasm/wasm-pack) 发布 Rust WebAssembly 包到 NPM。如果你使用 webpack，你现在可以使用 [rust-native-wasm-loader](https://github.com/dflemstr/rust-native-wasm-loader) 将 Rust 代码无缝集成到你的应用中。

如果您不想放弃 JavaScript 而使用 WebAssembly，那么您很幸运——现在有几个选项可供选择。如果你熟悉 TypeScript，有一个 [AssemblyScript](https://github.com/AssemblyScript/assemblyscript) 项目，它使用了官方的 [Binaryen](https://github.com/WebAssembly/binaryen) 编译器和 TypeScript。

因此，它可以很好地与现有的 TypeScript 和 WebAssembly 工具配合使用。 [Walt](https://github.com/ballercat/walt) 是另一个编译器，它坚持 JavaScript 语法(带有类似 TypeScript 的类型提示),并直接编译成 WebAssembly 文本格式。它没有依赖性，编译速度非常快，并且与 webpack 集成。这两个项目都在积极开发中，根据您的标准，它们可能不被认为是“生产就绪的”无论如何，它们都值得一试。

### 共用存储器

现代 JavaScript 应用程序经常在 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Worker) 中进行繁重的计算，以避免阻塞主线程和中断浏览体验。虽然这些工人已经存在好几年了，但是他们的局限性阻止了他们被更广泛的采用。工作人员可以使用 [postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage) 方法在其他线程之间传输数据，该方法要么克隆正在发送的数据(较慢)，要么使用[可传输对象](https://developer.mozilla.org/en-US/docs/Web/API/Transferable)(较快)。因此，线程之间的通信要么很慢，要么是单向的。对于简单的应用程序来说，这很好，但是它阻止了使用工人来构建更复杂的架构。

[SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) 和 [Atomics](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics) 是新特性，允许 JavaScript 应用程序在上下文之间共享固定的内存缓冲区，并对其执行原子操作。然而，在发现共享内存使浏览器容易受到之前未知的被称为[幽灵](https://meltdownattack.com/)的定时攻击后，浏览器支持被暂时移除。Chrome 在 7 月份重新启用了 SharedArrayBuffers，当时他们发布了一个缓解该漏洞的新安全功能。在 Firefox 中，它默认是禁用的，但可以被重新启用。Edge 已经[完全移除了支持](https://blogs.windows.com/msedgedev/2018/01/03/speculative-execution-mitigations-microsoft-edge-internet-explorer/#Yr2pGlOHTmaRJrLl.97)，微软也没有表示何时会重新启用。希望明年所有的浏览器都有缓解策略，这样这个关键的缺失功能就可以使用了。

### 帆布

Canvas 和 WebGL 等图形 API 到现在已经支持好几年了，但是一直局限于只在主线程中渲染。因此，渲染可能会阻塞。这导致了糟糕的用户体验。 [OffscreenCanvas](https://developer.mozilla.org/en-US/docs/Web/API/OffscreenCanvas#Asynchronous_display_of_frames_produced_by_an_OffscreenCanvas) API 通过允许您将画布上下文(2D 或 WebGL)的控制权转移给 web worker 来解决这个问题。然后，工作人员可以像平常一样使用 Canvas APIs，同时在主线程中无缝呈现，不会出现阻塞。

考虑到显著的性能节省，您可以期待图表和绘图库很快考虑支持它。浏览器支持目前仅限于 Chrome，Firefox 在幕后支持它，Edge 团队尚未公开表示支持。你可以期待它与 SharedArrayBuffers 和 WebAssembly 很好地配对，允许工作人员基于任何线程中存在的数据，从任何语言编写的代码进行渲染，所有这些都没有 janky 用户体验。这可能会让网络高端游戏的梦想成为现实，并允许在网络应用程序中使用更复杂的图形。

为 CSS 带来新的绘图和布局 API 的努力正在进行中。目标是向 web 开发人员公开 CSS 引擎的一部分，以揭开 CSS 的一些[“魔法”。W3C 的](https://blog.logrocket.com/new-horizons-in-css-houdini-and-the-paint-api-8b307cf387bb) [CSS Houdini 任务组](https://github.com/w3c/css-houdini-drafts/wiki)由来自主要浏览器厂商的工程师组成，在过去的两年里一直在努力工作，发布了[几个草案规范](https://drafts.css-houdini.org/)，它们现在处于设计的最后阶段。

CSS Paint API 是第一批登陆浏览器的，早在一月份就登陆了 Chrome 65。它允许开发人员使用类似上下文的 API 来绘制图像，这种 API 可以在 CSS 中需要图像的任何地方使用。它使用新的 [Worklet](https://drafts.css-houdini.org/worklets) 接口，这些接口基本上是轻量级的、高性能的 [Worker](https://developer.mozilla.org/en-US/docs/Web/API/Worker) 式的构造，用于专门的任务。像 worker 一样，它们在自己的执行上下文中运行，但与 worker 不同，它们是线程不可知的(浏览器选择它们在什么线程上运行)，并且它们可以访问渲染引擎。

使用 Paint Worklet，您可以创建一个背景图像，当包含它的元素发生变化时，它会自动重绘。使用 CSS 属性，您可以添加在更改时触发重新绘制的参数，并且可以通过 JavaScript 进行控制。除了 Edge 之外，所有的浏览器都承诺支持，但是现在，有一个[聚合填充](https://github.com/GoogleChromeLabs/css-paint-polyfill)。有了这个 API，我们将开始看到组件化的图像以类似于我们现在看到组件的方式被使用。

### 动画片

大多数现代 web 应用程序使用动画作为用户体验的重要部分。像谷歌的 Material Design 这样的框架已经使它们成为他们的设计语言的重要组成部分，认为它们对于创造富有表现力和易于理解的用户体验是必不可少的。考虑到其重要性的提升，最近有一股推动力量将一个更强大的动画 API 引入 JavaScript，这导致了 Web 动画 API (WAAPI)的出现。

正如 CSS-Tricks 指出的那样，WAAPI 提供了比 CSS 动画更好的开发体验，你可以轻松地记录和操作 JS 或 CSS 中定义的动画的状态。[浏览器支持](https://caniuse.com/#feat=web-animation)目前主要限于 Chrome 和 Firefox，但有一个[官方 polyfill](https://github.com/web-animations/web-animations-js/tree/master) 可以满足你的一切需求。

性能一直是网页动画的一个问题，这个问题已经通过引入[动画小工具](https://wicg.github.io/animation-worklet/)得到了解决。这种新的 API 允许复杂的动画并行运行，这意味着更高的帧速率动画不会受到主线程 jank 的影响。[动画工作小程序](https://blog.logrocket.com/using-animation-worklet-4a3914c19112)遵循与网络动画 API 相同的接口，但是在工作小程序执行上下文中。

它预计将在 Chrome 71(撰写本文时的下一个版本)和其他浏览器中发布。如果你今天想尝试一下，GitHub 上有官方的 [polyfill 和示例回购](https://github.com/GoogleChromeLabs/houdini-samples/tree/master/animation-worklet)。

### 安全性

Spectre 定时攻击并不是今年唯一的网络安全恐慌。NPM 固有的脆弱性在过去已经被写了很多，上个月我们得到了一个来自 T2 的警告。这并不是 NPM 本身的安全漏洞，而是一个被称为[事件流](https://www.npmjs.com/package/event-stream)的包，它被许多流行的包所使用。NPM 允许软件包作者将所有权转让给任何其他成员，黑客说服所有者将其转让给他们。黑客随后发布了一个新版本，该版本对他们创建的名为 [flatmap-stream](https://www.npmjs.com/package/flatmap-stream) 的包有新的依赖性，如果恶意包与 [copay-dash](https://www.npmjs.com/package/copay-dash) 包一起安装，该包的代码旨在窃取[比特币钱包](https://copay.io/)。

鉴于 NPM 的工作方式和社区随意安装看似有用的 NPM 软件包的倾向，这种攻击只会变得更加普遍。社区对软件包所有者寄予了很大的信任，这种信任受到了极大的质疑。NPM 用户应该知道他们正在安装的每个包(包括依赖项的依赖项)，使用一个锁文件来锁定版本，并注册类似 Github 提供的那些[安全警报。](https://blog.github.com/2017-11-16-introducing-security-alerts-on-github/)

NPM 意识到了社区的安全问题，他们在过去的一年里已经采取措施来改善这种状况。你现在可以用[双因素认证](https://blog.npmjs.org/post/166039777883/protect-your-npm-account-with-two-factor)来保护你的 NPM 账户，NPM v6 现在包括了一个[安全审计](https://docs.npmjs.com/auditing-package-dependencies-for-security-vulnerabilities)命令。

### 监视

[Reporting API](https://developers.google.com/web/updates/2018/09/reportingapi) 是一种新标准，旨在通过在出现问题时发出警报，让开发人员更容易发现他们应用程序的问题。如果您在过去几年中使用过 Chrome DevTools 控制台，您可能会看到*【干预】*警告消息，指出使用了过时的 API 或做了可能不安全的事情。这些信息仅限于客户端，但是现在您可以使用新的[reporting bserver](https://developers.google.com/web/updates/2018/07/reportingobserver)将它们报告给分析工具。

有两种报告:

*   [弃用](https://developers.google.com/web/updates/tags/deprecations)，当你使用一个过时的 API 时，它会警告你，并告诉你它什么时候会被删除。它还会告诉你文件名和它被使用的行号。
*   [干预](https://www.chromestatus.com/features#intervention)，当你以非预期的、危险的或不安全的方式使用 API 时，它会警告你。

而像 LogRocket 这样的工具可以让开发者洞察他们应用程序中的错误。到目前为止，第三方工具还没有任何可靠的方法来记录这些警告。这意味着问题要么被忽视，要么表现为难以调试的错误消息。Chrome 目前支持 ReportingObserver API，其他浏览器也将很快支持。

### 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

尽管 JavaScript 吸引了所有的注意力，但今年浏览器中还是出现了几个有趣的 CSS 新特性。

对于那些不知道的人，没有类似于 ECMAScript 的统一的 CSS3 规范。最后一个官方统一标准是 CSS2.1，CSS3 是指在此之后发布的任何标准。相反，每个部分都被单独标准化为一个“CSS 模块”。MDN 对每个模块标准及其状态有一个极好的概述。

截至 2018 年，一些较新的功能现已在所有主流浏览器中得到全面支持(这是 2018 年，IE 不是主流浏览器)。这包括 [flexbox](https://blog.logrocket.com/flexing-with-css-flexbox-b7940b329a8a) ，[自定义属性](https://caniuse.com/#feat=css-variables)(变量)，以及[网格布局](https://blog.logrocket.com/the-simpletons-guide-to-css-grid-1767565b3cf7)。

虽然在过去曾有过[关于在 CSS 中加入对嵌套规则的支持(比如 LESS 和 SASS)的讨论，但是那些提议没有任何进展。7 月份，W3C](https://tabatkins.github.io/specs/css-nesting/) [的 CSS 工作组决定对该提案再做一次研究，但还不清楚这是否是优先考虑的事情。](https://github.com/w3c/csswg-drafts/issues/2701#issuecomment-402392212)

### 节点. js

Node 在遵循 ECMAScript 标准方面继续取得出色进展，截至 12 月，他们[支持所有 ES2018](https://node.green/) 。另一方面，他们在采用 ECMAScript 模块系统方面进展缓慢，因此缺少了一个关键的功能，这个功能阻止了浏览器的功能对等，而浏览器已经支持 ES 模块一年多了。Node 实际上在 v11.4.0 中增加了[实验支持](https://nodejs.org/api/esm.html)的一个标志后面，但这要求文件使用新的。mjs 扩展，导致[担心](https://github.com/nodejs/modules/issues/57)采用缓慢以及这会对 Node 的丰富包生态系统产生什么影响。

如果您希望快速起步，并且不希望使用实验性的内置支持，Lodash 的创建者有一个有趣的项目，名为 [esm](https://medium.com/web-on-the-edge/tomorrows-es-modules-today-c53d29ac448c) ，它为节点 es 模块提供了比官方解决方案更好的互操作性和性能支持。

## 工具和框架

### 反应

React 今年有两个值得注意的版本。React 16.3 支持一套新的[生命周期方法](https://reactjs.org/blog/2018/03/29/react-v-16-3.html#component-lifecycle-changes)和一个新的官方[上下文 API](https://reactjs.org/blog/2018/03/29/react-v-16-3.html#official-context-api) 。React 16.6 增加了一个名为“悬念”的新功能，让 React 能够在组件等待数据获取或[代码分割](https://reactjs.org/docs/code-splitting.html#reactlazy)等任务完成时暂停渲染。

今年谈论最多的 React 话题是 [React Hooks](https://reactjs.org/docs/hooks-intro.html) 的推出。该提议旨在使编写更小的组件变得更容易，而不牺牲有用的功能，这些功能目前仅限于类组件。React 将提供两个内置钩子，一个是状态钩子，它让功能组件使用状态，另一个是[效果钩子](https://reactjs.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns)，它让你在功能组件中执行副作用。虽然没有从 React 中移除类的计划，但 React 团队显然希望钩子成为 React 未来的核心。在他们被宣布后，社区中有一个积极的反应([有些人可能会说被夸大了](https://twitter.com/dan_abramov/status/1057027428827193344))。如果你有兴趣了解更多，请查看[丹·阿布拉莫夫的博客文章](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)综合概述。

明年，React 计划发布一个名为[并发模式](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html#react-16x-q2-2019-the-one-with-concurrent-mode)的新特性(以前称为“异步模式”或“异步渲染”)。这将允许 React 在不阻塞主线程的情况下呈现大型组件树。对于具有深度组件树的大型应用程序，性能节省可能非常显著。目前还不清楚 API 到底是什么样子，但 React 团队的目标是尽快完成它，并在明年的某个时候发布。如果您对采用这一特性感兴趣，请通过采用 React 16.3 中发布的新生命周期方法来确保您的代码库是兼容的。

React 越来越受欢迎，根据 JavaScript 2018 年调查的状况，64%的受访者使用它并将再次使用它(自去年以来增加了 7.1%)，相比之下，[的 Vue](https://2018.stateofjs.com/front-end-frameworks/vuejs/) 为 28%(+9.2%)，而[的 Angular](https://2018.stateofjs.com/front-end-frameworks/angular/) 为 23%(+5.1%)。

### 网络包

[Webpack](https://webpack.js.org) 4 于[二月](https://github.com/webpack/webpack/releases/tag/v4.0.0-beta.0)发布，带来了巨大的性能改进，内置的生产和开发模式，易于使用的优化，如代码分割和缩小，实验性 WebAssembly 支持，以及 ECMAScript 模块支持。Webpack 现在比以前的版本更容易使用，以前复杂的功能，如代码分割和优化，现在设置起来非常简单。与 TypeScript 或 Babel 相结合，webpack 仍然是 web 开发人员的基础工具，在不久的将来，竞争对手似乎不太可能出现并取代它。

### 巴比伦式的城市

《通天塔》7 于今年八月在[发布，这是近三年来的第一次重大发布。主要变化包括](https://babeljs.io/blog/2018/08/27/7.0.0)[更快的构建时间](https://twitter.com/left_pad/status/927554660508028929)，一个新的包命名空间，以及支持 [preset-env](https://babeljs.io/docs/en/next/babel-preset-env.html) 的各种“stage”和年度 ECMASCript 预设包的弃用，这通过自动包含您支持的浏览器所需的插件极大地简化了 Babel 的配置。此版本还增加了[自动多孔填充](https://babeljs.io/blog/2018/08/27/7.0.0#automatic-polyfilling-experimental)，无需导入整个 Babel 多孔填充(这相当大)，也无需显式导入您需要的多孔填充(这既耗时又容易出错)。

Babel 现在[也支持 TypeScript 语法](https://blogs.msdn.microsoft.com/typescript/2018/08/27/typescript-and-babel-7/)，这使得开发者更容易将 Babel 和 TypeScript 结合使用。Babel 7.1 还增加了对新的[装饰者提案](https://babeljs.io/blog/2018/09/17/decorators)的支持，该提案与社区广泛采用的过时提案不兼容，但与浏览器将支持的内容相匹配。谢天谢地，巴别塔团队已经发布了一个[兼容性包](https://babeljs.io/blog/2018/09/17/decorators#upgrading)，这将使升级变得更加容易。

### 电子

电子仍然是为桌面打包 JavaScript 应用程序最流行的方式，尽管这是否是一件好事还有些争议。一些最流行的桌面应用程序现在使用 Electron 来降低开发成本，使开发跨平台应用程序变得容易。

一个常见的抱怨是使用电子的应用程序倾向于使用太多的内存，因为每个应用程序都打包了一个完整的 Chrome 实例(这是非常占用内存的)。 [Carlo](https://github.com/GoogleChromeLabs/carlo) 是谷歌的一个电子版，使用本地安装的 Chrome 版本(这是谷歌需要的)，导致应用程序对内存的需求减少。电子本身在提高性能方面没有太大进展，[最近的更新](https://electronjs.org/blog/electron-3-0)集中在更新 Chrome 依赖和小的 API 变化上。

### 以打字打的文件

TypeScript 在过去的一年里越来越受欢迎，成为 ES6 作为 JavaScript 主流风格的真正挑战者。自从微软每月发布新版本以来，开发在过去的一年中进展相当迅速。TypeScript 团队非常关注开发人员的体验，包括语言本身和围绕它的编辑器工具。

最近的版本增加了对开发者更友好的[错误格式化](https://blogs.msdn.microsoft.com/typescript/2018/07/30/announcing-typescript-3-0/#improved-errors-and-ux)和强大的重构特性，比如[自动导入更新](https://blogs.msdn.microsoft.com/typescript/2018/05/31/announcing-typescript-2-9/#rename-move-file)和[导入组织](https://blogs.msdn.microsoft.com/typescript/2018/03/27/announcing-typescript-2-8/#organize-imports)等等。与此同时，改进类型系统的工作还在继续，增加了一些新的特性，比如条件类型和未知类型。

2018 年 JavaScript 状况调查指出，[近一半的受访者](https://2018.stateofjs.com/javascript-flavors/typescript/)使用 TypeScript，在过去两年中呈强劲上升趋势。相比之下，它的主要竞争对手 Flow 在受欢迎程度上停滞不前，大多数开发者说他们不喜欢它缺乏工具和受欢迎的势头。TypeScript 令人钦佩，因为它使开发人员能够轻松地编写由强大的编辑器支持的健壮而优雅的代码。它的赞助商微软似乎比脸书更愿意支持它，开发者们显然也注意到了这一点。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.