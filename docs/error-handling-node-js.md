# Node.js 中的错误处理

> 原文：<https://blog.logrocket.com/error-handling-node-js/>

错误是开发人员生活的一部分。我们既不能逃跑，也不能躲避他们。在构建生产就绪软件时，我们需要有效地管理错误，以便:

1.  改善最终用户体验；即提供正确的信息，而不是一般的消息“无法完成请求”
2.  开发强大的代码库
3.  通过有效地发现 bug 来减少开发时间
4.  避免突然停止程序

因为你在这里，我假设你很可能是一个有 JavaScript 背景的 web 开发人员。让我们以读取 Node.js 中的文件而不处理错误的典型用例为例:

```
var fs = require('fs')

# read a file
const data = fs.readFileSync('/Users/Kedar/node.txt')

console.log("an important piece of code that should be run at the end")
```

注意，Node.js 应该在文件读取任务之后执行一些关键的代码。当我们运行它时，我们会收到如下所示的输出:

输出:

```
$node main.js
fs.js:641
  return binding.open(pathModule._makeLong(path), stringToFlags(flags), mode);
                 ^

Error: ENOENT: no such file or directory, open '/Users/Kedar/node.txt'
    at Error (native)
    at Object.fs.openSync (fs.js:641:18)
    at Object.fs.readFileSync (fs.js:509:33)
    at Object.<anonymous> (/home/cg/root/7717036/main.js:3:17)
    at Module._compile (module.js:570:32)
    at Object.Module._extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module._load (module.js:438:3)
    at Module.runMain (module.js:604:10)
```

这里，程序突然结束，没有执行必要的代码。我们将在后面的`try...catch`模块部分讨论修改后的错误处理代码。这个例子仅仅展示了在没有错误处理的情况下所面临的许多问题中的一个。让我们来看看我们将涵盖的内容，以便更好地理解我们如何处理错误:

在我们学习错误处理之前，让我们先了解 Node.js 中的一个`Error` 。

## 错误

`Error`是 Javascript 中`[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)`对象的扩展。该错误可以被构造并由[抛出](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/throw)或者传递给某个函数。让我们来看看一些例子:

```
throw new Error('bad request'); // throwing new error
callback_function(new Error('connectivity issue')); // passing error as an argument
```

当创建一个错误时，我们需要传递一个人类可读的字符串作为参数，以了解当我们的程序工作不正常时哪里出错了。换句话说，我们通过将字符串传递给`Error` [构造函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor)来创建一个对象。

您还需要知道 JavaScript 中的错误和异常是不同的，尤其是 Node.js。错误是一个`Error`类的实例，当您抛出一个错误时，它会变成一个异常。

人类不会导致所有的错误。有两种类型的错误，程序员和操作。我们用“错误”这个词来形容这两者，但现实中两者因根源不同而大相径庭。让我们看一看每一个。

### 程序员错误

程序员错误描述了所写程序的问题——bug。换句话说，这些都是程序员在编写程序时的失误造成的错误。我们无法正确处理这些错误，纠正它们的唯一方法是修复代码库。以下是一些常见的程序员错误:

*   数组索引越界—当只有六个元素可用时，试图访问数组的第七个元素
*   语法错误—定义 JavaScript 函数时未能关闭花括号
*   引用错误—访问未定义的函数或变量
*   弃用错误和警告—调用异步函数而不回调
*   类型错误— `x`对象不可迭代
*   未能处理操作错误

### 操作错误

每个程序都面临操作错误(即使程序是正确的)。这些是运行时由于外部因素导致的问题，会中断程序的正常流程。不像程序员的错误，我们可以理解和处理它们。以下是一些例子:

*   无法连接服务器/数据库
*   请求超时
*   用户输入无效
*   套接字挂起
*   500 来自服务器的响应
*   找不到文件

你可能想知道为什么这种隔离是必要的，因为两者导致相同的结果，中断程序？嗯，你可能需要根据错误的类型采取行动。例如，对于文件未找到错误(操作错误)，重新启动应用程序可能不是一个合适的操作，但当您的程序无法捕捉被拒绝的承诺(程序员错误)时，重新启动可能会有所帮助。

现在您已经知道了错误，让我们来处理它们。我们可以通过管理这些错误来避免程序的突然终止，这是生产就绪代码的重要组成部分。

## 错误处理技术

为了有效地处理错误，我们需要理解错误传递技术。

Node.js 中有四种报告错误的基本策略:

1.  `try…catch`街区
2.  复试
3.  承诺
4.  事件发射器

让我们一个一个地了解使用它们。

### `try…catch`街区

在`[try…catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)`方法中，`try`块包围了可能出现错误的代码。换句话说，我们包装我们想要检查错误的代码；`catch`块处理这个块中的异常。

下面是处理错误的`try…catch`代码:

```
var fs = require('fs')

try {
const data = fs.readFileSync('/Users/Kedar/node.txt')
} catch (err) {
  console.log(err)
}

console.log("an important piece of code that should be run at the end")
```

我们收到如下所示的输出:

