# 用 Nock 对 Node.js 应用程序进行 API 模拟测试

> 原文：<https://blog.logrocket.com/api-mock-testing-with-nock-node-js/>

当测试应用程序的单个单元时，我们的测试必须是独立的。这有助于提高我们测试的速度，并减少运行测试所需的依赖项的数量。

然而，应用程序的某些部分依赖于来自外部系统的数据来运行，例如数据库、文件系统或外部 API。为了测试这些部件，我们使用了一个`mock`对象。

### 什么是`mock`？

一个`mock`是一个方法或对象，它以受控的方式模仿外部系统的行为。模拟是以模拟外部系统行为的方式创建的——它们以与外部系统相同的方式被调用和响应。流行的模仿库的例子包括:

在本指南中，我们将学习如何使用 Nock 在一个简单的 Node.js 应用程序中执行 API 模拟测试。

### Nock 是什么？

Nock 是一个用于 [Node.js](https://blog.logrocket.com/tag/node) 的 HTTP 服务器模拟和期望库。Nock 的工作方式是覆盖`http.request`和`http.ClientRequest`函数，拦截对指定 URL 的所有请求，并返回模拟真实 URL 将返回的数据的指定响应。

使用 Nock 作为模拟服务器，我们可以很容易地在模块上执行测试，这些模块[像 URL 一样向外部对象](https://blog.logrocket.com/how-to-test-code-that-depends-on-external-apis-in-node-js/)发出 HTTP 请求，而不实际向 URL 发出任何请求。这使得我们的测试过程更快、更有效。

## Nock 安装和配置

使用以下命令安装 Nock。

```
$ npm install --save-dev nock
```

一个简单的 Nock 模仿对象看起来像这样:

```
import nock from "nock";
const scope = nock("https://api.weatherapi.com/v1")
     .get(``)
     .reply(200, {
       data: {
         id: 1,
         title: "The weather is nice",
         completed: true
       }
     });
```

这个模仿对象拦截对指定 URL 的`GET`请求——我们以`[https://api.weatherapi.com/v1](https://api.weatherapi.com/v1)`为例——并回复状态`200`和一组值。

## 用 Nock 模拟测试

假设我们想测试一个向 URL 发出 HTTP 请求并返回 URL 提供的数据的函数。

```
const getData = async () => {
   const res = await axios.get('https://api.example.com');

   const data = res.data;
   return data;
}

```

对`getData()`函数的简单 Jest 测试如下所示:

```
describe('expectedData', () => {

   it("checks if API returns expected data", async () => {
       nock('https://api.example.com')
           .get('/')
           .reply(200, {
               data: {
                   id: 1,
                   title: "The weather is nice",
                   completed: true
               }
           });
       const results = await getData();
       expect(results.data.title).toEqual("The weather is nice");
   });
});
```

每当测试运行时，它用 Nock 创建模拟服务器，并调用`getData()`函数。然后，模拟服务器拦截由`getData()`函数向`'https://api.example.com'` URL 发出的 HTTP 请求，并向其发送指定的回复。

然后，测试框架检查由`getData()`函数返回的数据是否与模拟服务器提供给它的数据相匹配。我们的测试是完全隔离的，因为我们没有对示例 URL 发出真正的 HTTP 请求，而是用 Nock 拦截请求并返回指定的数据集。

## 在 Nock 中指定请求

Nock 允许我们模拟对所有 HTTP 动词(`GET`、`PUT`、`POST`、`PATCH`、`DELETE`等)的请求。)并且还支持在 HTTP 或 HTTPS 上进行的调用。

通过 HTTP 发出的一个简单的`POST`请求如下所示:

```
scope = nock('http://api.example.com')
                .post('/users', {
                  username: 'user',
                  email: '[email protected]'
                })
                .reply(201);

```

在上面的代码片段中，我们创建了一个`mock`对象，它拦截对指定主机名(`[http://api.example.com/](http://api.example.com/)`)的`POST`请求，请求体为`{ username: 'user', email: '[[email protected]](/cdn-cgi/l/email-protection)'}`，并返回一个`201`状态代码。

Nock 还使得向我们的请求添加查询参数变得非常容易:

```
scope = nock('http://api.example.com')
                .get('/users')
                .query({username: 'sugar', email: '[email protected]'})
                .reply(200);

```

Nock 将拦截一个带有指定查询的 HTTP 请求，并返回一个状态代码`200`。

## 指定请求和响应头

Nock 使得附加请求和响应头变得很容易。如果您想测试 HTTP 请求或响应中是否存在特定的头，这很有用。

可以像这样添加简单的请求头:

```
const scope = nock('http://api.example.com', {
  reqheaders: {
    authorization: 'Basic Auth',
    'Content-Type': 'application/json'
  },
})
  .get('')
  .reply(200)
```

在上面的代码片段中，我们向一个简单的返回`200`状态代码的`GET`请求添加了`authorization`和`Content-Type`头。Nock 将拦截对添加了请求头的指定 URL 的 HTTP 调用，并返回`200`状态代码。

您还可以在回复中添加预期的邮件头。

```
const scope = nock('http://api.example.com')
  .get('')
  .reply(200, { license: 'MIT' })
```

这个代码示例在拦截到对`[http://api.example.com](http://api.example.com)`的`GET`请求时，返回一个状态代码`200`和一个报头`{ license: 'MIT' }` 。

## 指定自定义回复

Nock 为创建自定义回复提供支持。您可以像这样指定回复正文:

```
scope = nock('http://api.example.com')
                .get('/users')
                .reply(200, {
              data:{
                    id: 1,
                    username: 'user',
                    email: '[email protected]'
                  }
                });

```

这个 Nock 对象在拦截到对`[http://api.example.com/users](http://api.example.com/users)`的`GET`请求时，返回一个状态码`200`和一个主体(`data:{id: 1, username: 'user', email: '[[email protected]](/cdn-cgi/l/email-protection)'}`)。

您可以从模拟服务器返回错误回复。

```
scope = nock('http://api.example.com')
                .get('/users')
                .replyWithError({
              message: 'The server is down, ERROR'
                });

```

## 请求链接

您还会遇到这样的场景，您需要模拟依赖于先前请求的响应的 HTTP 请求。Nock 使得向模拟服务器添加链接行为变得非常容易。

```
const scope = nock('http://api.example.com')
  .delete('/users/1')
  .reply(200)
  .get('/users/1')
  .reply(404)
  .post(‘users’, , {
    username: 'user',
    email: '[email protected]',
  })
  .reply(200, ‘user created’)
  .get('/users')
  .reply(200, {
    _id: '1',
    username: 'user',
    email: '[email protected]',
  })
```

这个模拟对象包含四个不同的请求；只有当链上的请求完成时，链下的请求才会开始。

## 结论

在本文中，我们学习了如何使用 Nock 在 Node.js 应用程序中执行 API 模拟测试。Nock 是一个令人惊奇的库，它允许你测试我们的模块和函数，这些模块和函数独立地发出 HTTP 请求。

使用 Nock mocking 服务器也有助于提高我们的工程团队的效率，从而改进我们的软件开发过程。访问 Nock GitHub 页面,了解更多使用 Nock 进行更好的 API 模拟测试的惊人方法。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.