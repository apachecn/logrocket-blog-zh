# 颤振与 Xamarin - LogRocket 博客

> 原文：<https://blog.logrocket.com/flutter-vs-xamarin/>

***编者按:**本帖于 2022 年 8 月 23 日更新了关于跨平台开发、Flutter 和 Xamarin 的最新信息，并增加了一个 [Xamarin 与 Flutter 对比表](#xamarin-vs-flutter-comparison-table)。2022 年 10 月 25 日进行了其他更新，以反映 Xamarin 取消其商业使用费和其他相关信息。*

跨平台开发是目前技术领域最热门的话题之一。越来越多的公司选择混合框架而不是原生框架。

由于不断增长的需求，跨平台领域已经见证了市场上框架数量的激增。可用的选择越多，就越难确定哪个选项最适合给定的跨平台开发项目。

Flutter 和 Xamarin 是目前最流行的两种跨平台开发框架。Xamarin 已经存在了一段时间，有一种成熟的气息，而 Flutter 是一个由谷歌支持的相对较新的平台。

在本指南中，我们将深入研究这两个框架，然后对它们进行比较，以帮助您选择最适合您的用例的框架。

以下是我们将要介绍的内容:

## 什么是跨平台开发？

[跨平台开发](https://blog.logrocket.com/comparing-developer-experience-for-cross-platform-frameworks/)指的是使用通用的代码库和技术栈构建可以在各种平台上运行的应用程序，如 Android、iOS 和 web。您只需编写一次代码并将其部署在所有平台上，而无需创建多个特定于平台的应用版本。

### 跨平台开发的优势

跨平台开发的最大优势[之一](https://blog.logrocket.com/xamarin-vs-react-native/)是编写一次就可以在任何地方部署的能力。这有助于您保持跨多种平台部署的所有应用程序版本的一致性。

使用单个代码库在多个平台上部署可以节省您的时间和资源。它还提高了你的产品的受众范围，因为你可以很容易地在多个平台上锁定客户。

### 跨平台开发的缺点

与跨平台应用程序开发相关的大多数缺点都与性能和兼容性有关。

跨平台开发的一刀切性质限制了您利用特定于平台的机会来提高性能的能力。它还会阻止您的应用程序访问本机功能，这迫使您采用变通方法。

## 什么是 Xamarin？

Xamarin 是一个开源的跨平台开发框架，最初创建于 2011 年。它允许您使用。NET 语言，无需移植或重写代码就可以在多个平台上运行。

### Xamarin 是做什么用的？

Xamarin 在需要跨多个平台共享代码和业务逻辑的情况下非常有用。Xamarin 使用 C#和 Visual Studio 进行跨平台应用开发。

通俗地说，Xamarin 是一种建立在。净遗产。如果您对如何使用 Xamarin 有一个基本的了解，那么用 Xamarin 构建应用程序是非常容易的。NET 和 Visual Studio 工作。

不像其他跨平台的选择，比如 Flutter T1，你不需要学习一门全新的编程语言。这使得创建原型和小型跨平台应用程序的过程变得快速而简单。

[使用 Xamarin](https://dotnet.microsoft.com/en-us/platform/customers/xamarin) 构建的流行应用包括:

*   电影艺术与科学学院
*   看见 AI |微软
*   美国癌症协会
*   一英里音乐
*   FreshDirect

### 为什么要用 Xamarin？

Xamarin 与其跨平台对手相比如何？如上所述，使用 Xamarin 的原因是多方面的，但让我们强调几个独特的竞争优势。

#### 良好的性能

众所周知，使用 Xamarin 构建的应用程序表现出很好的性能指标。这要归功于 Xamarin 的 API 的出色优化和 Xamarin 团队的共同努力，以模仿类似本机的性能数字。

如果你看看 Xamarin 的 Android 架构是如何设计的，你会发现它的 Mono 内核与 Android 运行时虚拟机一起运行。这消除了在 Android 运行时之上建立桥的需要(像 React Native 需要的那样)，从而提高了性能。

#### 热重装

虽然它不是最初版本的一部分，但是最近 Xamarin 引入了 hot reload 来帮助你实时观察源代码的变化。

添加[热重载](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/xaml/hot-reload)改善并加速了开发人员的体验，使您能够花更多的时间来完善 Xamarin 应用程序的具体细节。

Xamarin 还支持热重启，帮助您快速测试多文件代码编辑、资源更改等重大更改，而不必经历完整的构建和部署周期。虽然你不会经常做这些改变，但是知道当你做的时候，仍然会比平常更快，这很好。

#### 完整的生态系统

Xamarin 提供了一个无与伦比的开发框架生态系统来构建各种应用程序。用 C#。NET 和 Microsoft Visual Studio 上的 Xamarin，您将能够设计出一流的跨平台产品。

#### 无缝体验

[Xamarin。Forms API](https://blog.logrocket.com/getting-started-with-collectionview-in-xamarin-forms/) 允许您利用 Xamarin 的标准接口元素来帮助跨各种平台重用您的代码。如果需要更精细的定制，可以使用 Xamarin.iOS 和 Xamarin。Android 用于更定制的、特定于平台的设计。

#### 免费使用

Xamarin 现在是一个完全开源、免费使用的框架，[包括商业用户](https://dotnet.microsoft.com/en-us/apps/xamarin)。

这意味着您可以轻松地在 Xamarin 中设置新项目，而不必担心与框架、编码环境或构建以后可能需要扩展的应用程序相关的任何成本。

### 使用 Xamarin 的限制

尽管 Xamarin 有很多优点，但您应该意识到与使用该框架相关的一些限制。

#### 缓慢更新

当 Xamarin 团队引入新的平台特性时，通常需要一段时间才能将更新反映到 Xamarin 工具中。这可能会在开发过程中制造障碍，减少开发人员的体验。

#### 大型应用程序

由于其跨平台的性质，Xamarin 应用程序在应用程序的发布版本上可能会占用大约 5-10 MB 的额外空间。这可能会增加最终用户的存储负载。

#### 厚重的图形

使用 Xamarin 构建具有复杂用户界面的应用程序可能会变得很棘手，因为为平台定制 ui 会引入大量特定于平台的定制。有时，这在很大程度上违背了构建跨平台代码库的目的。

#### 特定平台定制

为了符合特定于平台的标准，您可能会遇到被迫为每个平台定制代码库的情况。这可能会增加不必要的工作，使您无法理解和充分利用 Xamarin 提供的特定于平台的 API。

例如，你必须至少掌握管理特定平台语言的基本知识，比如 Android 的 [Kotlin 和 Java】以及 iOS 的 Swift 和 Objective-C。](https://blog.logrocket.com/kotlin-vs-flutter-a-comparison-guide/)

## 什么是颤振？

Flutter 是 Google 在 2018 年创建的开源跨平台应用开发框架。它旨在为开发人员提供一个易于使用的跨平台开发界面，并为用户优化性能。

众所周知，Flutter 可以在现代设备上实现真正的原生性能。它也可以适应[在 web 上发布高性能应用](https://blog.logrocket.com/how-to-migrate-a-flutter-mobile-app-to-the-web/)。

### 颤振是用来做什么的？

Flutter 使开发者能够构建同时在 iOS 和 Android 上运行的移动应用。它以其卓越的性能和出色的开发者体验而闻名。

Flutter 使用的编程语言是 Dart。[使用 Dart 和 Flutter](https://blog.logrocket.com/introduction-to-using-dart-in-flutter/) 提供了源代码的巨大可移植性。

[使用 Flutter 构建的流行应用](https://flutter.dev/showcase)包括:

*   艾比路工作室
*   丰田汽车
*   反射地
*   罗姆韦
*   通行证

### 为什么要用 Flutter？

[Flutter 在跨平台行业的人气正在上升](https://blog.logrocket.com/why-flutter-is-overtaking-react-native/)。让我们强调一下为你的下一个大项目选择 Flutter 的众多理由中的一小部分。

#### 浅层学习曲线

Flutter 比许多其他跨平台框架更容易学习，因为它专注于保持代码库尽可能简单。在这个过程中，它去掉了许多样板文件。

#### 热重装

Flutter 吸引了大量关注的一个最大原因是它的速度超级快。

Flutter 中的[热重新加载特性使您能够对源代码进行修改，并在测试设备上近乎实时地观察这些变化。这使得开发过程比在其他框架上更加一致。](https://blog.logrocket.com/why-flutter-is-overtaking-react-native/#hotreload)

与 Xamarin 类似， [Flutter 也支持热重启](https://docs.flutter.dev/development/tools/hot-reload#:~:text=F5%20in%20VSCode)-,Hot%20restart,-loads%20code%20changes)，这有助于您在做出通常需要您重新构建和安装应用程序的更改后快速重新加载和测试应用程序——比如添加或删除资源。

#### 高性能

通过超前的(AOT)编译，Flutter 可以宣称匹配真正的本机性能指标。其无与伦比的代码可重用性和性能使 Flutter 成为大型项目的完美跨平台框架。

#### 免费使用

和其他开源框架一样，Flutter 可以免费使用。

### 使用颤振的限制

虽然 Flutter 提供了极好的好处，特别是在性能方面，但它也引入了一些缺点，包括臃肿的代码和缺乏对某些库和平台的支持。让我们放大一些与 Flutter 应用程序开发相关的缺点。

#### 大型应用程序

和所有其他跨平台框架一样，Flutter 也有很多包袱。能够在多个平台上运行是以增加应用程序文件大小为代价的。

#### 缺乏第三方库

用 Flutter 编写应用程序的另一个重要缺点是，为框架设计的社区生成的库相对较少。这在很大程度上是因为颤振是市场上相对较新的技术。

#### 有缺陷的 iOS 支持

由于 Flutter 是谷歌的产品，它自然倾向于 Android 方面的东西。对 iOS 的支持是次要的，因此经常充满问题。

## Xamarin vs. Flutter:哪个更好？

既然您已经很好地了解了 Xamarin 和 Flutter 各自的优势，现在终于可以直接比较 Xamarin 和 Flutter 了。

我们将根据以下标准评估 Xamarin 与 Flutter:

*   表演
*   流行
*   开发语言
*   成分
*   代码重用
*   定价
*   支持和生态系统

### Xamarin 与颤振:性能

与本地框架相比，跨平台框架往往会对应用程序性能造成重大影响。这就是为什么评估性能差异很重要，这样您就不会为了跨平台的可移植性而牺牲用户体验。

正如我们在整篇文章中所强调的，由于其智能的提前编译策略，与几乎所有当代跨平台框架相比，Flutter 的性能非常好。然而使用 Xamarin。SkiaSharp 而不是平台 API 上的表单在某些情况下也很棒。

众所周知，Flutter 可以生成近乎原生的性能数字，所以如果你想创建一个高效的应用程序，Flutter 应该是你的不二之选。

### Xamarin vs. Flutter:受欢迎程度

因为 Xamarin 比 Flutter 早几年，所以它在更广泛的项目中使用。

然而，根据 [StackOverflow 的 2022 年开发者调查](https://survey.stackoverflow.co/2022/#most-loved-dreaded-and-wanted-misc-tech-love-dread)，Flutter 比 Xamarin 更受喜爱。此外，当开发者被要求为他们目前没有使用，但有兴趣开发应用程序的技术投票时，Flutter 排名第二。

然而，自 2020 年以来，Xamarin 的受欢迎程度有所下降，因为喜欢 Xamarin 的受访者人数从 45.4%下降到 38.53%。这意味着，与找到合适的 Flutter 开发者相比，找到合适的 Xamarin 开发者会更加困难。

![Stack Overflow 2022 Most Loved Vs Dreaded Technologies Survey Results Shown In A Horizontal Bar Chart Where Each Line Represents A Technology And Its Loved Vs Dreaded Percentage Ratio Is Shown In Blue And Purple. Flutter Is Fifth From The Top With Sixty-Eight Percent Of Respondents Loving It While Xamarin Is Second From The Bottom With Seventy-Four Percent Of Respondents Dreading It](img/aa56930158212ddf4beadbce584d1d4f.png)

### Xamarin 与 Flutter:开发语言

每当你需要学习一门新的开发语言来开始使用一个新的框架时，它都会增加学习曲线。如果一个框架要求你学习一种全新的语言，而这种语言在框架之外几乎没有用处，那么在投入时间之前，你可能要三思而行。

Xamarin 使用。NET 语言，比如 C#和 F#。这些流行的语言在 Xamarin 之外有几个用例。这使得你值得投入时间和资源来学习这些语言。尤其是 C#中的技能提升，将为作为开发人员的你打开许多大门。

另一方面，Dart 是一种相对较新的语言，最初是为了在 Chrome 中与 JavaScript 一起运行而引入的。后来它将重点转移到编译 JavaScript 来创建客户端应用程序，这就是 Dart 和 Flutter 的交集。

从那以后，Dart 成为了 Flutter 的编程语言。今天，它很少作为独立的技术使用。

如果您对 Dart 和都不熟悉。NET 语言，它们学习曲线的差异并没有太大关系。

然而，。与 Dart 相比，NET 语言有更广泛的使用案例，除了增加开发人员的学习曲线之外，Dart 也造成了锁定的情况。学习 Dart 只会让你在颤振发展的背景下受益。

在瞬息万变的行业市场，你到底要不要把时间沉下去学 Dart，值得考虑。

### Xamarin 与颤振:组件

Flutter 附带了一些很棒的 UI 组件集合，包括材质和库比蒂诺。这使得开发人员的体验变得轻而易举，并且不需要担心应用程序的样式。

另一方面，Xamarin。Forms 提供了一个详细的跨平台 UI 工具包，它由 UI 组件组成，这些组件看起来和感觉上都像本地组件，但可以在跨平台设置中使用。

一旦你设置了在你的应用中使用它们，这些组件就会被编译成它们的平台内变体。如果你想更多地控制你的应用程序的外观，你可以试试 [Xamarin.iOS](https://docs.microsoft.com/en-us/xamarin/ios/) 或 [Xamarin。安卓](https://docs.microsoft.com/en-us/xamarin/android/)。

在组件支持方面，这两个框架并驾齐驱。Flutter 和 Xamarin 都不会错。

### Xamarin 与 Flutter:代码重用

代码重用是跨平台应用程序开发的一个基本特征。因此，在决定跨平台开发框架时，这是一个需要考虑的关键因素。

Flutter 允许您为两个平台编写通用代码，但它也为特定于平台的差异留有很大空间。这意味着您必须为您的 Flutter 应用程序编写大量特定于平台的代码，以便在所有平台上都能很好地工作。

另一方面，Xamarin 引以为豪的是，它允许开发人员重用高达 90%的应用程序代码。像 Xamarin 这样的包。形式，Xamarin 使代码共享变得轻而易举。

### Xamarin 与 Flutter:定价

这两种技术都可以免费使用，它们的源代码也是公开的。这对使用这两种技术的开发人员来说都很好，尤其是如果可伸缩性对您很重要的话。

### Xamarin vs. Flutter:支持和生态系统

在选择两个框架时，考虑社区支持是很重要的。这包括面向初学者的学习资源、供开发人员讨论问题和想法的公共场所等等。

Flutter 拥有所有围绕它构建的框架和语言中最强大的社区之一。Google docs 对初学者非常友好，在 StackOverflow 等公共网站和论坛上有大量的内容可以按需寻求帮助。

社区对 Xamarin 的支持更加有限。虽然微软确实提供了一些免费的 Xamarin 课程和学习路径来帮助你入门，但内容的水平达不到 Flutter 的标准。

## Xamarin 与颤振:对照表

在我们结束讨论之前，这里有一个两个跨平台开发平台之间差异的快速总结:

|  |  | **Xamarin** |
| 所有者 | 谷歌 | 微软 |
| 首次在中介绍 | 2017 年 | 2011 年 |
| 性能 | 类原生 | 不如 Flutter 的 |
| 重负载 (用于 例如，图形和动画，相比于原生应用) | 适中 | 穷 |
| 跨平台特性 | 适用于多种平台的简单通用代码库 | 定制控件和渲染器需要在多个平台上实现相同的高级 UI 组件 |
| 人气 | 相当高 | 远低于颤振的 |
| 开发语言 | 镖 | 。像 C#、F#等网络语言。 |
| 学习曲线 | 简单易学 | 简单易学 |
| 组件支持 | 广泛的组件，可用的插件。集成很容易。 | 有许多组件可用，但集成并不顺利。 |
| 社区 | 巨大且不断增长 | 尺寸合适，但在缓慢减小 |
| 定价 | 免费、开源 | 免费、开源 |

## 结论

跨平台框架是一种乐趣。发布可以在多个平台上运行的代码的能力比您想象的更强大，除非您亲身体验。

对于企业来说，跨平台工具可以帮助你在多个领域建立立足点，而无需经历太多麻烦。

在选择合适的框架时，理解您的需求和目标非常重要。

Xamarin 是跨平台比赛中最古老的马匹之一，而 Flutter 对比赛来说相对较新。Xamarin 带来了许多与成熟框架相关的好处，但 Flutter 设法跟上了它强大的以社区为中心的方法。

开发项目的最佳跨平台框架总是取决于您的用例以及项目的目标和需求。但如果被迫选择一个，我会给 Flutter 一点点优势。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)