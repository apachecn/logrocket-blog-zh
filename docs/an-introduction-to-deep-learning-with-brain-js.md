# Brain.js - LogRocket 博客深度学习介绍

> 原文：<https://blog.logrocket.com/an-introduction-to-deep-learning-with-brain-js/>

使用 [Brain.js](https://brain.js.org/#/) 是构建[神经网络](https://en.wikipedia.org/wiki/Artificial_neural_network)的一种奇妙方式。它学习输入和输出之间的模式和关系，以便在处理相关问题时做出有根据的猜测。神经网络的一个例子是 [Cloudinary 的图像识别附加](https://cloudinary.com/addons)系统。

神经网络的 JavaScript？那是什么意思？我第一次看 Brain.js 的文档时也很震惊，然而，我真的对此感到兴奋。

## TL；速度三角形定位法(dead reckoning)

在本帖中，我们将讨论理解神经网络如何工作的一些方面。我们将学习向前和向后传播等术语，以及机器学习社区中使用的一些其他术语。然后，我们将利用 Brain.js 的能力，使用一个神经网络构建一个日常会议日程安排应用程序。

## 先决条件

在我们进一步讨论之前，本文假设:

*   [Node.js ≥ v6](https://nodejs.org/) 安装在您的机器上
*   您的机器上安装了 npm
*   你对 JavaScript 有所了解

## 什么是神经网络？

人工神经网络最终是生物神经网络工作机制的复制品，映射出学习能力。这种系统通过考虑例子来“学习”执行任务，通常没有用特定于任务的规则来编程。

## 这是什么意思？

人类的学习基本上是通过匹配图案和像素来推断像素放在一起时视觉上代表什么。使用一种被称为[多层感知器](https://en.wikipedia.org/wiki/Multilayer_perceptron)的方法来执行一些梯度下降。其工作方式是通过神经链上的不同点来组合模式，直到通过将像素匹配到边缘，然后将边缘匹配到模式，再将模式匹配到图形来达到结果。为了更全面地理解这个概念，查看这个[教程](https://www.youtube.com/watch?v=aircAruvnKk&t=106s)。

## Brain.js 是什么？

根据[官方文件](https://github.com/BrainJS/brain.js#Installation-and-Usage):

> Brain.js 是针对浏览器和 Node.js 用 JavaScript 编写的神经网络 GPU 加速库，简单、快速、易用。它提供了多种神经网络实现，因为不同的神经网络可以被训练来很好地做不同的事情。

我认为这真的很酷，特别是因为大多数关于机器学习的材料主要集中在 python 上，这使得来自 web 背景的开发人员的学习曲线有点陡峭。大脑。JS 为此提供了一个解决方案。

Brain.js 并不是第一个专注于 JavaScript 的机器学习库。然而，我个人推荐它，因为它使用 JSON 对象，开发者无需创建[张量](https://www.tensorflow.org/tutorials/customization/basics)并进行内存管理。

## 入门指南

当使用 Node.js 应用程序时，您可以使用以下方法将 Brain.js 安装到您的项目中，我们将在本节中介绍这些方法。

虽然根据[文档](https://brain.js.org/#/)，在处理项目中的库时，NPM 通常是首选:

> Brain.js 依赖原生模块`headless-gl`来获得 gpu 支持。在大多数情况下，从 npm 安装 Brain.js 应该就可以了。然而，如果您遇到问题，这意味着预构建的二进制文件不能从 Github 库下载，您可能需要自己构建。

#### 1)使用 NPM 安装

如果可以用 [npm](http://npmjs.org) 安装 brain.js:

```
npm install brain.js
```

确保以下依赖项已安装并且是最新的，然后运行:

```
npm rebuild
```

目前，我们将在 **CDN 实现**中使用 brain.js。

#### 2)在 CDN 上服务

```
<script src="//unpkg.com/brain.js"></script>
```

## 构建一个基本的异或门

现在，这并不是这篇文章的重点。我很想用这个例子来解释向后和向前传播，同时解释组成 Brain.js 的一些组件。我们可以像普通应用程序一样设置我们的项目。

一个[异或门](https://en.wikipedia.org/wiki/XOR_gate)是一个逻辑门，当输入值相同时输出一个`0`，当输入值不同时输出一个`1`。我们将通过以下步骤来实现这一点。

在`index.html`中，我们通过 CDN 导入 Brain.js 库，如下所示:

```
<html>
  <head>
    <script src="//unpkg.com/brain.js"></script>
    <script src = " index.js"> </script>
  </head>
</html>
```

然后我们进行到`index.js`来实现逻辑:

```
const net = new.brain.NeuralNetwork({hiddenLayers :[3]});
Const _Data = [
    {input : [0,0], output: [0]},
    {input : [0,1], output: [1]},
    {input : [1,0], output: [1]},
    {input : [1,1], output: [0]}
];
net.train(_Data);
console.log(net.run([0,0]));
console.log(net.run([0,1]));
console.log(net.run([1,0]));
console.log(net.run([0,0]));
```

从上面的代码块中我们可以看到，我们在该文件的第 1 行创建了一个 Brain.js 实例，因此我们能够使用它。我们还可以注意到一个`.train`对象，它实际上是用来训练系统的。

注意`hiddenLayers`被设置为`3`。记得我提到过一些层匹配像素到边缘，然后边缘到图案，然后图案到图形，这是由隐藏层完成的。

注意:使用`train()`用一组训练数据训练网络。网络必须在对`train()`的一次调用中批量训练所有数据。[更多的训练模式](https://brain.js.org/#/examples)可能需要更长的训练时间，但通常会使网络更好地对新模式进行分类。

在控制台结果中，我们可以看到输出并不完全输出`0`和`1`。然而，这并不意味着机器是错的。然而，它会给出一个最接近`0`或`1`的值。我们可以看到以下输出:

```
[0.038714755326509476]
[0.9301425814628601]
[0.9356828331947327]
[0.970003753900528]
```

现在让我们看看这实际上是如何做出这些假设的。

## 正向传播和反向传播

在前面的代码示例中，我们注意到`.train`方法接受了`_Data`，这是它处理向前传播和向后传播的地方，以便对输出应该是什么做出一些有根据的猜测。

解释这个概念的一个很常见的方法是用球和球门问题。比方说，一个足球运动员正在练习任意球，他必须计算出球需要飞多远，以及在踢球时要投入多少精力。基本上，他预测球要走多远，要放出多少能量，这叫做向前传播。

当我们试图测量从传播(球)回到源(球员)的距离时。这就是所谓的反向传播。如果玩家一遍又一遍地练习踢腿，他们会收集更多关于距离和所需能量的数据点。这个过程一直持续下去，直到我们达到目标。

在这个发生在`.train`方法阶段的前向和后向传播过程之后，`.run`方法接收输入的实际值，并根据训练数据给出输出。

神经网络不再需要测量与目标的距离，因为它现在已经有了执行其判断所需的实际数据(神经网络已被训练)。它现在可以给出精确的值。有点像足球运动员练习任意球。

注意:它的实际结构利用了一组通过激活函数(sigmoid 或 relu)传递的随机数(math.random)。

## 示例:创建会议日程

现在，根据我们从前面的例子中获得的知识，我们可以尝试创造一些有趣的东西。我每周都有会议，很难掌握所有的事情。所以我可以创建一个神经网络来帮助解决这个问题。

在这里，我列出了要做的事情和目的。我的目标是简单地问神经网络在一周的哪一天做什么。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
const meetings = {
"Stand-up with PM" : "Monday",
"Gym with frank" : "Tuesday",
"Check in with mentees" : "Wednesday"
"Take dogs for a walk" : "Thursday"
"Get drinks with RICO": "Friday"
"Call mom": ""
}

```

注意:我的训练数据输入是星期几，输出是任务。

## 如何构建培训数据

在同一个`index.js`中，我们将实现几行代码来训练网络。

```
Const _Data = [];
for(let taskName in meetings ){
  const dayOfWeek = meetings[tasks];
  _Data.push({
    input:{[dayOfWeek] : 1},
    input:{[taskName] : 1},
  });
}
```

上面的代码块迭代 meetings 对象，并将该值推送到训练数据`_Data`中，以便稍后用于训练网络。这个`_Data`接受一个输入作为一周中的某一天，该天被赋予一个值`1`。这意味着无论何时选择了一周中的某一天，该值自动为 1，所有其他值将被设置为`0`，因为使用 Brain.js 时，所有未定义的值都被设置为`0`并作为任务输出。

## 如何定义神经网络和训练

正如我们之前看到的，我们要做的就是创建一个 Brain.js 的新实例，如下所示:

```
const net = new brain.NeuralNetwork({hiddenLayers: [3]});
const stats = net.train(_Data)
console.log(net.run({'Monday': 1}));
```

上面的代码显示的是每一天的可能性，因此它返回一个包含所有日期及其概率的列表。然而，我想要的只是一天。所以我们创建了一个函数:

```
function SpecificDay(dayOfWeek){
  const result = net.run({[dayOfWeek] : 1});
  let highestvalue = 0;
  let highestTaskName = '';
  for(let taskName in result){
    if (result[taskName] > highestvalue){
      highestvalue = result[taskName];
      highestTaskName = taskName;
    }
  }
    return highestTaskName ;
}

```

上面的代码(也可以在 [codepen](https://codepen.io/ekwunoobinna/pen/RwwYLWQ) 上找到)获取神经网络预测，对其进行迭代，然后保存最高值并将其返回。

因此，如果我们通过运行以下命令来记录:

```
Console.log(SpecificDay("Wednesday"))
```

我们回到“与学员一起检查”。

## 结论

在本文中，我们讨论了机器学习社区中经常使用的几个概念，同时重点关注 Brain.js 以及它如何使用这些概念。在为这篇文章做研究时，我真的很兴奋，我真的很想用 Brain.js 构建更多的东西。快乐编码😄。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.