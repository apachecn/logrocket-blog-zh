# OkHttp 完全指南

> 原文：<https://blog.logrocket.com/a-complete-guide-to-okhttp/>

## OkHttp 是什么？

OkHttp 是来自 [Square](https://squareup.com/us/en) 的 Http 客户端，用于 Java 和 Android 应用。它旨在更快地加载资源并节省带宽。 [OkHttp](https://square.github.io/okhttp/) 在[开源项目](https://square.github.io/okhttp/works_with_okhttp/)中被广泛使用，并且是像[翻新](https://square.github.io/retrofit/)、[毕加索](https://square.github.io/picasso/)和许多其他库的骨干。

以下是使用 OkHttp 的主要优势:

*   HTTP/2 支持(有效的套接字使用)
*   连接池(在没有 HTTP/2 的情况下减少请求延迟)
*   GZIP 压缩(缩小下载大小)
*   响应缓存(避免重新获取相同的数据)
*   从常见连接问题中静默恢复
*   替代 IP 地址检测(在 IPv4 和 IPv6 环境中)
*   支持现代 TLS 功能(TLS 1.3、ALPN、证书锁定)
*   同步和异步呼叫支持

在本指南中，我们将通过为 Android 构建一个虚构的待办事项列表应用程序来介绍 OkHttp 的基础知识。

首先，让我们为我们的待办事项应用程序定义一些功能需求。我们的用户将希望能够从待办事项服务器上看到他们保存的待办事项，在服务器上保存一个新的待办事项，并安全和单独地访问他们自己的待办事项。

作为开发人员，我们希望能够轻松地调试我们的应用程序的网络通信，并减少服务器端的负载。

## 先决条件

稳定的 OkHttp 4.x 在 Android 5.0+ (API 级别 21+)和 Java 8+上工作。如果要求较低的 Android 和 Java 版本支持，还是可以依靠 OkHttp 3.12.x 分支，有一些[的考虑](https://square.github.io/okhttp/#requirements)。

在导入 OkHttp 时，还会带来两个依赖项: [Okio](https://square.github.io/okio/) ，一个高性能的 I/O 库， [Kotlin 标准库](https://kotlinlang.org/api/latest/jvm/stdlib/)。您不必单独导入这些内容。

要在您的 Android 项目中使用 OkHttp，您需要将其导入到应用程序级 Gradle 文件中:

```
implementation("com.squareup.okhttp3:okhttp:4.9.1")

```

不要忘记，在 Android 上，如果你想访问网络资源，你需要请求应用程序的`AndroidManifest.xml`文件中的`INTERNET`权限:

```
<uses-permission android:name="android.permission.INTERNET"/>

```

## 设置 OkHttp

为了让我们的用户从服务器上看到他们保存的所有待办事项，我们需要同步和异步 GET 请求，以及查询参数。

## 获取请求

为了从服务器获取我们的待办事项列表，我们需要执行一个 GET HTTP 请求。OkHttp 通过`Request.Builder`提供了一个很好的 API 来构建请求。

### 同步获取

发出 GET 请求就像这样简单:

```
OkHttpClient client = new OkHttpClient();

Request getRequest = new Request.Builder()
        .url("https://mytodoserver.com/todolist")
        .build();

try {
    Response response = client.newCall(getRequest).execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

```

如您所见，这是用 OkHttp 执行请求的一种**同步**方式。(你应该在一个非 UI 线程上运行，否则，你的应用会有性能问题，Android 会抛出一个[错误](https://developer.android.com/reference/android/os/NetworkOnMainThreadException)。)

### 异步获取

这个请求的**异步**版本在获取响应或发生错误时为您提供回调。

```
OkHttpClient client = new OkHttpClient();

Request getRequest = new Request.Builder()
        .url("https://mytodoserver.com/todolist")
        .build();

client.newCall(getRequest).enqueue(new Callback() {
    @Override
    public void onFailure(@NotNull Call call, @NotNull IOException e) {
        e.printStackTrace();
    }

    @Override
    public void onResponse(@NotNull Call call, @NotNull Response response) throws IOException {
        System.out.println(response.body().string());
    }
});

```

**注意** : *从现在开始，我将只展示调用的同步版本，以避免使用大量的样板代码。只要有可能使代码在非 Android 环境中可重用，我也会尽量使用标准的 Java APIs。*

### OkHttp 中的查询参数

您可以向您的请求传递查询参数，例如在服务器端实现对已完成或未完成的待办事项的过滤。

```
OkHttpClient client = new OkHttpClient();

HttpUrl.Builder queryUrlBuilder = HttpUrl.get("https://mytodoserver.com/todolist").newBuilder();
queryUrlBuilder.addQueryParameter("filter", "done");

Request request = new Request.Builder()
        .url(queryUrlBuilder.build())
        .build();

try {
    Response response = client.newCall(request).execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

```

`HttpUrl.Builder`将使用查询参数`[https://mytodoserver.com/todolist?filter=done](https://mytodoserver.com/todolist?filter=done)`生成正确的 URL。

你可能会问，“为什么不直接使用手工创建的 URL 呢？”你可以。但是一旦您的 URL 构建逻辑变得更加复杂(更多的查询参数)，那么这个类就派上用场了。库的开发人员有[更多的理由使用 HttpUrl](https://square.github.io/okhttp/4.x/okhttp/okhttp3/-http-url/#why-another-url-model) 。

## 发布请求

现在我们已经从服务器上下载了所有的待办事项。但是如何创建新的待办事项或将一个待办事项标记为已完成呢？一个简单的 POST 请求。

### 简单发布请求

让我们向端点发送 POST 请求:

```
OkHttpClient client = new OkHttpClient();

RequestBody requestBody = new FormBody.Builder()
        .add("new", "This is my new TODO")
        .build();

Request postRequest = new Request.Builder()
        .url("https://mytodoserver.com/new")
        .post(requestBody)
        .build();

try {
    Response response = client.newCall(postRequest).execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

```

如您所见，POST 请求的主体是一个`application/x-www-form-urlencoded`键值对数据。但是我们可以发送任何我们想要的类型。下面是一个 JSON 主体的例子:

```
OkHttpClient client = new OkHttpClient();

JSONObject jsonObject = new JSONObject();
jsonObject.put("todo_id", 123);
jsonObject.put("status", "done");

RequestBody requestJsonBody = RequestBody.create(
        jsonObject.toString(),
        MediaType.parse("application/json")
);

Request postRequest = new Request.Builder()
        .url("https://mytodoserver.com/modify")
        .post(requestJsonBody)
        .build();

try {
    Response response = client.newCall(postRequest).execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

```

### 文件上传

也有可能我们想在新的待办事项中附加一个文件(如图片):

```
OkHttpClient client = new OkHttpClient();

RequestBody requestBody = new MultipartBody.Builder()
        .addFormDataPart("new", "This is my new TODO")
        .addFormDataPart("image", "attachment.png",
                RequestBody.create(new File("path/of/attachment.png"), MediaType.parse("image/png"))
        )
        .setType(MultipartBody.FORM)
        .build();

Request postRequest = new Request.Builder()
        .url("https://mytodoserver.com/new")
        .post(requestBody)
        .build();

try {
    Response response = client.newCall(postRequest).execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

```

与前面类似，我们执行一个多部分 HTTP 请求，在这里我们可以附加所需的文件。

### 取消请求

保存待办事项时，可能会意外选择错误的附件，所以不要等到上传完成，要确保请求可以随时取消，并在以后使用正确的值重新启动。

```
// same request as before
Request postRequest = new Request.Builder()
        .url("https://mytodoserver.com/new")
        .post(requestBody)
        .build();

Call cancelableCall = client.newCall(postRequest);

try {
    Response response = cancelableCall.execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

// ... few seconds later from an other thread
cancelableCall.cancel();

```

现在，我们已经掌握了应用程序基本功能所需的所有知识。我们可以检查我们的待办事项列表，我们可以添加新的，我们可以改变它们的状态。

让我们看看应用程序的安全方面。

## OkHttp 中的安全性和授权

### 在请求上设置 HTTP 头

我们的后端实现了一个基于用户名/密码的基本认证，以避免看到和修改彼此的待办事项。

现在访问我们的数据需要在我们的请求上设置一个`Authorization`头。否则，请求可能会因一个`401 Unauthorized`响应而失败。

```
OkHttpClient client = new OkHttpClient();

Request request = new Request.Builder()
        .url("https://mytodoserver.com/todolist")
        .addHeader("Authorization", Credentials.basic("username", "password"))
        .build();

try {
    Response response = client.newCall(request).execute();
    System.out.println(response.body().string());
} catch (IOException e) {
    e.printStackTrace();
}

```

`Request.Builder`上的`addHeader()`方法将让我们指定尽可能多的自定义[标题](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)。

现在，只有有人知道我们的用户名和密码，我们的敏感数据才能被访问。但是，如果有人在网络上监听，并试图通过中间人攻击和伪造证书来劫持我们的请求，该怎么办呢？

OkHttp 通过使用 certificate pinner，为您提供了一种仅信任您自己的证书的简单方法。

### 在 OkHttp 中设置证书 pinner

```
OkHttpClient.Builder clientBuilder = new OkHttpClient.Builder();
clientBuilder.certificatePinner(
        new CertificatePinner.Builder().add(
                "mytodoserver.com","sha256/public_key_hash_of_my_certification"
        ).build()
);

OkHttpClient client = clientBuilder.build();

```

在这里，我们使用`OkHttpClient.Builder`来构建一个定制的 OkHttp 客户端(稍后会详细介绍)。然后，使用`CertificatePinner`，我们选择哪些特定域的哪些证书是可信的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

有关[证书锁定](https://square.github.io/okhttp/4.x/okhttp/okhttp3/-certificate-pinner/)和一般安全性的更多信息，请访问[相关 OkHttp 文档页面](https://square.github.io/okhttp/https/)。

## 用 OkHttp 调试

如果在提出请求时出现问题，我们必须更深入地探究问题发生的原因。OkHttp 有自己的[内部 API 来启用调试日志](https://square.github.io/okhttp/debug_logging/)，这很有帮助。但是我们也可以利用 OkHttp 的[拦截器](https://square.github.io/okhttp/interceptors/) API 来让我们的生活变得更容易。

### 拦截机

拦截器可以监控、重写和重试调用。我们可以使用它们在请求发出之前对其进行修改，在响应到达我们的逻辑之前对其进行预处理，或者简单地打印出关于请求的一些细节。

OkHttp 有自己的预制日志拦截器，我们可以通过 Gradle 导入:

```
implementation("com.squareup.okhttp3:logging-interceptor:4.9.1")

```

要使用它:

```
HttpLoggingInterceptor loggingInterceptor = new HttpLoggingInterceptor();
loggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BASIC);

OkHttpClient client = new OkHttpClient.Builder()
        .addInterceptor(loggingInterceptor)
        .build();

```

或者我们可以实现我们自己的定制拦截器:

```
static class BasicLoggingInterceptor implements Interceptor {
    @NotNull
    @Override
    public Response intercept(Interceptor.Chain chain) throws IOException {
        Request request = chain.request();

        System.out.println(String.format("Sending request %s on %s%n%s",
                request.url(), chain.connection(), request.headers()));

        Response response = chain.proceed(request);

        System.out.println(String.format("Received response for %s %n%s",
                response.request().url(), response.headers()));

        return response;
    }
}

// ...
// usage later on
OkHttpClient client = new OkHttpClient.Builder()
        .addInterceptor(new BasicLoggingInterceptor())
        .build();

```

我们也可以根据自己的需要在应用程序和网络级别上声明我们的拦截器。你可以在这里阅读更多关于这个[的内容。](https://square.github.io/okhttp/interceptors/#choosing-between-application-and-network-interceptors)

### 代理人

有时操纵我们后端 API 的响应是有用的。我们可以通过操作服务器端代码来实现这一点，但通过代理服务器更有效。

我们可以在设备本身上使用系统范围的代理配置，或者指示我们的 OkHttp 客户端在内部使用一个。

```
Proxy proxyServerOnLocalNetwork = new Proxy(
        Proxy.Type.HTTP,
        new InetSocketAddress("192.168.1.100", 8080) // the local proxy
);

OkHttpClient client = new OkHttpClient.Builder()
        .proxy(proxyServerOnLocalNetwork)
        .build();

```

## OkHttp 中的缓存

在我们调试完应用程序之后，您可能已经注意到我们完成了许多不必要的请求，这些请求给我们的服务器带来了额外的负载。如果后端没有变化，就没有必要再次获取待办事项列表。

OkHttp 中有一个[默认缓存实现](https://square.github.io/okhttp/caching/)，我们只需要指定缓存位置及其大小，如下所示:

```
OkHttpClient client = new OkHttpClient.Builder()
        .cache(new Cache(new File("/local/cacheDirectory"), 10 * 1024 * 1024)) //10 MB
        .build();

```

但是如果你想定制行为的话，你可以尽情发挥。

如果您有自定义的缓存逻辑，您也可以实现自己的缓存方式。例如，您可以首先对您的服务器执行一个`HEAD`请求，然后检查缓存指示头，如果有变化，对同一个 URL 执行一个`GET`请求来获取内容。

## OkHttp 配置

我们已经讲述了`OkHttpClient.Builder`的一些用法。如果我们想改变默认的 OkHttp 客户端行为，这个类是有用的。

有一些参数值得一提:

```
OkHttpClient client = new OkHttpClient.Builder()
        .cache(cache) // configure cache, see above
        .proxy(proxy) // configure proxy, see above
        .certificatePinner(certificatePinner) // certificate pinning, see above
        .addInterceptor(interceptor) // app level interceptor, see above
        .addNetworkInterceptor(interceptor) // network level interceptor, see above
        .authenticator(authenticator) // authenticator for requests (it supports similar use-cases as "Authorization header" earlier
        .callTimeout(10000) // default timeout for complete calls
        .readTimeout(10000) // default read timeout for new connections
        .writeTimeout(10000) // default write timeout for new connections
        .dns(dns) // DNS service used to lookup IP addresses for hostnames
        .followRedirects(true) // follow requests redirects
        .followSslRedirects(true) // follow HTTP tp HTTPS redirects
        .connectionPool(connectionPool) // connection pool used to recycle HTTP and HTTPS connections
        .retryOnConnectionFailure(true) // retry or not when a connectivity problem is encountered
        .cookieJar(cookieJar) // cookie manager
        .dispatcher(dispatcher) // dispatcher used to set policy and execute asynchronous requests
        .build();

```

有关完整列表，请访问[文档](https://square.github.io/okhttp/4.x/okhttp/okhttp3/-ok-http-client/-builder/)。

## WebSocket

考虑一个协作待办事项列表？或者在添加新的待办事项时通知用户？就一个待办事项进行实时聊天怎么样？OkHttp 也在这里为您提供服务。

如果您已经完成了 WebSocket 服务器端的实现，那么您可以连接到该端点，并从 OkHttp 客户端启动和运行实时消息传递。

```
OkHttpClient client = new OkHttpClient();

String socketServerUrl = "ws://mytodoserver.com/realtime";
Request request = new Request.Builder().url(socketServerUrl).build();

// connecting to a socket and receiving messages
client.newWebSocket(request, new WebSocketListener() {
    @Override
    public void onClosed(@NotNull WebSocket webSocket, int code, @NotNull String reason) {
        super.onClosed(webSocket, code, reason);
        //TODO: implement your own event handling
    }

    @Override
    public void onClosing(@NotNull WebSocket webSocket, int code, @NotNull String reason) {
        super.onClosing(webSocket, code, reason);
        //TODO: implement your own event handling
    }

    @Override
    public void onFailure(@NotNull WebSocket webSocket, @NotNull Throwable t, @Nullable Response response) {
        super.onFailure(webSocket, t, response);
        //TODO: implement your own event handling
    }

    @Override
    public void onMessage(@NotNull WebSocket webSocket, @NotNull String text) {
        super.onMessage(webSocket, text);
        //TODO: implement your own event handling for incoming messages
    }

    @Override
    public void onMessage(@NotNull WebSocket webSocket, @NotNull ByteString bytes) {
        super.onMessage(webSocket, bytes);
        //TODO: implement your own event handling for incoming messages
    }

    @Override
    public void onOpen(@NotNull WebSocket webSocket, @NotNull Response response) {
        super.onOpen(webSocket, response);
        //TODO: implement your own event handling
    }
});

// sending message
webSocket.send("new_todo_added");

```

## 测试

我们不能忘记测试。OkHttp 提供了自己的 [MockWebServer](https://github.com/square/okhttp/tree/master/mockwebserver) 来帮助测试 Http 和 HTTPS 网络调用。它让我们指定对哪个请求返回哪个响应，并验证请求的每个部分。

首先，我们需要通过 Gradle 导入它:

```
testImplementation("com.squareup.okhttp3:mockwebserver:4.9.1")

```

以下是一些重要的 API:

*   `MockWebServer.start()`:启动本地主机上的模拟 web 服务器
*   `MockWebServer.enqueue(mockResponse)`:队列 a `MockResponse`。这是一个 FIFO 队列，确保请求将按照排队的顺序接收响应
*   `MockResponse`:可脚本化的 OkHttp 响应
*   `RecordRequest`:由`MockWebServer`接收的 HTTP 请求
*   `MockWebServer.takeRequest()`:将下一个到达的请求带到`MockWebServer`

一旦我们理解了基础知识，我们就可以编写我们的第一个测试了。现在，对于一个基本的 GET 请求:

```
public class MockWebServerTest {
    final MockWebServer server = new MockWebServer();
    final OkHttpClient client = new OkHttpClient();

    @Test
    public void getRequest_Test() throws Exception {
        final String jsonBody = "{'todo_id': '1'}";
        // configure a MockResponse for the first request
        server.enqueue(
                new MockResponse()
                        .setBody(jsonBody)
                        .addHeader("Content-Type", "application/json")
        );

        // start the MockWebServer
        server.start();

        // create a request targeting the MockWebServer
        Request request = new Request.Builder()
                .url(server.url("/"))
                .header("User-Agent", "MockWebServerTest")
                .build();

        // make the request with OkHttp
        Call call = client.newCall(request);
        Response response = call.execute();

        // verify response
        assertEquals(200, response.code());
        assertTrue(response.isSuccessful());
        assertEquals("application/json", response.header("Content-Type"));
        assertEquals(jsonBody, response.body().string());

        // verify the incoming request on the server-side
        RecordedRequest recordedRequest = server.takeRequest();
        assertEquals("GET", recordedRequest.getMethod());
        assertEquals("MockWebServerTest", recordedRequest.getHeader("User-Agent"));
        assertEquals(server.url("/"), recordedRequest.getRequestUrl());
    }
}

```

## 结论

简而言之，OkHttp 是一个功能强大的库，它提供了许多额外的好处，包括 HTTP/2 支持、连接问题的恢复机制、缓存和现代 TLS 支持。

如果您曾经试图通过默认的 Android 和 Java 网络 API 从头实现这些功能，您就会知道这是多么的工作量和痛苦(以及您忘记了涵盖多少边缘情况)。幸运的是，用 OkHttp 在应用程序中实现联网使这变得很容易。

更多详情请访问[项目页面](https://square.github.io/okhttp/)和 [GitHub](https://github.com/square/okhttp) 。您可以找到一些有用的扩展、实现示例和测试示例。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)