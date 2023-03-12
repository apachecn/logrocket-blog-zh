# 如何使用 uvu:一个快速轻量级的测试跑步者- LogRocket 博客

> 原文：<https://blog.logrocket.com/how-to-use-uvu/>

[uvu](https://github.com/lukeed/uvu) (终极速度的简称，unleashed)被认为是 Node.js 和浏览器最快、最轻量级的测试运行器之一。其主要特性包括单独执行测试文件、支持异步测试、支持原生 es 模块、兼容浏览器、出色的轻量级大小、熟悉的 API、卓越的性能。这篇博文将涵盖 uvu 的用法，它与另外两个流行的测试运行程序库 [Jest](https://github.com/facebook/jest) 和 [AVA](https://github.com/avajs/ava) 的比较，以及为什么以及何时使用它进行测试。

## 为什么要用 uvu？

首先，uvu 支持异步测试，这是一些测试库支持的常见优点之一。它帮助确定被测试的代码在进入下一个测试之前已经完成了测试过程。一个异步(async)函数的主要目标只是阐明使用基于承诺的 API 所必须的语法。在异步测试中，将使用类似于`callback`或`promise`的方法来决定测试过程的完成。

另一个主要特点是浏览器兼容性。起初，uvu 与浏览器不兼容是一个问题，但通过对进程文件进行一点修改，这个问题得到了解决。您可以在此找到关于问题解决方案[的讨论。所以即使你有任何关于浏览器兼容性的问题，你也可以点击这个链接来更好的理解和解决你的问题。](https://github.com/lukeed/uvu/issues/37)

## 使用 uvu

使用 uvu 很简单，工作方式如下:

```
// tests/demo.js
// Source: https://github.com/lukeed/uvu

import { test } from 'uvu';
import * as assert from 'uvu/assert';

test('Math.sqrt()', () => {
  assert.is(Math.sqrt(4), 2);
  assert.is(Math.sqrt(144), 12);
  assert.is(Math.sqrt(2), Math.SQRT2);
});

test('JSON', () => {
  const input = {
    foo: 'hello',
    bar: 'world'
  };
  const output = JSON.stringify(input);
  assert.snapshot(output, `{"foo":"hello","bar":"world"}`);
  assert.equal(JSON.parse(output), input, 'matches original');
});

test.run();
```

现在您需要做的就是执行这个测试文件:

```
# via `uvu` cli, for all `/tests/**` files
$ uvu -r esm tests

# via `node` directly, for file isolation
$ node -r esm tests/demo.js
```

关于上述命令行，需要注意的一点是，`–r esm`仅针对传统 Node.js 模块指定，因为 Ecmascript (ES)模块存放在 Node.js 版本> 12.x 中。默认情况下，`.js`和`.cjs`文件被视为 Common.js，而`.mjs`文件扩展名仅作为 Ecmascript 模块(ESM)使用。

上面的例子可以被认为是最简单的方法，Node.js 将通过这个方法加载 ES 模块，并授权它们在需要时导入任何模块。此外，您还可以通过其他方式加载模块，如下所示。

Node.js 加载 ES 模块还有其他方式。这些方法包括类型、模块和 esm 包程序。以下是这些方法的完整指南:

## 主 uvu 模块

主 uvu 模块将协助所有 uvu 测试所需的测试或测试套件(与代码中的特定功能相关的一系列独立测试)。用户可以选择是选择`uvu.test`还是`uvu.suite`。通过`uvu.suite`，你可以获得许多额外的好处，比如一次测试多个文件，而如果你想只测试一个文件，你应该选择`uvu.test`(技术上来说`uvu.test`是一个未命名的测试套件)。

## `uvu.suite(name: string, context?:T)`

您可以在同一个文件中拥有任意多的套件，但是需要调用 suites `run`来将每个套件添加到 uvu 的队列中。这只是在创建一个新套件的同时返回一个套件。此处的名称对应于套件的名称，属于字符串类型。这将把所有控制台输出组合在一起，并在任何失败的测试的名称后面加上后缀。套件的上下文有一个空对象作为默认值，可以是任何类型。这将被传递给套件中的每个测试块和钩子。

## `uvu.test (name: string, callback: function)`

如果需要只测试一个文件，您可以导入这个`uvu.test`。这里的名称显然表示测试的名称，属于字符串类型，这里的回调由测试代码组成，属于`promise<any>`或`function<any>`类型。回调可能是异步的，即使被放弃也会返回值。

## 方法

### 创造

每一个套房都可以像这样调用`suite(name, callback)`。

### 运转

为了运行套件，应该将套件添加到 uvu 测试队列并使用`suite.run()`。

### 暂时把货物腾空

跳过一个套件有助于遗漏整个测试块，如`suite.skip(name, callback)`。

## 其他方法

为了组织一个环境或建立固定装置，一个理想的情况是在诉讼开始前以下面的方式请求给定的回叫。

同样，对于完成一个环境或设备，一个理想的情况是在完成套件后以下面的方式请求回调`suite.after(callback)`。

以下是上述描述的示例代码:

```
import { suite } from 'uvu';
import * as assert from 'uvu/assert';
import * as dates from '../src/dates';

const Now = suite('Date.now()');

let _Date;
Now.before(() => {
  let count = 0;
  _Date = global.Date;
  global.Date = { now: () => 100 + count++ };
});

Now.after(() => {
  global.Date = _Date;
});

// this is not run (skip)
Now.skip('should be a function', () => {
  assert.type(Date.now, 'function');
});

// this is not run (only)
Now('should return a number', () => {
  assert.type(Date.now(), 'number');
});

// this is run (only)
Now.only('should progress with time', () => {
  assert.is(Date.now(), 100);
  assert.is(Date.now(), 101);
  assert.is(Date.now(), 102);
});

Now.run();
```

## 为什么 uvu 比 Jest 和 AVA 好

首先，我们来看一下测试跑步者的时间对比。下面是几个测试者在两个计时下运行的示例测试(通过测试示例代码获得，这里的示例代码是[这里的](https://paper.dropbox.com/ep/redirect/external-link?url=https%3A%2F%2Fgithub.com%2Flukeed%2Fuvu%2Fcommit%2F538e441d665b8dd5b6b0678b51ba98e0706392d5&hmac=IT2Hiqx2JaEWHDS0x5GxsVSfHs7XH7DX%2BojOs6sKy7I%3D))的结果。第一个值是整个流程的总执行时间，另一个值是自我报告的性能时间，前提是已知:

```
~> "ava"   took   594ms ( ???  )
~> "jest"   took   962ms (356 ms)
~> "mocha" took   209ms (4 ms)
~> "tape"   took   122ms (  ???  )
~> "uvu"   took    72ms (1.3ms)
```

从上面的结果可以明显看出，uvu 是其竞争对手中最快的选择。

现在让我们也来谈一谈功能比较:

*   AVA 和 uvu 都提供异步测试，而 Jest 没有
*   Jest 和 uvu 允许你很容易地集成到其他应用程序中，而 AVA 作为一个极简测试库，不像其他两个提供这样的集成
*   AVA 只包含一个简单的 API，需要安装一个额外的库来支持模仿，而 Jest 和 uvu 有广泛的 API，不需要用户包含额外的库来支持众多的特性

## 结论

人们总是担心测试跑步者的表现，但 uvu 提供的功能已被证明是最好的功能之一。对于担心浏览器兼容性、高速测试、支持本地 ES 模块、异步测试以及从单个库中单独执行测试文件的人来说，它就像一个多合一的库。因此，每当你对所有这些事情感到焦虑时，你只需要切换到一个解决方案，那就是 uvu。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)