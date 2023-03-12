# 使用没有队列的 WebSocket 订阅

> 原文：<https://blog.logrocket.com/using-websocket-subscriptions-without-queues/>

我们生活在一个互联的世界，实时性的必要性从未像现在这样大。在实时领域有两个主要参与者:服务器发送事件(SSE)和 WebSockets。

这两者非常相似，都允许服务器将数据推送到客户端，而无需客户端轮询服务器的更新。

然而，主要的区别是 WebSockets 允许客户端做同样的事情(向服务器发送数据)，而 SSE 不允许。在 SSE 世界中，您发出一个请求，服务器可以多次回复该请求。

这两种方法各有利弊，根据您的需要，您可能会选择其中一种。在本文中，我们将关注 WebSockets。

## 什么是 Websockets？

WebSocket 协议描述了一种全双工 web 通道，在面对实时数据需求时经常使用。它提供了一种在服务器和客户端之间建立活动连接(套接字)的方法，以实现低开销的双向通信，并提供了定义良好的方法来传递支持的语言和致命错误。

它被广泛用于多人游戏、实时金融、在线聊天以及许多需要实时协作的地方。

您首先通过 HTTP 发送一个协议更新请求，服务器评估该请求，检查支持的子协议(套接字通信语言)，并以一个`101: Switching Protocols`响应结束升级。

成功更新后，通过此活动 TCP 连接进行的通信遵循 WebSocket 协议。此时，在套接字连接的整个持续时间内，服务器和客户机都可以随时相互发送消息。

## 通过 WebSockets 管理订阅

我们将把重点放在客户端，因为它比服务器复杂得多，同时也有趣得多。

在设计 WebSocket 客户端时，您会面临许多挑战，例如连接到服务器、重用现有套接字、处理断开连接、传达致命错误、惰性连接等。

然而，WebSocket 协议有助于将底层复杂的通道管理抽象为用户友好的连接规则、清晰的关闭事件和简单的数据发送方法。

但是，现在您只需要管理这些。决定使用 WebSockets 作为您的发布/订阅系统后，您还需要管理:静默重新连接、开始和结束订阅、在连接中断时重新订阅、向订阅者传播错误、管理向适当的侦听器的消息传递等等，这取决于您的用例。

通过向套接字添加订阅，您必须管理两层“活动通道”:实际的网络连接套接字和其中的单个订阅通道。

在设计订阅系统时，您需要考虑活动订阅者的队列，其中有一个中央管理器负责将消息分发到适当的目的地、传递错误，以及在必要时通过添加或删除侦听器来处理队列本身。

这种集中式系统使得订阅变得不必要的复杂和难以维护。

## 而是使用 JavaScript 事件工具

让我们试试更简单的方法，比如使用语言内置的“队列”:JavaScript 事件循环。

在这里，我们用承诺阻塞异步事件循环，承诺在挂起时发出事件，一旦连接/订阅完成就解决事件，或者如果在其生命周期中出现任何问题就拒绝事件。

这种方法通过强迫您依赖语言原语，从根本上简化了维护过程。错误处理？尝试/抓住。重试？把它放在一个循环里。正在完成？只需返回/解决。

JS 事件循环不再是在一个队列中构建一个队列，而是成为我们唯一需要的队列——计时、效率和内存管理现在是我们不再需要考虑的事情。

与其这样做，❌:

```
const complete = subscribe({
  onNext: (msg: unknown) => void
  onError: (err: Error) => void,
  onComplete: () => void,
});

```

做到这一点

```
const [complete: () => void, waitForCompleteOrThrow: Promise<void>] = await subscribe(listener: (msg: unknown) => void);

```

像这样使用它:

```
const [complete, waitForCompleteOrThrow] = await subscribe((msg) => {
  // handle message
});

// complete/cancel/stop wherever and whenever you want
onLeavePage(cancel);
onClickOnClose(cancel);
onComponentUnmount(cancel);

try {
  await waitForCompleteOrThrow;
  // completed
} catch (err) {
  // handle err
}

```

## 实现客户端

下面的代码示例是不言自明的，所以请仔细阅读，并返回注释以获得更多的理解。

首先，我们从构建一个`connect`函数开始，它建立了与服务器的正确连接，并提供了管理它的简单方法:

