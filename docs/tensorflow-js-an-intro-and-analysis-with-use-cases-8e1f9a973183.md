# TensorFlow.js:用例介绍和分析

> 原文：<https://blog.logrocket.com/tensorflow-js-an-intro-and-analysis-with-use-cases-8e1f9a973183/>

# TensorFlow.js:介绍和用例分析

## 

2019 年 4 月 24 日 3 min 阅读 941

## TensorFlow.js 是什么？

[TensorFlow.js](https://js.tensorflow.org) 是 Google 开发的 JavaScript 库，用于在浏览器中训练和使用机器学习(ML)模型。它是 [TensorFlow](https://www.tensorflow.org/) 的配套库，后者是一个流行的 Python ML 库。请继续阅读，了解它的特性、未来，以及它可以如何帮助您。

### 什么是机器学习？

机器学习(通常缩写为 ML)是人工智能的一个领域，粗略定义为研究允许计算机在没有特定指令的情况下执行任务的程序和算法。

一个相当典型的“监督学习”ML 程序的工作原理是创建一个“模型”，类似于一个数学模型，有输入和输出。然后，它接受一批具有输入和实际输出的训练数据，并通过调整模型的参数来“训练”自己，以最小化模型的误差量。换句话说，程序将改变模型，试图使其输出与期望的“实际”输出相匹配。

如果你想了解更多关于机器学习或人工智能的知识，请阅读[这篇文章](https://medium.com/machine-learning-for-humans/why-machine-learning-matters-6164faf1df12)或[机器学习维基百科条目](https://en.wikipedia.org/wiki/Machine_learning)。

## TensorFlow.js 的酷之处

下面是对 TensorFlow.js 的快速分析:

### 速度

TensorFlow.js 因为使用了 WebGL(一种 JavaScript 图形 API)，所以是硬件加速的，所以性能出奇的好。

TensorFlow 的 Node.js 版本, [tfjs-node](https://github.com/tensorflow/tfjs-node) 也存在，并提供比浏览器版本更好的性能。

### 加载现有模型

我最喜欢 TensorFlow.js 的一个特性是它允许你加载预训练的模型。这意味着你可以使用像[这个](https://github.com/tensorflow/tfjs-models)这样的库，并在你的网站上包括图像分类和姿势检测，而不需要自己训练模型。

TensorFlow.js 还允许您加载您在 TensorFlow 的 Python 版本中训练过的模型。这意味着您可以使用 Python 编写一个模型并对其进行训练，然后将其保存到 web 上的一个可用位置，并将其加载到您的 JS 中。这种技术可以显著提高性能，因为您不必在浏览器中训练模型。

## 用例

越来越多的企业正在使用机器学习来改善与用户的互动。人工智能程序处理一切事情，从自动驾驶汽车到视频游戏中的配对，像 Siri 和 Alexa 这样的聊天机器人，以及为用户建议内容。然而，在过去，机器学习是在后端服务器上处理的。

TensorFlow.js 的创建意味着*你可以在一个静态的 HTML 文档中创建和运行 AI 模型。*是的，你没听错:你可以不用设置服务器甚至数据库就能使用 AI。只要用户的浏览器支持 JavaScript(最好是 WebGL ),你就可以训练和使用 ML 模型，都是客户端的。

以下是 ML 的一些用法(并不是所有的例子都使用 TensorFlow.js)让你的头脑充满可能性:

*   **创作抽象艺术:**虽然这个例子对现实世界没那么“有用”(除非你想成为一名艺术品经销商)，但这是我最喜欢的例子之一。查看[这一页](http://blog.otoro.net/2015/06/19/neural-network-generative-art/)和[这一页](https://janhuenermann.com/blog/abstract-art-with-ml)的一些美丽迷人的例子。
*   **生成逼真的图像:**[thispersondoesnotexist.com](https://thispersondoesnotexist.com/)最近因使用生成性对抗网络生成全新人物的图像而成为新闻。[本网站](https://ai.googleblog.com/2015/06/inceptionism-going-deeper-into-neural.html)解释了谷歌开发的神经网络如何在不相关的图像中“找到”物体。
*   **玩游戏:**在视频游戏中使用 AI 玩家并不是一个新想法，在 TensorFlow.js 中已经有了例子[这个项目](https://github.com/MagicCube/tensorflow-rex-run)使用 TensorFlow.js 来自动化 Chrome 恐龙游戏。
*   **推荐内容**:通过 AI 进行内容推荐相当流行，被大多数媒体平台使用。有了 TensorFlow.js，内容推荐就可以在客户端处理了！

## TensorFlow.js 入门

TensorFlow.js 的完整教程超出了本文的范围，但是这里有一些非常有用的资源:

### 教程

*   TensorFlow.js 简介:帮助您了解 TensorFlow.js 基础知识的文章，并提供了大量示例。
*   [tensor flow . js 官方教程](https://www.tensorflow.org/js/tutorials):tensor flow . js 主页上的这些官方教程包括识别手写数字和创建一个根据马力估算汽车 mpg 的模型。

### 资源

*   [ml5.js](https://ml5js.org/) : ml5 是一个高级 js 库，在幕后使用 TensorFlow.js，旨在让机器学习对初学者来说更容易。该网站有教程、课程和例子，包括图像分类、文本生成和绘图生成器。
*   表情符号寻宝游戏(e moji Scavenger Hunt):这是一个互动的、类似 AR 的游戏，用 TensorFlow.js 开发，你可以通过手机的网络摄像头找到表情符号。
*   [井字游戏](https://github.com/nebrelbug/tictactoo):这是我用 TensorFlow.js 用图像分类技术做的一个 AI 井字游戏。
*   tfjs-vis :一个自我描述的“TensorFlow.js 浏览器内可视化工具集”

## TensorFlow.js 的未来

### TensorFlow.js 与渐进式 web 应用程序

随着 pwa 越来越受欢迎，我们可以期待看到越来越多的与 TensorFlow.js 和设备上存储的集成。由于 TensorFlow.js 允许您保存模型，您可以创建一个模型，它可以对每个用户进行自我训练，以提供个性化的体验，甚至可以离线工作。

### TensorFlow.js 开发

随着机器学习的使用不断增加，以及 JavaScript 开发变得越来越受欢迎，TensorFlow.js 似乎在不久的将来只会越来越受欢迎，因此它可能会经常获得新功能和更新。

[据 TensorFlow](https://github.com/tensorflow/tfjs/issues/1497) 报道，WebAssembly 后端也正在开发中，这将进一步提高性能。

## 结论

既然已经了解了一点 TensorFlow.js，那就去用它做点什么吧！过一会儿，你会惊讶于你能创造出什么。如果你被卡住了，看看文档和 GitHub 上的许多例子。

快乐创作！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.