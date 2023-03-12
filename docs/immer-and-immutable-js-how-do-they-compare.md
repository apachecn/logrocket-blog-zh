# Immer 和 Immutable.js:它们如何比较？- LogRocket 博客

> 原文：<https://blog.logrocket.com/immer-and-immutable-js-how-do-they-compare/>

不变性不是编程中的新概念。它是编程范例的基础，例如纯函数式编程。整个想法是避免在数据创建后对其进行直接更改。

下面是我们将在本文中讨论的内容:

*   JavaScript 中的不变性
*   Immer 和 Immutable.js 库简介
*   Immer 和 Immutable.js 的比较

## JavaScript 中的不变性

众所周知，字符串和数字等 JavaScript 原语是不可变的。这是真的，因为字符串不能被任何方法或操作改变。您只能创建新字符串。

让我们考虑下面的变量:

```
var name = "mark cuban"
name = "John Steve"
```

您可能会认为数据是可变的，因为 name 变量被重新分配给了另一个字符串，但事实并非如此。

重新分配不同于不变性。这是因为即使变量被重新分配，它也没有改变字符串“Eze Sunday”存在的事实。这和 13 加 3 不会改变原来的变量 13，或者你到 18 岁也不会改变你之前 17 岁的事实是一个道理。

即使变量可能被重新分配，不可变的数据仍然保持不变。

我们已经从上面的例子中确定了原语是不可变的，但这并不是故事的结尾。JavaScript 中的数据结构是可变的。其中之一是数组。

为了演示这一点，让我们声明一个变量并将其值设置为一个空数组，如下所示:

```
let arrOne = []
```

我们可以通过使用`.push()`函数轻松地更新上述数组的内容:

```
arrOne.push(2)
```

这将把数据 2 添加到数组的末尾，改变我们之前拥有的原始数组。

### Immer 和 Immutable.js 库简介

JavaScript 并不是为其数据完全不可变而编写的，但是在某些情况下，您需要一个不可变的数组或映射来轻松跟踪或记录数据集中的变化。

这在 React 框架中很明显，尤其是在处理状态和道具时。这就是不变性库发挥作用的地方。这些库有助于优化我们的应用程序，使跟踪应用程序中的变化变得更加容易。在本文中，我们将关注两个主要的不变性库。即 Immer 和 Immutable.js。

## 总是（德语词）

Immer 是你可以在应用程序中使用的众多不变性库之一。据其官网介绍，Immer 是基于写时复制机制的。整个想法围绕着将更改应用到临时的`draftState`，它充当当前状态的代理。Immer 将让您轻松地与数据进行交互，同时保持不变性带来的所有好处。

### 装置

要在应用程序中立即使用 Immer，请使用以下命令:

```
<script src="https://cdn.jsdelivr.net/npm/immer"></script>

//  It can also be installed in your application using NPM;
npm install immer
Or with yarn;
yarn add immer
```

### 使用

使用 Immer，大多数不变性工作都是在默认函数的帮助下完成的:

```
produce(currentState, producer: (draftState) => void): nextState
```

该函数接收`currentState`和`draftState`并更新`nextState`以反映对`draftState`所做的更改。

**例子**

考虑下面的代码:

```
import produce from "immer"
const baseState = [
    {
        todo: "Learn typescript",
        done: true
    },
    {
        todo: "Try immer",
        done: false
    }
]
```

可以使用 Immer 默认功能将新数据添加到状态，如下所示:

```
const nextState = produce(baseState, draftState => {
    draftState.push({todo: "Tweet about it"})
    draftState[1].done = true
})
```

