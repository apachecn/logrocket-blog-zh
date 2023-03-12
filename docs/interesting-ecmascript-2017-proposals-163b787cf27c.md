# 有趣的 ECMAScript 2017 提案未被采纳

> 原文：<https://blog.logrocket.com/interesting-ecmascript-2017-proposals-163b787cf27c/>

![](img/1a63410fb28d3b5949f46b6a4e039501.png)

很难跟上所有新功能提案的进度。每年都有几十个提案提交给控制 JavaScript 发展的委员会。因为他们中的许多人永远不会达到第二阶段，所以很难知道哪些是要跟踪的，哪些只是新奇的东西(或者只是疯狂的想法)。

保持在特性提案的顶端是困难的一个原因是现在有更多的特性提案。JavaScript 的开发速度过去要保守得多——从 ES5 到 ES6 用了六年时间。自 ECMAScript 2016 (ES7)以来，该流程要求每年发布一次，并且[更加标准化](https://tc39.github.io/process-document/)。

由于 polyfills 和 transpilers 近年来变得流行，一些早期的提议甚至在最终确定之前就已经获得了大量的采用。而且，因为提议在被接受之前可能会有很大的变化，一些人可能会发现他们使用了一个永远不会成为语言一部分的特性。

在讨论我认为最有趣的提议之前，让我们先花点时间熟悉一下目前的流程。

### ECMAScript 提案流程的五个阶段

阶段 0“稻草人”——所有提案的起点。在进入下一阶段之前，这些可能会发生显著变化。没有验收标准，任何人都可以在这个阶段提出新的建议。不需要任何实现，规范也没有任何标准。此阶段旨在开始关于该功能的讨论。目前有超过二十个第 0 阶段提案。

阶段 1“提案”——实际的正式提案。这些都需要一个[【冠军】](https://github.com/tc39/ecma262/blob/master/FAQ.md#what-is-the-process-for-proposing-a-new-feature)(即 TC39 委员会成员)。在这个阶段，API 应该是经过深思熟虑的，并且应该概述任何潜在的实现挑战。在这一阶段，开发了 polyfill 并制作了演示。这个阶段之后可能会发生重大变化，所以要小心使用。现阶段的提案包括期待已久的 [Observables type](https://github.com/tc39/proposal-observable) 和 [Promise.try](https://github.com/tc39/proposal-promise-try) 功能。

阶段 2“草案”——在这个阶段，使用正式的 TC39 规范语言精确地描述语法。在这个阶段之后，可能还会发生一些小的编辑上的变化，但是规范应该足够完整，不需要大的修改。如果一个提案能走到这一步，很有可能委员会希望这个特性最终会被包含进来。

第 3 阶段“候选”—提案已获批准，进一步的更改将仅在实现作者的请求下发生。这是 JavaScript 引擎开始实现的地方。在此阶段，Polyfills for proposals 可以放心安全地使用。

阶段 4“完成”——表示该提议已经被接受，并且规范已经与主 JavaScript 规范合并。预计不会有进一步的变化。JavaScript 引擎预计会发布它们的实现。截至 2017 年 10 月，共有[九个提案完成](https://github.com/tc39/proposals/blob/master/finished-proposals.md)，其中最引人注目的是[异步功能](https://github.com/tc39/ecmascript-asyncawait)。

既然有这么多提议，这里有几个目前正在考虑中的更有趣的提议。

[![](img/b982c6bf31870246aa57a18c095b8f16.png)](https://xkcd.com/927/)

[https://xkcd.com/927/](https://xkcd.com/927/)

### 异步迭代

ECMAScript 2015 增加了迭代器，包括 *for-of* 循环语法。这使得遍历可迭代对象变得相当容易，并使实现您自己的可迭代数据结构成为可能。

不幸的是，迭代器不能像访问文件系统一样用来表示异步数据结构。虽然您总是可以运行 Promise.all 并对一组承诺进行循环，但这需要同步确定“完成”状态。

例如，使用异步迭代器，您可以简单地在异步迭代器上循环，在需要时读取行，而不是在处理它们之前读取文件中的所有行。

您可以通过同时使用生成器语法和 async-await 语法来定义异步生成器函数:

```
async function* readLines(path) {
  let file = await fileOpen(path);

  try {
    while (!file.EOF) {
      yield await file.readLine();
    }
  } finally {
    await file.close();
  }
}
```

Async generator function example

#### 例子

然后，您可以在 for-await-of 循环中使用这个异步生成器:

```
for await (const line of readLines(filePath)) {
  console.log(line);
}
```

Using for-await-of

任何具有 Symbol.asyncIterator 属性的对象都被定义为*异步可迭代*，并且可以与新的 for-await-of 语法一起使用。下面是一个实际例子:

```
class LineReader() {
 constructor(filepath) {
   this.filepath = filepath;
   this.file = fileOpen(filepath);
 }
 [Symbol.asyncIterator]: {
   next() {
     return new Promise((resolve, reject) => {
       if (this.file.EOF) {
         resolve({ value: null, done: true });
       } else {
         this.file.readLine()
           .then(value => resolve({ value, done: false }))
           .catch(error => reject(error));
       }
     });
   }
 }
} 
```

Using Symbol.asyncIterator

该提案目前处于第三阶段，浏览器已经开始实施。在这个阶段，它可能会被包含在标准中，并最终被主流浏览器实现。然而，在此之前可能会对规范进行一些小的修改，所以现在使用异步迭代器存在一定程度的风险。

[再生器](https://github.com/facebook/regenerator)项目目前基本支持这种异步迭代器提议。然而，再生器本身并不支持 for-await-of 循环语法。Babel 编译器有[transform-async-generator-functions](https://www.npmjs.com/package/babel-plugin-transform-async-generator-functions)插件，支持异步生成器函数和 for-await-of 循环语法。

### 班级改进

有一个[提议](https://github.com/tc39/proposal-private-methods)将公共和私有字段以及私有方法添加到 ECMAScript 2015 中[引入的类语法中。这一提案是各种竞争性提案之间长期讨论和竞争的结果。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)

私有字段和方法的使用类似于它们的公共对应物，但是它们的名称前面有一个散列符号。任何标记为 private 的方法或字段在类外部都是不可见的，这确保了内部类成员的强大封装。

下面是一个类似 React 的组件的假设示例，它通过私有方法使用公共和私有字段:

```
class Counter {
  // public field
  text = ‘Counter’;

  // private field
  #state = {
    count: 0,
  };

  // private method
  #handleClick() {
    this.#state.count++;
  }

  // public method
  render() {
    return (
      <button onClick={this.handleClick.bind(this)}>
        {this.text}: {this.#state.count.toString()}
      </button>
    );
  }
} 
```

React-like component with private fields and methods

私有类的字段和方法目前还没有被 Babel 填充，尽管它很快就会被填充。Babel 的[transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/)插件支持公共字段，然而这是基于一个合并到这个统一的公共/私有类字段提案中的旧提案。该提案已于 2017 年 9 月 1 日进入第三阶段，因此当 polyfill 可用时，可以安全使用。

### 班级装饰者

装饰者是一个很好的例子，一个提议在被介绍后完全改变了。Babel v5 实现了最初的 stage 2 decorators 规范,它将 decorator 定义为接受目标、名称和属性描述符的函数。今天最流行的转换装饰者的方法是 Babel 的[转换-遗留-装饰者](https://github.com/loganfsmyth/babel-plugin-transform-decorators-legacy)插件，它实现了这个旧规范。

新规格完全不同。我们现在有了一个类成员的形式化描述，而不是一个具有三个属性的函数——decorator 是改变描述符的函数。这个新的“成员描述符”非常类似于 ES5 中引入的属性描述符接口。

现在有两种不同 API 的装饰器:成员装饰器和类装饰器。

*   成员装饰器接受一个成员描述符并返回一个成员描述符。
*   Class decorators 为每个类成员(即每个属性或方法)获取一个构造函数、父类和成员描述符数组。

在规范中，“extras”指的是装饰者可以添加的可选成员描述符数组。这将允许装饰者动态地创建新的属性和方法。例如，您可能让装饰者为一个属性创建 getter 和 setter 函数。

与旧规范一样，新规范允许您改变类成员的属性描述符。此外，在对象文字的属性上仍然允许 decorators。

在最终确定之前，规范可能会有很大的变化。语法上有歧义，旧规范的许多痛点没有得到解决。Decorators 是该语言的一个巨大的语法扩展，所以这种延迟是意料之中的。不幸的是，如果新的提议被接受，您将不得不显著地重构您的装饰器函数来使用新的接口。

许多库作者选择继续支持旧的提议和遗留装饰者巴别塔变换——即使新的提议处于阶段 2，而旧的提议仍处于阶段 0。最流行的使用 decorator 的开源 JavaScript 库，[core-decorator](https://github.com/jayphelps/core-decorators)，已经采用了这种方法。装饰库作者很可能在未来几年继续支持旧规范。

这个新提议也有可能被撤回，以支持另一个提议，装饰者可能不会在 2018 年将它变成 Javascript。在 Babel 完成新的转换插件的工作后，你将可以使用新的 decorators 提议。

### 导入功能

ECMAScript 第 6 版添加了 [import 语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)，并最终确定了新模块系统的语义。主要的浏览器[最近刚刚发布了支持，尽管他们在实现多少规范上有细微的差别。NodeJS 已经在 8.5.0 版本中发布了对 ECMAScript 模块的初步支持，位于-experimental-modules 标志之后。](https://jakearchibald.com/2017/es-modules-in-browsers/)

然而，该提案缺少一种异步导入模块的方式，使得在运行时动态导入模块代码变得困难。目前，在浏览器中动态加载模块的唯一方法是动态插入一个“module”类型的脚本标记，将导入声明作为其 textContent。

这样做的一种内置方式是提议的[动态导入](https://github.com/tc39/proposal-dynamic-import)语法，它要求一种“类似函数”的导入模块加载形式。这种新的动态导入语法在模块代码和普通脚本代码中都是允许的，为模块代码提供了一个方便的入口点。

去年，有人提议通过 System.import()函数来解决这个问题，但这个想法最终被排除在最终规范之外。这项新提案目前处于第 3 阶段，可能会在年底前纳入。

### 看得见的

[提出的可观察类型](https://github.com/tc39/proposal-observable)提供了一种处理异步数据流的标准化方法。它们已经在许多流行的 JavaScript 框架中以某种形式实现了，比如 RxJS。当前的提议大量借鉴了这些库。

可观察对象是通过可观察构造函数创建的，该构造函数采用订阅者函数:

```
function listen(element, eventName) {
 return new Observable(observer => {
   // Create an event handler which sends data to the sink
   let handler = event => observer.next(event);

   // Attach the event handler
   element.addEventListener(eventName, handler, true);

   // Return a  function which will be called to unsubscribe
   return () => {
     // Detach the event handler from the element
     element.removeEventListener(eventName, handler, true);
   };
 });
} 
```

Observable constructor usage

使用订阅功能来订阅可观察的:

```
const subscription = listen(inputElement, “keydown”).subscribe({
  next(val) { console.log("Got key: " + val) },
  error(err) { console.log("Got an error: " + err) },
  complete() { console.log("Stream complete!") },
}); 
```

Using an observable

函数的作用是:返回一个订阅对象。该对象有一个取消订阅的方法，可用于取消订阅。

Observables 不应与[弃用的对象混淆，observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) 函数是一种观察对象变化的方法。在 ECMAScript 2015 中，该方法被更通用的[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe)实现所取代。

Observables 目前是第一阶段，但它很可能很快就会进入下一阶段，因为它已经被 TC39 委员会标记为“准备推进”，并且得到了浏览器供应商的大力支持。已经有[三个 polyfill 实现](https://github.com/tc39/proposal-observable#implementations)可供选择，所以你今天就可以开始使用了。

### 做表情

CoffeeScript 因[让一切都成为表达式](http://coffeescript.org/#expressions)而闻名，尽管 coffeescript 的受欢迎程度有所下降，但它对 JavaScript 最近的发展产生了影响。

Do 表达式是一种提议的新语法，用于在单个表达式中包装多个语句。这将允许您执行以下操作:

```
let activeToDos = do {
  let result;
  try {
    result = fetch('/todos');
  } catch (error) {
    result = []
  }
  result.filter(item => item.active);
}
```

Do expression example

do 表达式中的最后一条语句作为完成值隐式返回。

Do 表达式在 [JSX](https://reactjs.org/docs/jsx-in-depth.html) 里面会很有用。do 表达式不使用复杂的三元运算符，而是使 JSX 内部的控制流更具可读性。

```
const FooComponent = ({ kind }) => (

   {do {
     if (kind === 'bar') {  }
     else if (kind === 'baz') {  }
     else {  }
   }}

) 
```

The future of JSX?

Babel 有一个[插件](https://babeljs.io/docs/plugins/transform-do-expressions/)来转换 do 表达式。该提案目前处于第一阶段，围绕 do 表达式如何与生成器和异步函数一起工作还存在许多未解决的问题，因此该规范可能会发生重大变化。

### 可选链接

另一个受 CoffeeScript 启发的提议是[可选链接提议](https://github.com/tc39/proposal-optional-chaining)，它增加了一种简单的方法来访问对象属性，这些属性可能是未定义的或空的，而不需要冗长的三元运算符。这类似于 CoffeeScript 的[存在运算符](http://coffeescript.org/#existential-operator)，但是缺少一些显著的特性，比如范围检查和可选赋值。

示例:

```
a?.b // undefined if `a` is null/undefined, `a.b` otherwise.
a == null ? undefined : a.b // using ternary operators
```

这个提议处于第一阶段，有一个叫做[Babel-plugin-transform-optional-chaining](https://www.npmjs.com/package/babel-plugin-transform-optional-chaining)的 Babel 插件实现了这个提议。TC39 委员会在 2017 年 10 月的[最后一次会议](https://github.com/tc39/tc39-notes/blob/master/es8/2017-09/sep-28.md#13iii-nullary-coalescing-operator)中对语法有所担忧，但看起来最终可能会采用可选链接提案。

### 标准化全局对象

很难编写可以在每种 JavaScript 环境下运行的代码。在浏览器中，全局对象是窗口——除非你在一个 [web worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 中，那么它就是自己。在 NodeJS 中，它是全局的，但那是添加在 V8 引擎之上的东西，不是规范的一部分，所以当直接在 V8 引擎上运行代码时，它是不可用的。

有一个[提议将](https://github.com/tc39/proposal-global)标准化，这是一个可以在所有引擎和运行时环境中使用的全局对象。这是目前的第三阶段提案，因此将很快被接受。

### 还有更多

TC39 委员会目前正在考虑 50 多项有效提案，还不包括 20 多项尚未推进的 0 阶段提案。

你可以在 TC39 委员会的 [GitHub 页面](https://github.com/tc39/proposals)上看到所有活动提案的列表。你可以在[阶段 0 提案](https://github.com/tc39/proposals/blob/master/stage-0-proposals.md)部分找到一些更粗略的想法，其中包括像[方法参数修饰器](https://docs.google.com/document/d/1Qpkqf_8NzAwfD8LdnqPjXAQ2wwh8BBUGynhn-ZlCWT0/edit)和新的[模式匹配语法](https://github.com/tc39/proposal-pattern-matching)这样的想法。

还有以前 TC39 会议的[会议记录](https://github.com/tc39/tc39-notes)和[议程](https://github.com/tc39/agendas)的存储库，在这里您可以有趣地看到委员会的优先事项以及当前正在解决的问题。如果您对演示感兴趣，它们会随会议节点一起存档。

在 ECMAScript 的最近版本中有几个主要的语法改变提议，这种趋势似乎还在继续。变化的步伐正在加快——每年都有更多的提议，2018 年版看起来会比 2017 年版有更多被接受的提议。

今年有一些更小的提议，为语言添加“生活质量”的增强，比如添加更好的内置对象类型检查的提议，或者在数学模块中添加 T2 度和弧度助手的提议。像这样的建议增加了标准库，而不是改变语法。它们更易于聚合填充，有助于减少安装第三方库的需求。因为它们不改变语法，所以很快被采用，并且在提议过程中花费的时间也较少。

新的语法很好，但是我希望我们在将来能看到更多这样的提议。人们经常说 Javascript 缺少一个好的标准库，但是很明显人们正在努力改变这种情况。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)