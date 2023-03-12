# AdonisJs vs. Django:应该使用哪个 web 框架？- LogRocket 博客

> 原文：<https://blog.logrocket.com/adonisjs-vs-django-which-web-framework-should-you-use/>

无论您是想为下一个项目决定 web 框架，还是想尝试 AdonisJs 或 Django，这篇文章都会让您对这两者有所了解。

## AdonisJs 和 Django 是什么？

AdonisJs 和 [Django](https://www.djangoproject.com/) 都是全栈式的 web 开发框架，具有相似而独特的特性，使它们适用于某些类型的项目。在本文中，我们将更仔细地观察这两个框架之间的比较。

据 [AdonisJs](https://adonisjs.com) 网站报道:

> AdonisJs 是一个 Node.js MVC 框架，可以在所有主流操作系统上运行。它为编写服务器端 web 应用程序提供了一个稳定的生态系统，因此您可以专注于业务需求，而不是最终决定选择哪个包。AdonisJs 以一致且富于表现力的 API 来构建全栈 web 应用程序或微 API 服务器，这让开发人员乐此不疲。

Django 网站描述 Django 为:

> 一个高级 Python web 框架，鼓励快速开发和干净、实用的设计。由经验丰富的开发人员构建，它解决了 web 开发的许多麻烦，因此您可以专注于编写应用程序，而无需重新发明轮子。它是免费和开源的。

简而言之，虽然您可以使用 AdonisJs 和 Django 来构建任何类型的应用程序，但我更喜欢将 Django 用于内容管理相关的应用程序，而将 AdonisJs 用于基于 SaaS 的应用程序。

现在，让我们看看如何设置它们。

## 设置 AdonisJs

设置 AdonisJs 很容易。因为它是基于 Node.js 的 web 框架，所以必须安装 Node.js 和 npm。

您可以使用 CLI、npx 或使用 Git 命令来设置它。

要使用 CLI 设置 AdonisJs，您需要首先通过在终端上运行以下命令来全局安装 CLI:

```
npm install -g @adonis/cli
```

这可能需要一段时间，取决于您的互联网连接强度。完成后，您将能够在您的终端上运行`adonis new musicplayer`来创建一个名为`musicplayer`的新 Adonis 应用程序。

要使用 Git 安装 AdonisJS，您必须已经安装了 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 。现在，运行下面的任何命令。

```
# Fullstack
> git clone --dissociate <https://github.com/adonisjs/adonis-fullstack-app>

# API
> git clone --dissociate <https://github.com/adonisjs/adonis-api-app>

# Slim
> git clone --dissociate <https://github.com/adonisjs/adonis-slim-app>

```

在上面的 Git 命令中，AdonisJs 完全打包了三种类型的应用程序:API 版本(如果您只需要构建一个 API)、slim 版本(没有 API)或 full stack(AdonisJs 拥有的一切)。设置哪一个由你决定。

更重要的是，为了用 AdonisJs 处理其他命令行任务，Adonis 附带了一个名为 [Ace](https://github.com/adonisjs/ace) 的 CLI。Ace 是一个强大的工具，旨在使构建和维护 AdonisJs 应用程序变得简单而有趣。

## 建立姜戈

Django 是一个 Python web 框架，这意味着我们需要在安装 Django 之前先安装 Python。我们将使用 Python 包管理器 [pip](https://pip.pypa.io/) 来安装 Django，如下所示:

```
pip install Django

```

一旦安装了 Django，您就可以使用 Django 命令行工具`django-admin`来创建您的第一个项目。

```
django-admin startproject school_portal

```

你的第一个项目启动并运行了吗？

太棒了。您还需要为这个项目创建您的第一个应用程序，因为 Django 允许您使用应用程序的概念创建可重用的应用程序。您可以根据需要拥有任意多的应用程序。

Django 在名为`manage.py`的文件中提供了一个脚本。有了这个脚本，您可以自动化 Django 允许您做的一切，比如进行迁移、登录 dJango shell 等等。

您可以使用`startapp`命令创建一个新的应用程序，如下所示:

```
python manage.py startapp student

```

此外，值得注意的是，Django 使用 Python 虚拟环境概念在隔离环境中创建项目。因此，对于大多数情况，您需要在运行这些命令之前安装并激活 [Python 虚拟环境](https://docs.python.org/3/library/venv.html)。

与 AdonisJs 不同，Django 没有 API 安装。然而，它与 Django Rest 框架包集成得很好，以防您想用 Django 构建一个 API，并且没有最小的 Django 安装。

Django 以“包括电池”而闻名，这是让您的项目尽快启动和运行所需的一切。

一般来说，Django 的设置要比 AdonisJs 的复杂一些。

## AdonisJs 和 Django 的建筑

AdonisJs 使用流行的 MVC(模型视图控制器)架构，而 Django 使用稍微独特的 MVT(模型视图模板)设计。我们将在下一节看到它们是如何组合在一起的。

### AdonisJs 和 Django 的模型

两个框架以不同的方式使用它们的模型定义。Django 模型描述了一个实体的所有内容。如果需要的话，你通常会更新它。

但是 AdonisJs 模型不同。它只定义模型的类名，扩展模型 API，然后导出模型。

Django 在模型中所做的大部分工作都是在 AdonisJs 的迁移级别上完成的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这里有一个 Django 数据库模型的例子。请注意，您必须按照自己的意愿手动创建这个模型。

```
from django.db import models

class Movie(models.Model):
    title = models.CharField(max_length=100, unique=True)
    year = models.DateField(auto_now=False, auto_now_add=False)
    length = models.IntegerField(null=True, blank=True)
    director_name = models.CharField(max_length=150, null=True, blank=True)
    language = models.CharField(max_length=100, null=True, blank=True)

    class Meta:
        db_table = "movie"

    def __str__(self):
        return self.title

```

您可以使用 Django 数据库 [inspectdb 工具](https://docs.djangoproject.com/en/3.1/howto/legacy-databases/)从遗留数据库生成模型。

```
python manage.py inspectdb > models.py

```

如果您正在将当前的应用程序堆栈迁移到 Django，这将非常方便。

以下是 AdonisJs 数据模型的一个示例:

```
'use strict'

const Model = use('Model')

class Movie extends Model {
}

module.exports = Movie

```

要创建此模型，请在项目根目录下的终端上运行命令:

`./ace make:model Post`

模型定义的其余部分将在迁移文件中完成。

### AdonisJs 和 Django 中的视图和模板

Django 使用 Django 模板语言(DTL)创建模板——MVT 中的“T”。这个模板引擎类似于 Jinja，允许您在模板 HTML 文件中编写 Python 代码，并支持文件分割。

它通常有一个基本模板文件，可以扩展该文件来创建其他页面布局。这是非常灵活的工作和表达。

以下是 Django 模板中的 Python 示例代码:

```
{% for party in parties%}

        <div class="form-check mb-2">
            <input class="form-check-input" type="radio" id="party" name="hello" value={{ party}}>
            <label for="{{party}}">{{ party}}</label>
        </div>

    {% endfor %}

```

像 Django 一样，AdonisJs 预装了 [Edge 模板引擎](https://edge.adonisjs.com/)。这是它看起来的一个例子。

```
@if(username)
  <h1> Hello {{ username }} </h1>
@else
  <h1> Hello anonymous </h1>
@endif

```

你有没有注意到它们中类似小胡子的语法？AdonisJs 和 DTL 以某种方式使用 mustache 语法来处理动态 JavaScript 内容——以 HTML 内插的形式。

Django 的视图提供了一个不同于传统 MVC 框架(如 Adonis、Laravel 等)中视图的功能。Django 的视图是您编写应用程序的业务逻辑的地方。

下面是 Django 视图的一个例子:

```
def index(request):
    users = User.objects.all()
    context = {"parties": users}
    return render(request, "user/index.html", context)

```

### AdonisJs 中的控制器

Django 没有控制器，但是 Adonis 有。对于 Django，如前所述，您将所有的业务逻辑都写在 **views.py** 文件中。但是 Adonis 有一个控制器来做它应该做的事情——响应 HTTP 请求，将任务委托给适当的服务，以及处理业务逻辑。

以下是 AdonisJs 控制器的一个示例:

```
const User = use('App/Model/User')

class UsersController {

  * index (request, response) {
    const users = yield User.all()
    yield response.sendView('users.list', {users: users.toJSON()})
  }

}

```

## 对 AdonisJs 和 Django 的 ORM 支持

Django 和 AdonisJs 最好的特性之一是它们的富于表现力的 ORM(对象关系映射器),允许您与数据库进行交互，而无需实际编写真正的特定于数据库的查询。

即使您决定更改数据库，也不需要重新编写整个查询。只需为特定的数据库安装数据库驱动程序，进行迁移，然后就可以了！你的应用程序又上线了。

Django 进一步允许您使用原始查询，以防您的查询过于复杂，无法适应现有的查询 API。

Adonis ORM 被称为 Lucid，它是用活动记录设计模式设计的，与 Django ORM 相同。Lucid 在内部使用[knex . js](http://knexjs.org/)——一个“包含电池”的 SQL 查询生成器，用于 [Postgres](https://www.postgresql.org/) 、 [MSSQL](https://www.microsoft.com/en-us/sql-server/sql-server-2019) 、 [MySQL](https://www.mysql.com/) 、 [MariaDB](https://mariadb.org/) 、 [SQLite3](https://sqlite.org) 、 [Oracle](https://www.oracle.com/index.html) 和 [Amazon Redshift](https://aws.amazon.com/redshift/) 。这意味着 Lucid 支持这些数据库。

Django ORM 还支持各种数据库，如 PostgreSQL、MariaDB、MySQL、Oracle 和 SQLite。

您可能已经注意到了我们之前是如何使用两种 ORM 在两种框架中查询用户表的。

又来了。对于 Lucid:

`const users = yield User.all()`

敬姜戈

`users = User.objects.all()`

## AdonisJs 和 Django 的文档

与 Django 不同，AdonisJs 提供了清晰的文档和用例示例。不要误解我，Django 的文档并不差，但是，在我看来，Adonis 做得更干净、更好。事实上，在文档的某些[部分，有视频可以帮助理解概念和实现。](https://preview.adonisjs.com/guides/http/routing)

## AdonisJs 和 Django 的学习曲线是怎样的？

这两个框架的学习曲线都可以被描述为是温和的。如果你已经知道 JavaScript/Node.js，并且熟悉 MVC 设计模式，那么学会 AdonisJs 并不困难。

对于 Django 来说，如果你已经熟悉 Python，启动并运行它并不困难。随着你不断地学习和建立，它会变得容易。

## 开发者社区

AdonisJS 有一个不断增长的开发人员社区，因为它是在 Django 创建(2003 年)后大约 13 年(2015 年)开始的。截至本文撰写之时，Django 在 GitHub 上拥有超过[5.5 万颗星星](https://github.com/django/django/stargazers)，而 Adonis 拥有大约[9.4 万颗星星](https://github.com/adonisjs/core/stargazers)。

## AdonisJs 和 Django 的安全

网络安全是一项艰巨的工作。但好消息是 AdonisJs 和 Django 都非常重视安全性。所以，它们被设计成安全的。让我们来看看它们的一些安全实现。

**1。)SQL 注入**:Django ORM 通过对用户控制的任何参数进行转义，恰当地处理了任何形式的 SQL 注入。这通常是使用 ORM 的一个好处，因为它会为您处理这个问题。这种行为也适用于 AdonisJs，因为它也使用适当的 ORM。

然而，正如我们之前看到的，如果您愿意，Django 允许您编写原始查询。但是，如果您不小心避开用户控制范围内的参数，您可能会为 SQL 注入打开大门。

**2。)跨站脚本(XSS)攻击等等** : Adonis 作为一个框架，有一个内置的[屏蔽中间件](https://adonisjs.com/docs/4.1/shield)，可以帮助你的应用避免大多数已知的攻击，比如 XSS、内容嗅探和脚本注入。它还分别实现了 [CORS](https://adonisjs.com/docs/4.1/cors) (跨产地资源共享)和 [CSRF](https://adonisjs.com/docs/4.1/csrf) (跨站请求伪造)中间件。

Django 也在内部实施了大部分安全措施。至于暴力攻击，Adonis 和 Django 默认都不实现这一点。你必须使用一个外部插件来做到这一点。

虽然该框架帮助您完成了应用程序安全性方面的大部分工作，但您还必须遵循最佳实践来确保您的应用程序保持安全，这一点很重要。

## AdonisJs 和 Django 中的身份验证

AdonisJS 和 Django 都有一个健壮的认证系统。您不再需要担心密码哈希或其他在开始新项目时通常会遇到的麻烦。

一般来说，AdonisJs 身份验证的功能更加丰富，因为它还提供了一个角色中间件，允许您有效地使用用户配置文件。对于 AdonisJs 能够在内部执行的一些任务，您必须使用像 Django Allauth 这样的外部库在 Django 中管理它们。

如果在你开始构建你的应用程序后，你想定制你的认证工作方式，Django 用户认证可能会给你带来一些麻烦，因为这需要一些复杂的工作来完成。

有一个自定义的用户模型，但通常在您开始一个新项目时使用。AdonisJs 对此更加灵活和宽容。

## 按指定路线发送

路由是每个框架的重要组成部分。AdonisJs 和 Django 有他们独特的处理路由和组路由的方式。

这是一条简单的 Django 路线:

```
urlpatterns = [ 
    path('books/', views.book_index),  
]

```

这是给 AdonisJs 的:

```
Route.get('posts', 'PostController.index')

```

如果您想对相似的路线进行分组，AdonisJs 为您提供了一个`Route.group` API:

```
// Grouped
Route.group(() => {
  Route.get('users', closure)
  Route.post('users', closure)
}).prefix('api/v1')

```

要用 Django 实现类似的东西，您必须使用 Django URL 名称空间。Django URLs 中的命名空间允许您用一个公共的 URL 名称或属性来标识一组 URL。

```
urlpatterns = [
  re_path('weblog/', include('blog.urls')),
  path('books/', views.book_index), 
]

```

`blog`应用程序中的所有 URL 都将具有属性`weblog`。

说到子域路由，AdonisJs 支持开箱即用。例如，我可以轻松地在我的 AdonisJs 应用程序中拥有一个类似于`eze.logrocket.com`的域。

就像下面这样简单:

```
Route.group(() => {
  Route.get('/', ({ subdomains }) => {
    return `The username is ${subdomains.user}`
  })
}).domain(':user.myapp.com')

```

Django 没有这个功能。如果您要构建一个多租户应用程序，这将是一个方便的特性，也是使用 AdonisJs 的一个好理由。

## 依赖性管理

因为 AdonisJs 运行在 Node.js 上，所以它使用 npm 包管理器来管理依赖项。因此，您可以在`package.json`文件中定义所有的依赖项。

Django 使用 Python 依赖管理文件`requirement.txt`来定义所有的应用依赖。

如果您希望从第一天开始使用 Django，Django 附带了一个内容管理系统，它完全设置了身份验证、权限和组权限。你只需要创建一个超级管理员密码。AdonisJs 没有这个特性。虽然 Django 包括所有的电池很棒，但我发现自己需要比我想的更多的东西。

## 结论

AdonisJs 和 Django 是两个非常强大的 web 开发框架。两者各有利弊。最终，它们只是工具，根据您的项目类型、兴趣和首选堆栈，您将决定最适合您的项目。

您将不得不接受这些缺点，可能会用社区中的其他工具或您将要构建的工具进行扩充，同时也享受这些框架的最佳部分。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.