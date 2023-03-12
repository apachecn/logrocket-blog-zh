# react Hooks——让我们不要太执着——log rocket 博客

> 原文：<https://blog.logrocket.com/react-hooks-lets-not-get-too-attached-11b0ac09b4b5/>

![](img/8bde93612e63365d1f7d3949ae168bdb.png)

React 团队在 React Conf 18 上宣布了一个名为 Hooks 的新功能的提议后，React 社区出现了奇怪的反应:一系列描述如何使用它的教程，好像它已经在 React 核心中了。

被明确描述为[“新功能提案”](https://reactjs.org/docs/hooks-intro.html)的东西被一些人视为功能*公告*。显然，有太多的宣传和“让我们现在就重写一切”的态度，正如 React 团队的 Dan Abramov 所说:

因此，让我们冷静一下，甚至不要为提议的 API 费心，并思考 React 是如何走到这一步的。

如果你现在更想看到代码并重构你的应用程序来使用钩子，那么[官方文档](https://reactjs.org/docs/hooks-intro.html)是一个很好的起点。

在[关于 React 的第一次会议演讲](https://www.youtube.com/watch?v=x7cQ3mrcKaY)中，Pete Hunt 使用单词 *hooks* 来指代`React.Component`的方法，该方法允许用户提供自定义更新逻辑，以*挂钩到* React 的内部并调整它们的行为。新的特性提案以类似的方式引入了*钩子*——通过将你的代码挂接到 React 的引擎中，作为一种与 React 交互的方式。

这种使用函数而不是从类继承的想法与 React 的功能和声明精神非常契合。因为基于类的组件遭受了古老的香蕉-大猩猩-丛林问题的困扰，正如乔·阿姆斯特朗所描述的:

> 面向对象语言的问题是，它们拥有所有这些隐含的环境。你想要一个香蕉，但你得到的是一只大猩猩拿着香蕉和整个丛林。

使用`React.Component`，通常您需要的只是状态或引用，但是您得到的是一组您永远不会使用的方法，以及编写可怕的`this`关键字的必要性(这是一个变位词)。基本上，Hooks 是一个放弃类的提议，代替*从*继承 React， *hook 变成* React。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 在没有`class` es 的情况下如何反应

React 基于类的 API 出现问题已经有一段时间了。ES6 类本身并不是真正的类(只是语法糖掩盖了原型继承)，它们不能很好地组合，并且使用`this`关键字会产生绑定问题，尤其是在执行异步操作时。

因此，对无类 React 开发的研究开始了。也许最值得注意的项目是`[recompose](https://github.com/acdlite/recompose)`(文档中的第一个例子是[向功能组件](https://github.com/acdlite/recompose#lift-state-into-functional-wrappers)提供状态)，该项目现在因为钩子的引入而停止。然而，`recompose`大量使用了高阶组件模式，不幸的是，这在渲染树中创造了一个难以阅读和虚假的层次。

另一个例子是 [Reactions](https://github.com/reactions/component) `[Component](https://github.com/reactions/component)`，它使用了另一个非常流行的模式来使类组件更加可组合——Render Prop。这里的问题是，用户首先必须非常熟悉基于类的 React API，才能对如何使用 Reactions `Component`有所了解。

还有一些很酷的项目使用新的 [ES6 代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)来管理状态，比如`[react-easy-state](https://github.com/solkimicreb/react-easy-state)`或者——以一种更轻量级和实验性的方式——`[react-recollect](https://github.com/davidgilbertson/react-recollect)`。我推荐关于后一个的[文章，它很好地探究了如何实现超级可读和自我解释的状态管理。可悲的是，对 ES6 代理的支持](https://hackernoon.com/a-different-way-to-manage-state-in-react-2d21dfb94482)[与理想的](https://caniuse.com/#feat=proxy)相差甚远。

目前，钩子实现允许替换*几乎*所有基于类的组件的功能(很快将添加`componentDidCatch`和`getDerivedStateFromError`的钩子等价体)，所以在添加它们以作出反应之后，搜索可以被取消。React 中的类不会很快消失，但是很明显 React 团队为图书馆设想了一个无类的未来。

### 请只给我一种成分

类和函数组件之间的二分法对用户来说是不必要的负担。我倾向于将函数组件视为成熟的类组件的一种轻量级的小兄弟——而且，这种区别与[表示和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)模式非常吻合。但是当我想象现在学习 React 时，我认为在开始编写组件之前就必须决定是否使用状态(React 最基本的特性)是一件令人失望的事情。

这有点像[同步与异步函数](http://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/) : `async` / `await`和`Promise`帮助我们逃离回调地狱，但是为什么程序员首先要关心一个操作是否是异步的呢？至少对我来说，钩子是一个比`class Something extends React.Component`更容易理解的抽象概念——我怎么知道在丛林中潜伏着什么，我必须带着`setState`功能？

### 更具声明性和功能性的未来

React 以其声明性而自豪，但是使用生命周期方法是非常必要的——一个经典的例子是在`componentDidMount`和`componentDidUpdate`方法中的重复代码。使用钩子，这可以通过`useEffect`函数来实现(在官方文档中有一个关于钩子的声明式 API 如何击败生命周期方法的必要性[的很好的例子)。](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)

Drop the “Stateless”, just “Functional Components“.

有了无状态的功能组件，React 更多地朝着函数式编程的方向发展。钩子在这一领域更进一步，因为它们可以不用类而只用函数来构建全功能的 React 应用程序。

当该提案最终确定时，这将是自该库推出以来 React 应用程序开发方式的最大变化。今天的漂亮模式，如高阶组件或 Render Prop，将会显得古怪，甚至粗糙(因为它们引入了一个错误的层次)。熟悉的`class App extends React.Component`及其生命周期方法列表将成为过去，如果没有 JSX 语法，一段代码甚至可能乍一看都不像 React 组件。

但在此之前，我们不要太依赖于提议的 API，而要专注于使用酷的、稳定的特性编写代码。

#### 进一步阅读

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).