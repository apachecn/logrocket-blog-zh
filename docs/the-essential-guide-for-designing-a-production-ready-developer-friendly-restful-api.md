# 设计生产就绪、开发人员友好的 RESTful API 的基本指南

> 原文：<https://blog.logrocket.com/the-essential-guide-for-designing-a-production-ready-developer-friendly-restful-api/>

开发人员是编程 API 的主要用户。通常，我们关心产品的 UI 和 UX，但是忽略了为 API 创建良好的 UX 的重要性。

在产品开发的初始阶段，它可能不会引起问题，但是一旦它被不同需求的多组开发人员使用，它很容易成为开发速度和产品执行的瓶颈。

在这篇文章中，我们将讨论如何避免这个问题，并确保您的 API 随着产品的增长而平稳扩展。

我们将讨论构建更好的 API UX 的一些最佳实践和指南，尤其是广泛使用的 RESTful API。

这不是说“这是构建 REST API 的最佳方式”的指南。每个产品都有不同的需求——这些是为您的 REST API 提供更好的 DX(开发人员体验)的通用指南。

### REST API 设计基础

盲目遵循 web 标准不会做出好的 API。RESTful 是一种创建 API 的灵活的架构风格。它并没有规定如何去做——相反，它只是告诉你在设计过程中需要记住什么。

以下是 REST API 设计的一些基本技巧:

*   从资源的角度考虑，而不是从 CRUD 操作的角度
*   使用正确的 HTTP 动词
*   精心制作不言自明的 URL
*   将正确的内容类型作为标题发送
*   使用正确的 HTTP 状态代码
*   正确处理错误并为客户端错误发送错误消息

在这篇文章中，我们将根据这些指导方针为工作公告板创建一个模拟 API。

### 从资源的角度考虑

REST API 围绕着创建资源。本质上，资源是应用程序的逻辑分割。

它不需要与您的数据模型相同。因为你可以在多个数据模型中使用资源，所以它不同于 CRUD。

例如，在我们的 job board 中，我们可以拥有多个资源，其中一些资源在其操作中使用多个数据模型。

*   乔布斯
*   使用的数据模型:工作、类别、工作类型
*   公司
*   使用的数据模型:公司、用户、订单
*   应用程序
*   使用的数据模型:应用程序、用户

在这些资源中，将会有多个操作——而不仅仅是数据模型的 CRUD。在下一节中，我们将探索如何使用 HTTP 动词和 URL 来分隔这些操作。

### HTTP 动词和 URL

l 有几个 HTTP 动词–GET、POST、PUT、PATCH、DELETE。所有这些 HTTP 动词都有特定的功能。

除了这些 HTTP 动词，资源还可以有多种功能。

例如:

*   `GET /jobs`–检索所有作业
*   `GET /jobs/1234`–检索作业 ID 为`1234`的特定作业
*   `POST /jobs`–创建新的工作列表
*   `PUT /jobs/1234`–更新作业 ID 为`1234`的作业
*   `DELETE /jobs/1234`–删除作业 ID 为`1234`的作业
*   `PATCH /jobs/1234`–更新作业 ID 为`1234`的部分作业。它类似于`PUT`，但是 put 更新整个作业，而`PATCH`更新作业数据的特定部分。

### 更好的 URL 模式

一个小提示:不要像这样构造 URL:

*   `POST /createJobs`创造就业❌
*   `GET /getAllJobs`去取❌所有的工作
*   `GET /getJobById`用 ID ❌获得特定的工作

这种方法是可行的，它也是一个 REST API。没有规定说你不能这样使用 REST API。

然而，这种方法不能很好地扩展。

对于使用它的开发人员来说，这将是一场噩梦，他们每次都需要浏览文档来检查特定操作所需的 URL 模式。

我建议对资源 URL 使用名词，而不是动词。用户看到就更容易知道更新和删除的网址。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`POST /jobs`–创建作业✅
`GET /jobs`–检索所有作业✅

使用 URL 的这个模板将帮助开发人员很容易理解他们需要向`/jobs/:id`发送一个删除请求来删除一个作业。

### 显式发送内容类型头

如果 URL 中没有明确指定默认内容类型，请始终发送默认内容类型。

现在，JSON 是默认的内容类型，它发送内容类型的头，这样用户就知道 API URL 返回什么类型的内容。

一些内容类型标题包括以下内容:

*   内容类型:应用程序/json
*   内容类型:文本/html
*   内容类型:应用程序/xml

### 小心处理嵌套资源

资源通常有很多关系，所以我们可能需要通过嵌套的资源来获取这些关系。如果没有正确定义嵌套资源，这可能会很棘手。

在我们的工作公告板示例中，一个工作可以有多个应用程序。您可以通过作业资源本身获取这些信息。

例如:

