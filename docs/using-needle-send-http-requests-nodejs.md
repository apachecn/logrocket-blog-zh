# 在 Node.js 中使用 Needle 发送 HTTP 请求

> 原文：<https://blog.logrocket.com/using-needle-send-http-requests-nodejs/>

在处理后端时，有些情况下我们需要向其他 API 发出 HTTP 请求来获取信息。Node.js 中有很多不同的包对此有所帮助，比如`node-fetch`和 [Axios](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/) 。

今天我们要看看另一个叫做 [Needle](https://www.npmjs.com/package/needle) 的包，看看为什么要用它而不是 Axios 之类的东西。

Needle 是为 Node 开发的 HTTP 客户机，它帮助我们向外部数据源发送 HTTP 请求来检索数据。当您不想向客户端应用程序公开 API 密钥等敏感信息时，应该在后端使用这种方法。

让我们看看这个包实际上是如何工作的。在本教程中，我们将使用一个假的 REST API ( [JSONPlaceholder](https://jsonplaceholder.typicode.com/) )通过我们的服务器用 Needle 发出 HTTP 请求来提供数据。

## 为什么你应该用针

我选择 Needle 而不是 Axios 的原因是，Needle 是为后端设计的，而 Axios 是为前端和后端设计的。不是说 Axios 是个坏包；这两个包我都用过不少，都很棒。但是 Needle 的特定用例位于后端，正如它的标语所写的，“Nodelands 中最精简、最漂亮的 HTTP 客户端”。

Needle 是由特定于节点的库组成的，这意味着它不能在前端的浏览器中工作。如果我们仔细看看`User-Agent` header Needle 在其请求中的默认设置，它被设置为运行的节点平台和版本。这意味着 Needle 应该在 Node 上运行，并使用较小的节点包来执行请求。这使得后端的这个包不那么臃肿。

此外，与 Axios 相比，Needle 的封装尺寸更小。虽然现在封装尺寸可能不那么重要了，但是像 Needle 这样较小的封装尺寸对于优化来说是非常好的。如果您关心包的大小，那么在后端使用 Needle，在前端使用 Axios 是有意义的。

当你在后端工作时，你肯定知道代码不会以任何方式在前端使用，那么为什么不节省一些空间，使用 Needle 而不是 Axios 呢？

使用 Axios 和 Needle 非常相似。在正常使用中，您不会发现性能差异，但是当您真的必须优化以使您的服务器更快时，使用像 Needle 这样的轻量级包是一个不错的选择。

## 创建一个示例 Node.js 服务器

让我们看看针的作用。我们的第一步是设置一个样本节点和 Express 服务器。

为此，转到一个安全的项目工作区文件夹，键入以下命令来为我们的项目设置`package.json`:

```
npm init -y

```

现在让我们像这样安装 Needle 和 Express 包:

```
npm install needle express

```

接下来，创建一个名为`index.js`的新文件，并在您的终端中使用`node index`来运行它。我强烈推荐使用`nodemon`，因为一旦你修改了文件，它会自动重新加载服务器。

使用以下命令安装它(您只需在每台机器上安装一次):

```
npm install -g nodemon

```

## 设置示例 Express 服务器

现在，转到`index.js`文件，使用下面的代码设置一个裸机 Express 应用程序。我们还将在此处进口针头以备将来使用:

```
const express = require("express");
const needle = require("needle");
const app = express();
const PORT = process.env.PORT || 8080;

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));

```

在上面的代码中，`process.env.PORT || 8080`意味着如果环境变量中没有提供端口，服务器将在端口 8080 上运行。当你部署在像 [Heroku](https://blog.logrocket.com/free-services-deploy-node-js-app/) 这样的服务上，并且你需要一些方法来本地测试服务器时，这是非常有用的。

## 设置路线和使用针

现在让我们创建一个路由，当发送一个请求时，它将从一个假 API 返回一个响应。这将是一个简单的`GET`请求:

```
app.get("/posts", async (req, res) => {
  try {
    const response = await needle(
      "get",
      "https://jsonplaceholder.typicode.com/posts"
    );
    console.log(response.body);
    res.json(response.body);
  } catch (err) {
    console.log(err);
    res.status(500).json({ message: "Internal server error" });
  }
});

```

让我们看看我们在上面的代码中做了什么。首先，我们在 route `/posts`处建立了一个快速路由，并使用了一个异步回调函数。然后，我们从 Needle 发出一个`GET`请求，并指定我们发送 HTTP 请求的 URL。

我们的响应存储在`response.body`中，我们使用`res.json()`将这个响应发送回用户。

如果在发出请求的过程中出现问题，我们使用`catch`块，如果出现问题，我们将发送一条错误消息作为响应。

现在，如果你使用像 Postman 这样的 HTTP 客户端或者简单地使用浏览器点击`[http://localhost:8080/posts](http://localhost:8080/posts)`，你将得到一个带有大量 JSON 格式的虚拟帖子的响应。

我们可以做更多的事情，比如检查 HTTP 状态代码。让我们将从请求中获得的状态代码作为响应返回:

```
res.status(response.statusCode).json(response.body);

```

在上面的代码片段中，我们只是添加了来自 express 的`.status()`方法来返回一个状态代码作为响应。

类似地，您可以将标头传递给请求，以发送请求数据，如 API 键

```
const response = await needle(
  "get",
  "https://jsonplaceholder.typicode.com/posts",
  { headers: { "x-my-custom-key": "abc123" } }
);

```

您可以像这样解析响应头:

```
console.log(response.headers);

```

## 结论

Needle 已经存在很多年了，甚至比 Axios 还要早。一定要亲自尝试一次！今年越来越多的人开始使用 Needle，这表明 devs 想要更多的优化。第一步是使用更小尺寸的包装。

然而，如果你只是一名学生，或者你正在做一个不一定需要优化的简单项目，我看不出使用 Axios 与使用 Needle 有什么不同。我认为优化部分在大规模的生产级应用中更有用，开发者需要尽可能保持事物的整洁。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.