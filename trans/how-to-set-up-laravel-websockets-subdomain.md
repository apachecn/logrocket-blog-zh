# 如何在子域名- LogRocket 博客上设置 Laravel WebSockets

> 原文：<https://blog.logrocket.com/how-to-set-up-laravel-websockets-subdomain/>

WebSockets 是许多现代 web 应用程序的重要组成部分。使用 WebSockets，您可以创建实时更新的应用程序，而无需重新加载页面或不断轮询服务器的更改。

Laravel 支持两种开箱即用的服务器端广播驱动:Pusher 和 Ably。虽然这两种解决方案可能更容易设置，并且可以提供额外的功能，但它们都是商业解决方案。如果您预算紧张，或者您只是在寻找一个开源解决方案，这就使得它们不理想。

Laravel WebSockets 是一个很好的开源替代品。它允许您轻松地将 WebSocket 支持添加到 Laravel >5.7 应用程序中。它带有一个调试仪表板和实时统计数据，以及其他功能。

在本教程中，我们将讨论如何在子域上设置 Laravel WebSockets。具体来说，我们将讨论以下主题:

## Laravel WebSockets 与 Pusher 的关系

在我们开始学习如何在一个子域上设置和使用 Laravel WebSockets 之前，让我们回顾一下 Laravel WebSockets 和 Pusher 之间关系的一些背景信息，以防止任何混淆。

