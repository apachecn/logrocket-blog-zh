# JavaScript 生成器:卓越的异步/等待日志博客

> 原文：<https://blog.logrocket.com/javascript-generators-the-superior-async-await/>

编写前端代码或 Node.js 代码的最大挑战之一是处理异步性。当像[公司](https://github.com/tj/co)这样的包允许我们用像`try`和`catch`这样的普通结构编写看起来同步的异步代码时，发生了一场原始的生成器革命:

```
co.wrap(function*() {
  try {
    yield fetch('http://some.domain');
  } catch(err) {
    // handle
  }
});
```

## 黑暗时代(异步/等待)

大约在这个时候，C#和。net 开始将最初的`async...await`构造(扁平化的异步代码)转变成更熟悉的形式:

```
public static async Task Main()
{
    Task<int> downloading = DownloadDocsMainPageAsync();

    int bytesLoaded = await downloading;
    Console.WriteLine($"{nameof(Main)}: Downloaded {bytesLoaded} bytes.");
}

```

一些非常聪明的人决定 JavaScript 应该在 JavaScript 语言中采用`async...await`关键字。Babel 和 regenerator 将关键字结构转换成代码，使用生成器来实现异步工作流。Nodejs 更进一步，让`async...await`成为一等语言公民。

代码如此吸引人是因为它看起来是同步的。代码似乎停止并等待，直到返回响应或出现错误。代码可以包装在熟悉的`try..catch`块中。

`async...await`获得了很大的牵引力，而发电机的转速却被更有限的`async...await`忽略了。

## 暂停和恢复

JavaScript 生成器函数的不同之处在于，它们最初并不执行，而是返回一个带有`next`函数的迭代器对象。函数中的执行可以在`next`调用之间的暂停点暂停和恢复。

我使用 npm 包[正面/效果](https://github.com/thefrontside/effection)已经有一段时间了。

Effection 利用生成器的魔力允许我们编写这样的代码:

```
run(function* () {
  let socket = new WebSocket('ws://localhost:8080');

  yield throwOnErrorEvent(socket);

  yield once(socket, "open");

  let messages = yield once(socket, "message");

  while(true) {
    let message = yield messages.next();
    console.log('Got message:', message);
  }
});
```

上面的代码中有一些漂亮的抽象，简化了编写更少代码和更简单代码的过程。

例如:

```
yield once(socket, "open");
```

上面的代码声明在 websocket `open`事件发生之前，执行不能继续。

如果我们用普通的 JavaScript 来做这件事，它看起来会像这样:

```
const remove = socket.addEventListener('open', (event) => {
  // proceed
});
```

## 发电机的本质

让我们快速回顾一下是什么让发电机如此强大。

生成器函数是一个[迭代器](https://codeburst.io/a-simple-guide-to-es6-iterators-in-javascript-with-examples-189d052c3d8e)，它返回一个我们接下来可以调用的对象。生成器看起来是一个函数，但是它的行为就像一个[迭代器](https://codeburst.io/a-simple-guide-to-es6-iterators-in-javascript-with-examples-189d052c3d8e)。

让生成器如此强大的是它们暂停和恢复执行的能力。

下面的`everySingleEvenNumber`生成器函数说明了这种能力:

```
function* everySingleEvenNumber() {
  let i = 0;
  while (true) {
    yield i += 2;
  }
}

var gen = everySingleEvenNumber();

console.log(gen.next().value); // 2
console.log(gen.next().value); // 4
console.log(gen.next().value); // 6
console.log(gen.next().value); // 8
```

`while (true)`构造看起来像一个无限循环，但是执行在每个`yield`之后被暂停，只有当迭代器`next`函数在`the console.log`代码中被调用时才被恢复。

局部变量`i`的当前值不会在每次调用之间重置，而是保持不变。

生成器不同于 async/await，在 async/await 中，执行消失，只有在承诺解决或拒绝时才返回。

## 作为执行线程的生成器

暂停和恢复功能的能力打开了比 async/await 在快速采用中关闭的更多的门。

允许你生成单独的进程作为生成器函数，并负责拆除所有生效的子进程。这种技术被称为[结构化并发](https://en.wikipedia.org/wiki/Structured_concurrency#:~:text=Structured%20concurrency%20is%20a%20programming,structured%20approach%20to%20concurrent%20programming)。

Effection 公开了一个可以`spawn`新的`detached`进程的`task`对象:

```
main(function* (task: Task) {
  console.log('in main');

  task.spawn(function* () {
    while (true) {
      yield sleep(100);
      console.log('awake');
    }
  });

  yield;
})
```

## 真实世界场景

下面是一个`flakyConnection`功能，在第五次尝试之前不会连接:

```
let attempt = 1;

function flakyConnection(): Promise<{ connected: boolean }> {
  return new Promise<{ connected: boolean }>((resolve) => {
    setTimeout(() => {
      attempt++;
      resolve({ connected: attempt === 5 });
    }, 100);
  });
}
```

要获得连接，客户端必须尝试五次才能成功。好的客户端代码还会包含超时，如果操作耗时太长，就会抛出异常。

编写超时的轮询代码是很烦人的，但是 effection 和生成器的挂起和恢复特性使得这是一个非常好的体验:

```
main(function* (parent: Task) {
  parent.spawn(function* (child) {
    child.spawn(function* () {
      console.log('primed to throw an Error');
      yield sleep(8000);

      throw new Error('you are out of time!  Better luck next time.');
    });

    while (true) {
      console.log(`connection attempt ${attempt}...`);
      const { connected } = yield flakyConnection();

      if (connected) {
        console.log('we are connected!');
        return true;
      }

      console.log('no cigar, we try again');

      yield sleep(2000);
    }
  });

  yield;
});
```

一个新流程被附加到通过`main`可用的`parent`任务对象。

下面的代码很好地处理了超时设置，如果客户端在 8000 毫秒后无法连接，将抛出异常:

```
child.spawn(function* () {
  console.log('primed to throw an Error');
  yield sleep(8000);

  throw new Error('you are out of time!  Better luck next time.');
});
```

effection `sleep`函数将暂停执行 8000 毫秒。如果 8000 毫秒后父进程仍然存在，那么它将抛出一个异常。

下面的代码将尝试以 200 毫秒的时间间隔进行连接，直到成功为止:

```
while (true) {
  console.log(`connection attempt ${attempt}...`);
  const { connected } = yield flakyConnection();

  if (connected) {
    console.log('we are connected!');
    return true;
  }

  console.log('no cigar, we try again');

  yield sleep(300);
}
```

上面的代码可以一直执行，直到出现连接或者超时异常抛出，此时生效将关闭所有子进程。

运行上述代码会产生以下输出:

```
primed to throw an Error
connection attempt 1...
no cigar, we try again
connection attempt 2...
no cigar, we try again
connection attempt 3...
no cigar, we try again
connection attempt 4...
we are connected!
```

下面是一个[回购](https://github.com/dagda1/task)上面的代码。

您可以通过将超时代码更改为如下所示来检查超时是否有效:

```
child.spawn(function* () {
  console.log('primed to throw an Error');
  yield sleep(4000);

  throw new Error('you are out of time!  Better luck next time.');
});
```

超时会导致以下输出:

```
primed to throw an Error
connection attempt 1...
no cigar, we try again
connection attempt 2...
no cigar, we try again
Error: you are out of time!  Better luck next time.
```

## 是时候进行从未发生过的革命了

我仍然将 async/await 用于没有工作流的简单的一次性异步任务，但这是一个有限的范例。

生成器函数可以解决所有其他函数无法解决的问题。启动和恢复执行线程是非常强大的，生成器内置了这种功能。

跳进来！水是温暖的。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.