# Laravel vs. AdonisJs:应该用哪个？

> 原文：<https://blog.logrocket.com/laravel-vs-adonis-js-which-should-you-use/>

Laravel 是一个流行的基于 MVC 的框架，它拥有创建 web 应用程序所需的一切。在身份验证、路由、数据库迁移、模型、助手等方面，它将为您提供全面的帮助。然而，对于小型或简单的应用程序来说，这可能有些过头了。

另一方面，AdonisJs 也是构建在 Node.js 之上的基于 MVC 的框架。此外，它高度关注开发人员的人体工程学、稳定性和信心。它基于与 Laravel 相同的概念。受 Laravel 的启发，Providers 和 Dependency Injection 之类的好东西允许更好的灵活性和代码组织。

在本文中，我们将对 Laravel 和 AdonisJs 进行比较。我们将分析每个框架的设置、视图、ORM、路由、数据库、CLI、IOC 和服务提供商、文件系统、社区优势和性能。

## 设置

Laravel 安装简单。使用 Homestead——一个开发环境——每一个需求都解决了。但是，如果您不想使用 Homestead，您必须确保您的服务器支持它的一些要求。其中包括 Tokenizer PHP 扩展、PDO PHP 扩展、JSON PHP 扩展等等。

`laravel new`将在您指定的目录中创建全新的安装:

```
laravel new project
```

或者，您可以通过 Composer Create-Project 创建新的安装:

```
composer create-project --prefer-dist laravel/laravel project
```

Laravel 允许自由配置，并且不强制项目结构，这使得它很灵活。安装后，您可以使用以下命令运行应用程序:

```
php artisan serve
```

阿多尼斯也很好设置。安装是通过 Adonis CLI 完成的，这是一个帮助您安装或运行 Adonis 命令的工具。必须首先全局安装 CLI:

```
npm i -g @adonis/cli
```

安装完成后，您可以使用`adonis new`命令在您指定的目录中进行新的安装:

```
adonis new project
```

另一种方法是通过 Git，这需要从 Github 克隆 repo:纯 API、全栈和精简样板。

安装完成后，启动应用程序并开始开发:

```
adonis serve --dev
```

### 视图

Laravel 有一个强大的模板引擎，叫做 Blade。它与 Twig 非常相似。真正的好处是直接在视图中使用 PHP 代码，这意味着当发出请求时，编译成 PHP 代码的刀片视图在服务器中处理:

```
<!-- Stored in resources/views/layouts/app.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

Adonis 自带 Edge 模板引擎，速度超快。它支持 Chrome DevTools 中的运行时调试，使用逻辑标签、布局和部分以及组件风格的文本重要性的物理标签。就像 Blade 一样，您也可以在视图中编写 JavaScript:

```
@if(hours < 12)
  <h2> Good Morning </h2>
@elseif(hours < 18)
  <h2> Good Afternoon </h2>
@else
  <h2> Good Evening </h2>
@endif
```

### ORM

Laravel 带有雄辩的 ORM，它观察[活动记录模式](https://en.wikipedia.org/wiki/Active_record_pattern)。与普通的数据库查询不同，这提供了高效而强大的 API 来驱动应用程序的数据流。它因其对应用程序数据库的组织而受人喜爱。它还支持最流行的数据库(MySQL、Postgre、SQLite 等)。

创建一个雄辩的模型，它与您的数据库表进行通信。模型允许您通过插入或查询与表进行交互。

通过使用`artisan`命令，我们可以更快地创建模型:

```
php artisan make:model User
```

在模型中，你可以设置你的约定等等。

创建用户的工作方式如下:

```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Create a new user instance.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        // Validate the request...

        $user = new User;

        $user->name = $request->name;

        $user->username = $request->username;

        $user->email = $request->email;

        $user->password = $request->password;

        $user->save();
    }
}
```

一个美妙的事情是它管理关系的 API:

```
return $this->hasOne('App\Models\Phone', 'foreign_key');
```

Adonis 附带了 Lucid ORM，它也使用活动记录模式。它附带了日期格式管理、生命周期挂钩，如密码散列、数据序列化、数据转换的 getter/setter 等等。

创建一个模型几乎和雄辩的 ORM 一样。如果需要，约定也很有用:

```
adonis make:model User
```

```
'use strict'

const Model = use('Model')

class User extends Model {
}

module.exports = User
```

创建用户与雄辩的 ORM 没有什么不同:

```
const User = use('App/Models/User')

const user = new User()

user.username = 'virk'
user.password = 'some-password'

