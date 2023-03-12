# Livewire vs. Inertia.js:比较 Laravel 前端- LogRocket 博客

> 原文：<https://blog.logrocket.com/livewire-vs-inertia-js/>

编者按:这篇文章在 2021 年 9 月进行了编辑和更新，以包括 Livewire 与 Inertia.js 的比较，这是利用 Vue.js 前端和 Laravel 后端的新方法。

Blade 是一种非常棒的模板语言，它允许我们制作全面的、动态的 Laravel 应用程序视图。然而，许多现代应用程序要求页面组件更新而无需页面重新加载，而传统的 Laravel 应用程序使用 Blade 和 jQuery 之类的 JavaScript 库来实现这一点很麻烦。

另一方面，使用 React、Svelte 和 Vue 等 JavaScript 框架构建的单页面应用程序(SPAs)可以毫不费力地做到这一点。

感谢 Laravel 社区的贡献者，现在我们有几种方法可以构建这种类型的应用程序。在本文中，我们将讨论两种最流行的前端技术，它们被用来构建 Laravel 的现代 web 应用程序:Livewire 和 Vue。

## 先决条件

要跟随本教程，您需要:

*   对 Vue 3 ( [或至少 Vue 2](https://blog.logrocket.com/refactoring-vue-2-apps-vue-3/) )有所了解
*   拉勒维尔的知识
*   使用 Blade 制作模板的经验

## 背景

从 Laravel 8 开始，Laravel 应用中的认证和前端现在有两种主要的搭建方式: [Laravel Jetstream](https://laravel.com/docs/8.x/starter-kits#laravel-jetstream) 和 [Laravel Breeze](https://laravel.com/docs/8.x/starter-kits#laravel-breeze) 。

通过 Jetstream，我们可以为 Laravel 应用提供认证、电子邮件验证、密码重置系统、会话管理、使用 Tailwind CSS 的前端等。我们还可以选择两种前端技术来构建这些应用程序:Inertia.js 和 Livewire。

Laravel Breeze 有些类似，但它是一种更简单的方法，可以通过身份验证和前端搭建来设置应用程序。另一个区别是，Breeze 只附带了 Inertia.js。

[Inertia.js 是一个 JavaScript 库](https://inertiajs.com/)，它将一个 JavaScript 前端连接到一个不同的后端，而不需要 API。除了 Vue，Inertia.js 还允许我们连接 React 和 Svelte 前端。

因此，我们可以在后端使用 Laravel 的[魔法，而不是使用非常复杂的 React 框架——比如 Gatsby 和 Next.js，以及像 Nuxt.js 这样的 Vue 框架——来处理服务器上的数据获取、数据处理和路由。](https://blog.logrocket.com/laravel-breeze-inertia-react-stack-vs-next-js-gatsby/)

Laravel Livewire 承诺提供 React 和 Vue 的水疗体验，但没有它们的复杂性。 [Livewire 组件向服务器发出 AJAX 调用](https://laravel-livewire.com/),虽然响应是 HTML，但 Livewire 并没有用新的 HTML 重新加载整个页面，而是根据变化改变了 DOM。

## 设置

在本文中，我们将讨论使用 Jetstream 和 Breeze 的背景。但是，您应该知道，为了使用 Livewire 或 Vue，您不需要使用 Jetstream 或 Breeze。

Vue 已经预装在 Laravel 中，您需要做的就是使用 Laravel Mix 将 Vue 组件编译成一个浏览器可用的 JavaScript 文件。

对于 Livewire，我们需要做的就是运行`composer require livewire/livewire`和[包括刀片](https://laravel-livewire.com/docs/2.x/quickstart)中的指令。然而，在这种情况下，我们将使用 Laravel Breeze 和 Jetstream 等初学者工具包提供的脚手架。

### Laravel Livewire

要使用 Jetstream 安装 Livewire，必须首先通过运行以下命令将 Jetstream 安装到全新的 Laravel 安装中:

```
composer require laravel/jetstream

```

然后，运行 `php artisan jetstream:install livewire`安装与 Livewire 相关的各种组件。然后，运行`php artisan vendor:publish --tag=jetstream-views`来发布和使用 Livewire 的刀片组件，比如输入、模态和按钮。

前端资产仍然需要通过运行`npm install && npm run dev`来安装。数据库也需要通过运行`php artisan migrate`来迁移。

### 惯性. js

要使用 Jetstream 安装惯性，必须通过运行以下命令将 Jetstream 安装到新的 Laravel 安装中:

`composer require laravel/jetstream`

惯性可以通过运行`php artisan jetstream:install inertia`来安装。通过运行`npm install && npm run dev`合并前端资产，通过运行`php artisan migrate`迁移数据库。

要在 Jetstream 上安装惯性，我们必须先运行`composer require laravel/breeze --dev`来安装 Laravel Breeze。

然后，通过运行以下命令安装 Breeze 的组件:

`php artisan breeze:install vue`

Breeze 也为我们提供了使用 React 的选项，但我们今天不会讨论这个问题。

通过运行`npm install && npm run dev`安装并合并前端资产。数据库也需要迁移:`php artisan migrate`。

## 创建组件和模板

惯性处理组件和模板与 Livewire 非常不同，因为惯性负责帮助 PHP 后端与 JavaScript 前端“对话”,而 Livewire 扩展了 Laravel 中已经可用的功能。

例如，让我们考虑一个简单的计数器特性，每次单击一个按钮，我们都需要增加 1。

在 Livewire 中，我们创建了一个`Counter` 组件，在那里我们初始化了我们的`$count` 属性，并有一个加载视图的`render()` 方法。

```
<?php

use Livewire\Component;

class Counter extends Component
{
    public $count = 0;

    public function render()
    {
        return view('livewire.counter');
    }

    public function increment()
    {
        $this->count++;
    }
}

```

然后，我们将 Livewire 嵌入到我们的视图中，在我们的刀片文件中包含这一行:`@livewire('counter')`。有了`wire:click` 指令，我们就可以绑定`click` 事件，这样每当前端触发事件时，我们就可以在后端运行该方法。

```
<div>
    <button wire:click="increment">Count</button>
    <span>{{ $count }}</span>
</div>

```

我们不必编写一行 JavaScript 就能获得交互式组件，这一事实对许多不喜欢编写前端代码的 PHP 开发人员来说非常有吸引力。Livewire 开发人员还可以继续使用 Blade，并访问它的所有指令和功能。

对于不介意钻研 JavaScript 领域的 Laravel 开发人员来说，惯性使得创建 JavaScript SPAs 变得非常简单。在后端，我们可以通过控制器/路由以及方法和助手函数的使用将数据发送到前端。在下面的例子中，我们有我们的`CounterController`，它有一个保存我们的增量值和我们的`increment()` 方法的`$counter` 属性。

```
<?php

namespace app\Http\Controllers;

use Illuminate\Http\Request;
use Inertia\Inertia;

class CounterController extends Controller
{
    public $counter = 1;

    public function index(){
        return Inertia::render('Home/Index', [
            'counter' => $this->counter,
        ]);
    }

    public function increment(Request $request){
        $count = $this->counter + $request->count;
      return Redirect::route('home', ['counter' => $count]);
    }
}

```

在我们前进之前，让我们把它放在那里，有一个与后端对话的计数器应用程序将是多余的。Vue 已经有了自己完成这项工作的工具。但是出于演示的目的，你会在下面看到两个按钮:一个通过 Vue 单独递增，另一个在惯性和我们后端的帮助下递增。

```
<template>
    <button @click="count++">Via Vue: {{count}}</button><br>
    <button @click="countup">Via Inertia: {{total}}</button>
</template>
<script>
export default {
    data(){
        return {
            count: 0,
            vuecount: 0, 
            total: this.total
        }
    },
    computed: {
        total() {
            this.vuecount = this.vuecount + this.$page.props.counter
            return this.vuecount
        }
    },
    methods: {
        countup() {
            this.$inertia.post('/counter', {count: this.vuecount})
        }
    }
}
</script>

```

## 访问数据

在 Inertia 出现之前，与 Vue 前端共享数据的唯一方式是通过 REST API。现在，当在 Inertia 和 Livewire 之间进行选择时，如何在前端共享和访问数据不必成为主要考虑因素。唯一的问题是需要在前端编写更多的代码。

Livewire 和 Inertia 渲染数据的方式有很大的区别。对 Livewire 的所有请求都呈现在服务器端，而对 Inertia 的所有请求都呈现在客户端。两者各有利弊。

对于客户端呈现的应用程序，请求被重定向到一个空的 HTML 文件，JavaScript 编译所有内容。这些应用程序通常非常快，但缺点是 SEO 有时会受到影响(像 Google 这样的搜索引擎仍然能够抓取 JavaScript 文件)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于服务器端呈现的应用程序，标记和数据在提供给客户端之前在服务器上编译。这是 web 从一开始就有的工作方式，但问题是这种方式比较慢。

尽管 Livewire 和 Inertia 都有它们的弱点，但它们都有解决方案。在 Livewire 的例子中，组件可以在第一次渲染后被缓存。对于 Inertia.js，目前正在构建一个 SSR 解决方案，其中页面将在第一次加载时在服务器端呈现，但后续请求在客户端完成。

## 反应性处理

反应性描述了现代应用程序基于用户交互动态变化的方式。Livewire 和 Vue 处理反应非常不同。

Vue 通过跟踪特定变量的变化并重新呈现 DOM 受影响的部分来处理反应。创建 Vue 组件时，您定义了 Vue 要跟踪的变量(反应数据)。这里有一个只有一个反应变量的例子，`message.`

```
<template>
    <h2>{{message}}</h2>
</template>
<script>
export default {
    data() {
        return {
            message: "Hello World!"
        }
    }
}
</script>

```

在上面的例子中，Vue 跟踪对我们的示例消息所做的每一个更改，并将适当地更新`h2`。这些更新发生在客户端——它不需要与后端交互，因为该组件的状态存在于浏览器中。

然而，有时浏览器中的状态需要与数据库中的数据同步，在这种情况下，AJAX 将更新前端和后端，使它们彼此同步。问题是，如果发生了一些事情，更新失败，导致前端和后端不同步，问题可能会出现。

相比之下，Livewire 组件的状态驻留在服务器上。同样，这也有利有弊。

```
<?php
namespace App\Http\Livewire;

use Livewire\Component;

class HelloWorld extends Component
{
    public $message = "Hello World";

    public function render() {
        return <<<'blade'
            <div>
                {{message}}

            </div>
        blade;
    }
}
?>

```

对于上面这样的简单组件，状态存储在服务器上没有任何影响。然而，对于基于用户交互频繁更新的动态组件，必须发出几个 AJAX 请求来更新服务器中的状态。

这可能会导致一些性能问题。Livewire 的开发者提出，为了解决这个问题，开发者可以[使用“脏状态”](https://laravel-livewire.com/docs/2.x/dirty-states)。

## 结论

在本指南中，我们探讨了 Livewire 和 Vue 之间的差异，并强调了它们的亮点和不足。

公平地说，目前，您对框架的选择主要取决于您是否喜欢使用 JavaScript。在大多数用例中，Livewire 提供了与 JavaScript 应用相同的能力和活力。

Vue 已经为反应性而建立，但 Livewire 生活在浏览器上，所以如果你想在不如 Google 先进的搜索引擎上，SEO 不会是问题。Laravel 的开发者被选择宠坏了。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。