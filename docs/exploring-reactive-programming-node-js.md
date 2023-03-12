# 使用 Node.js - LogRocket 博客探索反应式编程

> 原文：<https://blog.logrocket.com/exploring-reactive-programming-node-js/>

## 介绍

反应式编程为高级数据流提供了以可预测的方式创建和操作事件流的能力。

本文将教 Node.js 开发人员如何在 Node 中应用反应式编程，以及它的好处和缺点。

本文将涵盖以下内容:

1.  反应式编程基础
2.  为什么要考虑 Node.js 中的反应式编程？
3.  何时使用反应式编程方法
4.  反应式编程的好处
5.  反应式编程的缺点
6.  介绍编排及其优点/缺点
7.  用于节点的反应式编程库

## 什么是反应式编程？

简而言之，当输入变化导致输出的相应变化而不需要手动更新输出变化时，程序被称为是反应式的。这允许软件工程师绕过手动处理大型实现所带来的压力。

函数式反应式编程范例使我们的反应式代码库易于阅读和理解，因为它减少了回调地狱，这使得异步代码块难以阅读。

由于反应式编程与异步操作有很大关系，函数式方法使得确定异步操作的结果更加容易。

## 反应式编程基础

### 经营者

运算符是 Observables 非常依赖的方法。他们有以下使用案例:

*   处理异步请求时将异步事件转换为可观察事件
*   将多个可观察序列组合成一个可观察序列
*   错误处理
*   处理基于时间的操作

可观察算子包括`[filter(...)](https://rxjs.dev/api/operators/filter)`、`[mergeMap(...)](https://rxjs.dev/api/operators/mergeMap)`、`[of](https://rxjs.dev/api/index/function/of)`、`[from](https://rxjs.dev/api/index/function/from)`、`[concat](https://rxjs.dev/api/index/function/concat)`等方法。

### 可观察的流

