# 使用 GPU.js 提高 JavaScript 性能

> 原文：<https://blog.logrocket.com/improving-javascript-performance-with-gpu-js/>

你有没有尝试过运行复杂的计算或运算，却发现它花费了你无穷无尽的时间，减慢了你的进程？

有很多方法可以解决这个问题，比如使用 web workers 或者后台线程。GPU 从你的 CPU 中带走处理负载，给你的 CPU 更多空间用于其他进程。与此同时，web workers 仍然在您的 CPU 上运行，但是在不同的线程上。

在这本初学者指南中，我们将演示如何使用 [GPU.js](https://gpu.rocks/#/) 来执行复杂的数学计算，并提高 JavaScript 应用的性能。

## GPU.js 是什么？

GPU.js 是为 web 构建的 JavaScript 加速库，Node.js 用于图形处理单元(GPGPU)上的通用编程。它允许您将复杂和耗时的计算交给 GPU，而不是 CPU，以获得更快的计算和操作。还有一个后备选项:在 GPU 不在您的系统上的情况下，这些函数仍将在您的常规 JavaScript 引擎上运行。

当您的系统需要执行复杂的计算时，您实际上是将这一负担交给了系统的 GPU，而不是 CPU，从而提高了处理速度和时间。

高性能计算是使用 GPU.js 的主要优势之一。如果您希望在没有任何 WebGL 知识的情况下在浏览器中开始并行计算，GPU.js 是您的理想选择。

## 为什么应该使用 GPU.js

有数不清的理由让你应该使用 GPU 来执行复杂的计算，太多了，无法在一篇文章中探究。下面是使用 GPU 的一些最值得注意的好处。

*   GPU 可用于执行大规模并行 GPGPU 计算。这就是需要异步完成的计算类型
*   当 GPU 在系统中不可用时，它优雅地退回到 JavaScript
*   GPU 目前运行在浏览器和 Node.js 上，这是加速计算繁重的网站的理想选择
*   构建 GPU.js 时考虑了 JavaScript，因此函数使用合法的 JavaScript 语法

如果你认为你的处理器能够胜任这个任务，并且你不需要 GPU.js，看看下面用这个 GPU 和 CPU 运行计算的结果。

![GPU.js Texture Mode](img/535703a68c0a12c64fa0b8fd7e2d88ae.png)

Source: [Hackernoon](https://hackernoon.com/hn-images/1*FHKg0VBQi_S-sMlLKdJ-MQ.png)

可以看到，GPU 比 CPU 快 22.97 倍。

## GPU.js 如何工作

考虑到这种速度水平，JavaScript 生态系统就好像有了一个可以乘坐的火箭。GPU 帮助网站加载速度更快，尤其是那些必须在首页执行复杂计算的网站。你不再需要担心使用后台线程和加载程序，因为 GPU 运行计算的速度比普通 CPU 快 22.97 倍。

`gpu.createKernel`方法从一个 JavaScript 函数创建一个 GPU 加速内核。

将内核函数与 GPU 并行运行会加快计算速度，根据硬件的不同，速度会提高 1 到 15 倍。

## GPU.js 入门

为了展示如何使用 GPU.js 更快地计算一个复杂的计算，让我们来做一个快速、实用的演示。

安装:

```
sudo apt install mesa-common-dev libxi-dev  // using Linux

```

npm:

```
npm install gpu.js --save

// OR

yarn add gpu.js

```

在您的节点项目中需要 GPU.js。

```
import { GPU } from ('gpu.js')

// OR
const { GPU } = require('gpu.js')

const gpu = new GPU();

```

## 乘法演示

在下面的例子中，计算是在 GPU 上并行完成的。

首先，生成大量数据。

```
  const getArrayValues = () => {

    //Create 2D arrary here
    const values = [[], []]

    // Insert Values into first array
    for (let y = 0; y < 600; y++){
      values[0].push([])
      values[1].push([])

      // Insert values into second array
      for (let x = 0; x < 600; x++){
        values\[0\][y].push(Math.random())
        values\[1\][y].push(Math.random())
      }
    }

    //Return filled array
    return values
  }

```

创建内核(运行在 GPU 上的函数的另一种说法)。

```
  const gpu = new GPU();

  // Using `createKernel()` method to multiply the array
  const multiplyLargeValues = gpu.createKernel(function(a, b) {
    let sum = 0;
    for (let i = 0; i < 600; i++) {
      sum += a\[this.thread.y\][i] * b\[i\][this.thread.x];
    }
    return sum;
  }).setOutput([600, 600])

```

用矩阵作为参数调用内核。

```
  const largeArray = getArrayValues()
  const out = multiplyLargeValues(largeArray[0], largeArray[1])

```

输出:

```
console.log(out\[y\][x]) // Logs the element at the xth row and the yth column of the array
console.log(out\[10\][12]) // Logs the element at the 10th row and the 12th column of the output array

```

## 运行 GPU 基准测试

您可以按照 [GitHub](https://github.com/gpujs/benchmark) 上指定的步骤运行您的基准。

```
npm install @gpujs/benchmark

const benchmark = require('@gpujs/benchmark')

const benchmarks = benchmark.benchmark(options);

```

`options`对象包含可以传递给基准的各种配置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

前往 GPU.js 官方网站查看完整的计算基准。这将有助于您理解使用 GPU.js 进行复杂计算可以获得多少速度。

## 结论

在本教程中，我们详细探讨了 GPU.js，分析了它的工作原理，并演示了如何执行并行计算。我们还展示了如何在 Node.js 应用程序中设置 GPU.js。

继续编码！

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。