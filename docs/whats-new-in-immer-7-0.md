# Immer 7.0 的新功能- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-immer-7-0/>

6 月 10 日发布的 Immer 7.0 包括许多新的创新、优化和突破性的变化。在本指南中，我们将了解 Immer 最新版本的新增功能。

我们将介绍一些最具影响力的变化，包括:

*   `current`的介绍
*   `getters`和`setters`现在被一致地处理
*   `produce`不再接受不可绘制的对象作为第一个参数
*   只能在汇票上调用
*   数组面片计算

我们开始吧！

## 什么是 Immer？

您是否曾经想在 JavaScript 中使用不可变状态，但却以一种不方便的方式结束了工作，或者编写了许多行 JavaScript 代码？Immer 是一个很小的包，它允许你以一种更方便的方式使用不可变状态。

基本思想很简单:Immer 将您的所有更改应用到一个草稿状态，这是当前状态的代理。一旦你完成了草稿状态的改变，Immer 就会产生下一个状态。这意味着您的当前状态对象是来自突变的状态，并且保留了不变性的所有好处。

## 工作演示

让我们来看一个简单的演示。

### 装置

首先，升级到 Immer 的最新版本。在撰写本文时，[版本 7.0.5](https://github.com/immerjs/immer/releases/tag/v7.0.5) 是最新发布的版本。

```
yarn add immer
// OR
npm install immer

```

打开一个 JavaScript 文件并粘贴以下内容。

```
import produce from "immer"
const todos = [ //baseState
  {
    text: “learn Immer”
    done: false
  }
  {
    text: “learn cookimg
    done: true
  }
]
const nextState = produce (todos, draftState) = {
  draftState.push ({text: “writing”, done: false})
  draftState[0].done = true
});
// Comparisons
console.log(todos === nextState) //false
console.log (todos[0].done === nextState[0].done) //false
console.log(todos[1].done === nextState[0].done) // true

```

`baseState`将保持不变，但是`nextState`将是一个新的、不可变的树，它反映了对`draftState`所做的所有更改(并且在结构上共享未更改的内容)。

上面的演示应该让你对 Immer 有一个很好的了解。欲了解更多信息，请查看[文档](https://immerjs.github.io/immer/docs/introduction)。

现在让我们深入了解最新的更新。

## Immer 7.0 的 5 个主要变化

现在让我们深入了解最新的更新。

### 1.`current`方法

Immer 公开了一个命名的 export，`current`，它创建了草稿当前状态的副本。它会截取草稿的当前状态，并在不冻结对象的情况下完成它。这对于调试当前状态非常有用，因为这些对象不是代理对象。

这里有一个例子:

```
const base = {
    x: 0
}

const next = produce(base, draft => {
    draft.x++
    const orig = original(draft)
    const copy = current(draft)
    console.log(orig.x)
    console.log(copy.x)

    setTimeout(() => {
        // this will execute after the produce has finised!
        console.log(orig.x)
        console.log(copy.x)
    }, 100)

    draft.x++
    console.log(draft.x)
})
console.log(next.x)

// This will print
// 0 (orig.x)
// 1 (copy.x)
// 2 (draft.x)
// 2 (next.x)
// 0 (after timeout, orig.x)
// 1 (after timeout, copy.x)

```

可以看到 [`current`方法](https://immerjs.github.io/immer/docs/current)对`draft`的效果。

### 2.一致地处理 Getters 和 setters

如果你曾经将 Immer 与 Vue 或 React 一起使用，并试图与[类](https://immerjs.github.io/immer/docs/complex-objects)一起工作，你会熟悉它总是产生的错误:

`// Immer drafts cannot have computed properties.`

在 Immer 7.0 中，这个错误正式绝迹。拥有的 getters 参与复制过程，就像`Object.assign`一样。

您可以在[文档](https://immerjs.github.io/immer/docs/complex-objects#semantics-in-detail)中阅读更多关于 getters 和 setters 的内容。

## 3.不可制图的物体

不再接受不可绘制的对象作为第一个参数。不可起草对象是不可 JSON 序列化的对象，比如 JavaScript 中的`Date`对象(除非转换成字符串)。

Immer 7.0 [不支持这种类型的对象](https://github.com/immerjs/immer/blob/master/docs/complex-objects.md)，遇到一个就会抛出异常。

## 4.正本只能在汇票上兑付

你有没有试过在不能被代理的对象上调用 original，却被那个难看的`undefined`返回类型卡住了？对于版本 7，Immer 团队在原始 API 中添加了一个可捕捉的错误，所以它没有返回`undefined`，而是抛出了一个错误。

## 5.数组面片计算

阵列的修补程序现在以不同的方式计算，以修复它们不正确的各种情况。在某些情况下，它们现在更理想；在
其他人看来，就没那么回事了。例如，将项目拼接或取消移动到现有数组中可能会产生大量补丁。在 Immer 7.0 中，这不再是一个问题。

## 结论

在本指南中，我们介绍了 Immer 7.0 中一些激动人心的新功能和四个突破性的变化。下面列出了超出本文范围的其他错误修复和重大更改。

### 错误修复

1.  生产状态[的所有分支都应被冻结](https://github.com/immerjs/immer/issues/462)
2.  与[嵌套的`produce`](https://github.com/immerjs/immer/issues/588) 行为不一致
3.  不适用于[多填充符号](https://github.com/immerjs/immer/issues/577)
4.  显式调用 [`useProxies(false)`](https://github.com/immerjs/immer/pull/609) 不应该[检查代理](https://github.com/immerjs/immer/issues/514)的存在
5.  `getownPropertyDescriptors`在[浏览器](https://github.com/immerjs/immer/commit/c7a47e251e9289561d7a7c539576c80e17dae2de)或[爱马仕](https://github.com/immerjs/immer/issues/626)中不可用

前往 [GitHub](https://github.com/immerjs/immer/releases/tag/v7.0.0) 获取错误修复的完整列表。

### 重大变化

1.  不可数和符号字段永远不会被冻结

GitHub repo 有一个完整的重大变更列表。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。