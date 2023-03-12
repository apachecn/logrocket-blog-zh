# 洛达，路由树网络工具包:教程-日志火箭博客

> 原文：<https://blog.logrocket.com/roda-the-routing-tree-web-toolkit-a-tutorial/>

洛达是一个 Ruby web 框架，旨在将 Sinatra 和 Cuba 的精华引入 Ruby web 开发。洛达是围绕“路由树”的概念设计的

在 web 开发中，路由是接受请求，然后找到处理该请求的代码的过程。考虑如下所示的 HTTP 请求路径。

```
GET users/1/invoices 
```

当决定如何处理路由时，典型的 web 框架(例如 Ruby on Rails 或 Sinatra)会查看完整的请求路径，迭代一系列可能的路由来匹配请求。

另一方面，诸如洛达之类的路由树框架在继续之前检查请求路径的每一段。

在处理上述请求时，洛达查看了第一个片段`/users`。如果该段与请求不匹配，它会跳过整个`/users`分支，从而不考虑`/users`下的其他路由。如果`/users`匹配，它将在`/users`分支中查找`/1`。它继续这种方式，直到找到路线，或者如果路线不能匹配，返回一个`404`(未找到)状态。

## 为什么是洛达？

### 表演

由于路由处理不涉及路由数组的迭代，与其他 Ruby 框架相比，每个请求的开销要低得多。

