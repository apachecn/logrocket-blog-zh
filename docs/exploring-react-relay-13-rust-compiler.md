# 探索 React Relay 13 的 Rust 编译器

> 原文：<https://blog.logrocket.com/exploring-react-relay-13-rust-compiler/>

Relay 编译器是用于 React 应用程序的 GraphQL 代码生成工具。之前用 JavaScript 创建，最近用 Rust 重写了编译器。在本文中，我们将探索它的特性，它是如何工作的，以及为什么重写是必要的。

## 继电器及其编译器概述

市场上有相当多的 GraphQL 客户端可用，但是 Relay 具有一些独特的特性和优势。中继的一个优点是它让你从小范围的角度思考。

### 在中继中使用 GraphQL 片段

例如，当创建一个组件时，您可以通过创建一个片段来告诉 Relay 只获取该特定组件[中所需的特定数据。](https://blog.logrocket.com/graphql-fragments-explained/)

这样，你永远不需要担心大局。每个组件都可以有自己的片段，在编译时，所有的片段被缝合到一个查询中，该查询获取所有需要的数据。

一个片段和它的视图代码一起存在于一个组件中，这个概念叫做协同定位。主机代管的优势在于没有数据溢出，这使得您的应用程序能够更好地执行。

也没有欠取数据，这防止了由于丢失数据而可能发生的错误。

协同定位的另一个优点是，组件仅在其片段更改中指定数据时才重新呈现，从而避免了不必要的重新呈现。

### 继电器的数据屏蔽

Relay 还在运行时提供了它的范围管理优势；在数据用数据响应查询之后，编译器创建数据文件，当组件将要呈现时，这些文件为每个组件提供它自己所需的数据。

这个概念叫做数据屏蔽。这样做的好处是组件不能访问没有在它们的 GraphQL 片段中指定的数据。这样，对组件及其数据依赖项所做的更改不会影响另一个组件。

由于一个组件不能依赖另一个组件来获取数据，因此数据屏蔽可以防止数据依赖性错误，并为您的应用程序提供稳定性。

片段可以很容易地在多个组件中使用，易于重构，并使您的应用程序高效。

## 为什么 Relay 需要编译器

Relay 使用编译器来提高运行时性能并保证稳定性。使用 Relay，组件的大部分工作和 GraphQL 的通信都在构建时执行，从而显著提高应用程序的运行时性能。

### 重取和分页

像在应用程序中重新提取数据和分页这样的任务很难实现，而且容易出错。通过 Relay 的 API，如`[useRefetchableFragment](https://relay.dev/docs/api-reference/use-refetchable-fragment/)`和`[usePaginationFragment](https://relay.dev/docs/api-reference/use-pagination-fragment/)`，Relay 利用其编译器来自动化这些任务。

与手动实现分页相比，使用 Relay 的开箱即用分页功能，您只需要几行代码就可以在您的应用程序中实现分页。

编译器帮助您创建分页所需的自定义查询，并帮助您跟踪分页时经常需要的信息，如已经加载的数据和尚未加载的数据量。

它隐藏了大量的复杂性，如果您只是想将快速分页功能放在一起，这是非常好的。

### 自动类型生成

中继编译器还支持自动类型生成，以在应用程序中实现类型安全并防止错误。

中继编译器通过删除查询中的冗余来优化应用程序的性能，从而减少查询负载的大小。编译器创建紧凑、优化的查询，在运行时平稳地运行您的应用程序。

通过从应用程序包中排除 GraphQL 片段的模式或字符串表示，它还有助于节省用户的带宽并提高应用程序的性能。

### 使用唯一的查询 ID

中继编译器帮助用户节省带宽的另一种方式是，编译器生成唯一的查询 ID 并使用它从服务器获取数据，而不是向应用程序的服务器发送长查询。

## JavaScript 在 Relay 编译器中的局限性

如上所述，之前的编译器是用 JavaScript 编写的。根据 React Relay 团队的说法，JavaScript 最初被选择用于编译器，因为它是 Relay 运行时和其他 GraphQL 工具的编写语言。

但是，尽管尽了一切努力来优化 JavaScript 编译器，它的性能还是随着时间的推移而下降。该团队对 JavaScript 的最大挑战是它是单线程语言这一事实。

[在 Node.js 中，不能用共享内存](https://blog.logrocket.com/a-complete-guide-to-threads-in-node-js-4fa3898fe74f/)运行多线程。尽管可以创建[工作线程](https://blog.logrocket.com/node-js-multithreading-worker-threads-why-they-matter/)来共享内存，但是对于 Relay 所拥有的模式大小，这种方法并不高效。

## 为什么 Relay 在新编译器中使用 Rust

使用以前的 JavaScript 编译器，随着中继代码库的增长，编译代码需要的时间越来越多。

[根据 React Relay 团队](https://relay.dev/blog/2021/12/08/introducing-the-new-relay-compiler/#:~:text=Prior%20to%20the%20decision%20to%20rewrite%20the%20compiler%2C%20the%20time%20it%20took%20to%20compile%20all%20of%20the%20queries%20in%20our%20codebase%20was%20gradually%2C%20but%20unrelentingly%2C%20slowing%20as%20our%20codebase%20grew.%20Our%20ability%20to%20eke%20out%20performance%20gains%20could%20not%20keep%20up%20with%20the%20growth%20in%20the%20number%20of%20queries%20in%20our%20codebase)的说法，Relay 代码库中查询数量的持续增长降低了性能。它最终变得不适合它本来要解决的问题。

当 JavaScript 显然不再适用时，许多语言(没有 JavaScript 的单线程限制，并且具有强大的内部支持)在登陆 Rust 之前都被考虑过。

C++因其陡峭的学习曲线和薄弱的内存安全性而被淘汰；Java 因为没有提供足够的底层控制而被淘汰，OCaml 因为其低效的并发性而被淘汰。

最终，React 看中了 Rust 的速度、内存安全和并发性，以及可以在不同线程间轻松安全地共享的大型数据结构。新的基于 Rust 的编译器速度更快，具有许多新特性，并且在设计时考虑了可伸缩性。

## Relay 新 Rust 编译器的特点

新的编译器是作为独立模块的集合创建的，可以在其他 GraphQL 工具中使用。基本上，新 React Relay 编译器中使用的相同模块也在其他平台的 GraphQL 工具中内部使用。该编译器具有如下特性:

*   类型脚本支持
*   支持远程[持久化查询](https://relay.dev/docs/guides/persisted-queries/)
*   `@no_inline`指令，应用于片段以防止它们内联
*   `[@required](https://relay.dev/docs/guides/required-directive/)`指令，它简化了空检查

可以将`@required`指令添加到 GraphQL 片段的字段中，以处理运行时生成的空值。以下面的片段为例:

```
const data = useFragment(
    graphql`
      fragment ArticleComponent_article on Article {

              tech_article @required(action: NONE){
                  title @required(action: NONE)
              }
      }
    `,

```

上面，您有一个请求技术文章标题的基本片段。附加到标题字段的`@required`指令对其执行空检查。如果标题为空，那么它的父字段`tech_article`也被 Relay 声明为空。

同样的事情也会发生在应用于`tech_article`字段的`@required`指令上。

现在，动作参数是重要的部分。您可以在这里指定当 Relay 发现一个空字段时要做的事情。当 action 设置为`NONE`时，您的 UI 不会抛出错误，而是呈现良好，并且在您的 UI 中使用`title`值的任何地方都不会显示任何内容。

当然，在处理空支票时，还有其他选项可以应用于您的字段。当在代码中执行许多空检查时，这个特性特别有用。

[据 React Relay 团队](https://relay.dev/blog/2021/12/08/introducing-the-new-relay-compiler/#:~:text=Our%20vision%20for%20the%20compiler%20is%20that%20it%20provides%20even%20more%20high%2Dlevel%20tools%20for%20shipping%20features%20and%20avoiding%20boilerplate%2C%20gives%20developers%20real%2Dtime%20assistance%20and%20insights)称，重写也是为了支持一些未来的计划，如抽象应用程序中更常见的复杂性，以及在分页之外提供更多现成的功能。

新编译器中内置了一个工具，但尚未公开，它是一个 VS 代码扩展，通过在您键入时自动完成字段名，并在您悬停在某个字段上时向您显示该字段的信息，使 GraphQL 的使用变得更加容易。

看起来很多 JavaScript 工具目前正在 Rust 中被重写。但是为什么呢？速度更快，性能更好，内存效率更高。

Rust 没有使用 JavaScript 使用的传统垃圾收集方法，而是使用了[一个更高效的内存管理系统](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#the-stack-and-the-heap)，使其速度更快，性能更好。

Next.js 团队最近在框架中添加了 Rust 编译器，以取代像 [Babel](https://blog.logrocket.com/why-you-dont-need-babel/) 和 Terser 这样的 JavaScript 工具。这样做是为了最大限度地提高性能、实现更快的构建和刷新率。他们的新 Rust 编译器比 Babel 快 17 倍，比 Terser 快 7 倍。

快速和内存高效，Rust 找到了一种方法，将低级控制和高级舒适设计引入软件世界。[内存安全](https://en.wikipedia.org/wiki/Rust_(programming_language)#:~:text=Rust%20is%20designed%20to%20be,inputs%20to%20be%20already%20initialized.)，这是 Rust 最突出的特性之一，也是最大的卖点，让你在编译时轻松识别并排除 bug。

我们也开始看到工具的替代工具 [Rust，比如 Prettier、ESLint 和 Webpack 的涌现。](https://leerob.io/blog/rust#from-javascript-to-rust)

## 结论

Rust 在过去六年( [2016](https://insights.stackoverflow.com/survey/2016#technology-most-loved-dreaded-and-wanted) 、 [2017](https://insights.stackoverflow.com/survey/2017#most-loved-dreaded-and-wanted) 、 [2018](https://insights.stackoverflow.com/survey/2018#technology-_-most-loved-dreaded-and-wanted-languages) 、 [2019](https://insights.stackoverflow.com/survey/2019#technology-_-most-loved-dreaded-and-wanted-languages) 、 [2020](https://insights.stackoverflow.com/survey/2020#most-loved-dreaded-and-wanted) 和 [2021](https://insights.stackoverflow.com/survey/2021/#technology-most-loved-dreaded-and-wanted) )被评为最受欢迎的编程语言，它似乎很好地补充了 JavaScript。凭借 JavaScript 的简单易用，以及 Rust 的速度和内存效率，我相信这两种语言将势不可挡。

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