# 使用辛烷-日志火箭博客优化 Laravel 应用程序的快速指南

> 原文：<https://blog.logrocket.com/optimizing-laravel-apps-octane/>

Laravel 团队宣布在 2021 年发布 Laravel Octane，其目的是通过在 RAM 内存中缓存 Laravel 依赖容器的实例来减少请求/响应时间，从而提高 Laravel 应用程序的速度和性能。这个过程是由名为 [Swoole](https://openswoole.com/) 和 [RoadRunner](https://roadrunner.dev/) 的工具完成的。

在本帖中，我们将就如何使用 Octane 优化您的 Laravel 应用进行快速入门指导，同时考虑一项基准分析，以展示 RoadRunner、Swoole 和 Nginx 之间的性能差异。

*向前跳转:*

*   **Swoole** **:** Swoole 是一个 PHP 扩展，它帮助低级或传统的无状态模型(如事件循环和异步)提高 PHP 的性能。与用 Go 构建的 roadrunner 相比，Swoole 更受欢迎，因为它是一个 PHP 扩展
*   **road runner****:**road runner 是一个用 Go 编写的高性能 PHP 应用服务器、负载平衡器和进程管理器——它是一个二进制应用程序，需要在使用前安装
*   [**auto cannon**](https://github.com/mcollina/autocannon)**:**auto cannon 是一个用 Node.js 编写的 HTTP 基准测试工具，用于评估 web 应用的性能

## 为什么是 Laravel 辛烷？

[Laravel Octane](https://laravel.com/docs/9.x/octane) 是一个为带有 Swoole 或 RoadRunner 的 Laravel 应用程序提供服务的包，有助于提高性能。

传统的 Laravel 应用程序由 Apache、Nginx 和 Lighttpd 等 web 服务器提供服务，每个请求都会产生一个 PHP-FPM 工作器。这种方法导致在每次请求时创建进程和引导 Laravel 应用程序的开销，这被称为无状态方法，因为在每次请求时没有 PHP 进程被重用。

虽然 Swoole 和 RoadRunner 仍然对所有请求使用工作进程，但它们只服务于引导框架的第一个请求(依赖容器)，其他任何请求都来自框架的引导版本。

## 辛烷的优点

*   它提升了你的应用程序的性能
*   与传统的 Laravel 应用程序相比，它节省了资源

## 辛烷的挑战

*   代码更改可能是一个挑战，因为 Octane 将你的应用程序缓存在内存中，在浏览器刷新后，你的代码更改可能看不到，除非 Octane 正在运行或处于**手表**模式
*   由于应用程序在内存中运行，内存泄漏可能是另一个需要研究的挑战，因为所有数据都存储在内存中——尤其是静态和全局变量

## 设置 Laravel Octane 应用程序

既然我们已经详细了解了辛烷的组成和作用，让我们开始把它付诸行动吧。

以下是开始的方法:

```
❯ composer create-project laravel/laravel laravel-octane

❯ composer require laravel/octane

❯ php artisan octane:install

 Which application server you would like to use?:
  [0] roadrunner
  [1] swoole
 > 0

```

## 安装应用程序服务器

RoadRunner 或 Swoole 需要为您的应用提供服务，因为它们都是外部包，但大多数情况下，RoadRunner 将在选择应用服务器后安装。如果没有，请使用下面的命令手动安装:

```
composer require spiral/roadrunner

```

安装 Swoole 可能有点不同，因为它是一个 PHP 扩展，而不是一个包，需要的过程很少。以下命令用于安装它并开始安装过程:

```
pecl install swoole

```

(注意:我们不会介绍用 PHP 安装和设置 Swoole 的过程，但这里有一个简单的[指南](https://medium.com/@mfkhao2009/install-or-update-php-swoole-extension-by-make-from-source-code-on-mac-4810ee807831)来做这件事)

使用以下命令启动您的应用程序，然后导航到您的浏览器。

```
 php artisan octane:start

   INFO  Server running…

  Local: http://127.0.0.1:8000

```

您还可以根据您环境的 CPU 线程指定使用哪种技术/服务器以及运行多少线程，如下所示。

```
php artisan octane:start --workers=4 --server=roadrunner

```

## 使用 AutoCannon 对应用服务器进行基准测试

本文中使用的项目是一个简单的页面，其中包含一些数据。我们将在测试的每个阶段更换应用服务器(RoadRunner、Swoole 和 Nginx)，以便通过使用 AutoCannon 来评估和比较每个服务器的性能。

这个过程将帮助您决定哪个应用服务器最适合您的项目。

AutoCannon 能够产生大量的流量，即使在单个多核 CPU 上运行也是如此；我们将使用 100 个并发连接、10 个管道连接和 3 个工作线程运行基准测试 10 秒钟来触发请求。

### **Swoole**

```
❯ autocannon http://127.0.0.1:8000 -d 10 -w 3 -c 100 -p 10
Running 10s test @ http://127.0.0.1:8000
100 connections with 10 pipelining factor
3 workers

/
┌─────────┬────────┬─────────┬─────────┬─────────┬────────────┬───────────┬─────────┐
│ Stat    │ 2.5%   │ 50%     │ 97.5%   │ 99%     │ Avg        │ Stdev     │ Max     │
├─────────┼────────┼─────────┼─────────┼─────────┼────────────┼───────────┼─────────┤
│ Latency │ 201 ms │ 1773 ms │ 3175 ms │ 3304 ms │ 1854.07 ms │ 657.15 ms │ 4201 ms │
└─────────┴────────┴─────────┴─────────┴─────────┴────────────┴───────────┴─────────┘
┌───────────┬─────┬──────┬─────────┬─────────┬─────────┬─────────┬─────────┐
│ Stat      │ 1%  │ 2.5% │ 50%     │ 97.5%   │ Avg     │ Stdev   │ Min     │
├───────────┼─────┼──────┼─────────┼─────────┼─────────┼─────────┼─────────┤
│ Req/Sec   │ 0   │ 0    │ 503     │ 576     │ 475.3   │ 166.71  │ 440     │
├───────────┼─────┼──────┼─────────┼─────────┼─────────┼─────────┼─────────┤
│ Bytes/Sec │ 0 B │ 0 B  │ 4.13 MB │ 4.73 MB │ 3.91 MB │ 1.37 MB │ 3.62 MB │
└───────────┴─────┴──────┴─────────┴─────────┴─────────┴─────────┴─────────┘

Req/Bytes counts sampled once per second.
# of samples: 30

6k requests in 10.02s, 39.1 MB read

```

### **走鹃**

```
❯ autocannon http://127.0.0.1:8000 -d 10 -w 3 -c 100 -p 10
Running 10s test @ http://127.0.0.1:8000
100 connections with 10 pipelining factor
3 workers

-
┌─────────┬────────┬─────────┬─────────┬─────────┬────────────┬───────────┬─────────┐
│ Stat    │ 2.5%   │ 50%     │ 97.5%   │ 99%     │ Avg        │ Stdev     │ Max     │
├─────────┼────────┼─────────┼─────────┼─────────┼────────────┼───────────┼─────────┤
│ Latency │ 119 ms │ 1692 ms │ 2314 ms │ 2587 ms │ 1617.82 ms │ 574.62 ms │ 3153 ms │
└─────────┴────────┴─────────┴─────────┴─────────┴────────────┴───────────┴─────────┘
┌───────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐
│ Stat      │ 1%      │ 2.5%    │ 50%     │ 97.5%   │ Avg     │ Stdev   │ Min     │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┤
│ Req/Sec   │ 366     │ 366     │ 544     │ 861     │ 546.3   │ 124.68  │ 366     │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┤
│ Bytes/Sec │ 3.01 MB │ 3.01 MB │ 4.47 MB │ 7.08 MB │ 4.49 MB │ 1.02 MB │ 3.01 MB │
└───────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘

Req/Bytes counts sampled once per second.
# of samples: 30

6k requests in 10.02s, 44.9 MB read

```

要通过 Nginx 对应用进行基准测试，我们需要设置 Laravel [Valet](https://laravel.com/docs/9.x/valet) ，然后继续运行相同的命令；但是在这种情况下，我们使用`127.0.0.1`，因为它运行在端口`80`上。

```
❯ autocannon http://127.0.0.1 -d 10 -w 3 -c 100 -p 10
Running 10s test @ http://127.0.0.1
100 connections with 10 pipelining factor
3 workers

/
┌─────────┬────────┬────────┬────────┬────────┬───────────┬─────────┬────────┐
│ Stat    │ 2.5%   │ 50%    │ 97.5%  │ 99%    │ Avg       │ Stdev   │ Max    │
├─────────┼────────┼────────┼────────┼────────┼───────────┼─────────┼────────┤
│ Latency │ 111 ms │ 169 ms │ 202 ms │ 235 ms │ 166.22 ms │ 23.1 ms │ 290 ms │
└─────────┴────────┴────────┴────────┴────────┴───────────┴─────────┴────────┘
┌───────────┬─────────┬─────────┬─────────┬─────────┬─────────┬────────┬─────────┐
│ Stat      │ 1%      │ 2.5%    │ 50%     │ 97.5%   │ Avg     │ Stdev  │ Min     │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼────────┼─────────┤
│ Req/Sec   │ 4551    │ 4551    │ 5691    │ 6343    │ 5718.8  │ 464.3  │ 4548    │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼────────┼─────────┤
│ Bytes/Sec │ 2.13 MB │ 2.13 MB │ 2.67 MB │ 2.98 MB │ 2.68 MB │ 218 kB │ 2.13 MB │
└───────────┴─────────┴─────────┴─────────┴─────────┴─────────┴────────┴─────────┘

Req/Bytes counts sampled once per second.
# of samples: 32

0 2xx responses, 62950 non 2xx responses
64k requests in 10.01s, 29.5 MB read

```

## 结论

根据 bechmark 的分析，您会注意到 Nginx 总共运行了 64k 的请求，这比 Swoole 和 RoadRunner 发出的请求都要多得多，大约是 12k 的**，或者说每个都是 6k。**

总之，我认为在这种情况下 Swoole 和 RoadRunner 是更好的选择，但这并不意味着 Nginx 或其他 web 服务器(如 Apache 和 lighttpd)不应该被考虑，因为它们仍然被许多人用于为数百万个拥有大量并发用户的网站提供服务，并且仍然是很好的选择。

感谢您的阅读，请告诉我您对 Laravel Octane(或其他 web 服务器)的看法，以及您喜欢在自己的项目中使用哪些服务器。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)