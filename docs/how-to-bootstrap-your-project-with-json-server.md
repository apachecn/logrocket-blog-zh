# 如何用 JSON 服务器引导你的项目

> 原文：<https://blog.logrocket.com/how-to-bootstrap-your-project-with-json-server/>

JSON Server 使前端开发人员能够快速构建一个假的 REST API 来验证应用程序的接口在从后端接收数据时的反应。当所需的后端路由尚未完成时，这尤其有用。

从高层次上来说，这并不复杂:您所需要做的就是创建一个定义端点和样本数据的 JSON 文件——不需要进一步的配置。事实上，你可以在五分钟内引导一个假的 REST API 。

在本教程中，我们将演示:

*   如何用 JSON 服务器引导一个简单的 REST API
*   如何发送`GET`、`POST`和`PUT`请求
*   如何发送带有过滤和排序参数的高级请求
*   如何定义数据段之间的关系

让我们开始吧！

## 安装 JSON 服务器

在发送任何请求之前，我们需要安装 JSON 服务器。我们最好使用 npm 来全局安装`json-server`包。

```
npm install -g json-server

```

既然 JSON 服务器已经全局安装，我们可以创建我们的第一个服务器配置。

## 了解 JSON 服务器配置

下一步是创建我们的第一个`db.json`文件，它保存了伪 REST API 的 JSON 配置。下面是一个示例配置，它包括三个端点:authors、books 和 library。

```
{
 "authors": [
   { "id": 1, "name": "Michiel Mulders", "genre": "fiction" }
 ],
 "books": [
   { "id": 1, "title": "some title", "authorId": 1 }
 ],
 "library": { "name": "City library" }
}

```

### 多个端点

请注意我们是如何定义端点的:每个具有复数名称的端点都包含一个对象数组，每个对象代表该端点的一个数据库记录。例如，`authors`端点将返回一个作者对象，其名称与米歇尔·穆尔德斯相同。

对于多个端点，JSON Server 生成以下路由:

*   `GET /authors`返回所有作者
*   `GET /authors/1`返回 ID 为 1 的作者
*   `POST /authors`在数据库中创建新作者
*   `PUT /authors/1`更新 ID 为 1 的整个作者对象
*   `PATCH /authors/1`更新 ID 为 1 的作者对象的特定字段
*   `DELETE /authors/1`删除特定的作者对象

### 奇异端点

JSON 配置显示了一个单独的端点库。当您以单数形式定义端点时，它只能返回一个对象。不可能定义一个数组。

对于为单个端点生成的路径，`GET/POST/PUT/PATCH /library`是对该对象可用的基本操作集，但不能删除该对象。

现在我们理解了 JSON 服务器生成的可能的路由。让我们开始我们的第一个服务器！

## 启动您的第一个 JSON 服务器

为了启动服务器并为上述端点提供服务，我们将在命令行中使用`json-server`命令。我们将告诉`json-server`命令查看我们的`db.json`配置文件。

```
json-server --watch db.json

```

您可以通过向`[http://localhost:3000/authors/1](http://localhost:3000/authors/1)`发送一个`GET`请求来验证您的服务器正在运行。如果它返回一个结果，您就可以探索其他端点了。

### 如何发送`POST`请求

