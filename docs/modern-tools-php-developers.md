# PHP 开发人员的现代工具

> 原文：<https://blog.logrocket.com/modern-tools-php-developers/>

当讨论 PHP 时，回避房间里的大象是没有意义的；PHP 曾经是一个非常平庸的工具，这可能是一种保守的说法。事实上，我最近看到一篇文章，讨论了为什么人们不喜欢 PHP 的问题。然而，以我个人的经验，开发人员经常混淆他们认为的 PHP 和它实际上是什么。

事实是 PHP 的名声是从某个地方来的。但是 PHP v4 和更早版本的黑暗时代已经过去了。如果你没有惊恐地尖叫着跑开，你就会看到 PHP 的发展，它确实发展了。

在本文中，我将介绍现代 PHP 开发环境是什么样的，回顾语言本身以及作为补充的可用工具。随着当今 web 开发的复杂性，你不能孤立地判断语言。您需要了解所有帮助您创建高质量软件的卫星技术。

在这篇文章的结尾，你可能会对 PHP 有多糟糕有所反思。然而，如果你是 PHP 的粉丝，你会有更多的理由来捍卫你的选择。我们开始吧！

## 什么是现代 PHP？

在我们深入了解 PHP 的伟大之处之前，让我们先建立一个现代 PHP 的定义。在撰写本文时，PHP v8.1.0 刚刚问世，[PHP 基础即将成为现实](https://opencollective.com/phpfoundation)，PHP v5.6 即将走到生命的尽头。所以，当我提到现代 PHP 时，我指的是 v7 和更高版本。

自从在 v5.0 中重写以来，该语言及其工具的发展令人印象深刻。PHP v5.0 标志着 PHP 历史上的一个转折点，将它带入了真正的面向对象语言的领域。

另一个不连续的飞跃是引入了 [Composer](https://getcomposer.org/) ，一个 PHP 依赖项管理器，这无疑在业余和专业开发之间划了一条线。但是，我有点超前了，我们稍后将深入讨论这个问题。让我们回顾一下在最近几个版本中对 PHP 的一些主要改进。

## 从≥v7.x 开始的 PHP 语言改进

自 2015 年 12 月 3 日发布的 [PHP v7.0](https://www.php.net/releases/index.php) 以来，引入了几个令人兴奋的新功能，如类型声明、内置加密、支持复杂数据结构、命名参数和属性。

语法也经历了一些强大的改进，比如[箭头函数](https://www.php.net/manual/en/functions.arrow.php)、宇宙飞船操作符和零合并。每个新版本都比前一个版本有重大的性能改进。

对于三四个版本之前就离开 PHP 的人来说，这些新特性可能会让他们感到非常震惊。为了获得这些伟大特性的大部分，你可能必须是一个重度 PHP 用户，然而，对于我们这些不经常使用 PHP 的人来说，PHP 已经引入了额外的新特性来适应日常使用情况。

现在我们已经掌握了最新 PHP 版本引入的特性，让我们来构建我们的工具箱。在接下来的章节中，我将讨论一些我认为在 PHP 专业软件开发中不可或缺的工具。它们是以递增的顺序呈现的，这意味着我相信这将是最容易采用的途径。

## 调试器

在引入像 [XDebug](https://xdebug.org/) 和 [ZendDebugger](https://www.zend.com/downloads/zend-studio-web-debugger) 这样的调试器之前，开发人员被迫花费过多的时间来理解应用程序行为不端的根本原因。

实际上，调试包括在程序执行期间查看变量的内容。一般来说，PHP 以批处理模式使用，这意味着只有当脚本运行完成时，输出才可见，这使得开发人员很难猜测错误发生时的上下文。

此外，可用于此任务的工具，如`[var_dump](https://www.php.net/manual/en/function.var-dump.php)`、[、`echo`、](https://www.php.net/manual/en/function.echo.php)和[、`print_r`、](https://www.php.net/manual/es/function.print-r.php)具有留下痕迹的高风险，可能会暴露敏感信息并降低恶意攻击者的门槛。

XDebug 和 ZendDebugger 都可以很好地与 PhpStorm 和 VS Code 等现代 ide 一起解决上面提到的问题。如果您喜欢直接通过命令行，那么从 v5.6 开始，`[phpdbg](https://www.php.net/manual/en/book.phpdbg.php)`与 PHP 捆绑在一起。

## 依赖性管理

将外部库作为依赖项导入曾经是 PHP 中的一大难题。然而，PHP 成熟度最显著的变化之一是随着 [Composer](https://getcomposer.org/) 的发布。在 Composer 之前，PHP 使用的是 [PEAR](https://pear.php.net) ，用更原始的方式解决了同样的问题。

例如，使用 PEAR 获得单个项目的依赖关系是很复杂的。PEAR 的依赖关系管理是一种要么全有要么全无的情况，因此在同一台服务器上运行几个项目是很困难的，尤其是当每个项目依赖于不同的或冲突的依赖关系集时。

另一方面，Composer 的依赖性管理要简单得多。每个项目都有自己的`composer.json`和`vendor`文件夹，保持所有内容独立。

Composer 的另一个巨大优势是它的版本控制系统，它有内置的智能来确定最适合依赖关系树的部分；想想那些有自己的依赖关系的依赖关系。另一方面，PEAR 在这方面做得很差。

如今，PHP 最佳实践需要熟悉 Composer。我们将讨论的大多数工具都要求它在您的工作环境中可用。

## MVC 框架

如果您正在构建任何重要的应用程序，那么在实际解决客户的问题之前，您可能需要创建许多样板代码。想想认证、路由和数据库管理等问题。

在 PHP 的早期，这是一个真正的挑战。如今，有许多可用的 MVC 框架，最著名的是 [Symfony](http://symfony.com/) 和 [Laravel](https://laravel.com/) ，它们可以作为你的任务的基础。Symfony 和 Laravel 都拥有大量的社区支持和广泛的使用。

## 自动化测试

自动化测试工具已经成为整个软件开发行业的标准。每种语言都有自己的工具，PHP 的最大玩家肯定是 [phpUnit](https://phpunit.de/) 。

phpUnit 最初被设计为一个单元测试框架，但是其他工具帮助扩展了它，以提供其他类型的测试，比如端到端和集成测试。

使用 phpUnit 非常简单。假设您有一个如下所示的类:

```
&lt;?php

namespace LeewayAcademy;

class Calculator
{
    public function add(int $a, int $b): int
    {
        return $a + $b;
    }
}

```

阅读代码时，您可能会认为它会工作。但是使用 phpUnit，您可以定义一组可重复的测试，帮助您建立和证明您的信心水平。例如，一个测试用例将如下所示:

```
<?php

use PHPUnit\Framework\TestCase;
use LeewayAcademy\Calculator;

class CalculatorTest extends TestCase
{
    public function testAdd()
    {
        $sut = new Calculator();
        $this->assertEquals(3, $sut->add(1, 2));
        $this->assertEquals(10, $sut->add(5, 5));
        $this->assertEquals(10, $sut->add(0, $sut->add(4, 6)));
    }
}

```

上面的代码使用不同组的输入运行`add`方法，然后验证输出是否与预期的相匹配。您可以使用以下命令用 phpUnit 运行测试:

```
php vendor/phpunit/phpunit/phpunit --no-configuration --filter CalculatorTest --test

```

上面的代码将产生如下所示的输出:

```
Testing started at 10:07 ...
PHPUnit 9.5.11 by Sebastian Bergmann and contributors.
Time: 00:00.006, Memory: 4.00 MB
OK (1 test, 3 assertions)

```

您可以根据需要多次运行这种测试。如果它们都通过了，你将有一些实际的证据证明你的应用程序正在做它应该做的事情。当然，这些工具[只和你写的测试](https://blog.logrocket.com/hosting-all-your-php-packages-together-in-a-monorepo/)一样好，但是那完全是另外一个讨论了。

其他值得一提的工具包括[代码接收](https://codeception.com/)和[行为](https://behat.org/)。两者都在底层使用 phpUnit，但是编写测试的方法不同。

## 静态分析工具

缺乏静态分析曾经是 PHP 和其他非编译语言的一大缺点。一些错误隐藏在模糊的执行路径中，在正常的测试情况下很难发现它们。我们现在有 phpstan，诗篇，和 Exakat，仅举几例。例如，考虑以下错误:

```
<?php

function f(int $p): int
{
        return $p * 2;
}

$a = 'M';

echo f($a);

```

使用静态分析工具，不需要运行代码，只需发出如下所示的命令，就可以检测出如上所示的`type`不匹配错误:

```
vendor/bin/phpstan analyse test.php --level 5

```

上面的代码将产生以下输出:

```
 1/1 [▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓] 100%

 ------ ---------------------------------------------------------- 
  Line   test.php                                                  
 ------ ---------------------------------------------------------- 
  10     Parameter #1 $p of function f expects int, string given.  
 ------ ----------------------------------------------------------                                                                                
 [ERROR] Found 1 error 

```

现在，您有了关于可能被忽略的错误的非常精确的信息。通过将这些工具包含在持续集成管道中或作为 Git 挂钩的一部分运行，您可以更容易地提高代码库的质量。

## 部署工具

开发人员的工作不会在他们写完最后一行代码后就结束。要获得受众，您的应用程序必须首先到达生产服务器。

对于旧版本的 PHP，部署应用程序需要将新文件推送到远程位置。然而，如今，情况有点复杂了。您可能必须处理数据库更新、目录许可和大量其他小任务，以使一切正常运行。通常，错过这些操作中的一个或以不同的顺序运行它们会导致整个部署失败。

就像自动化测试工具一样，PHP 生态系统为将应用程序投入生产并根据需要进行更新提供了极好的工具，避免了巨大的麻烦。其中包括 Deployer、Rocketeer、Pomander 和 easydeploy。例如，下面是我在一个客户项目中使用的 Deployer 配置:

```
&lt;?php
namespace Deployer;

require 'recipe/codeigniter.php';

// Project name
set('application', 'APP');

// Project repository
set('repository', '[email protected]:maurochojrin/REPO.git');
set('branch', 'master');

set('default_stage', 'prod');

// [Optional] Allocate tty for git clone. Default value is false.
set('git_tty', true); 

// Shared files/dirs between deploys 
add('shared_files', [
    'application/config/database.php',
    'app_env.php',
]);
add('shared_dirs', [
    'application/sessions',
    'application/logs',
    'assets/uploads/excel',
    'logs',
]);

// Writable dirs by web server 
add('writable_dirs', [
    'application/sessions',
    'assets/uploads',
    'application/logs',
]);

// Hosts

host('THE_HOST')
    ->stage('prod')
    ->identityFile('~/.ssh/MauroChojrin.pem')
    ->set('user', 'ubuntu')
    ->set('deploy_path', '~/{{application}}');

// Tasks

task('build', function () {
    run('cd {{release_path}} && build');
});

task('pwd', function () {
    $result = run('pwd');
    writeln("Current dir: $result");
});

// [Optional] if deploy fails automatically unlock.
after('deploy:failed', 'deploy:unlock');

```

有了这样的配置，每当我将新版本投入生产时，我只需运行下面的命令:

```
dep deploy

```

该脚本将远程运行使应用程序对用户可用所需的每个任务。如果你还在通过 FTP 推送文件，你可能想看看这些工具。

## 异步执行

PHP 的另一个常见抱怨是缺乏异步执行支持。有几个项目瞄准了这个方向，比如 [Swoole](https://www.swoole.co.uk/) 和 [ReactPHP](https://reactphp.org/) 。看看下面的代码，这些代码来自 [Swoole By Examples 库](https://github.com/deminy/swoole-by-examples):

```
#!/usr/bin/env php
<?php

declare(strict_types=1);

/**
 * How to run this script:
 *     docker exec -t $(docker ps -qf "name=client") bash -c "time ./io/blocking-io.php"
 *
 * This script takes about 3 seconds to finish, and prints out "12".
 *
 * Here the PHP function sleep() is used to simulate blocking I/O. The non-blocking version takes about 2 seconds to
 * finish, as you can see in script "non-blocking-io.php".
 */
(function () {
    sleep(2);
    echo '1';
})();

(function () {
    sleep(1);
    echo '2';
})();

```

将其与非阻塞的对应部分进行比较:

```
#!/usr/bin/env php
<?php

declare(strict_types=1);

/**
 * How to run this script:
 *     docker exec -t $(docker ps -qf "name=client") bash -c "time ./io/non-blocking-io.php"
 *
 * This script takes about 2 seconds to finish, and prints out "21".
 *
 * Here the Swoole function co:sleep() is used to simulate non-blocking I/O. If we update the code to make it work in
 * blocking mode, it takes about 3 seconds to finish, as you can see in script "blocking-io.php".
 *
 * To see how the code is executed in order, please check script "non-blocking-io-debug.php".
 */
go(function () {
    co::sleep(2);
    echo '1';
});

go(function () {
    co::sleep(1);
    echo '2';
});

```

从语法上来说，它们看起来非常相似，但在本质上，第二个版本利用了 Swoole 的并行处理能力，减少了实现最终结果所需的时间。

在 PHP v8.1 中，[纤程](https://php.watch/versions/8.1/fibers)作为一个开箱即用的特性被引入，所以如果异步是你的目标，没有什么可以阻止你在不离开 PHP 的情况下实现它。

## 结论

PHP 已经走过了漫长的道路。遗憾的是，并不是每个 PHP 开发人员都遵循了这些最佳实践，所以您仍然可以在那里找到大量的代码。但是，这更多反映的是个人的责任，而不是工具的缺点。

好的一面是，如果你愿意，有很多优秀的资源可以升级 PHP。我希望你喜欢这篇文章。如果您还没有，我希望您现在是 PHP 的粉丝，或者至少愿意尝试一下。让我们来扭转 PHP 的名声。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)