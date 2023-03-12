# async/await 是错误的抽象

> 原文：<https://blog.logrocket.com/async-await-is-the-wrong-abstraction/>

不可否认的是，`async....await`模式非常简单，简化了异步编程，更像是服务器端编程，因为没有了`try....catch`块的保护，开发人员会感到有点不安全和害怕。

我们的意识或左脑在可以被认为是对现实的抽象中运作。宇宙是在同一时间同时发生的一系列无穷小的事件，我们的意识无法理解，它顺序或线性地思考，我们一次处理一个想法。

我们试图用`async....await`做的是忽略现实，让这些异步操作看起来同步发生。以这种方式逃避现实是很棒的，直到它不再是。

我时常会看到有人发推文，说他们意识到`async...await`有根本性的缺陷，原因如下……如果你自己没有发现的话。

> 没有描述

当我看到这样的推文时，我感到同情和同情，并相信又有一名成员加入了我们现实世界的搏击俱乐部。我不需要告诉你这个俱乐部的第一条规则是什么。

我第一次意识到这一点是在我开发一个允许用户将大视频文件上传到 Azure blob 存储的功能时。因为这些文件很大，必须分成独立的块。我在一个`for...of`循环中使用了`async...await`。然后出现了用户想要中途取消上传的需求。就在那时，这个神奇的几乎同步的代码块不适合这个目的。

取消承诺链

## 这是无法回避的，而且`async...await`中也完全没有支持取消的东西。下面是从属呼叫链的一个简单示例:

这里我们有一个经典的承诺链，每个电话都在等待最后一个电话。如果我们想在`updateUserAddress`取消，不打电话给`updateFinancialStatus`怎么办？

```
async function updatePersonalCircumstances(token) {
  const data = await fetchData();
  const userData = await updateUserData(data);
  const userAddress = await updateUserAddress(userData);
  const financialStatus = await updateFinancialStatus(userAddress);

  return financialStatus;
}

const token = {};
const promise = updatePersonalCircumstances(token);
```

现在我们已经到了这一部分，你坐得舒服吗？那么让我把它拼出来…..

一旦你打了一个电话，除非潜在的承诺解决或拒绝，否则你永远不会出来。

半生不熟的解决方案

## 取消这个链的唯一方法是像这样包装每个单独的`async..await`调用:

不幸的是，我们需要检查每个电话，看看是否有取消。我们把做正确事情的全部责任推给了用户。

```
async function updatePersonalCircumstances(token) {
  let cancelled = false;

  // we can't reject, since we don't have access to
  // the returned promise
  token.cancel = () => {
    cancelled = true;
  };

  const data = await wrapWithCancel(fetchData)();
  const userData = await wrapWithCancel(updateUserData)(data);
  const userAddress = await wrapWithCancel(updateUserAddress)(userData);
  const financialStatus = await wrapWithCancel(updateFinancialStatus)(userAddress);

  // we check after each call to see if something has happend
  if (cancelled) {
    throw { reason: 'cancelled' };
  }

  return financialStatus;

  function wrapWithCancel(fn) {
    return data => {
      if (!cancelled) {
        return fn(data);
      }
    }
  }
}

const token = {};
const promise = updateUser(token);

token.cancel(); // abort!!!
```

发电机复兴

## 当我第一次遇到这个问题时，我正在做一个 angular 项目，它有一个依赖项 [RxJS](https://rxjs-dev.firebaseapp.com/) 。 [RxJS 可观测量](https://blog.logrocket.com/understanding-rxjs-observables/)对[取消](https://medium.com/the-geeks-of-creately/cancelling-observables-rxjs-f4cf28c3b633)有一流支持。rxjs 的问题是很难跟上它的速度，它是巨大的。我已经忘记了我所学到的关于 rxjs 可观测量的大部分内容，但是它们真的很适合取消。要是 JavaScript 有取消的原生支持就好了？嗯，有点像。