![A graph comparing Roda's ability to serve more requests while using less memory as compared to other Ruby frameworks](img/f662cd97b4997b05ae4b2e01152e6398.png)

来源:洛达网站

### 可用性

路由树的有趣之处不仅在于性能，还在于在路由过程中的任何时候，您都可以对当前请求进行操作。

### 简单

洛达设计简单，内部和外部都是如此。它使用一个路由树来让你写更简单和干巴巴的代码。

### 可靠性

洛达几乎没有全球命名空间污染。路由树内部使用的所有实例变量都以下划线(`@_variable`)为前缀，因此它们不会与用户想要使用的实例变量冲突。此外，洛达为常数加上前缀`Roda::` ( `Roda::constant`)，以避免与应用程序中使用的常数冲突。

### 展开性

洛达通过广泛的插件支持是可扩展的。您可以覆盖洛达的任何部分，并调用`super`来获得默认行为。

## 简单的路由树

让我们从创建一个非常小的 web 应用程序开始，来理解使用洛达是什么样子的。洛达更像是一个 Ruby 库，而不是一个框架，并且没有用于执行常规任务的 CLI 实用命令。因此，与 Rails 和其他框架不同，它没有用于创建新项目、生成模型、控制器和其他命令重复任务的命令。

让我们创建一个目录来保存我们的项目文件。

```
mkdir my_roda_app 
```

然后我们添加一个`Gemfile`来管理我们将要使用的宝石。将`roda` gem 添加到文件中，然后添加`puma`，这将是我们的 web 应用服务器。我们的`Gemfile`会是这个样子。

```
source "https://rubygems.org"

gem "roda"
gem "puma" 
```

现在我们运行`bundle install`来安装我们添加的 gems。

```
bundle install 
```

大多数 Ruby web 框架都是建立在 Rack 之上的，根据 Rack 的[文档](https://github.com/rack/rack)，它是一个模块化的 Ruby web 服务器接口。

> Rack 为用 Ruby 开发 web 应用程序提供了一个最小的、模块化的、适应性强的接口。通过以尽可能简单的方式包装 HTTP 请求和响应，它将 web 服务器、web 框架和它们之间的软件(所谓的中间件)的 API 统一和提取到一个方法调用中。
> 
> 机架文档

洛达是机架兼容的，所以我们首先使用标准文件名`config.ru`创建一个机架备份文件。在其中我们需要`roda`，然后创建一个新的类来表示我们的应用程序。这个应用程序将继承自`Roda`类。

因为洛达是建立在框架之上的，所以从`Roda`继承的每个类都隐含地成为框架应用。为了告诉 Rack(和 web 服务器)为 HTTP 请求执行我们的应用程序，我们必须告诉`run`这个`App`类。

```
require "roda"

class App < Roda
  route do |r|
   "Hello World!"
  end
end

run App 
```

然后从命令行，我们运行`rackup`命令来启动 web 服务器并开始服务请求。

```
rackup 
```

打开浏览器，导航到 [http://127.0.0.1:9292](http://127.0.0.1:9292) 看到“Hello world！”

![](img/68930ea53942d2f467efe72b8d198e5f.png)

我们的应用程序在当前状态下返回“Hello world！”作为对每个请求路由的响应。让我们介绍一下洛达的路由方法，让应用程序返回相同的响应，但只针对特定的请求路径和方法。

## 洛达路由树方法

### `r.on`方法

第一种路由树方法是`r.on`，在路由树中创建分支。让我们将`r.on`添加到我们的示例应用程序中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
require "roda"

class App < Roda
  route do |r|
    r.on 'users' do
     "Hello Users!"
    end
  end
end

run App 
```

我们用字符串`users`调用了`r.on`，如果请求路径以`users`开头，它将匹配当前的请求路径。

`Roda.route`方法是路由树的开始。所有进入我们应用程序的请求都将让位于传递给`Roda.route`的块。该块产生了一个带有一些附加方法的路由请求实例。按照惯例，块自变量被命名为`r`(例如`|r|`)。传递给路由请求实例的附加方法(例如，`r.on`)用于路由请求。

路由方法接受称为匹配器的参数，用于匹配当前请求。在上面的例子中，我们将字符串匹配器`users`作为参数传递给`r.on`来创建`users`分支。

在洛达中，字符串匹配器通常匹配请求路径的第一段。因此，如果请求路径以`users`开头，它将匹配并被重定向到传入的块，后者返回“Hello Users！”洛达用作响应主体的字符串。

如果请求路径以`profile`开始(不匹配)，那么`r.on`返回`nil`而不向阻塞让步，执行将在调用后继续。如果不存在其他块，就像上面的例子一样，`Roda.route`将返回`nil`，洛达将返回一个带有空响应体的`404`状态代码。

### `r.is`方法

`r.on`将匹配所有以`users`开始的请求路径，包括`users/non-existent-user`。通常，这不是我们想要的。相反，我们希望为任何没有被特别处理的路径返回一个`404`响应。

这就是`r.is`派上用场的地方。只有当所有参数都匹配并且匹配后路径中没有其他条目时，它才匹配路径。换句话说，它最终确定了路由路径。

`r.is`将只匹配`users`而不匹配`users/non-existent-user`。洛达的路由是结合使用`r.on`和`r.is`方法完成的。`r.on`对请求路径进行前缀匹配，`r.is`对请求路径进行全匹配。因此，当`r.on`创建分支时，`r.is`创建叶节点。

让我们在例子中添加一个`r.on`。

```
require "roda"

class App < Roda
  route do |r|
    r.on 'users' do
      r.is "list" do
       "Hello Users!"
      end
    end
  end
end

run App 
```

我们的应用程序将匹配任何带有路径`/users/list`的请求。`r.on`创建一个处理`users`下所有路径的分支，`r.is`创建一个仅当当前路径是`list`时才匹配的节点。`r.is`能够匹配`list`，因为当请求被路由时，请求路径被修改。

当对`users/list`的请求进来时，路由树使用初始请求路径`users`。当`r.on "users"`请求方法匹配时，它从请求路径的前面消耗`users`。在`r.on`块中，请求路径是空的。

在下一个`r.is`块中，当前路径是`list`，并且只有当它的所有匹配器在整个请求路径上都一致时才会匹配。所以在这个例子中，请求将被匹配，并且“Hello Users！”会被归还。

### `r.get`

路由通常也会考虑请求方法。当`r.on`和`r.is`关注使用请求路径的路由时，`r.get`基于`GET`请求方法进行路由。如果在没有匹配器的情况下调用它，它会对请求方法进行简单的匹配。如果使用匹配器调用，将根据请求路径执行终端匹配。

```
require "roda"

class App < Roda
  route do |r|
    r.on 'users' do
      r.is "list" do
        r.get do
         "Hello Users!"
        end
      end
    end
  end
end

run App 
```

一个`GET users/list`请求将返回“你好，用户！”

### `r.post`

当`r.get`匹配`GET`请求时，`r.post`匹配`POST`请求。

```
require "roda"

class App < Roda
  route do |r|
    r.on 'users' do
      r.is "list" do
        r.get do
         "Hello Users!"
        end
        r.post do
         "User added"
        end
      end
    end
  end
end

run App 
```

一个`POST users/list`请求将返回“用户已添加”`r.get`和`r.post`用于处理相似请求路径上的各种请求方法。

## 路由期间的代码执行

洛达能够在路由过程中执行代码。这使得编写更简单、更简洁的代码变得容易，并且避免了代码重复。

如果您想在访问一条路线之前检查某人是否登录，您可以将检查登录的代码放在`Roda.route`块的第一行。这在处理同一请求路径的不同请求方法时很有用。例如，`GET`请求将找到一个用户并返回他们的详细信息，而`POST`请求将找到一个用户并更新他们的详细信息。

我们可以将查找用户的逻辑抽象为由两种请求方法共享的路由块，如下所示。

```
class App < Roda
  route do |r|
    require_login!
    r.on 'users' do
      r.is ":id" do |user_id|
        @user = User.find(user_id)
        r.get do
         @user
        end
        r.post do
         @user.update(params[:user])
        end
      end
    end
  end
end

run App 
```

## 结论

洛达的设计是轻量级的，只提供让你的应用程序快速运行的基本功能。所有附加功能都通过[插件](http://roda.jeremyevans.net/documentation.html#included-plugins)加载；洛达有超过 60 个内置插件，所有插件的质量都和洛达一样，所以你很少需要外部插件。

## 信用

[Github 上的杰雷米·埃文斯](https://github.com/jeremyevans/roda)
[ruby conf 2014–洛达:路由树 Web 框架由杰雷米·埃文斯](https://www.youtube.com/watch?v=W8zglFFFRMM&t=184s)
[掌握洛达](https://fiachetti.gitlab.io/mastering-roda/)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)