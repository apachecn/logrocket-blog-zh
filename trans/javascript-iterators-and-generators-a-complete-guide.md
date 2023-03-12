# JavaScript 迭代器和生成器:完整指南

> 原文：<https://blog.logrocket.com/javascript-iterators-and-generators-a-complete-guide/>

随着 [ES6](https://262.ecma-international.org/6.0/) 的引入，迭代器和生成器被正式添加到 JavaScript 中。

迭代器允许您迭代遵循规范的任何对象。在第一部分中，我们将看到如何使用迭代器并使任何对象可迭代。

这篇博文的第二部分完全集中在生成器上:它们是什么，如何使用它们，以及在什么情况下它们会有用。

我总是喜欢看事情是如何在幕后工作的:在之前的博客系列中，我解释了 JavaScript 如何在浏览器中工作。作为继续，我想在本文中解释 JavaScript 的迭代器和生成器是如何工作的。

## 什么是迭代器？

在理解生成器之前，我们需要彻底理解 JavaScript 中的迭代器，因为这两个概念是密切相关的。在本节之后，很明显生成器只是一种更安全地编写迭代器的方法。

顾名思义，迭代器允许你在一个对象上*迭代*(数组也是对象)。

很可能，您已经使用了 JavaScript 迭代器。例如，每次你在数组上迭代*T2，你已经使用了迭代器，但是你也可以迭代`Map`对象甚至字符串。*

```
for (let i of 'abc') {
  console.log(i);
}

// Output
// "a"
// "b"
// "c"

```

任何实现了 [iterable 协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)的对象都可以通过使用“for…of”进行迭代。

再深入一点，通过实现返回迭代器对象的`@@iterator`函数，可以使任何对象都是可迭代的。

### 使任何对象都是可迭代的

为了正确理解这一点，最好看一个让常规对象可迭代的例子。

我们从一个包含按城市分组的用户名的对象开始:

```
const userNamesGroupedByLocation = {
  Tokio: [
    'Aiko',
    'Chizu',
    'Fushigi',
  ],
  'Buenos Aires': [
    'Santiago',
    'Valentina',
    'Lola',
  ],
  'Saint Petersburg': [
    'Sonja',
    'Dunja',
    'Iwan',
    'Tanja',
  ],
};

```

我举这个例子是因为如果数据以这种方式构造，就不容易迭代用户；为此，我们需要多个循环来获取所有用户。

如果我们试图迭代这个对象，我们将得到下面的错误消息:

```
▶ Uncaught ReferenceError: iterator is not defined

```

为了使这个对象可迭代，我们首先需要添加`@@iterator`函数。我们可以通过`Symbol.iterator`访问这个符号。

```
userNamesGroupedByLocation[Symbol.iterator] = function() {
  // ...
}

```

正如我之前提到的，迭代器函数返回一个迭代器对象。该对象在`next`下包含一个函数，该函数返回一个具有两个属性的对象:`done`和`value`。

```
userNamesGroupedByLocation[Symbol.iterator] = function() {
  return {
    next: () => {
      return {
        done: true,
        value: 'hi',
      };
    },
  };
}

```

`value`包含迭代的当前值，而`done`是一个布尔值，它告诉我们执行是否已经完成。

当实现这个函数时，我们需要特别小心`done`的值，因为它总是返回`false`将导致无限循环。

上面的代码示例已经代表了 iterable 协议的正确实现。我们可以通过调用迭代器对象的`next`函数来测试。

```
// Calling the iterator function returns the iterator object
const iterator = userNamesGroupedByLocation[Symbol.iterator]();
console.log(iterator.next().value);
// "hi"

```

> 用“for…of”对一个对象进行迭代使用了幕后的`next`函数。

在这种情况下使用“for…of”不会返回任何内容，因为我们立即将`done`设置为`false`。通过这种方式实现它，我们也不会得到任何用户名，这就是为什么我们首先要使这个对象可迭代。

### 实现迭代器函数

首先，我们需要访问代表城市的对象的键。我们可以通过对关键字`this`调用`Object.keys`来得到这个结果，关键字引用了函数的父类，在本例中是`userNamesGroupedByLocation`对象。

如果我们用关键字`function`定义了 iterable 函数，那么我们只能通过`this`访问这些键。如果我们使用一个箭头函数，这将不起作用，因为它们继承了其父类的作用域。

```
const cityKeys = Object.keys(this);

```

我们还需要两个变量来跟踪我们的迭代。

```
let cityIndex = 0;
let userIndex = 0;

```

> 我们在迭代器函数中定义这些变量，但是在`next`函数之外，这允许我们在迭代之间保存数据。

在`next`函数中，我们首先需要获得当前城市和当前用户的用户数组，使用我们之前定义的索引。

我们现在可以使用这些数据来更改返回值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
return {
  next: () => {
    const users = this[cityKeys[cityIndex]];
    const user = users[userIndex];

    return {
      done: false,
      value: user,        
    };
  },
};

```

接下来，我们需要在每次迭代中增加索引。

我们每次都增加用户索引，除非我们到达了给定城市的最后一个用户，在这种情况下，我们将把`userIndex`设置为`0`，并增加城市索引。

```
return {
  next: () => {
    const users = this[cityKeys[cityIndex]];
    const user = users[userIndex];

    const isLastUser = userIndex >= users.length - 1;
    if (isLastUser) {
      // Reset user index
      userIndex = 0;
      // Jump to next city
      cityIndex++
    } else {
      userIndex++;
    }

    return {
      done: false,
      value: user,        
    };
  },
};

```

注意不要用“for…of”迭代这个对象。鉴于`done`始终等于`false`，这将导致无限循环。

我们需要添加的最后一件事是将`done`设置为`true`的退出条件。在遍历完所有城市后，我们退出循环。

```
if (cityIndex > cityKeys.length - 1) {
  return {
    value: undefined,
    done: true,
  };
}

```

将所有内容放在一起后，我们的函数如下所示:

```
userNamesGroupedByLocation[Symbol.iterator] = function() {
  const cityKeys = Object.keys(this);
  let cityIndex = 0;
  let userIndex = 0;

  return {
    next: () => {
      // We already iterated over all cities
      if (cityIndex > cityKeys.length - 1) {
        return {
          value: undefined,
          done: true,
        };
      }

      const users = this[cityKeys[cityIndex]];
      const user = users[userIndex];

      const isLastUser = userIndex >= users.length - 1;

      userIndex++;
      if (isLastUser) {
        // Reset user index
        userIndex = 0;
        // Jump to next city
        cityIndex++
      }

      return {
        done: false,
        value: user,        
      };
    },
  };
};

```

这允许我们使用一个“for…of”循环从对象中快速获取所有名称。

```
for (let name of userNamesGroupedByLocation) {
  console.log('name', name);
}

// Output:
// name Aiko
// name Chizu
// name Fushigi
// name Santiago
// name Valentina
// name Lola
// name Sonja
// name Dunja
// name Iwan
// name Tanja

```

如您所见，使一个对象可迭代并不是魔术。然而，这需要非常小心，因为`next`函数中的错误很容易导致无限循环。

如果你想学习更多关于行为的知识，我鼓励你试着让你选择的对象也是可迭代的。你可以在这个 [codepen](https://codepen.io/fgerschau/pen/XWNoxQr) 上找到本教程中代码的可执行版本。

为了总结我们为创建 iterable 所做的工作，下面是我们遵循的步骤:

*   用`@@iterator`键给对象添加一个迭代器函数(可通过`Symbol.iterator`访问)
*   该函数返回一个包含`next`函数的对象
*   `next`函数返回一个具有属性`done`和`value`的对象

## 什么是发电机？

我们已经学习了如何使任何对象可迭代，但是这和生成器有什么关系呢？

虽然迭代器是一个强大的工具，但像我们在上面的例子中那样创建它们并不常见。在编写迭代器时，我们需要非常小心，因为错误可能会产生严重的后果，并且管理内部逻辑可能具有挑战性。

生成器是一个有用的工具，它允许我们通过定义函数来创建迭代器。

这种方法不容易出错，并且允许我们更有效地创建迭代器。

生成器和迭代器的一个本质特征是它们允许你根据需要停止和继续执行。我们将在本节中看到几个利用这一特性的例子。

### 声明生成器函数

创建生成器函数与常规函数非常相似。我们需要做的就是在名字前面加一个星号(`*`)。

```
function *generator() {
  // ...
}

```

如果我们想创建一个匿名的生成器函数，这个星号会移动到关键字`function`的末尾。

```
function* () {
  // ...
}

```

### 使用`yield`关键字

声明一个生成器函数只是工作的一半，它本身并不是很有用。

如前所述，生成器是创建可迭代的更简单的方法。但是迭代器如何知道它应该迭代函数的哪一部分呢？它应该迭代每一行吗？

这就是`yield`关键字发挥作用的地方。你可以把它想象成你可能从 JavaScript Promises 中知道的`await`关键字，但是是针对生成器的。

我们可以将这个关键字添加到我们希望迭代停止的每一行。然后，`next`函数将返回该行语句的结果，作为迭代器对象的一部分(`{ done: false, value: 'something' }`)。

```
function* stringGenerator() {
  yield 'hi';
  yield 'hi';
  yield 'hi';
}

const strings = stringGenerator();

console.log(strings.next());
console.log(strings.next());
console.log(strings.next());
console.log(strings.next());

```

这段代码的输出如下:

```
{value: "hi", done: false}
{value: "hi", done: false}
{value: "hi", done: false}
{value: undefined, done: true}

```

调用`stringGenerator`本身不会做任何事情，因为它会在第一个`yield`语句处自动停止执行。

一旦功能结束，`value`等于`undefined`，`done`自动设置为`true`。

### 使用产量*

如果我们给 yield 关键字添加一个星号，我们就把执行委托给另一个迭代器对象。

例如，我们可以用它来委托给另一个函数或数组:

```
function* nameGenerator() {
  yield 'Iwan';
  yield 'Aiko';
}

function* stringGenerator() {
  yield* nameGenerator();
  yield* ['one', 'two'];
  yield 'hi';
  yield 'hi';
  yield 'hi';
}

const strings = stringGenerator();

for (let value of strings) {
  console.log(value);
}

```

该代码产生以下输出:

```
Iwan
Aiko
one
two
hi
hi
hi

```

### 将值传递给生成器

迭代器为生成器返回的`next`函数有一个额外的特性:它允许您覆盖返回值。

以之前的例子为例，我们可以覆盖`yield`本来会返回的值。

```
function* overrideValue() {
  const result = yield 'hi';
  console.log(result);
}

const overrideIterator = overrideValue();
overrideIterator.next();
overrideIterator.next('bye');

```

> 在传递一个值来启动生成器之前，我们需要调用一次`next`。

### 生成器方法

除了任何迭代器都需要的“next”方法，生成器还提供了一个`[return](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/return)`和`[throw](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/throw)`函数。

#### 返回函数

在迭代器上调用`return`而不是`next`将导致循环在下一次迭代时退出。

调用`return`之后的每次迭代都会将`done`设置为`true`，将`value`设置为`undefined`。

如果我们向这个函数传递一个值，它将替换迭代器对象上的`value`属性。

这个来自 [Web MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/return)的例子很好地说明了这一点:

```
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const g = gen();

g.next(); // { value: 1, done: false }
g.return('foo'); // { value: "foo", done: true }
g.next(); // { value: undefined, done: true }

```

#### 投掷功能

生成器还实现了一个`throw`函数，它不是继续循环，而是抛出一个错误并终止执行:

```
function* errorGenerator() {
  try {
    yield 'one';
    yield 'two';
  } catch(e) {
    console.error(e);
  }
}

const errorIterator = errorGenerator();

console.log(errorIterator.next()); 
console.log(errorIterator.throw('Bam!'));

```

上面代码的输出如下:

```
{value: 'one', done: false}
Bam!
{value: undefined, done: true}

```

如果我们在抛出错误后试图进一步迭代，返回值将是未定义的，`done`将被设置为`true`。

### 为什么要用发电机？

正如我们在本文中看到的，我们可以使用生成器来创建可迭代对象。题目可能听起来很抽象，我不得不承认我自己很少需要使用生成器。

然而，一些用例极大地受益于这个特性。这些情况通常利用了您可以暂停和恢复生成器的执行这一事实。

#### 唯一 ID 生成器

这是我最喜欢的用例，因为它非常适合发电机。

生成唯一的增量 id 要求您跟踪已经生成的 id。

使用生成器，您可以创建一个无限循环，每次迭代都会创建一个新的 ID。

每当您需要一个新 ID 时，您可以调用`next`函数，生成器会处理剩下的事情:

```
function* idGenerator() {
  let i = 0;
  while (true) {
    yield i++;
  }
}

const ids = idGenerator();

console.log(ids.next().value); // 0
console.log(ids.next().value); // 1
console.log(ids.next().value); // 2
console.log(ids.next().value); // 3
console.log(ids.next().value); // 4

```

> 尼克，谢谢你提出这个想法。

#### 发电机的其他用例

还有许多其他的用例。正如我在本文的[中所发现的，有限状态机也可以使用生成器。](https://evan-brass.medium.com/finite-state-machines-and-js-c9b55fc3f8f9)

相当多的库也使用生成器，例如 [Mobx-State-Tree](https://mobx-state-tree.js.org/intro/welcome) 或 [Redux-Saga](https://redux-saga.js.org/) 。

> 你遇到过其他有趣的用例吗？请在下面的评论区告诉我。

## 结论

生成器和迭代器可能不是我们每天都需要使用的东西，但当我们遇到需要它们独特功能的情况时，知道如何使用它们会有很大的好处。

在本文中，我们学习了迭代器以及如何使任何对象可迭代。在第二部分，我们学习了什么是发电机，如何使用发电机，以及在什么情况下可以使用发电机。

如果你想了解更多关于 JavaScript 如何工作的知识，你可以看看我的博客系列[JavaScript 如何在浏览器](https://felixgerschau.com/javascript-event-loop-call-stack/)中工作，解释事件循环和 JavaScript 的内存管理。

### 延伸阅读:

通过理解上下文，更容易地调试 JavaScript 错误

## 调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.