*   `GET /jobs/1234/applications`–获取特定工作 ID 的所有申请(`1234`)
*   `GET /jobs/1234/applications/:123`–获取作业 ID 为(`1234`)的作业的应用 ID 为(`123`)的具体应用
*   `/companies/12345/applications`–获取特定公司的所有申请(`12345`)。

这里您可以看到`Jobs`和`Companies`都与`Applications`资源有关系。

在这种情况下，通过嵌套资源创建新的应用程序是不明智的。

相反，通过嵌套资源进行检索，并通过`Applications`资源创建新的应用程序。

换句话说，使用`POST /applications`创建一个新的应用程序，它将包含关于一个特定任务的信息。

在某些情况下，这是最有效的方法，但不是全部。最终，这取决于用例。

如果一个应用程序的唯一直接连接是工作而不是公司，那么这种方法将会起作用。您可以为`POST /jobs/1234/applications`中的工作创建应用程序。

尽管如此，将资源分开并尽可能避免嵌套总是好的。

一般来说，尽量不要超过一层嵌套，并确保在逻辑上分割成单独的资源。

### 支持过滤以避免嵌套资源

在我们的用例中，使用过滤可以帮助我们避免嵌套:

*   `GET /applications?jobId=1234`–这将获取 ID 为的特定作业的所有应用程序
*   `GET /applications?companyId=12345`–这将获取 ID 为的特定公司的所有应用程序

过滤器也可以基于字段:

*   `GET /jobs?jobType=Remote`–用`jobType: Remote`获取作业
*   `GET /jobs?categories=developers,designers,marketers`–过滤器可以是一个数组。在这种情况下，它过滤类别`developers`、`designers`和`marketers`中的所有工作

### 支持搜索

有两种类型的搜索:

*   基于字段的搜索
*   一般搜索

通用搜索可以作为以`q`或`search`为关键字的查询字符串传递。
比如:`/jobs?q=searchterm`

基于字段的搜索与基于字段的筛选相同。

一些字段使用精确匹配进行筛选，而另一些字段使用部分基于正则表达式的匹配进行筛选。
比如:`/jobs?title=marketing ninja`。在这里，我们可以搜索部分标题为`marketing ninja`的职位

### 使用正确的 HTTP 状态代码，并在整个 API 中保持一致

我们都知道特定的 HTTP 状态代码意味着什么——200、4xx、5xx、302 等等。

我们使用这些状态代码让 API 消费者确切地知道在处理他们的请求时发生了什么。一致地使用它**是良好的 API 用户体验的关键。**

需要注意的是，您不需要支持所有的 HTTP 状态代码，但是您应该尝试支持符合您的 API 需求的 HTTP 状态代码。

您不希望发送状态代码为`200`的`Not found`错误。这是一种不好的做法，会让用户混淆错误是否发生。

以下是 API 中 HTTP 状态代码的一些示例:

*   获取、放置、修补–200 OK
*   POST-201 创建
*   删除–204 无内容

以下是一些错误的状态代码:

*   400–错误的请求
*   401–未经授权
*   404 未找到
*   429–请求太多
*   500–内部服务器错误

### 错误消息和响应

在响应中发送客户机错误的详细信息也是一个好主意，这样 API 用户就可以向他们的最终用户显示错误的详细信息。

具有适当误差响应的示例响应如下:

```
// A sample response
{
  errors: [{
    'status': 'InvalidError'
    'message': 'Invalid value for email',
    ... // Other details of the error
  }, {
    ... // Next error object
  }],
  data: {
  ... // Any data
  }
}
```

### 异步响应

如果一个 API 动作正在后台进行异步操作，立即向用户发送响应。不要等到流程结束才发送带有适当状态代码的响应。

通常，在这种情况下，您将使用`202 Accepted`。这并不意味着操作已经完成，只是意味着它已经被接受。

电子邮件触发和大量计算是异步操作。

### 选择字段:允许客户端获取他们实际需要的内容

允许您的 API 用户选择他们想要的字段。默认情况下，向他们发送所有相关数据。

如果用户明确要求特定的细节，只发送所要求的细节。这样，您的 API 将能够灵活地发送客户要求的准确数据。

示例:

*   `GET /jobs?fields=id,title,description,jobType,categories`–专门显示明确传递给字段查询字符串的字段中的作业。

### 按需扩展资源

数据模型有多个模型的 ID 引用。如果您的响应时间很慢，那么在解析资源时，默认情况下不要从多个模型展开对象。

例如，以下代码片段显示了以 jobType 和 categories 作为 id 的作业响应:

```
// GET /jobs
[{
  title: 'Job title',
  description: 'Job description',
  jobType: 1233043949238923, // ID ref to jobType model
  categories: [ // ID ref to categories model
    1029102901290129,
    0232392930920390,
  ]
},
{
... // Job Objects
}]
```