在这种情况下，`baseState`保持不变，而`nextState`将被更新以反映对`draftState`所做的更改。你可以从 Immer 的官网[这里](https://www.google.com/url?q=https://www.google.com/url?q%3Dhttps://immerjs.github.io/immer/docs/introduction%26amp;sa%3DD%26amp;ust%3D1605999893510000%26amp;usg%3DAOvVaw3jO0Fdf_irSp6rc_1f7128&sa=D&ust=1605999893534000&usg=AOvVaw0DL7xOUQ5IVQAmHXq3m3Ry)了解更多关于 Immer 的信息。

## 不可变的. js

[Immutable.js](https://www.google.com/url?q=https://www.google.com/url?q%3Dhttps://immutable-js.github.io/immutable-js/%26amp;sa%3DD%26amp;ust%3D1605999893510000%26amp;usg%3DAOvVaw1-174UBy7P2rtAcoQCbbNi&sa=D&ust=1605999893534000&usg=AOvVaw1lTqUdf0JhuAzBZX-H_As0) 是寻找不变性库时要考虑的另一个选项。Immutable.js 与 Immer 的目的相同，但它采用了不同的方法。它为像地图和列表这样的数据结构提供了一个 API。

### 装置

可以使用 npm 安装 Immutable.js:

```
npm install immutable
```

**例子**

我们可以用 Immutable.js 执行映射操作，方法是从已安装的包中请求`map`并使用它，就像这样:

```
const { Map } = require('immutable');

const map1 = Map({ a: 1, b: 2, c: 3 });

const map2 = map1.set('b', 50);

map1.get('b') + " vs. " + map2.get('b'); // 2 vs. 50
```

在上面的例子中，我们的对象`{ a: 1, b: 2, c: 3 }`用`Map()`函数包装。我们继续对它执行`get`和`set`操作，同时保持数据不变。

除了对象，我们还可以使用如下所示的`List`函数创建不可变数组:

```
List(['apple','orange','grape'])
```

以上是使用`List`函数在 Immutable.js 中的数组实现。

通过将对象和数组直接转换成不可变的数据，`fromJS function`有助于绕过用`Map({})`和`List([])`函数包装它们的需要。

```
fromJS(['apple','orange','grape'])
```

上面的代码将数组直接转换成不可变的数据。

### Immer v. Immutable.js:你该选哪个？

现在有一个大问题:你应该在这两个库中选择哪一个？首先，让我们分别列出这些库的优点和缺点。我们从 Immer 开始。

## Immer 带来的好处

与使用其他库(如 Immutable.js)相比，使用 Immer 有很多好处。

*   样板文件的减少:使用 Immer，您无需额外的样板文件就可以编写更精确的代码，从而导致代码库的整体减少
*   不变性适用于普通的 JavaScript 数据类型和结构:Immer 允许使用普通的 JavaScript、对象、映射和数组，而不需要学习新的 API
*   Immer 是强类型的，没有基于字符串的路径选择器
*   对补丁的支持

### Immer 作为一个库的缺点

有几件事使使用 Immer 变得困难。首先，您需要一个支持代理对象的环境来使用 Immer。此外，Immer 不支持复杂的对象类型，如类实例。

### 不可变. js 带来的好处

就像 Immer 一样，Immutable.js 也有它的好处。其中一些包括:

*   除了其他常规的 JavaScript 数据结构之外，Immer 还为您提供了非 javaScript 原生的数据结构。其中一些包括订购的地图和记录
*   不可变的，js 让您知道在您的 reducer 中已经被改变的精确数据，使得开发更容易
*   与其他不可变库相比，Immutable.js 以创纪录的速度写入数据

### 不可变. js 的缺点

尽管 Immutable.js 写数据很快，但在执行读操作时要慢得多。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，Immutable.js 迫使您学习新的、有时复杂的语法和 API，只是为了执行基本的操作。例如，向数组中添加额外的数据需要使用`.set()`方法，这对于 JavaScript 来说不是传统的方法。

不可变的. js 还迫使你在应用程序中处处使用唯一的构造类型。这意味着对于您构建的每个不可变集合，您必须利用适当的不可变. js 集合，而不是传统的集合。这可能会造成很大的压力，尤其是在将您的代码迁移到另一个不使用这些集合的代码库时。

另一方面，Immer 提供了大致相同的功能，但灵活性要大得多。这也是很多开发者坚持的原因。它可以让你用你已经习惯的古典物品来创造收藏。

## 结论

如果你需要更快的应用程序编写速度，你可以选择 Immutable.js。另一方面，如果你想在坚持使用传统 JavaScript 数据结构和对象类型的同时编写更少的代码，那么 Immer 就是你的选择。

总的来说，Immer 和 Immutable.js 都是很好的库，您应该尝试在您的应用程序中使用它们。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.