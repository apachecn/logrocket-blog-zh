# npm - LogRocket 博客上的顶级阵列验证实用程序比较

> 原文：<https://blog.logrocket.com/comparison-of-the-top-array-verification-utilities-on-npm-128dda083b8c/>

[Rog Locket Follow](https://blog.logrocket.com/author/roglocket/) 3/4 stack developer and web-scale hacker. I code exclusively #serverless backends.

# npm 上顶级阵列验证实用程序的比较

## 

2018 年 4 月 13 日 2 分钟阅读 590

![](img/b3d52c043476ec94c9d3f00d8d81d702.png)

Source: [http://www.monkeyuser.com/2017/npm-delivery/](http://www.monkeyuser.com/2017/npm-delivery/)

几周前，我在做我的兼职项目`array-length`，这是一个高性能的新 JavaScript 库，用于计算 JavaScript 数组中的元素数量，当时我面临一个有趣的挑战。我需要一种方法来验证用户的输入确实是一个数组，而不是可能破坏我的算法的复杂控制流的其他类型的对象。

正如任何足够有利可图的商业机会经常出现的情况一样，市场上有各种旨在解决这一问题的解决方案。从适合初创公司和中端市场客户的专注、同类最佳的库，到提供广泛功能的高度可扩展的企业级解决方案，不一而足。

鉴于有无数的选择，我惊讶地发现，几乎没有文献可以帮助买家做出明智的决定。也许阵列验证市场太新，Gartner 和 Forrester 之类的公司无法覆盖，这是我对 JavaScript 库进行评论的典型来源。

取而代之的是，我决定花几个星期的时间来观察这片风景，并把我的发现写下来。下面是我对前三名解决方案的总结，涵盖了各种指标，包括性能、可伸缩性、可维护性、互操作性、可调试性、可读性和网络可伸缩性。

### isarray

`isarray`是一个优秀的 npm 包，可以准确检测其给定参数是不是数组。而 README 声明，“ *isarray 用于较旧的浏览器和不推荐使用的 Node.js 版本。”*我发现它在新浏览器和最新版本的 Node 上运行得相当好。

它接受一个参数并返回一个布尔值，判断给定的对象是否确实是一个数组。

#### 使用

```
var isArray = require('isarray');
console.log(isArray([])); *// => true* console.log(isArray({})); *// => false*
```

**优点**

*   简洁、高度表达的语法
*   与 MongoDB 配合良好
*   出色的投资回报率(只有 2 个月的回报期)

**缺点**

*   没有第三方插件生态系统
*   3 个公开的 GitHub 问题

### 是数组

`is-array`采用了一种完全不同的方法来验证 JavaScript 数组，在包标题中的`is`和`array`之间选择了一个连字符，而不是一个空字符串。与复合标题`isarray`相比，它显著提高了可读性，同时仍然提供了有眼光的工程师所期望的大部分功能。

它接受一个参数并返回一个布尔值，判断给定的对象是否确实是一个数组。

#### 使用

```
var isArray = require('is-array');
console.log(isArray([])); *// => true* console.log(isArray({})); *// => false*
```

**优点**

*   它很酷，因为它不太受欢迎

**缺点**

*   更长的名字意味着更大的源代码(更慢的 Git 推送，更少的可用硬盘空间，等等)
*   许可麻省理工学院许可证意味着一个大公司可以拥抱，扩展和消灭它

### Array.isArray()

最后一招，仅适用于不允许使用第三方模块的情况(企业安全约束、在甜点岛上使用等)，`Array.isArray()`是一个内置的 JavaScript 方法，它是与 ES5 标准一起引入的。

它接受一个参数并返回一个布尔值，判断给定的对象是否确实是一个数组。

#### 使用

```
console.log(Array.isArray([])); *// => true* console.log(Array.isArray({})); *// => false*
```

**优点**

**缺点**

*   不在 npm 上
*   Opera 4 中不支持
*   如果您尝试使用 webpack 导入它，将会引发错误

### 参考

* * *

Rog Locket 是一名开发人员和幽默作家，他为 LogRocket 博客撰写每周讽刺专栏。他完全是虚构的。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)