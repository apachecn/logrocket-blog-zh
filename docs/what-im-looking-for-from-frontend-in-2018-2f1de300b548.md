# 2018 年的前端:更多共识，更少复杂性

> 原文：<https://blog.logrocket.com/what-im-looking-for-from-frontend-in-2018-2f1de300b548/>

![](img/68f36745db2162d3c99fc74d43cb6822.png)

[2017 年是前端 web 开发不平凡的一年](https://blog.logrocket.com/frontend-in-2017-the-important-parts-4548d085977f)。

像 React 和 Angular 这样的框架继续在社区中享有大规模的支持，但像 Vue 这样的新竞争者也在流行中爆发。Webpack 仍然是构建工具的首选，NPM 是软件包系统的首选。WebAssembly 以前所未有的速度向众多令人兴奋的新用例打开了 web。像 GraphQL 这样的技术革新了 API 在 web 应用程序中的编写和使用方式。

与此同时，语言本身也在不断发展。ECMAScript 标准的 2017 版增加了异步函数，大大改善了开发人员编写异步代码时的体验。现在所有主流浏览器都支持它们。另一个值得注意的增加是共享内存和原子操作。

然而，共享内存于 1 月 5 日在所有主流浏览器中被暂时禁用，因为有消息称它们向浏览器开放了涉及投机执行的旁路攻击。

预计今年某个时候，当浏览器供应商找到一种方法来防止这种漏洞时，共享内存将变得可用。

[![](img/576491a4328cdae7545b9efa365439da.png)](https://logrocket.com/signup/)

### 库和框架

#### 反应

React 经历了非常多事的 2017 年，9 月份发布了 [React 16](https://reactjs.org/blog/2017/09/26/react-v16.0.html) 。这是 React 迄今为止最大的版本，增加了**片段**(您现在可以返回一个数组，而不必将所有内容包装在一个无用的< div >元素中)、更好的**错误处理**和错误边界(错误现在会导致 React 在根或特殊错误边界组件处卸载)、**门户**(您现在可以将 React 子级渲染到 React DOM 层次结构之外的 DOM 节点中)和**流**(允许服务器端渲染应用程序将渲染流传送到客户端，而不必等待整个渲染)

此外，React 还[切换到 RFC 流程](https://reactjs.org/blog/2017/12/07/introducing-the-react-rfc-process.html)，为 React 团队贡献创意。任何人都可以提交 RFC 提案，只要这些提案改变了 React API。React 团队已经发布了他们的[提议的上下文变更](https://github.com/reactjs/rfcs/pull/2)作为第一个 RFC，这是一个非常有趣的阅读。

该群体还提交了几项提案，涵盖了许多有趣的新功能，包括:

*   [渲染函数参数](https://github.com/reactjs/rfcs/pull/4) —为了减少代码混乱，它建议渲染函数将属性、状态和上下文作为参数。
*   [setState 返回一个承诺](https://github.com/reactjs/rfcs/pull/10)——当您需要 setState 是同步的并且您处于一个带有 async / await 的环境中时，它会很好地配对。
*   [异步安全静态生命周期挂钩](https://github.com/reactjs/rfcs/pull/6)——对传统的基于类的应用编程接口的彻底改造，使其更容易处理异步数据，同时避免不必要的渲染，并为功能组件提供清晰的升级路径。

当然，并不是所有的提议都会出现在未来的版本中，但是 React 团队向社区开放规划是件好事。随着最近像 [Yarn](https://github.com/yarnpkg/rfcs) 和 [Ember](https://github.com/emberjs/rfcs) 这样的项目采用 RFC 过程，RFC 过程在社区中变得更加流行。

由于设置现代 web 开发所需的所有不同工具相对复杂，样板项目在 React 社区中一直很受欢迎。大多数建议用户克隆项目来使用，并从那里开始构建。这对初学者来说可能很困惑，因为他们被一个复杂的“白板”淹没了，这个白板上有大量他们不理解的依赖项和配置代码。

脸书的 [create-react-app](https://github.com/facebookincubator/create-react-app) 不同——它是一个 CLI 工具，包装了 Webpack、Babel、PostCSS 和 Jest，允许零配置开发。去年它的受欢迎程度持续增长，从 2017 年初的 18k GitHub stars 到年底的 40k。它还允许您通过提供“弹出”命令来停止使用 create-react-app，该命令安装依赖项并复制配置文件，以便您可以手动更改它们。可以说，这是 React 近年来受欢迎程度增长的部分原因。

对于服务器渲染的 React 应用， [next.js](https://github.com/zeit/next.js) 是一个流行的框架选择。它提供了一个“通用”web 应用程序所需的所有工具，而不需要复杂的配置和设置。这一点很重要，因为最近人们对通用(或“同构”)应用的兴趣似乎有所下降，转而青睐渐进式 web 应用——可能是因为开发的复杂性。如果你开始一个新项目，我会认真考虑将 next.js 作为一个框架选择。

我认为 React 社区最终会开发类似于 create-react-app 的东西，但目标是更复杂的应用程序。

Next.js 接近这一目标，但它的目标是服务器渲染的应用程序，这绝不是大多数。在我看来，配置和易用性之间有一个最佳平衡点，这是任何框架都没有达到的。

> 对于越来越多对构建系统配置的复杂性和维护它所需的开发时间失望的开发人员来说,“包含电池”方法的吸引力将变得诱人。

#### 有角的

尽管 Angular 的最新版本([版本 5.1.3](https://github.com/angular/angular/blob/master/CHANGELOG.md) )于 1 月 3 日发布，但 AngularJS 项目(又名旧 Angular 1.x 版本)仍在积极开发中，甚至[在 2017 年 12 月 18 日发布了版本 1.6.8](https://github.com/angular/angular.js/blob/master/CHANGELOG.md) 。许多大公司仍然在使用 Angular 的传统版本，因此重要的速度改进和安全修复已经被反向移植到 AngularJS。

目前还不知道谷歌对传统项目的支持将于何时结束，但在过去[官方说法](https://stackoverflow.com/questions/37037251/angularjs-1-x-support-lifecycle)是，在大部分网络流量流向 angular.io 域名(下一代版本的网站)而不是 angularjs.org 之前，支持不会结束。然而，鉴于传统版本使用相当自由的[麻省理工学院许可](https://github.com/angular/angular.js/blob/master/LICENSE)，即使官方支持在 2018 年消失，你也可以期待进一步的发展。

最近的 Angular 版本令人印象深刻，尤其是最新的 v5 版本。它继续通过提供创新功能(如模板的提前编译和服务人员集成到一个整洁且易于设置的包中)在每个版本中都将自己与竞争对手区分开来。

虽然这些特性当然适用于任何应用程序，但 Angular 的亮点在于它的集成工具。Angular CLI 易于使用，现在支持使用[应用外壳](https://developers.google.com/web/fundamentals/architecture/app-shell)快速生成通用和渐进式 web 应用。

React 社区信奉一种不那么固执己见的前端开发哲学。在很大程度上，开发人员必须手动设置这些复杂的特性，除非他们使用[众多样板项目](https://github.com/topics/boilerplate)中的一个。许多开发人员更喜欢自己设置东西，这样他们就能理解系统的所有部分。

有时感觉网络社区在固执己见和集中到不固执己见和分散之间循环。人们不禁想知道 React 社区最终是否会朝着另一个方向发展。

> 在参与了几个大型定制 React / Redux / Webpack 项目之后，基本上已经为您设置好了一切，并且“正常工作”是一个非常有吸引力的前景。

鉴于最近的发布，看看 Angular 在未来一年是否会变得更受欢迎将是一件有趣的事情。虽然很难衡量，但当你查看 NPM 的下载量时，Angular 似乎并没有增长多少。React】继续扩大领先优势，尤其是在去年。它现在每天的 NPM 下载量几乎是现在的三倍。

#### 某视频剪辑软件

[Vue](https://github.com/vuejs/vue) 已经成为 2017 年非常流行的视图框架替代 React。两者都利用了虚拟 DOM，并且都是基于组件的，非常轻量级。在 JavaScript 2017 年的 State survey 中 Vue 被列为继 Angular 1 和 React 之后第三个最常用的前端框架[。最值得注意的是，这是调查中最“愿意学习”的框架。](https://stateofjs.com/2017/front-end/results)

Vue 核心团队计划在今年二月之前发布 2.6 版本,主要关注错误处理、功能组件和服务器端渲染。在 React 的带领下，他们还计划在未来的版本中只针对 evergreen 浏览器。

> Vue 在过去的一年里越来越受欢迎，但是很难看到它取代 React 成为前端视图库之王。

关于它对来自 Angular 的[开发者的吸引力已经写了很多，我希望这种吸引力会继续下去。一般的观点是，与 React 不同，Vue 不要求你使用 JSX，与 Angular 不同，它不要求你使用 TypeScript。](https://medium.com/reverdev/why-we-moved-from-angular-2-to-vue-js-and-why-we-didnt-choose-react-ef807d9f4163)

它的模板语言也和 Angular 的非常相似。此外，Vue 有一系列维护良好的软件包，它们共同提供与 Angular 类似的功能，但以一种更分散的方式提供。

### 模块捆扎机

#### 网络包

[Webpack 3](https://medium.com/webpack/webpack-3-official-release-15fd2dd8f07b) 于 2017 年 6 月发布，并将范围提升作为其旗舰功能。scope hoisting 不是将每个模块包装在一个单独的外壳中，而是将所有模块放在一个外壳中，而不会破坏它们。这可以显著改善包的执行时间和包的大小。这是另一个模块捆绑器 [Rollup](https://github.com/rollup/rollup) 的一个显著特征，它一直是 Webpack 2 及以后功能的灵感来源。

Webpack 团队[为 Webpack v4 计划了](https://medium.com/webpack/road-to-webpack-4-week-20-21-1641d03ce06e)许多重要的特性，在撰写这篇博文时它还处于 alpha 阶段，预计很快就会发布。主要特性是 WebAssembly 模块支持——目标是使 Webpack 中的 WASM 模块像 ECMAScript 模块一样易于使用。还计划对 Webpack 生成 CSS 的方式进行彻底检查，而不是将 CSS 注入 JavaScript Webpack 4 现在将生成 CSS 资产。

新版本还将关注构建性能——这个问题被 Webpack 社区投票选为重中之重。

> 在我看来，Webpack 还应该更多地关注文档和配置。尽管 Webpack 擅长灵活的配置，但它牺牲了用户体验。

已经有人提出了 Webpack 零配置模式，但是尽管像 package 这样的其他模块打包器大受欢迎，它还没有被优先考虑。

#### 包裹

[Parcel](https://parceljs.org/) 于 2017 年末在 GitHub 上引起了巨大的轰动，在不到一个月的时间里聚集了令人印象深刻的一万四千颗星星。人们对 Webpack 混乱的配置和缓慢的构建时间越来越不满，它利用这一点推出了一个“零配置”设置。它已经有了几个由 Webpack 推广的重要的模块捆绑特性，比如代码分割和热模块替换。

剩下的大部分开发似乎集中在添加更小的功能，使其与 Webpack 不相上下——如入口点和一个全面的插件系统。

我将在 2018 年密切关注包裹的发展，看看它是否会成为 Webpack 统治地位的主要挑战者，这将是一件有趣的事情。

> 尽管 Webpack 的最新版本引入了有价值的功能，新的文档站点也是一个巨大的改进，但感觉 Webpack 现在是颠覆性的。

为复杂的用例配置 Webpack 简直是一项艰巨的任务。

如果 Parcel 成功地利用了开发人员的挫折感，并以较少的配置提供了一个更简单的替代方案，那么它可以发展成一个大项目。

#### 其他工具

Gulp 和 Browserify 仍然被成千上万的项目以某种形式使用，但它们不再被认为是前端构建工具的前沿。它们的持续开发对于现有系统的维护非常重要，并且它们可能仍然会在新项目中用于非常特殊的用例；然而，在过去几年中，开发人员普遍认为它们过于复杂，需要太多的手动设置。去年，在 Webpack 日益扩大的领先优势中期，这两家公司在 NPM 的下载量都持续相对稳定的下降。

### 工具

#### 以打字打的文件

TypeScript 有一个计划在一月份发布的版本，其中包括新的 ECMAScript 特性，比如[数字分隔符](https://github.com/tc39/proposal-numeric-separator)和一些涉及对象文字和类的高级类型系统改进。同样计划中的还有[一个改变](https://github.com/Microsoft/TypeScript/pull/19675)，它将改进 TypeScript 的模块系统处理非 ECMAScript 模块的方式。

这使得它更符合 Babel 处理模块互操作性的方式。希望这将使使用不同类型的模块时更容易使用 TypeScript，这对于新用户来说通常是一个令人困惑的痛点。这个版本还计划通过增加对 ECMAScript 模块自动转换的支持来改进已经令人印象深刻的重构特性。

> 微软的 TypeScript 明显战胜了 Flow(来自脸书的对手类型检查工具)。这有几个原因，但在我看来，这只是微软运行 TypeScript 项目的好坏的问题。

与[零星的、较小的流量发布](https://github.com/facebook/flow/releases)相比，微软每月发布[大量](https://github.com/Microsoft/TypeScript/releases)。使用 TypeScript 的工具也更好，使用 [tslint](https://github.com/palantir/tslint) 的出色 linter 支持和使用 Visual Studio 代码(和许多其他编辑器)的奇妙[编辑器支持](https://code.visualstudio.com/docs/languages/typescript)，它们提供了使用 Flow 根本不可能实现的自动转换。

一个是否是更好的类型系统几乎无关紧要——我敢打赌大多数开发人员更关心支持和易用性。

此外，围绕 TypeScript 的社区要大得多。与[流类型](https://github.com/flowtype/flow-typed)提供的类型定义相比，通过 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 项目可以为 TypeScript 提供更多的流行 NPM 包的类型定义。如果不说别的，这个事实对任何使用 Flow 的项目的长期生存能力都是一个严重的威胁。

### 移动的

通用 web 应用程序最初流行是在 React 推出的时候。这项创新允许前端 web 应用程序首先在服务器上呈现，代价是开发复杂性。虽然它们仍然受欢迎，但它们绝不是事实上的做事方式。

对于移动设备，开发者今天开始专注于开发所谓的[渐进式网络应用](https://developers.google.com/web/progressive-web-apps/)——这是由谷歌发起的一项倡议，旨在使网络应用对移动用户更加友好。对于开发者来说，这意味着更加关注速度和移动用户体验。这是通过使用新技术实现的，如服务工作者提供离线支持，应用程序清单文件定制应用程序在操作系统中的外观。这可以看作是响应式网页设计的自然发展。

谷歌还赞助了[加速移动页面](https://www.ampproject.org/) (AMP)项目，该项目通过标准化一种轻量级文档格式来大幅减少移动设备上网页的加载时间，这种格式使用了 [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)和谷歌提供的缓存。它很快被网络上的主要内容出版商采用，然而关于出版商[广告收入](https://www.wsj.com/articles/google-amp-gets-mixed-reviews-from-publishers-1477648838)的争议仍然存在，并且担心通过在谷歌的服务器上托管[内容而放弃控制权。](https://www.theregister.co.uk/2017/05/19/open_source_insider_google_amp_bad_bad_bad/)

如果我们希望网络保持竞争力和吸引力，我们需要与移动应用竞争。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

> 虽然他们不能做手机应用程序能做的一切，但是渐进式网络应用程序是保持网络长期健康发展的重要一步。我预计它们在未来会变得更受欢迎，甚至是强制性的。

### 把所有的放在一起

总的来说，frontend 已经趋向于围绕现有项目和 web 开发的许多不同部分进行整合。React、Webpack、TypeScript 继续变得更加流行。Vue 和 Parcel 似乎会对各自领域的领先者构成更大的威胁；与此同时，Angular 和 Browserify 等老牌技术仍然存在，但正在慢慢衰落。

一些趋势还在继续，比如基于组件的设计。最近没有一个新图书馆受到欢迎，挑战了这个网络。这绝不是一个新概念，最近它的复兴也不局限于网络开发。我不认为应用程序架构会很快发生根本性的变化。

有一种趋势是开发人员更友好的，“固执己见”的工具。你可以从 Webpack 和 React 生态系统的复杂性中看到这一点。简单比复杂好，但是如果不复杂，很难满足各种各样的用例。

> 前端开发需要的是更多的共识。它经常被嘲笑为过于复杂，我也有同感。

虽然最近的重点是吸引新的开发人员，但我认为我们也应该注意一般企业 web 项目的复杂性——包括应用程序本身和围绕它的构建工具。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.