接下来，我们将使用一个显式请求来扩展 jobType 和 Categories 数据:`GET /jobs?expand=jobType,categories`

```
// GET /jobs?expand=jobType,categories
[{
  title: 'Job title',
  description: 'Job description',
  jobType: 'Remote', // Resolved from jobType model
  categories: [ // Resolved from categories model
    {
      name: 'Front end developer' 
    },
    {
      name: 'React developer'
    },
  ]
},
{
... // Job Objects
}]
```

### 支持排序，在前端实现更大的灵活性

默认情况下，每个资源都有不同的排序顺序。通过扩展，最好为 API 用户提供基于字段排序的灵活性。支持升序和降序响应非常容易。

例如:

*   `GET /jobs?sort=createdDate`–这只是按照`createdDate`以升序对响应进行排序
*   `GET /jobs?sort=-createdDate`–以相反的顺序排序(降序)
*   `GET /jobs?sort=-createdDate,title`–按多个值排序(createdDate 降序排列，title 升序排列)

> 您不需要遵循相同的约定，这完全取决于您使用的框架。这只是一个如何支持资源排序的一般示例。

### 明智地使用分页

对于较小的资源，不需要使用分页。

但是，一旦响应超过了一定的大小，分页就可以派上用场了。使您的分页实现简单明了。

例如:

*   `GET /jobs?page=2&size=10`–此处，`page`表示页数,“尺寸”表示每页工作数量的限制。在此示例中，第 2 页包含从 11 到 20 的作业。

在响应中，我们将向 API 用户发送相关的页面信息和内容:

```
// Sample paginated list example
  {
    data: [
      {
        ... // actual response data
      }
    ],
    pageInfo: {
      currentPage: 2,
      hasNextPage: false,
      hasPrevPage: true,
      ... // Add any more pagination related information
    }
  }
```

到目前为止，我们已经介绍了创建 REST API 所需的最基本的概念。

现在我们要换个话题，讨论一些创建开发人员友好的、生产就绪的 RESTful API 的高级概念。

### 在 API 的早期阶段使用 HATEOAS

开发人员经常讨厌 HATEOAS，不仅仅是因为名字本身就有“讨厌”这个词。我不打算深入讨论 HATEOAS 是什么——我只想告诉你它是做什么的。

HATEOAS 是一种将所有相关资源 URL 显式发送到端点的方法。它允许消费者轻松地在您的资源之间导航，而不必自己构建 URL。

这是 RESTful APIs 背后的主要概念之一。它允许 API 用户了解任何给定资源及其相关资源上的不同操作。

例如:
`GET /jobs`–获取所有作业。

它对 HATEOAS 的回应是这样的:

```
// HATEOAS links are in the links section
{
  data: [{...job1}, {...job2}, {...job3}, ...],
  links: [
    // GET all applications
    {
      "rel": "applications",
      "href": "https://example.com/applications",
      "action": "GET",
      "types": ["text/xml","application/json"]
    },
    {
      "rel": "jobs",
      "href": "https://example.com/jobs",
      "action": "POST",
      "types": ["application/json"]
    },
    {
      "rel": "jobs",
      "href": "https://example.com/jobs",
      "action": "DELETE",
      "types": []
    }
  ]
}
```

所有相关链接都被添加到响应本身。它帮助 API 用户在资源和不同操作之间导航。

### 认证和授权

在允许用户完成任何会改变数据的操作之前，始终要对用户进行身份验证和授权。

您还应该通过在授权墙后面保护敏感信息来限制对所有敏感信息的访问。对于没有完成必要的身份验证和授权的用户，应该只提供公共信息。

以下是在身份验证和授权过程中需要记住的一些提示:

*   实施 RBAC(基于角色的访问控制)并允许用户拥有多个角色
*   为每个角色提供精确的权限，并允许用户级别的某些权限
*   始终进行身份验证，然后检查用户是否有权执行该操作。如果他们没有被授权，发送一个`403 forbidden`响应。
*   如果用户没有通过认证，发送一个`401 Unauthorized`响应
*   对于无效的凭证，发送一个`401 Unauthorized`响应

### API 安全性

安全性是一个广泛的话题。在 API 级别，最佳实践是，

*   总是验证请求数据
*   遵循拒绝优先原则，仅当 API 请求通过特定端点的所有检查时才允许
*   不允许在没有适当验证的情况下对 API 进行批量操作
*   编写集成测试和一些端到端测试，以增强对 API 操作的信心

### 当您需要对 API 进行重大更改时，版本控制可以节省您的时间

API 是用户和开发者之间的契约。当您对模式进行重大更改时，通常会忘记契约，破坏现有 API 客户端的东西。

这就是 API 版本控制的用武之地。

例如:

*   `GET /v1/jobs`–获取 API 版本 1 并发送 XML 响应
*   `GET /v2/jobs`–默认情况下发送 JSON 响应

