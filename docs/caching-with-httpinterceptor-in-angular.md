# Angular 中的 HttpInterceptor 缓存

> 原文：<https://blog.logrocket.com/caching-with-httpinterceptor-in-angular/>

[HttpInterceptor](https://angular.io/api/common/http/HttpInterceptor) 是 Angular 中最强大的功能之一。你可以用它来模拟一个后端，这样你就可以轻松地测试 Angular 应用程序，而不需要设置服务器。

在这个 HttpInterceptor 教程中，我们将演示如何使用 HTTP interceptor 来缓存 [HTTP 请求](https://blog.logrocket.com/5-ways-to-make-http-requests-in-node-js/)。

我们将讨论以下内容:

## 为什么要缓存 HTTP 请求？

缓存 HTTP 请求有助于优化您的应用程序。想象一下，每次发出请求时都从服务器请求一段数据，即使数据从未随时间发生变化。这将影响您的应用程序的性能，因为在服务器中处理数据并在数据从未因之前的请求而改变时发送数据需要时间。

为了消除当服务器中的数据没有发生变化时处理数据的时间延迟，我们需要检查服务器中的数据是否发生了变化。如果数据发生了变化，我们会处理来自服务器的新数据。如果没有，我们跳过服务器中的处理，发送之前的数据。这叫做[缓存](https://en.wikipedia.org/wiki/Cache_(computing))。

在 HTTP 中，并不是所有的请求都被缓存。`POST`、`PUT`和`DELETE`请求没有被缓存，因为它们改变了服务器中的数据。

`POST`和`PUT`向服务器添加数据，而`DELETE`从服务器移除数据。所以我们需要在每次请求它们的时候处理它们，而不是缓存它们。

请求可以被缓存。他们只是从服务器获取数据而不改变它们。如果在下一个`GET`请求之前没有`POST`、`PUT`或`DELETE`请求发生，则来自上一个`GET`请求的数据不会改变。我们只是简单地返回以前的数据或响应，而不访问服务器。

现代浏览器有一个内置的机制来缓存我们的 HTTP 请求。我们将向您展示如何在 Angular 中做到这一点。

## 为什么要使用 HttpInterceptor？

`HttpInterceptors`都是有棱角的特殊服务。在向服务器发出实际请求之前，HTTP 请求会在链中通过它们传递。

简单来说，`HttpInterceptors`拦截并处理 HTTP 请求。通常，`HttpInterceptors`调用`next.handle(transformedReq)`来转换传出的请求，然后将它们传递给链中的下一个拦截器。在极少数情况下，拦截器自己处理请求，而不是委托给链的其余部分。

## 在 Angular 中使用 HttpInterceptor

我们将创建我们的`HttpInterceptor`,以便每当我们发出一个`GET`请求时，该请求将通过链中的拦截器。我们的拦截器将检查请求以确定它是否已经被缓存。如果是，它将返回缓存的响应。如果没有，它将把请求传递给链的其余部分，最终发出一个实际的服务器请求。拦截器将在收到响应时监视响应，并缓存它，以便任何其他请求都将返回缓存的响应。

我们还将提供一种重置缓存的方法。这是非常理想的，因为如果 GET API 处理的数据在最后一次请求后被`POST`、`PUT`、`DELETE`修改了，我们仍然会返回缓存的数据。我们将处理陈旧的数据，我们的应用程序将显示错误的结果。

### 设置`HttpInterceptor`

所有 HttpInterceptors 都实现了`HttpInterceptor`接口:

```
export interface HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>;
}

```

用请求`req: HttpRequest<any>`和下一个请求`HttpHandler`调用 intercept 方法。

`HttpHandler` s 负责调用链中下一个 HttpInterceptor 中的`intercept`方法，并传递将调用下一个拦截器的下一个`HttpHandler`。

为了设置我们的拦截器，`CacheInterceptor`:

```
@Injectable()
class CacheInterceptor implements HttpInterceptor {
  private cache: Map<HttpRequest, HttpResponse> = new Map()
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>{
    if(req.method !== "GET") {
        return next.handle(req)
    }
    const cachedResponse: HttpResponse = this.cache.get(req)
    if(cachedResponse) {
        return of(cachedResponse.clone())
    }else {
        return next.handle(req).pipe(
            do(stateEvent => {
                if(stateEvent instanceof HttpResponse) {
                    this.cache.set(req, stateEvent.clone())
                }
            })
        ).share()
    }
  }    
}

```

因为请求方法不是 GET 请求，所以我们沿着链传递它——没有缓存。

如果是 GET，我们使用将`req`作为键传递的`Map#get`方法从`cache` map 实例获取缓存的响应。我们将请求`HttpRequest`存储为一个键，将响应`HttpResponse`存储为 map 实例`cache`中的值。

地图的结构如下:

| **键** | **值** |
| `HttpRequest {url: "/api/dogs" ,…}` | T2`HttpResponse {data: ["alsatians"],…}` |
| T2`HttpRequest {url: "/api/dogs/name='bingo'" ,…}` | T2`HttpResponse {data: [{name:"bingo",…}],…}` |
| T2`HttpRequest {url: "/api/cats" ,…}` | T2`HttpResponse {data: [“serval”],…}` |

一个键是一个 HttpRequest 实例，它对应的值是一个`HttpResponse`实例。我们使用它的`get`和`set`方法来检索和存储`HttpRequests`和`HttpResponses`。所以当我们在`cache`中调用`get`时，我们知道我们会得到一个`HttpResponse`。

响应存储在`cachedResponse`中。我们检查以确保它不为空(即，我们得到一个响应)。如果是，我们克隆响应并返回它。

如果我们没有从缓存中得到响应，我们知道请求以前没有被缓存过，所以我们让它通过并监听响应。如果我们看到一个，我们使用`Map#set`方法缓存它。`req`成为键，响应成为值。

缓存时，我们需要注意陈旧的数据。我们需要知道数据何时发生了变化，并向服务器请求更新缓存。

我们可以使用不同的方法来实现这一点。我们可以使用`If-Modfied-Since`头，我们可以在`HttpRequest`头上设置到期日期，或者我们可以在头上设置一个标志来检测何时发出完整的服务器请求。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们采用第三种选择。

**注意**:缓存的休息方式有很多种；上面列出的选项只是我想到的几个。

这里的技巧是当用户发出请求时，给请求添加一个参数，这样我们可以测试我们的`CacheInterceptor`中的头，并知道何时将它传递给服务器。

```
public fetchDogs(reset: boolean = false) {
    return this.httpClient.get("api/dogs", new HttpHeaders({reset}))
}

```

方法`fetchDogs`有一个`reset`布尔参数。如果`reset`参数被设置为`true`，那么`CacheInterceptor`必须发出一个服务器请求。在发出请求之前，它在报头中设置`reset`参数。标题包含复位，如下所示:

```
reset | true
or
rest | false

```

`CacheInterceptor`必须检查报头中的复位参数，以确定何时停止缓存。让我们将它添加到我们的`CacheInterceptor`实现中:

```
@Injectable()
class CacheInterceptor implements HttpInterceptor {
  private cache: Map<HttpRequest, HttpResponse> = new Map()
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>{
    if(req.method !== "GET") {
        return next.handle(req)
    }
    if(req.headers.get("reset")) {
        this.cache.delete(req)
    }
    const cachedResponse: HttpResponse = this.cache.get(req)
    if(cachedResponse) {
        return of(cachedResponse.clone())
    }else {
        return next.handle(req).pipe(
            do(stateEvent => {
                if(stateEvent instanceof HttpResponse) {
                    this.cache.set(req, stateEvent.clone())
                }
            })
        ).share()
    }
  }    
}

```

我们在请求头中请求了`reset`参数。如果`reset`参数是`true`，我们使用`Map#delete`方法从`cache`中删除`HttpRequest/HttpResponse`缓存。然后，在删除缓存的情况下，发出一个服务器请求。

最后，我们需要在`HTTP_INTERCEPTROS`数组令牌中注册我们的`CacheInterceptor`。没有它，我们的拦截器就不会在拦截器链中，我们也不能缓存请求。

```
@NgModule({
    ...
    providers: {
        provide: HTTP_INTERCEPTORS,
        useClass: CacheInterceptor,
        multi: true
    }
})
...

```

这样，我们的`CacheInterceptor`将挑选 Angular 应用程序中的所有 HTTP 请求。

## 为什么你应该采用角

拦截器非常有用，主要是因为它们大大减少了实现 HTTP 缓存所需的大量代码。

为此，我强烈建议您采用 Angular。React、Vue.js、Svelte 都缺乏这个能力。使用这些框架来实现 HTTP 缓存和拦截将是一件非常头痛的事情。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。