我们可以通过[邮递员](https://www.getpostman.com/)或 [curl](https://gist.github.com/subfuzion/08c5d85437d5d4f00e58) 发送一个`POST`请求。不要忘记为请求传递正确的头。因为我们处理的是 JSON API，所以我们需要发送`Content-Type: application/json`头。

一个`POST`请求可以简单地通过请求体发送。下面的主体将在我们的数据库中创建一个新的 author 对象。不要忘记将请求发送到正确的 URL: `[http://localhost:3000/authors](http://localhost:3000/authors)`。

```
{
    "name": "Gregory Salis",
    "genre": "nonfiction"
}

```

如您所见，请求体不需要传递 ID。JSON 服务器将自动为您的 author 对象创建一个新的 ID。

### 如何发送`PUT`请求

发送一个`PUT`请求与发送一个`POST`请求非常相似。首先，我们必须指定需要更新的对象的 ID。让我们使用 URL `[http://localhost:3000/authors/1](http://localhost:3000/authors/1)`更新 ID 为 1 的原始作者对象。

这个`PUT`请求必须将体裁从小说修改为非小说。因此，请求正文将如下所示:

```
{
    "name": "Michiel Mulders",
    "genre": "nonfiction"
}

```

除了发送简单的`GET`、`POST`和`PUT`请求，JSON Server 还允许更高级的请求，比如过滤、排序和搜索。

## 高级 JSON 服务器请求

为了丰富其功能，JSON Server 提供了搜索、排序和过滤选项。

### 整理

可以通过`_sort`选项对数据进行排序。假设我们想按流派对作者进行排序。排序请求将如下所示:

```
http://localhost:3000/authors?_sort=genre

```

但是，排序结果的顺序需要从`asc`切换到`desc`。`_order`选项允许我们将排序从升序改为降序。

```
http://localhost:3000/authors?_sort=genre&_order=desc

```

### 过滤

我们可以通过定义要过滤的字段和所需值来实现数据过滤。下面的示例链接将检索所有非小说作者。该请求定义了类型字段和所需的值`nonfiction`。

```
http://localhost:3000/authors?genre=nonfiction

```

另外，JSON Server 允许我们组合过滤器。现在，该请求应该检索作者米歇尔·穆尔德斯的所有非小说类书籍。下面的请求定义了两个由&字符连接的字段。

```
http://localhost:3000/authors?genre=nonfiction&name=Michiel%20Mulders

```

注:米歇尔和穆尔德斯之间的空间由`%20`编码。这是编码 URL 的常见做法。最后，`json-server`提供了几种搜索的可能性。

### 搜索

最后但同样重要的是，搜索可以以多种方式执行。全文搜索选项有助于查找特定值。在查找值时，这种方法是最简单的。在这里，请求应该找到名称中包含“Michiel”的所有作者。

```
http://localhost:3000/authors?q=michiel

```

此外，JSON Server 有助于查找特定字段的值。例如，该请求应该返回所有在“类型”字段中包含“Michiel”的匹配记录。当调查`db.json`文件时，这个请求应该返回一个空响应，因为“Michiel”没有出现在流派字段中。

我们可以通过将`_like`操作符附加到我们想要搜索的字段来进行过滤。现在，请求包含了一个`genre_like`选项。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
http://localhost:3000/authors?genre_like=michiel

```

另外，`_like`操作符支持[正则表达式](https://www.regular-expressions.info/)查询。下面的查询应该检索所有以关键字`non`开头的流派。注意附加在`genre_like`选项后面的星号——那是正则表达式的一部分。

```
http://localhost:3000/authors?genre_like=non*

```

更多操作符，如`_gte`、`_lte`、`_ne`，可以在 [JSON 服务器文档](https://github.com/typicode/json-server#operators)中找到。

## 了解与 JSON 服务器的关系

一个不具备定义端点间关系能力的伪 JSON 服务器是没有用的。大多数数据需要定义数据关系的能力。让我们讨论一下`json-server`是如何定义数据点之间的关系的。

首先，记住我们开始时的`db.json`配置:

```
{
 "authors": [
   { "id": 1, "name": "Michiel Mulders", "genre": "fiction" }
 ],
 "books": [
   { "id": 1, "title": "some title", "authorId": 1 }
 ],
 "library": { "name": "City library" }
}

```

现在请注意`books`端点的`authorId`字段。通过使用数据库中另一个数据实体的单数形式，我们可以通过添加`id`关键字来链接它。在这里，我们将标题为“某个标题”的书链接到 ID 为 1 的作者。

此外，这允许我们查询 ID 等于 1 的作者米歇尔·穆尔德斯写的所有书籍。该查询需要添加关键字`books`来指定检索该作者的所有书籍。

```
http://localhost:3000/authors/1/books

```

同样，我们进一步过滤这些端点，例如下面的请求。

```
http://localhost:3000/authors/1/books?title_like=some

```

但是如果我们想要包含父资源(作者)呢？

### 包括父资源

此外，`json-server`允许我们通过 *`_embed`* 选项来包含父资源。这个请求现在看起来略有不同，因为我们从`authors`端点开始，要求嵌入 ID 等于 1 的作者的所有书籍。

```
http://localhost:3000/authors/1?_embed=books

```

当发送上面的`GET`请求时，结果如下:

```
{

    id: 1,
    name: "Michiel Mulders",
    genre: "fiction",
    books:
    [
                  {
                           id: 1,
                       title: "some title",
                       authorId: 1
                   }
    ]
}
```

## 使用 JSON 服务器的 4 个有用技巧

为了结束我们的教程，我们将回顾一些常见的最佳实践，以帮助您最大限度地利用`json-server`。

除了生成路线之外，`json-server`还提供了许多对你有用的有趣功能。让我们探索四个最有趣的特性。

### 1.数据库快照

假设您已经更新并创建了许多新对象，并且您不想丢失您的进度。您可以点击键盘上的`s + enter`来获取当前数据库状态的快照。

快照保存在同一个文件夹中，名称后面会附加一个 unix 时间戳，结果如下:`db-1578083664783.json`。

### 2.添加自定义路线

“定制路线”这个名称实际上很容易让人误解。`json-server`允许您为现有路线创建别名。

假设您的前端依赖于一个从以`/api/v1`开始的根路径提供的 API。由于`json-server`不包括这个前缀，我们必须创建一个自定义路由。我们将创建一个`routes.json`文件来告诉`json-server`要建立哪些映射。下面的例子将所有的`json-server`路由映射到所需的前缀`/api/v1`。

```
{
  "/api/*": "/$1"
}

```

接下来，我们还可以为长 URL 创建更短的路径。

```
{
  "/api/*": "/$1",
  "/:resource/:id/show": "/:resource/:id"
}

```

要告诉`json-server`使用这些别名，请将`--routes` 选项传递给 start 命令。

```
json-server --watch db.json --routes routes.json

```

### 3.更改端口

默认情况下，`json-server`在端口 3000 上提供其 API。我们可以用`--port`选项改变端口。

```
json-server --watch db.json --port 5000

```

### 4.定制中间件

最后，`json-server`允许您定义定制的中间件。例如，让我们创建一个`headers.js`文件，其中包含一小段代码，用于向请求添加额外的自定义头。

`json-server`遵循 Express.js 用于请求的相同模式:`req`、`res`和`next`。因此，您可以修改请求的数据，或者给`res`对象添加一个额外的头，如下所示:

```
module.exports = (req, res, next) => {
  res.header('X-Hello', 'World')
  next()
}

```

要使用上述中间件服务于`json-server`，请传递`--middlewares`选项。

```
json-server --watch db.json --middlewares headers.js

```

## 结论

在您开始疯狂使用模拟 API 之前，让我们快速回顾一下我们所学到的内容。

JSON Server 注重简单性和使用速度。它允许任何前端开发人员在不到五分钟的时间内构建一个假的 REST API。

此外，很容易添加数据之间的关系。只需添加关键字`id`就可以链接任何数据。`json-server`允许您像常规 API 一样过滤、排序和查询数据。

下次验证前端组件时，考虑使用`json-server`快速引导 JSON REST API。当您的项目需要特殊组件时，`json-server`允许通过中间件进行定制。

要了解 JSON Server 还能做些什么，请查看官方文档。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)