await user.save()
```

它还有一个表达性的 API 来管理关系。

```
hasOne(relatedModel, primaryKey, foreignKey)
```

### 按指定路线发送

像 Laravel 一样，Adonis 接受基本的路由文件作为 URL 和闭包，这是定义路由的一种非常简单的方式:

```
Route.get('posts/:id', ({ params }) => {
  return `Hello  ${params.id}`
})
```

另一种最常用的启用路由的方法是将控制器插入到路由文件中的端点。route 文件在您的 start 文件夹下，不像 Laravel，它存储在项目的根文件夹中。

拉勒维尔

```
Route::get('/user', [UserController::class, 'index']);
```

**AdonisJs** 

```
Route.get('users', 'UserController.index').as('users.index')
```

这两个框架还对端点进行分组，并分配与端点相关联的 HTTP 动词。

拉勒维尔

```
Route::prefix('admin')->group(function () {
    Route::get('users', function () {
        // Matches The "/admin/users" URL
    });
});
```

**AdonisJs**

```
Route.group(() => {
  Route.get('users', closure)   // GET /api/v1/users
  Route.post('users', closure)  // POST /api/v1/users
}).prefix('api/v1')
```

### 数据库ˌ资料库

目前，Laravel 支持 4 个数据库:MySQL、PostgreSQL、SQLite 和 SQL Server。您的数据库的所有配置都位于`config/database.php`。该文件中定义了数据库连接，包括默认连接。

Laravel 支持像版本控制一样的迁移。这个约定允许编写数据模型更改。

就像 Laravel 一样，AdonisJs 支持迁移和播种。播种有助于用数据测试表。

AdonisJs 支持六种数据库，分别是 MySQL、PostgreSQL、MariaDB、SQLite3、MSSQL 和 Oracle。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 硬币指示器 （coin-levelindicator 的缩写）命令行界面（Command Line Interface for batch scripting）

AdonisJs 和 Laravel 带着他们自己的`CLI`从盒子里出来。这使得开发人员可以针对特定任务运行命令，从运行迁移、生成控制器、模型和播种数据库到创建自己的命令。

Laravel 的命令工具叫 Artisan，AdonisJs 的命令工具叫 Ace。

### 国际奥委会和服务提供商

依赖关系在任何应用程序中都很难管理。他们可以是独立的，也可以是从属的。如果处理不当，它们可能会导致错误。Adonis 使用 IoC 解决了这个问题，而 Laravel 称之为服务容器，它通常管理类依赖并执行依赖注入。

服务提供者处理中间件、事件监听器、路由和服务容器绑定的注册。大多数情况下，您将注册您的中间件，以便在您的应用程序中使用。这种模式适用于 Laravel 和 AdonisJs。

### 文件系统

AdonisJs 使用其专用的名为 FlyDrive 的驱动提供程序(必须在服务提供程序中注册)。这有助于本地和远程文件系统的交互，比如亚马逊 S3。

从`npm`中取出我们的驱动器是必须的，因为它不是默认安装的:

```
adonis install @adonisjs/drive
```

接下来，我们在服务提供商处注册它:

```
const providers = [<br> '@adonisjs/drive/providers/DriveProvider'<br>]
```

Laravel 使用 FlySystem 作为其 API，甚至可以灵活地在本地和远程文件系统(亚马逊 S3)之间切换，因为 API 保持不变。

### 社区力量

到目前为止，AdonisJs 的社区还很弱，这意味着它非常小。仅此一点就导致 StackOverflow 上投稿人少，回答少。大多数问题永远找不到，所以你可能需要深入挖掘。

同时，两个框架的文档都很棒，并且解释得很好。它们涵盖了一切，对有经验的和新的开发人员非常有帮助。

### 表演

Laravel 和 AdonisJs 都非常快，在大多数情况下，您的 web 应用程序部署到服务器时会很快。如果您觉得您的应用程序的响应时间很慢，那么没有必要进行比较。相反，请进行以下工作:

*   移除未使用的包
*   使用 AdonisJs 或 Laravel 的最新版本
*   缓存路由器
*   缓存配置文件
*   缓存视图
*   减少或减少视图。
*   使用快速加载
*   索引最常用的数据库表列
*   以及更多优化技巧

有了这些，我们就不用进行性能比较了。

## 结论

在本文中，我们研究了 Laravel 和 AdonisJs 之间的基本区别，并强调了它们的最佳特性。总的来说，没有一个真的比另一个更好——它们都实现了预期的目标，这完全取决于您的用例。

此外，请记住，相似之处包括路由、迁移、模型、控制器、中间件、配置文件和. env。除此之外，两者的速度和性能都相当。

对于 Node.js 开发人员来说，AdonisJs 可能是一个有趣的 MVC 工具。对于那些想从 PHP 迁移到 Node.js 的人来说，那么 AdonisJs 是正确的选择。

最好记住这两个框架都是健壮的，并且能够解决需要解决的问题。两者都是开源的。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.