```
/**
 * A simple WebSocket connect function that resolves once the socket
 * opens and the server acknowledges the connection.
 */
export async function connect(
  url: string,
): Promise<
  [
    socket: WebSocket,
    complete: () => void,
    throwOnCloseOrWaitForComplete: () => Promise<void>,
  ]
> {
  const socket = new WebSocket(url);

  /**
   * For if the socket closes before you start listening
   * for the
   */
  let closed: CloseEvent;

  /**
   * Once promises settle, all following resolve/reject calls will simply
   * be ignored. So, for the sake of simplicity, I wont be unlistening.
   */
  await new Promise<void>((resolve, reject) => {
    /**
     * From: https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_client_applications
     * > If an error occurs while attempting to connect, first a simple event
     * > with the name error is sent to the WebSocket object (thereby invoking
     * > its onerror handler), and then the CloseEvent is sent to the WebSocket
     * > object (thereby invoking its onclose handler) to indicate the reason for
     * > the connection's closing.
     *
     * Keeping this in mind, listening to the `onclose` event is sufficient.
     * Close events (code + reason) should be used to communicate any critical
     * problem with the socket.
     */
    socket.onclose = (event) => {
      closed = event;
      reject(event);
    };

    /**
     * Sometimes the socket opens and closes right after, so try relying an
     * acknowledgment message from the server to confirm the connection instead
     * of the `onopen` event.
     */
    socket.onmessage = ({ data }) =>
      data === 'ack' ? resolve() : reject(new Error("Didn't acknowledge!"));
  });

  return [
    socket,
    () => socket.close(1000, 'Normal Closure'), // normal closure is completion
    /**
     * The promise is the state flag. If pending, socket is active; if rejected,
     * socket closed; and if resolved, socket completed.
     */
    () =>
      new Promise<void>((resolve, reject) => {
        const check = (event: CloseEvent) => {
          if (event.code === 1000) {
            resolve();
          } else {
            reject(event);
          }
        };
        if (closed) return check(closed);
        socket.addEventListener('close', check);
      }),
  ];
}

```

很直接，对吧？但是，这感觉太简单了。您通常想要更复杂的行为，比如仅在需要时建立 WebSocket 连接，并在完成后关闭它们。

通过重用`connect`函数，实现惰性连接相当简单:

```
/**
 * Makes a lazy connect function that establishes a connection
 * on first lock and closes it on last release.
 */
export function makeLazyConnect(
  url: string,
): () => Promise<
  [
    socket: WebSocket,
    release: () => void,
    waitForReleaseOrThrowOnClose: () => Promise<void>,
  ]
> {
  let connecting: ReturnType<typeof connect> | null,
    locks = 0;
  return async function lazyConnect() {
    /**
     * A new lazy connection is established, increment the locks.
     * Once all locks are released, the actual socket connection will
     * complete.
     */
    locks++;

    /**
     * Promises can resolve only once and will return the fullfiled value
     * on each subsequent call. So we simply reuse the connect promise.
     */
    if (!connecting) connecting = connect(url);
    const [socket, complete, throwOnCloseOrWaitForComplete] = await connecting;

    let release = () => {
      /**
       * Release the lazy connect lock. The actual decrementation
       * happens below, in the release waiter. Note that this function
       * will be replaced with the `released` resolve function in the
       * following promise.
       */
    };
    const released = new Promise<void>((resolve) => (release = resolve)).then(
      () => {
        /**
         * Release the lock by decrementing the locks.
         */
        if (--locks === 0) {
          /**
           * If no lazy connection locks exist anymore, complete
           * the actual socket conection.
           */
          complete();
        }
      },
    );

    return [
      socket,
      release,
      () =>
        Promise.race([
          released,
          throwOnCloseOrWaitForComplete()
            /**
             * Complete or close, both close the socket, create
             * a new one on next connect.
             */
            .finally(() => (connecting = null)),
        ]),
    ];
  };
}

```

厉害！现在我们利用了惰性连接，最后一部分是订阅的实际订阅功能。之前构建的所有元素现在汇集在一起:

```
/** A globally unique ID used for connecting responses. */
export type ID = number;

/**
 * For starting a subscriptions. Holds the unique ID
 * for connecting future responses.
 */
export interface RequestMsg {
  id: ID;
  request: string;
}

/**
 * The response message for an active subscription. ID would
 * be the same one as requested in the request message.
 */
export interface ResponseMsg {
  id: ID;
  response: string;
}

/**
 * Complete message indicating that the subscription behind
 * the ID is done and will not be emitting further events. Complete
 * message is bi-directional so both the server and the client
 * can complete a subscription.
 */
export interface CompleteMsg {
  complete: ID;
}

/**
 * Isolated, self sustained, unit that has all the necessary logic built
 * right in. It establishes a lazy connection with the configured server,
 * silently retries on abrupt closures, generates unique subscription IDs,
 * dispatches relevant messages to the listener, offers a stop method (complete)
 * which closes the lazy connection on last unsubscribe and a promise that resolves
 * on completions and rejects on possible problems that might occur with the socket.
 */
let currId = 0;
export function subscribe(
  connect: ReturnType<typeof makeLazyConnect>,
  request: string,
  listener: (response: string) => void,
): [complete: () => void, waitForCompleteOrThrow: Promise<void>] {
  /**
   * A reference to the completer which will be replaced with a new
   * complete function once the connection is established and the
   * subscription is requested. If the user completes the subscription
   * early (before having connected), the `completed` flag is used
   * to release the connection lock ASAP.
   */
  let completed = false;
  const completerRef = {
    current: () => {
      /** For handling early completions. */
      completed = true;
    },
  };

  const waitForCompleteOrThrow = (async () => {
    for (;;) {
      try {
        const [socket, release, waitForReleaseOrThrowOnClose] = await connect();

        /**
         * If the user completed the subscription before the connection,
         * release it right away - we dont need it.
         */
        if (completed) return release();

        /**
         * Subscribe and listen...
         */
        const id = currId++;
        socket.send(JSON.stringify({ id, request } as RequestMsg));
        const onMessage = ({ data }: MessageEvent) => {
          const msg = JSON.parse(data) as ResponseMsg | CompleteMsg;
          if ('complete' in msg && msg.complete === id) {
            release();
          } else if ('id' in msg && msg.id === id) {
            listener(msg.response);
          }
        };
        socket.addEventListener('message', onMessage);

        /**
         * Assign a new completer which notifies the server that we are
         * done with the subscription, removes the socket message listener
         * and releases the lazy connection lock.
         */
        completerRef.current = () => {
          socket.send(JSON.stringify({ complete: id } as CompleteMsg));
          release();
        };

        /**
         * Completing the subscription releases the connection lock,
         * waiting for the release is the same as waiting for the complete.
         */
        await waitForReleaseOrThrowOnClose();
        socket.removeEventListener('message', onMessage);
        return;
      } catch (err) {
        if ('code' in err && err.code === 1006) {
          /**
           * Its completely up to you when you want to retry, I've chosen
           * to retry on the CloseEvent code 1006 as it is used when the
           * socket connection closes abruptly (for example: due to client
           * network issues).
           */
          continue;
        } else {
          /**
           * All other errors are considered fatal, rethrow them to break
           * the loop and report to the caller.
           */
          throw err;
        }
      }
    }
  })();

  return [() => completerRef.current(), waitForCompleteOrThrow];
}

```

我们最终得到的是一个孤立的、自我维持的单元，它内置了所有必要的逻辑。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`subscribe`函数与已配置的服务器建立一个惰性连接，在突然关闭时静默重试，生成唯一的订阅 id，将相关消息发送给监听器，提供一个 stop 方法(complete ),在最后一次取消订阅时关闭惰性连接，以及一个承诺，在完成时解决并拒绝套接字可能出现的问题。

## 结论

真的就这么简单！只需几行代码，您就可以实现一个弹性订阅客户端，它使用 WebSocket 协议作为传输层。

进一步的改进和解决方案很容易添加，逻辑很容易理解，代码不会让您感到疲劳。此外，可以在服务器端应用相同的习惯用法，以提高稳定性并降低复杂性。

您可以从本文的代码中看到[运行中的](https://github.com/enisdenjo/ws-sub-just-js)。

感谢您的阅读，我希望这篇文章对您的实时工作有所帮助！👋

附:这些简单的想法和惯例有助于将`[graphql-ws](https://github.com/enisdenjo/graphql-ws)`带入生活。

如果你对如何在一个相当复杂的环境中应用所有这些感兴趣，你会发现它的客户端实现相当有趣。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.