这样，我们就不会破坏现有消费者的 API。相反，我们可以在必要的时候显示一个反对的警告，并要求现有用户使用新版本的 API。

版本控制还可以在其他几个方面帮助您:

*   它允许您发布实现的测试版本
*   它给你的 API 用户时间来适应任何变化

广泛使用的版本化方法的一些例子包括基于数字和基于日期的版本化。

最后，版本控制不需要在 URL 上。一些 API，如 Github REST，将版本控制作为自定义头传递:

接受:application/vnd.github.v3+json

*   v3 是 REST API
*   v4 是图 QL API

### 必要时限制费率

大多数 API 不要求速率限制，但是它可以为您的 API 增加一些基本的安全性。

速率限制有几个级别:

*   基于特定时间段内请求数量的速率限制(基于窗口的速率限制)。当分配的时间到期时，它会自动复位。
*   基于点数的费率限制，用户需要充值才能再次使用。如果用户没有充值，他们会收到一条错误消息。
*   通过自定义标头发送有关速率限制的信息，以便客户端知道在一个窗口期内他们还有多少请求或他们的当前配额。

Github 是这样对他们的 API 进行速率限制的:

```
curl -i https://api.github.com/users/octocat
HTTP/1.1 200 OK
Date: Mon, 01 Jul 2013 17:27:06 GMT
Status: 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 56
X-RateLimit-Reset: 1372700873
This way, you don’t need to fetch from DB every time.
```

现代数据库针对读取进行了优化，因此这并不总是必要的。尽管如此，尽可能地缓存有助于提高读取速度。

虽然缓存很有价值，但是它给 API 增加了额外的复杂性，因为每当数据发生变化时，您都需要中断并重新缓存。

如果数据没有改变，服务器应该返回`304 Not Modified`。这个响应将向您的浏览器客户端显示数据没有改变，并提示服务器重新使用以前获取的旧数据。

### 实施 CORS

CORS 允许跨域访问 API。大多数应用程序只需要将某些域列入白名单，以允许来自这些域的 CORS。

对于公共 API，您可能需要允许任何人获取数据，前提是他们拥有正确的身份验证密钥集。在这种情况下，实现 CORS 允许所有域，并开始黑名单域，如果他们看起来可疑。

### 当你陷入困境时，伐木会拯救你

日志记录是开发任何 web 平台不可或缺的一部分。API 也是如此——我们需要根据优先级(错误、信息、警告)来分离日志。)

当出现错误和安全问题时，适当的日志记录和分离将加快以后的调试。

请记住这些提示，以确保您的日志尽可能高效:

*   尝试遵循日志记录中的一些标准(例如:JSON 日志)。使用框架进行日志记录将有助于促进标准化，并从长远来看为您节省大量时间。
*   尝试在日志上创建警报和分析模式，以识别问题
*   不要升级同一优先级范围内的所有错误。在按照 API 中的优先级对每个错误进行分类之前，检查有多少用户受到了影响，问题有多大。日志应该有助于识别这些模式。
*   确保记录所有请求、会话以及关于请求来源的详细信息，以便您可以评估任何与安全相关的问题

### 监控设置

以下是监控设置时需要记住的一些提示:

*   投资一个好的监控系统
*   显示 API 的状态页面
*   确保您的支持易于获得。通过 Twitter 跟进也是一个好主意——这为那些想寻找简单问题答案的人节省了大量时间。
*   监控响应时间
*   检查慢速查询并尝试优化它们

### 面向开发人员的 API 文档

为开发人员开发 API 文档时，确保所有内容都是最新的非常重要:

*   更新 API 文档和你的拉取请求，如果可能的话，包括文档的版本控制
*   记录开发 API 时做出的小决定，并将其添加到版本描述中。这确保了在同一个 API 上工作的每个人都知道每个决定背后的原因。这也有助于团队自主工作。

Postman collections 和 Swagger API 文档是开发人员文档的好例子。

### 面向消费者的文档

公共 API 文档如下:

*   对你的资源保持清晰
*   展示关于限制和如何不滥用 API 的细节
*   API playground 将增强用户体验，也有助于直接测试该特性，而无需复杂的设置
*   必要时显示警告

如果您想阅读优秀的 API 文档，请查阅以下资源:

### 选择合适的框架，不要什么都自己做

您可以将这最后一条建议应用到您正在进行的任何开发项目中，包括 API 开发。

一般来说，重用开源框架来为消费者构建一个可靠的 API 比重新发明轮子更容易。

### 结论

本指南是构建优秀 API 用户体验的起点。

在很多情况下，我们只需要构建一个快速的 API，它可能不会被普通大众使用。

确保访问 API 的用户，只实现当前产品级别所必需的内容，然后根据需要进行扩展。过早优化从来都不是一个好主意。

欢迎在评论中分享你对构建 API 的见解和经验。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)