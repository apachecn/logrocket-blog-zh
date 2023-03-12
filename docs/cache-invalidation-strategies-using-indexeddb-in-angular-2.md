# Angular 2+ - LogRocket 博客中使用 IndexedDB 的缓存失效策略

> 原文：<https://blog.logrocket.com/cache-invalidation-strategies-using-indexeddb-in-angular-2/>

创建离线优先的 web 应用程序不是一项简单的任务。需要考虑的因素很多(超出了一篇文章所能涵盖的范围)，并且存在许多潜在的陷阱。然而，关于在用户的浏览器中存储数据，需要考虑两个非常重要的问题:在哪里存储数据，以及如何确保数据不会过时。

假设您的任务是构建一个销售点(POS)应用程序，该应用程序需要能够在网络连接不良的地区运行。缓存产品信息、客户和购买数据需要大量的规划。你如何设计你的应用程序，使其具有低网络占用空间？您将数据存储在哪里，以确保在无法访问网络的情况下可以访问这些数据？您如何确保运行该应用程序的所有设备都使用相同的数据集？

## 存储选项

在 web 开发领域，客户端缓存数据并不是一个新概念。 [HTML Web 存储](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)从 2009 年开始所有主流浏览器都支持，一些前端框架内置了缓存机制。

[RxJS 库](https://rxjs.dev/)有两个操作符可以用来缓存 HTTP 调用:`[publishReplay()](https://rxjs.dev/api/operators/publishReplay)`和`[refCount()](https://rxjs.dev/api/operators/refCount)`。使用这两个操作符来缓存从一个可观察对象最后发出的值是在 Angular 中实现缓存的一种廉价而快速的方法，特别是如果您的数据不经常改变的话。如果您的数据经常更改，或者您的应用程序需要完全支持离线，您会希望使用浏览器存储。

由于其易用性和成熟性， [localStorage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 是一个诱人的候选。然而，它也有缺点。在大多数浏览器中，你只能存储大约 5MB 的数据。对于许多场景来说，这已经足够了，但是如果您计划开发一个离线优先的应用程序，您很快就会发现自己已经达到了这个极限。它还是同步的，这意味着如果您不断地访问 localStorage API，页面上的其余 JS 将需要等待操作完成。使用 localStorage 作为主要的客户端数据存储机制来开发离线优先的渐进式 web 应用程序是一场艰苦的战斗。

幸运的是，我们有 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 。IndexedDB 是一个异步的客户端 NoSQL 存储，目前被超过 90%的用户浏览器所支持。在 Chrome、Firefox 和 Edge 中，IndexedDB 的存储配额是根据可用磁盘空间确定的，在某些情况下，还会根据卷大小进行分层。在所有现代浏览器中，存储限制至少是 50MB，所以可以放心地假设您将能够存储比 localStorage 提供的 5MB 更多的内容。在许多情况下(尤其是在桌面设备上)，存储千兆字节的数据是可能的。

与任何技术一样，IndexedDB 也有一些缺点。这个 API 相当笨重，而且不像 localStorage 那么简单。

下面是一个非常基本的例子，说明如何使用 IndexedDB API 创建一个事务并插入一个项目。

```
function insertIntoDB() {
    // Create a product to be inserted into the database
    var products = [
      { id: 1, name: 'T-Shirt', price: 10 }
    ];

    // Create a transaction for inserting into the DB
    var transaction = db.transaction(["products"], "readwrite");
    var objectStore = transaction.objectStore("products");

    // Finally add the item. This returns an IDBRequest object
    var objectStoreRequest = objectStore.add(products[0]);

    objectStoreRequest.onsuccess = function(event) {
      // Success callback
    };

    objectStoreRequest.onerror = function(event) {
      // Error callback
    };
};
```

这个例子假设您已经创建了数据库，并定义了模式，这本身就是一个复杂的过程。

不幸的是，处理默认 IndexedDB API 所需的工作可能会让许多开发人员感到厌烦，并导致他们为了 localStorage 诱人的易用性而牺牲可伸缩性和性能。我不怪他们，除非你想困在回调地狱，否则我会建议避免默认的 IndexedDB API。

如果你正在使用 Angular2+,你很有可能想要使用 RxJS Observables 来管理你的异步任务(比如 IndexedDB 存储和检索)。幸运的是，有一些很棒的 Angular2+库对 IndexedDB 进行了改进，为它提供了一个类似于 localStorage 的 API，但具有异步客户端存储的优势。尤其是，`[@ngx-pwa/local-storage](https://github.com/cyrilletuzi/angular-async-local-storage)`是一个很棒的 Angular 应用程序库。这个 API 非常简单，它内置了对 RxJS Observables 的支持。

下面是一个使用`@ngx-pwa/local-storage`将项目保存到 IndexedDB 的示例:

```
// Notice how the API looks just like localStorage. Simple, and familiar.
this.localStorage.setItem('products', products).subscribe(() => {});

// ...and it can be simplified even further with auto-subscription:
this.localStorage.setItemSubscribe('products', products);
```

界面更加简洁，不需要使用回调、设置事务或管理数据库。

## 保持数据新鲜(缓存失效)

假设您已经决定使用 IndexedDB，下一个关键的决定将是如何确保您的缓存数据保持最新。每当用户执行一个动作时，您可以按需发出 HTTP 请求，但是这有损于整体用户体验。你最终会使用更多的带宽，当你受到用户网络的支配时，不可能保证一致的用户体验。

缓存失效绝不是一个容易解决的挑战。菲尔·卡尔顿(Phil Karlton)的名言(尽管是开玩笑的)是有原因的:

> "计算机科学中只有两个难题:缓存失效和命名事物."

下面提出的每个策略都有缺点和权衡。如果你的应用程序在线时，你的用户需要获得最新的数据，那么你要为此付出代价。

您存储的数据类型以及数据生命周期的具体环境也会影响您选择的策略。就本文而言，我们将使用销售点(POS) web 应用程序的理论场景，该应用程序在用户的浏览器中缓存可销售的产品。

### **轮询**

轮询是最简单的缓存失效策略之一，可以通过多种方式实现。有两种主要的轮询类型–短轮询和长轮询。在我看来，服务器发送的事件或 WebSockets 几乎总是长轮询技术的更好替代方案。对于本文，我们将只讨论短轮询。然而，如果您有兴趣学习更多关于长轮询技术的知识，我建议从 [Comet](https://en.wikipedia.org/wiki/Comet_(programming)) 及其包含的策略开始。

一个简单的短轮询示例检索要在 POS 应用程序上显示和缓存的产品列表，如下所示:

```
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable, interval } from 'rxjs';
import { map, tap, concatMap } from 'rxjs/operators';
import { LocalStorage } from '@ngx-pwa/local-storage';

interface Product { 
  id: number, 
  name: string, 
  price: number 
} 

@Injectable({
  providedIn: 'root'
})
export class PollingService {
  private api = 'http://localhost:3000';

  constructor(
    private http: HttpClient,
    private localStorage: LocalStorage
  ) {}

  public poll(): void {
    interval(10000) // Poll every 10s
      .pipe(
        // concatMap ensures that each request completes before the next one begins.
        // Use of concatMap becomes increasingly important as your polling frequency goes up.
        concatMap(() => this.updateCache())
      ).subscribe();
  }

  private updateCache(): Observable<Product[]> {
    return this.localStorage.getItem<any>('products').pipe(
      concatMap(() => {
        return this.getProducts().pipe(
          tap(products => {
            if (products.length) {
              // This will set a Product[] in IndexedDB with a key of 'products'
              this.localStorage.setItemSubscribe('products', products);
            }
          }),
          map(products => {
            return products;
          })
        );
      })
    );
  }

  private getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(`${this.api}/products`)
      .pipe(
        map(products => {
          return products;
        }),
      );
  }
}
```

该服务包含三个功能:`poll()`、`updateCache()`和`getProducts()`。`poll()`函数每十秒钟调用一次`updateCache()`，然后触发对服务器的 HTTP 请求。如果数据被返回，那么它被存储在 IndexedDB 中。

短轮询有一些缺点。每当没有新数据要检索时，任何 HTTP 请求都是浪费带宽。在实际场景中，每十秒钟轮询一次“list”端点也会给数据库带来不必要的压力。有一些方法可以帮助提高轮询的效率，但是它们涉及到后端基础设施的改变。我不会深入探讨，但是通过使用`updatedAt`时间戳，您可以在轮询时潜在地减少数据库的负载。

假设我们的`/products`端点返回给我们这样的对象:

```
[
  { "id": 1, "name": "T-Shirt", "price": 10, "updatedAt": "2019-04-23T18:25:43.511Z"},
  { "id": 2, "name": "Mug", "price": 5, "updatedAt": "2018-04-23T19:22:13.601Z"}
]
```

我们可以使用`updatedAt`属性来告诉服务器我们拥有的数据版本。在上面的`PollingService`示例中的`getProducts()`函数中，我们可以传递 IndexedDB 中最近的`updatedAt`时间戳，作为 HTTP 请求中的 URL 参数。

在后端，您可以简单地运行一个查询来找出最后一个产品是何时添加或修改的:

```
SELECT TOP 1 updatedAt
FROM products
ORDER BY updatedAt DESC
```

如果来自客户端的时间戳与来自查询的时间戳不同，那么显然产品已经被添加、删除或修改，我们应该运行一个查询来获取所有产品。如果时间戳与查询中的时间戳匹配，那么我们可以简单地向客户端返回一个特殊的响应，指定缓存是最新的。

这种时间戳策略还可以与服务器发送的事件和 WebSockets 结合使用，以确保数据在网络连接丢失后保持新鲜。

然而，我们对时间戳策略做了一个大的假设。我们假设检查时间戳所需的时间远远少于从后端数据库列出所有产品所需的时间。根据您的数据库、索引和总体后端基础设施的不同，差异可能可以忽略不计。

### 服务器发送的事件

服务器发送事件(SSE) API 是一种 HTML 规范，用于从服务器向客户端单向发送数据，它经常被 WebSockets 忽略和掩盖。与 WebSockets 相比，SSE 最大的吸引力在于它的易用性。与 WebSockets 不同，SSE 是通过 HTTP 协议传输的，带有一种特殊的 MIME 类型`text/event-stream`。因此，使用您现有的服务器基础设施来实现 SSE 是非常容易的。不幸的是，任何版本的 Internet Explorer 或 Edge 都不支持 T2。然而，有[多填充物](https://github.com/Yaffle/EventSource)可用。

从 Angular 内部处理 SSE 非常简单。每个事件都有一个`type`和`data`。对于我们的例子，我们希望在产品被添加、更新或删除时得到通知。我们的假设是后端为每一个发送不同类型的事件。因此，我们为每种类型的事件附加一个侦听器:

```
import { Injectable } from '@angular/core';
import { LocalStorage } from '@ngx-pwa/local-storage';

interface Product { 
  id: number, 
  name: string, 
  price: number 
} 

@Injectable({
  providedIn: 'root'
})
export class ServerSentEventService {
  private api = 'http://localhost:3000/stream';
  private eventSource: EventSource;

  constructor(
    private localStorage: LocalStorage
  ) {
    this.eventSource = new EventSource(this.api);
  }

  public listen(): void {
    this.eventSource.addEventListener('productAdd', (message: MessageEvent) => this.addProductToCache(message));
    this.eventSource.addEventListener('productUpdate', (message: MessageEvent) => this.updateProductInCache(message));
    this.eventSource.addEventListener('productDelete', (message: MessageEvent) => this.deleteProductFromCache(message));
  }

  ...
}
```

我们已经设置了侦听器，所以现在我们需要做的就是更新我们的缓存。当事件被触发时，根据事件的类型，监听器的一个回调函数将被触发。当接收到一个`productAdd`事件时，我们只想将来自`MessageEvent`的产品对象附加到我们当前的 IndexedDB 缓存中。同样，对于`productUpdate`和`productDelete`，我们希望从我们的 IndexedDB 数据库中检索项目，并进行必要的调整。

下面是一个在收到 SSE 时从 IndexedDB 中添加、更新或删除项目所需的实现的工作示例。

```
import { Injectable } from '@angular/core';
import { LocalStorage } from '@ngx-pwa/local-storage';

interface Product { 
  id: number, 
  name: string, 
  price: number 
} 

@Injectable({
  providedIn: 'root'
})
export class ServerSentEventService {
  private api = 'http://localhost:3000/stream';
  private eventSource: EventSource;

  constructor(
    private localStorage: LocalStorage
  ) {
    this.eventSource = new EventSource(this.api);
  }

  public listen(): void {
    this.eventSource.addEventListener('productAdd', (message: MessageEvent) => this.addProductToCache(message));
    this.eventSource.addEventListener('productUpdate', (message: MessageEvent) => this.updateProductInCache(message));
    this.eventSource.addEventListener('productDelete', (message: MessageEvent) => this.deleteProductFromCache(message));
  }

  private addProductToCache(eventMessage: MessageEvent): void {
    this.localStorage.getItem<Product[]>('products').subscribe((products: Product[]) => {
      products = products || []; 
      let eventMessageProduct: Product = JSON.parse(eventMessage.data);
      products.push(eventMessageProduct);

      this.localStorage.setItemSubscribe('products', products);
    });
  }

  private updateProductInCache(eventMessage: MessageEvent): void {
    this.localStorage.getItem<Product[]>('products').subscribe((products: Product[]) => {
      let eventMessageProduct: Product = JSON.parse(eventMessage.data);
      let productIndex = products.findIndex((product => product.id === eventMessageProduct.id));
      products[productIndex] = eventMessageProduct;

      this.localStorage.setItemSubscribe('products', products);
    });
  }

  private deleteProductFromCache(eventMessage: MessageEvent): void {
    this.localStorage.getItem<Product[]>('products').subscribe((products: Product[]) => {
      let eventMessageProduct: Product = JSON.parse(eventMessage.data);
      products = products.filter(item => item.id !== eventMessageProduct.id)

      this.localStorage.setItemSubscribe('products', products);
    });
  }
}
```

如您所见，在所有场景中，我们从 IndexedDB 数据库中加载现有产品，并对数组中的对象进行调整。在每种情况下，这些操作后面都有一个`setItem`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然而，这种策略有一个明显的缺陷。如果客户端的 internet 连接丢失，或者与服务器的连接终止，我们如何知道在重新连接时数据不会过时？

重新连接到事件流是微不足道的(在一些浏览器中会自动发生)，但总有可能会错过一些事件。这对于 SSE 和 WebSockets 都是一个警告，每当应用程序重新获得网络连接时，都需要使用缓存刷新(例如，使用轮询部分中描述的时间戳)。要检测网络连接状态，您可以使用`[navigator.onLine](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/onLine)`属性。

除了在互联网连接断开时刷新缓存之外，每当用户在您的应用程序上启动新会话时(例如，用户登录)，您都需要强制刷新缓存。

### websocket

与服务器发送的事件类似，WebSockets (WS)允许数据从服务器实时传输到客户端。然而，WebSocket 协议允许双向数据传输——也就是说，数据可以通过同一连接从客户端传输到服务器。IE 和 MS Edge 都支持 WebSockets，这使得它成为 SSE 的一个很好的替代品。

然而，有一个问题。WebSockets 使用自己的协议，而不是 HTTP，所以要利用 WebSockets，您需要设置一个服务器来处理 WebSocket 协议。

除此之外，建立一个监听传入消息的 WebSocket 连接相当简单。

```
import { Injectable } from '@angular/core';
import { webSocket, WebSocketSubject } from "rxjs/webSocket";
import { LocalStorage } from '@ngx-pwa/local-storage';

interface Product { 
  id: number, 
  name: string, 
  price: number 
} 

interface Message {
  action: string,
  data: Product
}

@Injectable({
  providedIn: 'root'
})
export class WebSocketService {
  private api = 'ws://localhost:3000/echo';
  private subject: WebSocketSubject<string>;

  constructor(
    private localStorage: LocalStorage
  ) {}

  public connect(): void {
    this.subject = webSocket(this.api);
    this.subject.subscribe(
      message => this.dispatchMessage(message)
    );
  }

  private dispatchMessage(message: string): void {
    let parsedMessage: Message = JSON.parse(message);
    switch(parsedMessage.action) {
      case 'add':
        this.addProductToCache(parsedMessage);
      case 'update':
        this.updateProductInCache(parsedMessage);
      case 'delete':
        this.deleteProductFromCache(parsedMessage);
    }
  }
  ...
}
```

与 SSE 不同，通过 WebSockets 接收的消息没有`type`属性。WebSocket 消息只是一个字符串值，没有额外的元数据。因为有多种可能的操作，我们需要扩展从服务器发送的 JSON 对象，以包含所需的操作(例如，添加、更新或删除)。对此的另一个解决方案是为每种类型的操作打开一个到不同端点的连接。

我们在示例顶部通过接口定义的`Message`类型有这个额外的`action`参数。然后,`dispatchMessage()`函数可以使用它来决定对包含数据的缓存执行什么操作。此时，每个添加、更新和删除函数的逻辑在功能上仍然与 SSE 示例中的相同。

与我们的 SSE 示例类似，每当应用程序上线时，无论是在初次登录时，还是在网络中断后，WebSockets 都需要与信息的初始获取一起使用。

## 结论

在本文中，我们探讨了渐进式 web 应用程序的客户端数据存储位置，以及保持数据新鲜的缓存失效策略。

IndexedDB 是客户端存储的一个很好的解决方案，它可以通过使用第三方库得到增强，这些库为存储和检索提供了更干净的 API。

有多种工具可用于更新客户端缓存，但是(不频繁的)轮询与可以将数据从服务器实时推送到客户端的技术(服务器发送的事件或 WebSockets)相结合，可以确保缓存尽快更新，同时最大限度地减少不必要的网络开销。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。