一个[可观察的](https://blog.logrocket.com/understanding-rxjs-observables/)流是随时间处理的多个输入值的数组。一个可观察的流向它的订阅者发出事件，订阅者依次监听这些事件以进行进一步的处理。可观察的流可以被组合以创建新的流。使用数组方法如`map`、`reduce`、`filter`等来操作流。

可以将值发送给订阅者，如下所示:

```
import { of, Observable } from "rxjs"; 
const emitter : Observable<string> = of("Sam", "Ray", "Thomas");
```

### 订阅者

可观察订户更像数组迭代器。它们循环遍历产生的可观察的流，使得转换或处理每个流成为可能。

下面的片段显示了如何订阅可观察的流:

```
emitter.subscribe((value: string) => {
  console.log(`Name: ${value}`)
})

```

反应式编程有一些内置的订阅方法，如`emit`和`flatMap` map 方法，它们允许我们监听可观察流的每个值，并处理它们以满足我们的需求。

## 反应系统的标准

完全反应式 Node.js 系统应满足以下标准:

### 响应式建筑

一个反应式系统应该通过对用户交互提供及时的响应来拥有良好的用户体验。

### 弹性建筑

如果正确实施，弹性架构将允许系统在不破坏整个系统的情况下对错误做出响应。

这种体系结构确保每个节点都有一个副本。如果主节点出现故障，其他可用节点将会有某种形式的后备。

### 可攀登的

该系统应该能够处理变化的负载，这与它在基础设施需要很少或不需要资源时缩小规模，以及在基础设施需要更多资源时扩大规模的能力有关，以便提供有效的成本管理策略。

此外，系统还应该能够处理时间点负载。

## 为什么要考虑 Node.js 中的反应式编程？

既然我们已经简要讨论了反应式编程的基础，那么了解考虑使用 Node.js 进行编程的反应式方法的原因也是很重要的。

### 可量测性

编写功能性的反应式代码可以更容易地管理代码库，并提高项目的可伸缩性。

### 功能实现

对于需要定期更改功能或添加新功能的项目，编写功能性反应代码可以更容易地将新功能添加到现有项目中。

### 与时间相关的错综复杂

当向外部 API 发出异步请求时，我们确实会遇到一些时间限制约束。这些约束可以用编程的反应方法有效地处理。

### 减少代码冗长

实现反应式编程范例将大大减少实现给定特性所需的代码量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 介绍编排及其优点/缺点

在反应式编程出现之前，使用 Node.js 构建微服务需要 orchestrator 模式来协调所有的服务交互。

orchestrator 模式的一个典型用例是在电子商务应用程序中使用微服务来依次处理以下任务:从购物车中获取客户订单，计算总金额，生成账单，在成功支付后，更新产品库存，并为卖方创建一个带有`Pending`状态的订单 ID。

虽然这为处理应用程序的逻辑流提供了一种系统的方法，但是依赖关系的紧密耦合的一个主要缺点是会破坏整个系统。例如，如果前面的服务关闭，那么所有相关的服务都不会被执行。

## Node.js 中何时使用反应式编程方法

反应式编程并不是一种放之四海而皆准的方法，但是它有一些非常适合的特定情况:

*   反应式编程模式非常适合需要将应用程序流分散到可管理的微服务中的情况
*   当将应用程序交付生产的时间有限时
*   当前依赖关系中的临时关闭可能导致整个系统崩溃
*   当有大量异步代码块并且等待的结果可能会被延迟时，反应式编程也非常适合

## Node.js 中反应式编程的缺点

尽管函数式反应式编程方法减少了 orchestrator 模式遇到的缺点，但它不能取代 orchestrator 模式，因为它有自己的缺点:

*   由于分解应用程序流和跨所有服务分布而产生的冗余代码块
*   为了构建反应式服务，需要对流和事件循环有透彻的理解

## Node.js 中流行的反应式编程库

### RxJS

这是 JavaScript 中最流行的反应式编程库之一，被积极地维护着。

在撰写本文时，RxJS 正处于从 v7 到 v8 的过渡阶段，它在上周的下载量超过了 2700 万次。这一转变的特点是重写了库，以获得更好的性能、更好的模块化、更好的可调试调用堆栈和向后兼容性。

下面是 RxJS 用法的一个简单例子:

```
import { range } from "rxjs";
import { map, filter } from "rxjs/operators";

range(1, 200)
  .pipe(
    filter(result => result % 2 === 1),
    map(result => result * 2 )
  )
  .subscribe(result => console.log(result));

```

### 反应器. js

Reactor.js 是另一个用于[反应式编程](https://en.wikipedia.org/wiki/Reactive_programming)的 JavaScript 库。虽然与 Bacon.js 和 Rxjs 相比，它并不太受欢迎，但它以重量轻而闻名。使用 Reactor.js 维护复杂数据模型的一致性要容易得多，因为它会自动跟踪反应变量，并在任何反应变量的值发生变化时重新触发观察器。
使用 Reactor.js，不需要手动设置订阅/侦听器，因为依赖关系是自动为您设置的。

下面是 Reactor.js 用法的一个简单示例:

```
const reactor = new Reactor({ name: "Doe" });

observe(() => {
  console.log("My name is ", reactor.name);
}); // prints "My name is Doe"

reactor.name = "John "; // prints "My name is John"

```

Reactor 基于与 [Bacon.js](https://github.com/baconjs/bacon.js) 和 [Knockout.js](https://knockoutjs.com) 相同的反应原理。

其他用于反应式编程的 JavaScript 库包括:

*   飞行
*   培根. js
*   Knockout.js
*   克菲尔
*   最

## 结论

在本文中，我们探讨了反应式编程，它的好处，以及它何时最适合我们的 Node.js 项目。此外，我们还讨论了编排、其优点/缺点，以及 Node.js 中用于反应式编程的 JavaScript 库。

希望这篇文章对你有所帮助。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.