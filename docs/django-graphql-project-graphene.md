# Django 和 graph QL:Graphene-log rocket 博客演示项目

> 原文：<https://blog.logrocket.com/django-graphql-project-graphene/>

***编者按:**本文于 2022 年 5 月 3 日更新，以反映 GraphQL 和 Django 的最新版本，并更好地详细说明 GraphQL 和 Django 演示项目，包括使用石墨烯进行集成。*

在过去八年左右的时间里，Django 一直是制作快速 web 应用程序、API 端点或其他应用程序的管理面板的最有效框架。

Django 的最大优势之一是它能够让用户编写更少的代码，更快地开始工作，特别是如果您包括一个管理面板和一个完全可管理的数据库迁移过程作为基本功能的话。

Django REST Framework 是一个外部工具包，它使得构建 API 端点变得很容易。它基本上只用几行代码就将完整的 CRUD API 包装在 Django 模型周围。

这意味着用 Django 构建任何基本的 CRUD API 都有助于将开发重点更多地放在 UI 部件上，UI 部件是所有软件产品的关键元素。

类似地，GraphQL 旨在通过提供严格类型查询语言和单个 API 端点来自动化后端 API，在该端点上，您可以从 UI 查询所有需要的信息，并触发动作(突变)以将数据发送到后端。

我的 GraphQL 之旅是从脸书的 API 开始的，GraphQL 就是从那里来的。

自然，GraphQL 被认为非常接近 JavaScript 世界，主要是因为基于浏览器的应用程序是该技术的第一批采用者。

这就是为什么我的第一个 GraphQL server+client 是在 Node.js 和 React.js 中完成的，在 GraphQL API 的基础上构建了第一个应用程序后，我开始专门将其用于我的基于 Web 的项目。

在本文中，我将回顾 GraphQL 和 GraphQL 订阅的优点。我还将使用 GraphQL 和 Django 构建一个示例项目，展示这种组合对于 web 开发的强大功能。我会用石墨烯来整合。

### *向前跳转:*

## GraphQL 的优势

