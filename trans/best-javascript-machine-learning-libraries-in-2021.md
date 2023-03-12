# 2021 年最佳 JavaScript 机器学习库

> 原文：<https://blog.logrocket.com/best-javascript-machine-learning-libraries-in-2021/>

JavaScript 不需要特别介绍——它是 web 开发人员中最流行的跨平台语言之一。

虽然有些人认为它只是一种前端开发语言，但 JavaScript 现在已经成为一种通用的编程语言，它的可能性是无穷无尽的。

正在寻找可以在机器学习项目中使用的顶级 JavaScript 库吗？他们来了。

## 突触的

[Synaptic](https://caza.la/synaptic) 是麻省理工学院创建的知名 JavaScript 神经网络库，可以与 Node.js 或浏览器配合使用。该库的一个重要特征是，由于其无架构算法和预制结构，它能够构建和训练任何一阶或二阶神经网络架构。

它还可以将网络作为独立的功能导入或导出到 JSON，以便它们可以与其他网络连接，甚至是 gate 连接。Synaptic 包括一些有趣的内置架构，如 Hopfield 网络、状态机、多层感知器、长短期记忆网络(LSTMs)等。而且，因为 Synaptic 是一个开源库，任何人都可以贡献它或者免费使用它。

Synaptic 还包括一个训练器，它能够通过嵌入式 Reber 语法测试、解决 XOR、完成分心的序列回忆任务和内置训练任务等测试来训练任何特定的神经网络。它还有助于比较不同神经网络架构的性能。

以下示例显示了如何使用突触库创建神经网络:

```
// creating a network
const { Layer, Network } = window.synaptic;

let innerLayer = new Layer(2);
let hiddenLayer = new Layer(3);
let outerLayer = new Layer(1);

innerLayer.project(hiddenLayer);
hiddenLayer.project(outerLayer);

const Network = new Network({
input: innerLayer,
hidden: [hiddenLayer],
output: outerLayer
});

```

现在，您可以对上面创建的神经网络进行测试。

## Brain.js

[Brain.js](https://brain.js.org) 是一个基于 JavaScript 的快速运行库，用于机器学习和神经网络。它可以在浏览器中使用，也可以与 Node.js. With Brain 一起使用。JS，不同类型的网络可用于不同的任务。支持[长短期记忆 NN](https://en.wikipedia.org/wiki/Long_short-term_memory) 、[递归 NN](https://en.wikipedia.org/wiki/Recurrent_neural_network) 、[前馈 NN](https://en.wikipedia.org/wiki/Feedforward_neural_network) 等多种神经网络。

由于使用 GPU 进行计算，Brain.js 是一个快速处理库。即使 GPU 不可用，它也会恢复为纯 JS 并继续处理。Brain.js 提供了多种神经网络实现，并鼓励在服务器端与 Node.js 一起构建训练和运行这些神经网络。

这个库的另一个好处是，您不必非常熟悉神经网络就可以使用它。为了将您的网站与这些网络模型集成，您只需将它们实现为一个函数或使用 JSON 格式。

Brain.js 可用于使用高级语言快速创建简单的神经网络。它允许你用几行代码和一个好的数据集来构建一些真正有趣的功能。再者，脑子。JS 提供了在客户端 javascript 上运行的能力。

下面的例子是关于如何使用 [MNIST](https://en.wikipedia.org/wiki/MNIST_database) 数据集创建一个神经网络，该数据集有大约 50，000 个手写数字样本。

```
// creating a network
const context = require('brain');

const getData = function(content) {
  const rows = content.toString().split('\n');

  const values = [];
    for (covaluesnst i = 0; i < rows.length; i++) {
      const inputVal = rows[i].split(',').map(Number);

      const outputVal = Array.apply(null, Array(10)).map(Number.prototype.valueOf, 0);
      outputVal[inputVal.shift()] = 1;

      values.push({
            input: inputVal,
            output: outputVal
        });
    }
    return values;
};

```

您可以处理由上面的示例创建的神经网络的测试过程。

## TensorFlow.js

[TensorFlow.js](https://www.tensorflow.org/js) 是 Google Brain gathering 搭建的开源 JavaScript 库。它通过其完整而灵活的各种工具推动硬件加速。由于它的深度学习层和全面的线性代数，这个库已经成为所有基于机器学习的 JavaScript 项目的面包和黄油。

TensorFlow.js 允许用户在浏览器的帮助下训练神经网络，或者在推理模式下执行预训练的模型，同时将机器学习构建模块带入网络。您可以运行当前可用的默认 TensorFlow 模型，或者甚至将它们转换成一些 python 模型作为附加。

此外，TensorFlow.js 使得使用 Javascript 的低级线性代数从头构建模型变得非常容易。

TensorFlow.js 还包括一些预先存在的机器学习模型。它们可以用来重新训练你自己的数据。它还提供了在任何地方部署机器学习模型的能力，包括设备，而不管您使用的是什么语言、内部部署、浏览器还是云。

你可以考虑在下一个基于机器学习的 JavaScript 项目中使用 TensorFlow.js。它提供了更好的计算图形可视化，同时也提供了一些好处，如频繁的新发布、快速更新和无缝性能。

此外，TensorFlow.js 是高度并行的，旨在与 ASIC、GPU 等众多后端软件结合使用。

而且 TensorFlow.js 是 [TensorFlow](https://www.tensorflow.org) 的一个版本，包括了很多其他的颠覆版本比如面向全体验的 TensorFlow Extended、面向移动设备的 TensorFlow Lite、面向 Rust 绑定的 TensorFlow Rust 等等。

以下代码描述了如何使用 TensorFlow.js 创建一个简单的神经网络来执行干扰。这个模型需要一个输入值和一个输出值来处理一个神经网络。

这里，我们通过调用`tf.sequential`方法尝试了一个新的模型实例。由此，我们可以得到一个新的序列模型。顺序模型可以被称为一种模型，其中一层的输出被用作另一层的输入，即模型的拓扑是层的原始“堆栈”——没有任何分支或跳跃。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然后，可以通过调用`model.add`方法添加第一层，这将创建一个密集层。在下面的示例中，我们向神经网络添加了一个具有一个输入和一个输出的密集层:

```
// Defining a  machine learning sequential model
const modelObj = tf.sequential();

// Add a single layer
modelObj.add(tf.layers.dense({units: 1, inputShape: [1]}));

```

之后，我们可以包括模型的损失和优化器函数:

```
// Specify loss and optimizer for model
modelObj.compile({loss: 'meanSquaredError', optimizer: 'sgd'});

```

这是通过将配置对象传递给模型实例的编译方法来实现的。配置对象包含如下两个属性:`loss`和`optimizer`

## 头脑

用 JavaScript 编写的 [Mind](https://github.com/stevenmiller888/mind) 是一个绝对灵活的库，用于神经网络和处理浏览器和 Node.js 以做出更好的预测。Mind 的一个关键特性是，它使用矩阵实现来处理训练数据，同时允许开发人员自定义网络拓扑。

开始使用这个库非常方便，因为它可以快速插入，并且比其他库更容易下载和上传插件。易于配置预先训练的网络也是另一个优点。

看看下面这个关于如何在神经网络中使用 Mind 的例子。

```
let category = [
  'Action',
  'Adventure',
  'Animation',
  'Comedy',
];

let input = [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ];

movie.category.forEach(function(category) {
  let index = category.indexOf(category);
  if (index > -1) input[index] = 1;
});

//  train the network using the ratings provided by a user using Mind()
let mind = Mind()
  .learn([
    { input: input, output: [ ratings / 5 ] }
  ]);

```

## ConvNetJS

[ConvNetJS](https://cs.stanford.edu/people/karpathy/convnetjs/) 是一个 JavaScript 库，专门用于训练深度学习模型和与神经网络合作。这个库最重要的特点就是完全依赖于浏览器，所以根本不需要任何其他特殊的软件比如 GPU，编译器。ConvNetJS 也支持 Node.js。

ConvNetJS 由具有完全连接的层和非线性的普通神经网络模块组成。这个库拥有使用简单的 JavaScript 公式化和求解神经网络的能力，同时为一些常见的网络模块提供支持。

它还提供了用于指定神经网络、分类和后悔问题的功能、用于图像处理的卷积网络、基于深度 Q 学习的实验性强化学习模块以及仍处于实验水平的补充学习模块。

请参考下面的简单代码示例，了解如何在神经网络中实现 ConvNet.js:

```
// create a network
const layer_defs = [];

const network = new convnetjs.Net();
net.makeLayers(layer_defs);

```

ConvNetJS 在有经验的开发人员中很受欢迎，是一个不断更新的库。另一个很棒的特性是它在 Node.js 下的可用性。

## ML5.js

[ML5.js](https://ml5js.org/) 是一个完全打包的、全面的开源库，用于使用 Node.js 和浏览器进行机器学习。在 Node.js 中使用它时，您可以添加自己的依赖项。

它基于 TensorFlow 构建，没有任何外部依赖。与 Tensorflow 类似，这个库除了为机器学习算法管理内存之外，还可以处理由 GPU 加速的数学运算。

ML5.js 可以在浏览器中轻松访问许多预先训练好的机器学习算法，以便用于各种目的，例如检测人体语言和音高，定制图像，生成文本，用英语查找语言关系，创作音乐曲目等。

这个库被授权提供对机器学习的深入理解，以及各种复杂性，如伦理计算和数据收集，使其适合，甚至适合初学者。

ML5.js 为无人监督和有人监督的问题以及关键任务和简单模型提供了实用程序。此外，它是面向 Typescript 和 JavaScript 开发人员的一体化通用 JavaScript 机器学习库，包括数学和统计、回归算法、人工神经网络、非监督和监督学习、特征提取、线性模型、打包、集成、分解、聚类等支持库。

不仅如此，ML5.js 还允许随机数生成、排序、数组和哈希表上的位操作——它甚至为用户提供了优化、数组操作和线性代数的例程。这个库的另一个优点是它支持交叉验证。

```
// Initialize an Image Classifier method
let classifier;

// A variable to hold an image 
let image;

function preload() {
  classifier = ml5.imageClassifier('MobileNet');
  image = loadImage('images/car.png');
}

```

## 神经外科

[Neuro.js](https://neuro.js.org) 是一个 JavaScript 框架，用于开发和训练强化学习模型和深度学习模型，这些模型被广泛用于制作人工智能技术和聊天机器人的助手。

许多开发人员使用这个库来开发、实践和训练深度学习和机器学习模型，然后将它们部署在 web 浏览器或 Node.js 及其 js 脚本上。

这个库的一些主要优点是，它有助于进行实时分类，为学习提供在线支持，并在创建 ML 项目时支持多标签表单的分类。看看下面使用 Neuro.js 库构建的颜色分类代码示例。

Neuro.js 库的性能驱动和简单特性使机器学习对任何使用它的人来说都是可访问和实用的:

```
//Using an array of input and output pairs:
const neuroModel = require("neuro.js");

const colorsClassifier = new neuroModel.classifiers.NeuralNetwork();

colorsClassifier.trainBatch([
  { input: { red: 0.03, green: 0.7, black: 0.5 }, output: 0 }, // = black
  { input: { red: 0.16, green: 0.09, black: 0.2 }, output: 1 }, // = white
  { input: { redr: 0.5, green: 0.5, black: 1.0 }, output: 1 } // = white
]);

console.log(colorsClassifier.classify({ red: 1, green: 0.4, b: 0 })); 
// 0.99 = nearly white

```

## Keras.Js

[Keras.js](https://transcranial.github.io/keras-js) 可以认为是仅次于 TensorFlow.js 的深度学习应用第二广泛的 js 框架，它在与神经网络库合作的开发者中非常受欢迎。由于 Keras 后端使用了几个框架，所以可以在 CNTK、TensorFlow 和其他框架中训练模型。

使用 Keras 构建的机器学习模型可以在浏览器中运行。尽管模型也可以在 Node.js 中运行，但是只有 CPU 模式可以用于此。不会有 GPU 加速。

许多领先的公司，如网飞和优步，都在研究神经网络的 Keras 模型，以增强用户体验。一些科学组织如 [NASA](https://www.nasa.gov) ， [CERN](https://home.cern) 等。，将这项技术用于他们与人工智能相关的项目。Keras 被认为是人工智能库的 JS 替代方案，它允许您在项目中执行不同的模型，并利用 WebGL 3D 设计的 API 提供的 GPU 支持。

## 结论

在本文中，我们介绍了几个 JavaScript 库，您可以在从事机器学习或数据科学时使用它们。

尽管 JavaScript 与深度学习和机器学习等主题并不十分密切，但预计它将在未来几年成为 ML 开发者中最突出的语言。

上述平台和库的开发将是背后的主要原因。而且，因为 JS 是一种基础编程语言，所以使用算法和提出许多问题的解决方案会非常方便。

最终，使用 JavaScript 进入数据科学领域肯定会成为初学者的一大优势，也是程序员在机器学习方面取得进步的一种令人敬畏的方法。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。