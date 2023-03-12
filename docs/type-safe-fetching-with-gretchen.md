# 使用 gretchen 进行类型安全获取

> 原文：<https://blog.logrocket.com/type-safe-fetching-with-gretchen/>

几乎每个现实世界的应用程序都依赖于数据获取和某种与外部 API 的异步交互。就像任何编程问题一样，有相当多的方法可以解决这个问题；有些简单，有些复杂。然而，一个共同的主题是，这些解决方案通常缺少一个或多个功能，如插值、重新获取等。

在浏览器环境中，我们提供了简单且相对灵活的`fetch` API。但是，它带来了一个问题:它假设所有不成功(≥4xx)的异常并抛出它们。因此，我们仅限于用`Promise.catch`或`async/await`模式中的`try/catch`来捕捉这些异常。

这样做的问题是，众所周知，这些抛出的异常很难类型化，因为它们可以是任何形状。回想起来，我们知道，如果允许我们处理错误，我们通常会对错误的形式有所了解。`[gretchen](https://github.com/truework/gretchen)`来了。

`gretchen`提供了与`fetch`开箱即用的声音对等，这使得它很容易适应遗留代码库。它提供了对常见请求/响应问题的弹性，比如从 API 返回的错误和不一致的响应。这是通过内置的超时处理、可配置的重试逻辑和错误规范化来处理的。早在 Internet Explorer 11 时，它就提供了良好的浏览器支持。

## 入门指南

### 装置

您可以通过`yarn`或`npm`进行安装

```
npm install gretchen

OR

yarn add gretchen
```

### 使用

使用`fetch`，您将得到类似于:

```
const request = await fetch("/api/dogs/1");
const dog = await request.json();
```

类似地，使用`gretchen`，您将拥有:

```
import { gretch } from "gretchen";
const { data: dog1} = await gretch("/api/dog/1").json();
```

你会注意到这提供了足够的抽象，在不牺牲灵活性的情况下，烘托了`fetch`的易用性。另一个需要注意的重要区别是如何在一条语句中解析出`JSON`。

## 用`gretchen`抓取

`gretchen`真正开始发光，并在错误处理方面与原生`fetch`区分开来。最常见的选择是返回一个类型化的实体，然后您可以对其进行断言。这将给 TypeScript 一个机会来理解我们正在处理什么。

```
try {
  const cats = await getAllCats();
 return  cats
} catch (e) {
  if (error instanceof ErrorType) {
    // handle Error
  }
}
```

这样做的问题是，您必须手动检查它是否满足条件。诚然，您可以编写一个中间件来执行这种检查，但是这是一个不必要的额外步骤，并且增加了样板文件。理想的解决方案是根本不需要执行这种检查，用更少的代码优雅地完成它。

另一种选择是不抛出任何异常。默认情况下,`fetch`以这种方式工作。因此，我们能够根据是否获得所需的数据来配置我们的响应——也就是说，对于成功和错误响应，我们可以抽象出断言并优雅地处理响应。这类似于`RESULT`型中的铁锈。

```
enum Result<T, E> {
   Ok(T), // success data
   Err(E), // error
}
```

然后，我们可以像这样处理响应:

```
if (response.status < 300) {return Result.Ok(response);
} else {
return Result.Err(response);
}
```

虽然这种模式可能很吸引人，但它引入了一层复杂性，对于新手来说可能不太直观。试图通过在没有异常的情况下提供更清晰的抽象来简化这一点。

`gretchen`没有返回一个带有检查被区分联合类型的方法的对象，而是采用了一种类似于 Go 中错误处理的模式。联合的两半作为单独的实体返回:

```
const { error, data } = await getEndpoint();

if (error) {
  // handle error
} else {
  // handle data
}
```

### 获取请求

```
const response = await fetch("/user/12345");

const { ok, status } = response;
```

然后，如果错误不是`ok`，您可以处理响应，或者处理成功响应。

### 非获取请求

非获取请求与`GET`请求非常相似，但是就像`fetch`一样，您需要传递方法选项来获取，就像这样:

```
const { status, error, data: user } = await gretch("/note/create", {
  method: "POST",
  json: {
    title: "Take over",
 text: "We are the world"
  }
}).json();

if (error) {
  // handle error
} else {
  // handle user
}
```

在内部，`gretchen`已经解决了你在`fetch`中遇到的大部分样板文件，比如等待响应，然后转换成 JSON，比如`arrayBuffer`、`blob`、`formData`、`json`和`text`。

## 其他细节

### 自动重试一些失败的请求

默认情况下，`gretchen`将重试两次返回 408、413 或 429 的 GET 请求。网络问题或服务器负载可能会导致失败。这为您提供了重试这些请求的灵活性，当然，您可以覆盖默认的重试次数，如下所示:

```
await gretch("/notes", {
  method: "POST",
  retry: {
    attempts: 9, // number of retry
    methods: ["POST"]
  }, 
  json: {
        title: "Take over",
       text: "We are the world"
  }
}).json();
```

### 处理请求超时

这类似于重试请求。默认情况下，请求会在 10 秒后超时。您可以选择配置超时时间。

## `gretchen`用打字稿

您可以指定错误和响应的类型。这对于`gretchen`允许的通用接口来说很简单。

`gretchen`源代码中的输入看起来像这样:

```
gretch<T = DefaultGretchResponse, A = DefaultGretchError>
```

您会注意到包含了默认的响应和错误类型，因此，您可以以相同的方式传递您的错误和数据响应类型。例如:

```
interface Note {
id: number
author: string
title: string
text: string
}
interface CustomError {
  message: string
code: number
};

const { error, data } = await gretch<Note, CustomeError>(
  "/note/1"
).json();
```

将这些结合在一起，您应该有一个健壮的响应-错误处理，如下所示:

```
interface Note {
id: number
author: string
title: string
text: string
}
interface CustomError {
  message: string
code: number
};

const { error, data } = await gretch<Note, CustomeError>(
  "/note/1"
).json();

error ? handleError() : handleSuccess()
```

## 最后

但是为什么`gretchen`，我听到你问？我们已经有了做类似工作的类似抽象。这并不完全正确。`gretchen`更进一步，通过对已经流行的`fetch`语法提供一个非常微妙的抽象，允许类型安全抓取，你已经知道并喜欢这个语法。

此外，`gretchen`使得优雅地输入预期的数据和任何可能发生的错误变得容易。乍一看，这似乎并不重要，但是随着应用程序变得越来越复杂，安全地输入 API 响应的能力对于向使用它们的任何客户端提供清晰性大有帮助。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.