你可能已经猜到了，Django 有一个支持 GraphQL 的库叫做 [Graphene-Django](https://docs.graphene-python.org/projects/django/en/latest/) ，和 Django REST 框架非常相似。但是，Django REST 和 Django with GraphQL 之间存在显著的差异。

关键的区别在于 UI 的可用性:使用 REST API，您会得到无数带有特定参数名的 URL，您必须在这些 URL 中检查类型和特定值。

同时，使用 GraphQL，您可以定义类似于下面代码的变化，并获得严格定义的变量名和类型，这成为自动 GraphQL 类型验证的一部分。

```
type Mutation {
  userLogin(email: String!, password: String!): UserLoginResponse
}

type UserLoginResponse {
  token: String
  error: Boolean
  message: String
}

```

GraphQL 还附带了封装在其类型系统中的另一个好处:它自动生成文档，您可以在其中获得可用的查询和变异及其参数/返回类型。

Django REST 也生成某种形式的文档，但是它不如 GraphQL Playground 有用。

如果你认为这种类型的接口适用于所有类型的 GraphQL 端点，那你就错了——这只适用于开发模式服务器。不过，您可以下载 GraphQL Playground standalone 之类的软件，并为任何公共 GraphQL 端点手动配置它。

就安全性而言，拥有一个 API 端点自然比拥有数百个端点更容易管理——尤其是当您考虑到 GraphQL 会自动保持特定的类型规则，并且不允许参数名或参数值不正确的请求时。

## 姜戈·❤️·格拉夫 QL

让我们用 Django 和 GraphQL 构建一个基本项目来演示这个设置有多强大。一方面，您可以通过数据库获得简单的 CRUD 管理。另一方面，您将获得一种非常强大的具有单一端点的 API 查询语言。

### 项目设置

*   在 IDE 终端或独立终端模拟器中打开一个空目录
*   创建虚拟环境:`python -m venv venv`
*   激活虚拟环境:`source ./venv/bin/activate`
*   安装 Django 和石墨烯:`pip install django graphene_django`
*   创建新的 Django 项目:`django-admin startproject graphqlpractice`
*   光盘放入上层`graphqlpractice`目录

### 创建模型

现在，让我们创建并定义我们的模型。创建一个新的 Django 应用程序`django-admin startapp postusers`，并在`settings.py`中注册应用程序，如下所示:

```
INSTALLED_APPS = [
...
"postusers.apps.PostusersConfig"
]
```

接下来，像这样定义`postusers/models.py`中的模型:

```
from django.db import models
class Author(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Post(models.Model):
    title = models.CharField(maxlength=100)
    body = models.TextField()
    author = models.ForeignKey(
        Author, relatedname="posts", on_delete=models.CASCADE
    )
    def str(self):
        return self.title
```

现在，让我们通过向`postusers/admin.py`添加以下代码来向管理面板注册模型:

```
from django.contrib import admin
from .models import Author, Post
admin.site.register(Author)
admin.site.register(Post)
```

接下来，进行迁移:`python manage.py makemigrations`。

> **注意，**如果你得到一个关于`force_text`的错误，这与 Django 4 中的突破性变化有关；要么降级到 Django 3，要么按照指示[这里](https://exerror.com/importerror-cannot-import-name-force_text-from-django-utils-encoding/)

现在，运行迁移:`python manage.py migrate`

创建一个超级用户— `python manage.py createsuperuser` —并使用`python manage.py runserver`运行您的服务器。然后前往`localhost:8000/admin`，添加一些作者和帖子。

### 设置石墨烯

现在，让我们建立石墨烯，一个与 Django 集成的强大库。首先，将石墨烯添加到`/graphqlpractices/settings.py`中已安装的应用程序中，如下所示:

```
INSTALLEDAPPS = [
    ...
    'django.contrib.staticfiles',
    "graphenedjango"
    ...
]
```

然后，将 GraphQL 端点添加到`/graphqlpractice/urls.py`:

```
from django.contrib import admin
from django.urls import path
from graphenedjango.views import GraphQLView
urlpatterns = [
    path('admin/', admin.site.urls),
    path("graphql", GraphQLView.asview(graphiql=True)),
]
```

接下来，添加下面的`settings.py`来标识您的 GraphQL 模式所在的位置，`postusers/schma/schema.py`；您需要创建模式目录和文件。

```
GRAPHENE = {
    "SCHEMA": "postusers.schema.schema"
}
```

这个项目最有趣的部分是用 Python 定义 GraphQL 类型和查询。它实际上基于您的数据库模型，但是您也可以在不使用 Django 模型的情况下定义定制查询。

将以下内容添加到`postusers/schema/schema.py`:

```
from graphene_django import DjangoObjectType
import graphene
from postusers.models import Post as PostModel
from postusers.models import Author as AuthorModel
class Post(DjangoObjectType):
    class Meta:
        model = PostModel
        fields = ['id', 'title', 'body', 'author']

class Author(DjangoObjectType):
    class Meta:
        model = AuthorModel
        fields = ['id', 'name', 'posts']
    def resolve_posts(self, info):
                return PostModel.objects.filter(author=self)
    @classmethod
    def get_node(cls, info, id):
        return Author.objects.get(id=id)
class Query(graphene.ObjectType):
    authors = graphene.List(Author)
    posts = graphene.List(Post)
    def resolve_authors(self, info):
        return AuthorModel.objects.all()

    def resolve_posts(self, info):
        return PostModel.objects.all()
schema = graphene.Schema(query=Query)

```

现在您可以运行服务器并转到`localhost:8000/graphql`来测试一些 GraphQL 查询，如下所示:

```
query {
  authors {
    name
      posts {
        title
      }
  }
}

```

最需要记住的是，你可以查询任何你想要的字段，这将影响 UI 端的整体加载时间和流量使用。

对于较大的用户群，保持低流量并只查询您需要的字段是很重要的。在 REST API 的情况下，您无论如何都会获得所有字段。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
query {
  posts {
    title
    author {
      name
    }
  }
}

```

这是 Python 定义的基本查询结果。它非常简单，并且与 REST APIs 相比，比您想象的更具表现力。你可以在这里找到这个练习[的最终代码供参考](https://github.com/AlexMercedCoder/graphql-with-django-graphene-example)。

## GraphQL 订阅呢？

GraphQL 订阅的功能是告诉服务器在数据可用时基于特定的查询检索数据。

它几乎实时地与 WebSockets 一起工作，这意味着我们必须以某种方式包含 Django Websockets，并配置我们的后端服务器来接受 Websockets。

基本上，GraphQL 只是一个 API 查询语言解释，当处理客户端和服务器端 GraphQL 语言解释时，它可以与任何类型的网络传输一起工作。

乍一看似乎很难，但是在 Django WebSockets 模块上有一个开源库和 Django GraphQL 订阅。

```
# settings.py

GRAPHENE = {
    'SCHEMA_INDENT': 4,
    'MIDDLEWARE': [
        # Other middlewares

        'graphenedjangosubscriptions.depromise_subscription',
    ]
}
```

这将足以在以后作为订阅查询来处理订阅模式。

顺便提一句，Pinterest 实际上完全基于 GraphQL 订阅，而 graph QL 订阅是建立在 Django 后端之上的(但可能做了一些修改)。

## 结论

在我看来，使用 GraphQL 的 Django 比使用 REST API 的 Django 功能更强大，可扩展性更强。

然而，它还没有经过实战检验，大公司仍在采用这种组合。尽管如此，基于您从这个简单的配置中所能得到的，想象一下当您将 Django 与现代技术的 GraphQL 结合使用时，web 开发会变得多么舒适。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.