```
$node main.js
{ Error: ENOENT: no such file or directory, open '/Users/Kedar/node.txt'
    at Error (native)
    at Object.fs.openSync (fs.js:641:18)
    at Object.fs.readFileSync (fs.js:509:33)
    at Object.<anonymous> (/home/cg/root/7717036/main.js:4:17)
    at Module._compile (module.js:570:32)
    at Object.Module._extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module._load (module.js:438:3)
    at Module.runMain (module.js:604:10)
  errno: -2,
  code: 'ENOENT',
  syscall: 'open',
  path: '/Users/Kedar/node.txt' }
an important piece of code that should be run at the end
```

处理并显示错误。最后，剩余的代码按计划执行。

### 回收

一个[回调函数](https://blog.logrocket.com/a-complete-guide-to-the-node-js-event-loop/)(通常用于异步代码)是我们在其中实现错误处理的函数的参数。

回调函数的目的是在使用主函数的结果之前检查错误。回调通常是主函数的最后一个参数，它在操作出现错误或结果时执行。

回调函数的语法如下:

```
function (err, result) {}

```

第一个参数表示错误，第二个参数表示结果。如果出现错误，第一个属性将包含错误，第二个属性将未定义，反之亦然。让我们来看一个例子，在这个例子中，我们试图通过应用这种技术来读取一个文件:

```
const fs = require('fs');

fs.readFile('/home/Kedar/node.txt', (err, result) => {
  if (err) {
    console.error(err);
    return;
  }

  console.log(result);
});
```

结果看起来像这样:

```
$node main.js
{ Error: ENOENT: no such file or directory, open '/home/Kedar/node.txt'
    at Error (native)
  errno: -2,
  code: 'ENOENT',
  syscall: 'open',
  path: '/home/Kedar/node.txt' }
```

我们收到错误，因为文件不可用。我们也可以用用户定义的函数实现回调。下面的示例说明了一个用户定义的函数，该函数使用回调将给定的数字加倍:

```
const udf_double = (num, callback) => {
  if (typeof callback !== 'function') {
    throw new TypeError(`Expected the function. Got: ${typeof callback}`);
  }

  // simulate the async operation
  setTimeout(() => {
    if (typeof num !== 'number') {
      callback(new TypeError(`Expected number, got: ${typeof num}`));
      return;
    }

    const result = num * 2;
    // callback invoked after the operation completes.
    callback(null, result);
  }, 100);
}

// function call
udf_double('2', (err, result) => {
  if (err) {
    console.error(err)
    return
  }
  console.log(result);
});
```

上面的程序会抛出一个错误，因为我们传递的是字符串而不是整数。结果如下:

```
$node main.js
TypeError: Expected number, got: string
    at Timeout.setTimeout (/home/cg/root/7717036/main.js:9:16)
    at ontimeout (timers.js:386:14)
    at tryOnTimeout (timers.js:250:5)
    at Timer.listOnTimeout (timers.js:214:5)
```

### 承诺

Node.js 中的`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`是当代处理错误的方法，通常比回调更受欢迎。由于承诺是回调的替代方案，让我们将上面讨论的例子(`udf_double`)转换为利用承诺:

```
const udf_double = num => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (typeof num !== 'number') {
        reject(new TypeError(`Expected number, got: ${typeof num}`));
      }

      const result = num * 2;
      resolve(result);
    }, 100);
  });
}

```

在函数中，我们将返回一个[承诺](https://blog.logrocket.com/improve-async-programming-with-javascript-promises/)，它是我们主要逻辑的包装器。在定义`Promise`对象时，我们传递两个参数:

1.  `resolve` —用于解决承诺并提供结果
2.  `reject` —用于报告/抛出错误

现在，让我们通过传递输入来执行函数:

```
udf_double('8')
  .then((result) => console.log(result))
  .catch((err) => console.error(err));

```

我们得到一个错误，如下所示:

```
$node main.js
TypeError: Expected number, got: string
    at Timeout.setTimeout (/home/cg/root/7717036/main.js:5:16)
    at ontimeout (timers.js:386:14)
    at tryOnTimeout (timers.js:250:5)
    at Timer.listOnTimeout (timers.js:214:5)
```

这看起来比回调简单多了。我们也可以使用一个实用程序，比如`util.promisify()`，将基于回调的代码转换成`Promise`。让我们将回调部分的`fs.readFile` 示例转换为使用`promisify`:

```
const fs = require('fs');
const util = require('util');

const readFile = util.promisify(fs.readFile);

readFile('/home/Kedar/node.txt')
  .then((result) => console.log(result))
  .catch((err) => console.error(err));
```

这里我们承诺了`readFile`函数。我们得到的结果如下:

```
[Error: ENOENT: no such file or directory, open '/home/Kedar/node.txt'] {
  errno: -2,
  code: 'ENOENT',
  syscall: 'open',
  path: '/home/Kedar/node.txt'
}
```

#### 异步/等待

Async/await 只是一种语法糖，意在增加承诺。它为异步代码提供了同步结构。对于简单的查询，`Promise` s 可以很容易使用。

尽管如此，如果您遇到复杂查询的场景，理解代码*看起来*好像是同步的就更容易了。

注意，`async`函数的返回值是一个`Promise`。`await`等待承诺被解决或拒绝。让我们使用 async/await 实现`readFile`示例:

```
const fs = require('fs');
const util = require('util');

const readFile = util.promisify(fs.readFile);

const read = async () => {
  try {
    const result = await readFile('/home/Kedar/node.txt');
    console.log(result);
  } catch (err) {
    console.error(err);
  }
};

read()
```

我们正在创建`async` read 函数，在这个函数中我们使用`await`读取文件。输出如下所示:

```
[Error: ENOENT: no such file or directory, open '/home/Kedar/node.txt'] {
  errno: -2,
  code: 'ENOENT',
  syscall: 'open',
  path: '/home/Kedar/node.txt'
}
```

### 事件发射器

我们可以使用 events 模块中的 EventEmitter 类来报告复杂场景中的错误——冗长的异步操作会导致大量的失败。我们可以不断地发出错误，并使用发射器来监听它们。

让我们来看看这个例子，我们试图模拟一个接收数据的场景，并检查它是否正确。我们需要检查前六个索引是否是整数，不包括第零个索引。如果六个索引中的任何一个不是整数，我们就会发出一个错误，并根据这个错误做出进一步的决策:

```
const { EventEmitter } = require('events'); //importing module

const getLetter = (index) =>{
    let cypher = "*12345K%^*^&*" //will be a fetch function in a real scenario which will fetch a new cypher every time
    let cipher_split = cipher.split('')
    return cipher_split[index]
}

const emitterFn = () => {
  const emitter = new EventEmitter(); //initializing new emitter
  let counter = 0;
  const interval = setInterval(() => {
    counter++;

    if (counter === 7) {
      clearInterval(interval);
      emitter.emit('end');
    }

    let letter = getLetter(counter)

    if (isNaN(letter)) { //Check if the received value is a number
      (counter<7) && emitter.emit(
        'error',
        new Error(`The index ${counter} needs to be a digit`)
      );
      return;
    }
    (counter<7) && emitter.emit('success', counter);

  }, 1000);

  return emitter;
}

const listner = emitterFn();

listner.on('end', () => {
  console.info('All six indexes have been checked');
});

listner.on('success', (counter) => {
  console.log(`${counter} index is an integer`);
});

listner.on('error', (err) => {
  console.error(err.message);
});

```

首先，我们导入`events`模块来使用`EventEmitter`。然后我们定义`getLetter()`函数来获取新的密码，并在`emitterFn()`请求时发送特定索引的值。`emitterFn()`将初始化`EventEmitter`对象。我们逐个获取所有六个索引的值，如果它不是整数，就发出一个错误。

一个变量存储从`emitterFn()`接收的值，我们使用`listener.on()`来监听它们。检查完所有索引后，程序将结束。输出如下所示:

```
1 index is an integer
2 index is an integer
3 index is an integer
4 index is an integer
5 index is an integer
The index 6 needs to be a digit
```

```
All six indexes have been checked
```

## 处理错误

现在您已经知道了报告错误的技巧，让我们来处理它们。

### 重试该操作

有时，错误可能是由外部系统对有效请求造成的。例如，当使用 API 获取一些坐标时，您会得到错误 503，服务不可用，这是由于过载或网络故障引起的。

此时，服务可能会在几秒钟后恢复，并且报告错误可能不是理想的做法，所以您重试该操作。此外，如果您在堆栈的底层，这可能不是一个好主意，因为所有层都在重试该过程，等待时间会大大延长。在这种情况下，最好中止，让客户端从他们那边重试。

### 向客户报告故障

当从客户端接收到错误的输入时，重试没有意义，因为我们可能会在重新处理不正确的信息时得到相同的结果。在这种情况下，最直接的方法是完成剩余的处理，并将其报告给客户端。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 直接在堆栈顶部报告故障

有时直接报告错误是合适的，因为您可能知道原因。例如，当您试图打开一个不存在的文件时，就会产生在`try…catch blocks`部分中讨论的`ENOENT`错误，您可以使用上面讨论的任何方法来报告它。这样，您可以报告创建文件来解决错误。

如果出现不可恢复的错误，让程序崩溃是最好的选择。例如，由于未经许可访问文件而导致错误；除了让系统崩溃并让 sysadmin 提供访问权限之外，您别无选择。崩溃也是处理程序员错误恢复系统最实用的方法。

## 结论

总之，如果您努力编写好的代码并交付可靠的软件，适当的错误处理是强制性的。在这篇文章中，我们了解了 Node.js 中的错误以及处理它们的重要性。我们讨论了报告错误的初步方法:`try…catch`块、回调和承诺、异步/等待语法以及事件发射器。我们还学习了在报告这些错误时如何处理它们。

最后，我希望这对您有所帮助，现在您可以处理 Node.js 应用程序中的错误了。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.