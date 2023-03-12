# MobX 6.0 的新特性- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-mobx-6-0/>

要使一个库广为人知并在 React 中广泛使用，需要满足很多要求。特别是对于状态管理库，库所基于的核心概念对于开发人员是否会采用该框架有很大的影响。

MobX 被社区采用并用于许多不同项目的原因之一是因为它的理念。一个非常直接、简单、健壮和非个性化的库，MobX 只会变得更好久而久之。几乎没有样板文件的状态管理库使开发人员的工作变得更加容易，这也是 MobX 相对于 React 中其他传统状态管理库的主要优势之一。

MobX 是 React 中最常用的状态管理库之一，它现在有了一个[新版本](https://mobx.js.org/README.html)，引入了一些变化，以使该库更加简单、健壮和可伸缩。MobX 不是 React 应用程序的专有库，它也可以用于其他 JavaScript 库和框架。

我们将探索 MobX 6.0 版本中的新特性，并了解如何将我们的代码从旧版本迁移到最新版本。

## MobX

如果您还没有尝试过 MobX，我将向您展示关于 MobX 如何真正工作的一些概念。在 React 应用程序中使用 MobX 有很多好处，可以使它更具可伸缩性、更健壮和更简单。来自[文档。](https://mobx.js.org/README.html#introduction)

> 任何可以从应用程序状态派生的东西都应该是。自动地。

基本上，MobX 有四个您应该记住的重要概念:

*Observables* 负责跟踪数据结构(类、对象、数组、引用)。每当我们商店中的价值发生变化时，MobX 都会为我们跟踪新的价值:

```
import { observable } from "mobx";
class Store {
  @observable counter = 0;
}
```

*动作*负责修改我们的状态。每当我们想要更新一个值时，我们需要执行一个动作。动作总是在响应事件、单击按钮或提交表单时发生，例如:

```
import { observable } from "mobx";
class Store {
  @observable counter = 0;
  @action increment() {
    this.counter++;
  }

  @action decrement() {
    this.counter--;
  }
}
```

行动负责改变和修正我们的观察。在 MobX 中，默认情况下，没有办法在动作之外更新状态。这条规则使您的代码库和状态更加可预测、无错误和健壮。

*计算的*值用于导出可观察信息。它们是一个跟踪您的状态的函数，每当它发生变化时，它们的返回值也会发生变化:

```
import { observable } from "mobx";
class Store {
  @observable counter = 0;
  @action increment() {
    this.counter++;
  }

  @action decrement() {
    this.counter--;
  }

  @computed counterMoreThan10() {
    return this.counter > 10;
  }
}
```

*反应*与计算值非常相似，但不同之处在于它会触发副作用，并在可观察到的变化时发生，反应的目标是自动执行副作用。

现在我们已经对 MobX 的核心概念有了一点了解，让我们来了解一下 6.0 版本中真正发生了什么变化，以及它的 API 是如何变得更容易使用和更强大的。

## MobX 6.0

几个月前，MobX 6 的提案是由库的创建者 Michel west strate T1 提出的。随着许多关于新版本的提议，关于 MobX 即将到来的新版本中应该包含什么和应该删除什么有很多讨论。

这些是新 MobX 6.0 中的变化和新功能:

## 装修工

众所周知，很多项目都使用了 JavaScript decorators，比如 Angular、NestJS，当然还有 MobX。装饰者目前正处于第二阶段提案阶段，看起来它不会很快得到支持。

在新的 6.0 版本中最有争议的一点是装饰者是否应该被放弃以获得一些优势，比如:

*   与标准的现代 JavaScript 更加兼容
*   在大多数设置中开箱即用(例如[创建-反应-应用](https://github.com/facebook/create-react-app))
*   减少包的大小

在 MobX 中不再需要 decorator，尽管在以前的版本中没有 decorator 也可以使用这个库，但是很多开发人员一开始并不喜欢 MobX，仅仅是因为 decorator 的使用。

例如，这是 MobX 在以前版本中使用装饰器的一个例子:

```
import { observable } from "mobx";
class Store {
  @observable counter = 0;
  @action increment() {
    this.counter++;
  }

  @action decrement() {
    this.counter--;
  }

  @computed counterMoreThan10() {
    return this.counter > 10;
  }
}
```

这是 MobX 在 6.0 版本中的一个例子:

```
import {
  observable,
  action,
  computed,
  makeObservable
} from "mobx";
class Store {
  counter = 0;
  constructor() {
    makeObservable(this, {
      counter: observable,
      increment: action,
      decrement: action,
      counterMoreThan10: computed
    });
  }
  increment() {
    this.counter++;
  }
  decrement() {
    this.counter--;
  }
  get counterMoreThan10() {
    return this.counter > 10;
  }
}
```

对于一些开发人员来说，没有 decorators 的语法更干净、更简单，[对于其他人来说，开发人员的体验变得更差了](https://github.com/mobxjs/mobx/issues/2325)。

事实是`makeObservable`实用函数使得集成更加容易，不再需要仅仅因为 decorators 而下载和使用许多不同的包。

`makeObservable`实用函数应该包装在`constructor`方法中，并且对于状态的每个属性，为了使其可见，应该使用注释来指定:

```
constructor() {
  makeObservable(this, {
    counter: observable,
    increment: action,
    decrement: action,
    counterMoreThan10: computed
  });
}
```

装饰器仍然可以在 MobX 中使用，但是我们仍然需要向类中添加一个构造函数，使用`makeObservable`实用函数，我们可以省略第二个参数:

```
class Store {
  @observable counter = 0;
  constructor() {
    makeObservable(this);
  }
  @action increment() {
    this.counter++;
  }
  @action decrement() {
    this.counter--;
  }
  @computed get counterMoreThan10() {
    return this.counter > 10;
  }
}
```

## 自动观察

在 6.0 版本中引入的`makeAutoObservable`实用函数比`makeObservable`实用函数更强大。

默认情况下，它使所有属性都是可观察的，尽管您仍然可以用特定的注释来覆盖:

```
class Store {
  counter = 0;
  constructor() {
    makeAutoObservable(this);
  }
  increment() {
    this.counter++;
  }
  decrement() {
    this.counter--;
  }
  get counterMoreThan10() {
    return this.counter > 10;
  }
}
```

这个`makeAutoObservable`确实是 MobX 新版本中最重要和最棒的变化之一。这对开发人员来说更容易，消除了提及他们在商店中可能拥有的每个可观察的、动作和计算的值的需要。

## 委托书

MobX 有几种不同的配置方式，为了在一些 JavaScript 引擎中使用它，需要做一些事情。

在 MobX 5.0 中，需要对代理的支持，这一需求导致了一些 JavaScript 引擎的支持问题，特别是对于 Internet Explorer 和 React Native(取决于引擎)。

在幕后，MobX 使用代理来使数组和普通对象可见。问题是它会给一些不支持代理的 JavaScript 引擎带来一些问题。在 MobX 6.0 中，仍然支持和需要代理，但是现在有一种方法可以通过使用`configure`函数来禁用它:

```
import { configure } from "mobx";
configure({     
  useProxies: "never"
})

```

## 结论

多年来维护和更新一个库并不是一件容易的事情，MobX 社区应该为此受到表扬。这个库一直在变得越来越好，并且从一开始就遵循它的理念，"*任何可以从应用程序状态中派生出来的东西，都应该被派生出来。自动”。*

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