使用 [Laravel WebSockets](https://github.com/beyondcode/laravel-websockets) 时，您应该将 Laravel 的`BROADCAST_DRIVER`配置设置为`pusher`。您还需要设置其他`pusher`配置，如`PUSHER_APP_ID`、`PUSHER_APP_KEY`和`PUSHER_APP_SECRET`，并安装`pusher/pusher-php-server` composer 包。

这使得这个包看起来像一个 Pusher 软件开发工具包(SDK ),需要 Pusher 凭证才能工作。但事实并非如此。使用 Laravel WebSockets 实际上不需要 Pusher 凭证！

Laravel WebSockets 背后的主要思想是替换 Pusher 驱动程序，而不让 Laravel 框架知道这个替换。

当您使用 Laravel WebSockets 时，Laravel 认为您正在使用 Pusher 驱动程序，并将启用其功能。然而，在幕后，这些功能已经被 Laravel WebSockets 包所取代。

最重要的是，这个包用对我们自己的服务器的调用代替了对 Pusher 服务器的调用，然后包在那里处理请求。它还实现了推送消息协议。因此，所有现有的支持 Pusher 的包和应用程序也可以使用这个包。

## 项目和领域结构

如果你有一个标准的 Laravel 设置，其中前端和后端都在一个根文件夹中，你可能在设置这个包时会少一些麻烦。浏览一下[包文档](https://beyondco.de/docs/laravel-websockets/getting-started/installation)应该能让你很快上手并运行。

如果你的应用程序的前端和后端是完全分离的，你必须做一些不同的设置。这种设置通常需要两个独立的存储库或至少两个独立的文件夹。以`awesomeapp-backend`和`awesomeapp-frontend`为例。

然后，您需要设置两个子域来指向每个文件夹，如下所示:

*   `awesomeapp.test`或`app.awesomeapp.test`可以指向你的前端文件夹`awesomeapp-frontend`
*   `api.awesome.test`可以指向你的后台文件夹`awesomeapp-backend`

当你有这样的设置时，你最有可能要处理的一件事就是 CORS。

### 处理 CORS

一个网页可以自由地加载来自不同来源的图像、样式表、脚本、iframes 和视频。然而，某些跨域请求，比如 AJAX 请求，默认情况下被所有主流 web 浏览器实现的同源安全限制所禁止。

[跨源资源共享(CORS)定义了一种方式来指定该限制的例外](https://blog.logrocket.com/the-ultimate-guide-to-enabling-cross-origin-resource-sharing-cors/)。这是一种机制，在这种机制中，浏览器和服务器可以进行交互，以确定允许跨来源请求是否安全。

### CORS 如何影响我们？

那么这对我们意味着什么呢？嗯，我们的子域`app.awesomeapp.test`和`api.awesome.test`算作不同的起源，尽管有相同的域主机。

因此，当我们在前端子域`app.awesomeapp.test`中设置`laravel-echo`时，库将尝试向后端子域`api.awesomeapp.test`发出一个 AJAX 请求。因为它们会被浏览器视为独立的源，所以请求会失败…

…除非我们启用 CORS。谢天谢地，在拉勒维尔启用 CORS 是相当容易的。

## 在拉勒维尔启用 CORS

要为我们的 Laravel WebSocket 服务器配置 CORS，从您的 Laravel 安装的`config`文件夹中打开`cors.php`配置文件。将拉勒维尔的`broadcasting/auth`路线添加到`paths`数组中，并将`supports_credentials`选项设置为`true`。

在底层，`supports_credentials`选项将应用程序的 [`[Access-Control-Allow-Credentials]`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Credentials) 头设置为值`true`:

```
// config/cors.php
return [
    'paths' => ['api/*', 'sanctum/csrf-cookie', 'broadcasting/auth'],
    // ...
    'supports_credentials' => true,
];

```

或者，您可以将`Broadcast::routes`方法调用放在您的`routes/api.php`文件中。这将为`broadcasting/auth`路由添加前缀`api/`，消除了将路由添加到`paths`数组的需要，因为默认情况下`api/*`路径(每个带有`api`前缀的路由)都在数组中。

当调用`Broadcast::routes`方法时，您应该指定向通道路由发出请求时要使用的中间件。如果你使用拉勒维尔圣地，这可能是`auth:api`或`auth:sanctum`。

```
// routes/api.php
Broadcast::routes(['middleware' => ['auth:sanctum']]);

```

```
// config/cors.php
return [
    'paths' => ['api/*', 'sanctum/csrf-cookie'],
    // ...
    'supports_credentials' => true,
];

```

另外，确保来自前端的 HTTP/AJAX 请求将`XMLHttpRequest.withCredentials`选项设置为`true`。如果您使用的是`axios`，这可以通过在应用程序的全局`axios`实例上设置`withCredentials`选项来实现，如下面的代码所示:

```
axios.defaults.withCredentials = true;

```

最后，您应该确保可以从根域的任何子域访问您的应用程序的会话 cookie。这可以通过在您的应用程序的`config/session.php`配置文件中使用前导`.`作为域的前缀来实现。

```
'domain' => '.example.com',

```

为了使配置具有环境竞争性，您应该使用`.env`文件中的`SESSION_DOMAIN`环境变量来设置该选项:

```
// config/session.php
'domain' => env('SESSION_DOMAIN', null),

```

```
# .env
# ...
SESSION_DOMAIN=.example.com

```

现在，让我们继续设置 Laravel WebSockets！

## 安装`laravel-websockets`

Laravel WebSockets 可以使用 composer 安装。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
composer require beyondcode/laravel-websockets

```

这个包附带了一个迁移来存储关于 WebSocket 服务器的统计信息。如果您不打算使用此功能，可以跳过这一步。

运行以下命令发布迁移文件:

```
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="migrations"

```

然后，运行迁移:

```
php artisan migrate

```

接下来，通过运行以下命令发布 Laravel WebSocket 配置文件:

```
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="config"

```

正如我们所讨论的，Laravel WebSockets 包与 Pusher 结合使用，因此我们还需要安装官方的 Pusher PHP SDK:

```
composer require pusher/pusher-php-server "~3.0"

```

确保使用 Pusher 作为您的广播驱动器。这可以通过在您的`.env`文件中设置`BROADCAST_DRIVER`环境变量来实现:

```
BROADCAST_DRIVER=pusher

```

最后，确保在您的`.env`文件中设置了`APP_NAME`、`PUSHER_APP_ID`、`PUSHER_APP_KEY`和`PUSHER_APP_SECRET`环境变量。

**注意**，你设置什么样的`PUSHER_`变量并不重要，只要确保它们对每个项目都是唯一的就行了。

```
PUSHER_APP_ID=pusherid
PUSHER_APP_KEY=pusherkey
PUSHER_APP_SECRET=pushersecret
PUSHER_APP_CLUSTER=pushercluster

```

## Laravel WebSockets 配置

如前所述，当从您的 Laravel 应用程序广播事件时，Pusher 驱动程序的默认行为是将事件信息发送到官方 Pusher 服务器。但是由于 Laravel WebSockets 包自带 Pusher API 实现，我们需要告诉 Laravel 将事件信息发送到我们自己的服务器。

我们通过向我们的`config/broadcasting.php`文件的`pusher`部分添加`host`和`port`配置密钥来实现这一点。Laravel WebSocket 服务器的默认端口是`6001`。

```
'pusher' => [
    'driver' => 'pusher',
    'key' => env('PUSHER_APP_KEY'),
    'secret' => env('PUSHER_APP_SECRET'),
    'app_id' => env('PUSHER_APP_ID'),
    'options' => [
        'cluster' => env('PUSHER_APP_CLUSTER'),
        'encrypted' => true,
        'host' => '127.0.0.1',
        'port' => 6001,
        'scheme' => 'http'
    ],
],

```

## 配置 WebSocket 应用程序

Laravel WebSockets 使用`apps`的概念来支持开箱即用的多租户。这允许您将包与当前的 Laravel 应用程序分开托管，并使用一台服务器为多个 WebSocket 应用程序提供服务。

默认应用程序使用您现有的推送器配置。这应该适用于大多数用例。

```
'apps' => [
    [
        'id' => env('PUSHER_APP_ID'),
        'name' => env('APP_NAME'),
        'key' => env('PUSHER_APP_KEY'),
        'secret' => env('PUSHER_APP_SECRET'),
        'enable_client_messages' => false,
        'enable_statistics' => true,
    ],
],

```

确保使用您的广播配置部分中相同的`id`、`key`和`secret`，否则来自 Laravel 的广播事件将无法工作。

### 客户端消息

通常，所有 WebSocket 消息在广播给其他用户之前都要经过您的 Laravel 应用程序。但是有时您可能希望将事件直接从一个客户端发送到另一个客户端，而不经过服务器。

例如，以聊天应用程序中的打字事件为例。您可以使用`enable_client_messages`键为您的`config/websockets.php`配置文件中的每个应用程序配置该选项。

您应该小心使用该功能，因为这些事件消息来自其他用户，可能会被篡改。

### 统计数字

如前所述，Laravel WebSockets 包附带了一个仪表板，用于监控有关 WebSocket 服务器的统计信息。要为您的任何应用启用或禁用此功能，您可以修改`enable_statistics`选项。

## 启用 Laravel 中的事件广播

要在 Laravel 中启用事件广播，您需要注册`App\Providers\BroadcastServiceProvider`。您可以通过将`App\Providers\BroadcastServiceProvider::class`添加到您的`config/app.php`文件的`providers`数组中来做到这一点:

```
'providers' => [
   // ...
    App\Providers\BroadcastServiceProvider::class,
    // ...
],

```

在新的 Laravel 应用程序中，这个提供者已经在数组中了，您只需要取消对它的注释。
`BroadcastServiceProvider`包含注册广播授权路由和回调所需的代码。

如果您之前在`routes/api.php`文件中添加了`Broadcast::routes`方法，那么您可以放心地在`BroadcastServiceProvider`的`boot`方法中注释或删除这一行。

```
public function boot()
{
    // Broadcast::routes();
    require base_path('routes/channels.php');
}

```

后端就这么多了，现在我们来设置前端 app 吧！

## 设置 Laravel 回声

Laravel Echo 是一个 JavaScript 库，它使得订阅频道和收听由 Laravel 服务器端广播驱动程序广播的事件变得非常容易。在这种情况下，它是 Laravel Websockets 的 Pusher API 实现。

Echo 可以通过 npm 软件包管理器轻松安装。我们还将安装`pusher-js`,因为 Laravel Websockets 包使用推送通道广播器:

```
npm install --save-dev laravel-echo pusher-js

```

[为了让 Laravel Echo 与 Laravel WebSockets 一起工作](https://blog.logrocket.com/whats-new-laravel-9/)，我们需要注意初始化 Laravel Echo 时的一些配置选项。具体来说，我们需要添加`wsHost`和`wsPort`参数，并将它们指向我们的 Laravel WebSocket 服务器主机和端口。

为了让 Laravel WebSocket 授权请求成功，我们还需要使用`authEndpoint`提供一个定制的身份验证端点，并使用`auth.headers.Authorization`提供一个授权头。

当省略`authEndpoint`选项时，Laravel Echo 将尝试使用运行它的同一台主机进行身份验证。但是在我们的例子中，由于 WebSocket 服务器在不同的子域上，请求将会失败。

**注意**，如果你的`Broadcast::routes`方法调用在`App\Providers\BroadcastServiceProvider`文件中，这是默认的，你的`authEndpoint`的值应该类似于:`[http://api.awesomeapp.test/broadcasting/auth](http://api.awesomeapp.test/broadcasting/auth)`。

如果您将方法调用放在了`routes/api.php`文件中，那么它应该类似于:`[http://api.awesomeapp.test/api/broadcasting/auth](http://api.awesomeapp.test/api/broadcasting/auth)`。

```
import Echo from "laravel-echo"
window.Pusher = require('pusher-js');

window.Echo = new Echo({
  broadcaster: 'pusher',
  key: 'pusherkey',
  wsHost: 'api.websockets.test',
  wsPort: 6001,
  // wssPort: 6001,
  forceTLS: false,
  // encrypted: true,
  disableStats: true,
  auth: { headers: { Authorization: 'Bearer sometoken' } },
  authEndpoint: 'http://api.awesomeapp.test/api/broadcasting/auth', // OR
  // authEndpoint: 'http://api.awesomeapp.test/broadcasting/auth',
})

```

或者，您可以在初始化 Laravel Echo 时配置一个自定义授权器。这允许您配置 Laravel Echo 来使用您的全局`axios`实例，该实例应该为跨域请求正确配置，并具有正确的授权头。

```
import Echo from "laravel-echo"
window.Pusher = require('pusher-js');

window.Echo = new Echo({
  broadcaster: 'pusher',
  key: 'pusherkey',
  wsHost: 'api.awesomeapp.test',
  wsPort: 6001,
  // wssPort: 6001, // For SSL
  forceTLS: false,
  // encrypted: true, // For SSL
  disableStats: true,
  authorizer: (channel, options) => {
    return {
      authorize: (socketId, callback) => {
        axios
          .post('/api/broadcasting/auth', {
            socket_id: socketId,
            channel_name: channel.name,
          })
          .then((response) => {
            callback(false, response.data)
          })
          .catch((error) => {
            callback(true, error)
          })
      },
    }
  },
})

```

确保`key`的值与后端配置中的`PUSHER_APP_KEY`的值相同。

默认情况下，Pusher JavaScript 客户端试图发送统计信息，但是我们可以使用`disableStats`选项禁用它。

将 Laravel WebSockets 与自定义 SSL 证书结合使用时，一定要使用`wssPort`选项，而不是`wsPort`。同样，使用`encrypted`选项并将其设置为`true`。

就是这样！现在，您可以将 Laravel Echo 特性与 Laravel WebSockets 结合使用。

## 结论

在本文中，我们学习了所有关于 Laravel WebSockets 的知识。我们讨论了包和推送器之间的关系，如何在处理多个子域时处理 CORS，以及如何在我们的应用程序中设置 Laravel WebSockets。我们还学习了如何设置 Laravel Echo 来处理 Laravel WebSockets，尤其是在处理多个子域时。

虽然`laravel-websockets`包的主要目的是使 Pusher JavaScript 客户端或 Laravel Echo 尽可能易于使用，但是您并不局限于这个用例或 Pusher 协议。

要了解更多关于如何在其他用例中使用该包的信息，请查看 Laravel WebSockets 文档。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)