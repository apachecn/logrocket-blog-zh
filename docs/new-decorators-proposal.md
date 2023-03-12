# 新装修者提案

> 原文：<https://blog.logrocket.com/new-decorators-proposal/>

JavaScript 从来没有被设计成经典的 OOP 编程语言，这是最初开发时最常见的编程思想。它本来是一种简单的脚本语言，有一些特性，比如函数和 JSON 对象结构。总有一些黑客强迫 JavaScript 函数充当一个类，但这更多的是一种变通办法，而不是有意的设计。

今天，JavaScript 运行在几乎所有的软件平台上，是世界上最流行和最通用的编程语言之一。在过去的几年里，ECMAScript 社区一直在将曾经非常简单的语言塑造成一种更强大的语言，以帮助 JavaScript 保持其主导地位。结果，我们现在有了全功能的类，并且可以[用 JavaScript](https://blog.logrocket.com/5-must-read-javascript-tutorials-2631123543f6/) 做比我们想象的更多的事情。

装饰器代表了这一演变中最重要的步骤之一，它们对于保持 JavaScript 与 Java、Python、C#等语言的竞争力至关重要。让我们仔细看看 decorators，看看我们如何使用它们从 JavaScript 中获得更多的功能。

## 什么是装修工？

如果您是 Python 或 Java 开发人员，您可能已经熟悉了术语 decorator。但是关于是否将 decorators 作为核心语言特性包含在 JavaScript 中的问题已经被激烈争论了多年。在不影响语言解释性能的情况下创建这样一个特性并不是一件容易的事情，因为这样做会直接影响操作函数的方式。

```
import { @logged } from "./logged.mjs";

class C {
  @logged
  method(arg) {
    this.#x = arg;
  }

  @logged
  set #x(value) { }
}

new C().method(1);
// starting method with arguments 1
// starting set #x with arguments 1
// ending set #x
// ending method

```

例如，上面显示的`@logged`装饰器实际上包装了函数，并在调用给定函数时打印日志。这似乎是一件容易实现的事情，但是用许多装饰符包装一个函数会导致堆栈溢出，因为它发生在递归中，调用了太多的函数。

这就是为什么 decorators 是 ES 社区中持续时间最长的特性请求之一。事实上，这是该功能的第二次迭代。由于大型应用程序的内存消耗问题，它最初很难获得广泛采用。这个迭代被优化了，但是看起来我们需要等待原生版本。

## 编写自定义装饰器

装饰器是一个简单的函数，它接收要调用的参数函数。这意味着解释应该用装饰函数本身包装原始函数，并用原始函数名保存在内存中。

让我们深入研究代码，以描绘一幅更清晰的画面。

```
// logged.mjs

export decorator @logged {
  @wrap(f => {
    const name = f.name;
    function wrapped(...args) {
      console.log(`starting ${name} with arguments ${args.join(", ")}`);
      f.call(this, ...args);
      console.log(`ending ${name}`);
    }
    Object.defineProperty(wrapped, "name", {
      value: name,
      configurable: true
    });
    return wrapped;
  })
}

```

如您所见，还有另一个装饰器`@wrap`，它实际上执行了函数包装技巧。这是少数几个可以在任何地方使用的内置装饰器之一:

*   `@wrap` —用给定函数的返回值替换方法或整个类
*   `@register` —创建类后调用回调
*   `@expose` —创建类后，用函数调用回调以访问私有字段或方法
*   `@initialize` —创建类的实例时运行回调

在这个特殊的例子中，我们有一个使用本机`@wrap`装饰器包装成传递函数的`@logged`装饰器。

## JIT 和 decorators 的问题

JavaScript 的 JIT 可以优化大多数编码用例，但它只在预热过程中运行基本优化，在预热过程中它初始化全局环境、函数等。实际的装饰器稍后运行，并包装/更改一个已经优化的函数，这导致了非优化的代码。稍后，当那个装饰器被调用时，JIT 将针对那个特定的情况再次运行并优化它。这意味着装饰者越多，JIT 在代码执行的“快速”阶段运行的次数就越多。毫不奇怪，装饰者被认为是非常耗费资源的。

按照第二阶段的建议，开发人员不会依赖 JIT 优化。相反，他们会尝试通过制作一些预定义的装饰器并基于它们构建其他装饰器来优化事情。这应该有助于解决一些内存分配问题，因为标准的、优化的 decorators 将覆盖大多数资源密集型操作。

## 代码静态分析

分析 JavaScript 代码很困难，因为函数返回的不是静态类型，而且 VSCode 或 Webstorm 并不总是能猜出函数要返回什么。对于 decorator，这个任务甚至更难，因为按照设计，像`@register`这样的 decorator 会改变函数的工作方式——以及返回类型。这意味着 ES 社区不仅要负责 decorators 的优化实现，还要负责所有其他支持性的库和 ide。

## 是时候采用装修工了吗？

在生产中使用 decorator 有点早，但是很多公司已经在使用 TypeScript/Babel decorator 了。当然，你会看到一些林挺错误，告诉你一个装饰器会消耗很多内存，但是你仍然可以使用它们。ECMAScript 社区没有在生产中推出它的明确计划，但是对于 JavaScript 编码人员来说，这是使用 JavaScript 进行完整功能编程循环的第二次机会。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.