我最近发现了 [effection.js](https://github.com/thefrontside/effection/tree/master) ，它是为了解决这个问题而产生的，但后来却拓展了发电机的应用范围。

对于发电机，如果我们想取消，您可以立即返回或丢弃发电机。它实际上是一个没有这种便利的黑盒子。

以下是取消承诺链的更好解决方案:

上面的代码是一个更彻底的例子的基本实现，我在这篇文章的最后链接到这个例子。关键是取消功能:

```
function runner(fn, ...args) {
  const gen = fn(...args);
  let cancelled, cancel;
  const promise = new Promise((resolve, promiseReject) => {
    cancel = () => {
      cancelled = true;
      reject({ reason: 'cancelled' });
    };

    let value;

    onFulfilled();

    function onFulfilled(res) {
      if (!cancelled) {
        let result;
        try {
          result = gen.next(res);
        } catch (e) {
          return reject(e);
        }
        next(result);
        return null;
      }
    }

    function onRejected(err) {
      var result;
      try {
        result = gen.throw(err);
      } catch (e) {
        return reject(e);
      }
      next(result);
    }

    function next({ done, value }) {
      if (done) {
        return resolve(value);
      }
      return value.then(onFulfilled, onRejected);
    }
  });

  return { promise, cancel };
}

function* updatePersonalCircumstances() {
  const data = yield fetchData();
  const userData = yield updateUserData(data);
  const userAddress = yield updateUserAddress(userData);
  const financialStatus = yield updateFinancialStatus(userAddress);

  return financialStatus;
}

const { promise, cancel } = runner(updatePersonalCircumstances);

// cancel baby!!!
cancel();
```

调用`cancel`拒绝了承诺，但使其可取消的关键是生成器功能一直在起作用。我们可以使用[生成器抛出函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/throw)作为中止信号来表示取消，或者我们甚至可以使用[生成器的返回函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/return)来停止执行承诺链。

```
cancel = () => {
  cancelled = true;
  reject({ reason: 'cancelled' });
};
```

我在这里提出的观点是，在整个调用序列中，生成器总是在起作用，而在`async...await`中没有这样的便利。

现实世界中的发电机

## 我已经创建了[这个更复杂的代码沙箱](https://codesandbox.io/s/use-abort-f5kbt?file=/src/useAbortable/useAbortable.ts)，它将这个功能封装到一个 [React 钩子](https://reactjs.org/docs/hooks-reference.html)中。我还使用了 [xstate](https://github.com/davidkpiano/xstate) 来表示异步请求中的各种状态变化。使用有限状态机给了代码一个更好的抽象，并且优于伪阻塞范例，伪阻塞范例有明显的局限性，比如本文的反面角色，即`async...await`。

效果. js

## 我要感谢前端人员让我看到了 JavaScript 生成器这块未开采的金子。天空是无限的，它们可以用在任何可以想象的环境中，比如构建工具:

查看[效果](https://github.com/thefrontside/effection/tree/master)来改变你的观点。

```
import { createConnection, Connection, ConnectionConfig } from 'mysql';
import { spawn, timeout, Operation } from 'effection';
import { main } from '@effection/node';

import { Deferred } from './deferred';

main(function* prepare(): Operation<void> {

  let connection: Connection = yield function* getConnection(): Operation<Connection> {
    // asynchronously wait for 10s and then raise an exception.
    // if a connection is created before the timeout, then this
    // operation will be cancelled automatically because the enclosing
    // operation returned.
    yield spawn(function*(): Operation<void> {
      yield timeout(10000);
      throw new Error('timeout out waiting 10s for mysql connection');
    });

    // Loop "forever" trying to repeatedly create a connection. Of
    // course it isn't forever, because this loop is racing against
    // the timeout.
    while (true) {
      try {
        return yield connect({
          user: "root",
          host: "localhost",
          port: 3306
        });
      } catch (error) {
        // if its a socket error or a MysqlError, we want to try again
        // otherwise, raise the exception
        if (!error.errno) {
          throw error;
        }
      }
    }
  }

  try {
    //now we have the connection and can query, migrate, etc...
  } finally {
    connection.destroy();
  }
});

/**
 * Create a mysql connection as an effection Operation.
 */
function* connect(config: ConnectionConfig): Operation<Connection> {
  let { resolve, reject, promise } = Deferred<Connection>();
  let connection = createConnection(config);

  connection.connect((err?: Error) => {
    if (err) {
      reject(err);
    } else {
      resolve(connection);
    }
  });

  return yield promise;
}
```

收场白

## 我认为我们已经满足于方便而不是功能。我仍然使用`async..await`,它非常适合一次调用的场景，但是我和许多其他人发现，它对于更复杂的现实情况来说是最小的。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).