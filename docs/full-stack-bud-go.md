# 使用 Bud 和 Go 构建全栈应用

> 原文：<https://blog.logrocket.com/full-stack-bud-go/>

全栈框架有许多形状和大小，但它们通常有两种通用模型。第一个模型包括像 [Ruby on Rails](https://rubyonrails.org/) 和 [Laravel](https://laravel.com/) 这样的框架，它们以后端为中心，具有帮助你在同一个项目中构建前端的特性，以及易于使用的约定和模板。然后是第二个模型，包括 [Next.js](https://nextjs.org/) 、 [Nuxt.js](https://nuxtjs.org/) 和 [SvelteKit](https://kit.svelte.dev/) ，它们更加以前端为中心，但允许您在同一个项目中创建无服务器的函数作为后端。

新的 Go 框架 [Bud](https://denim-cub-301.notion.site/Hey-Bud-4d81622cc49942f9917c5033e5205c69) 与这些全栈框架不同，它有一个框架，开始时非常简洁，但允许前端和后端代码根据用户的需求进行扩展，而不是扩展出一个复杂的 CLI，其中可能包含不必要的文件和配置。Bud 是一个框架，它使事情变得简单，但可以在需要时扩展。

Bud 内置了前端开发 Svelte 的功能，后端使用 Go，使用更快的工具集来开发 web 堆栈的各个部分。让我们测试一下 Bud，并创建一个调用一些后端端点的页面。

在这篇文章中:

## 建立 Bud 框架

先决条件:

*   Curl 安装在您的终端环境中
*   Go v1.6 以上版本
*   节点. js

要安装 Bud，运行以下命令:
`curl -sf [https://raw.githubusercontent.com/livebud/bud/main/install.sh](https://raw.githubusercontent.com/livebud/bud/main/install.sh) | sh`

使用命令`bud -h`确认一切正常。然后，用`bud create first-app`新建一个 app。

这会给你一个模块名。Go 中模块名称的标准是您将它推到的存储库的名称，比如`github.com/username/repo-name`。让我们将目录更改到新的应用程序文件夹中，`cd first-app`。

运行`npm install`安装所有依赖项。

要运行开发服务器，运行命令`bud run`，服务器应该允许您的 web 应用程序在`localhost:3000`可见。

## 芽文件夹结构

检查项目时，您会看到以下结构:

```
/first-app
├─ /bud 
├─ /node_modules 
├─ .gitignore
├─ go.mod
├─ go.sum
├─ package.json
└─ package.lock.json

```

每当你运行`bud run`的时候，框架就会查看你的代码，在这个文件夹里生成必要的 Go app，你就不需要去碰这个文件夹里的文件了。当您准备好部署应用程序时，运行`bud build`，这将把所有东西打包到`/bud/app`文件夹中的一个二进制文件中。

可以添加以下目录，这些目录将被框架识别:

*   这个文件夹将包含代表你的应用程序的后端路径的控制器文件。每个文件都可以通过文件名为端点提供标准的 CRUD 路由
*   `view`:该文件夹可以包含细长的文件，并会根据文件名自动生成路径
*   `public`:托管静态资产，如图像和 CSS 文件
*   这个目录将被忽略，所以它是一个存放特定于应用程序的代码的好地方，这些代码不应该被视为后端或前端路径

## 在 Bud 中创建根页面

虽然我们的页面是用 Svelte 定义的，但是我们应该总是为页面准备一个相应的控制器。让我们首先创建一个`controller`文件夹，并在该文件夹中创建一个名为`controller.go`的文件。这将处理端点`/`。

对于所有其他控制器，您将创建一个文件夹，其中包含一个名称相似的 Go 文件。

例如，端点`/posts`将是`/controller/posts/posts.go`，等等。

在每个文件中，我们应该声明一个`Controller`结构，然后定义将这个结构作为接收方的方法。下面是我们根路由的`controller.go`的样子:

```
package Controller
// Controller Struct to house all Action methods
type Controller struct {}
// Index shows a list of users
// Will automatically render view/index.svelte
// GET /users
func (c *Controller) Index() {}

```

方法触发器的名字就是 RESTful 约定的名字，我们不需要输入所有的代码。这里，`index`方法将从我们的`view`文件夹中名为`index.svelte`的文件中返回一个视图。

视图基本上是通过基于文件的路由来完成的，如果您曾经使用过 Next、Nuxt 或 SvelteKit，您会很熟悉这一点。因为 Bud 用的是 Svelte，`index.svelte`应该代表你的主页面。因此，在您的项目中创建一个`view`文件夹，并在其中用下面的代码创建`index.svelte`。

```
<div>
    <h1>Here is the Main page of your app</h1>
    <section>
        <button on:click={handleClick}>Click This</button>
    </section>
</div>
<script>
    const handleClick = (event) => {
        alert("You Clicked the button")
    }
</script>
<style>
    h1, section {
        text-align: center;
    }
    button {
        border: none;
        padding: 10px;
        font-size: 1em;
        background-color: brown;
        color: white;
        border-radius: 40px;
    }
</style>

```

现在，如果我们运行我们的 Bud 服务器并前往`localhost:3000`，我们将看到我们的页面在运行。现在，Bud 可以在任何控制器中处理以下操作:

| 行动方法 | HTTP 方法 | 统一资源定位器 | 呈现的模板 |
| --- | --- | --- | --- |
| `index` | 得到 | /端点 | `/view/endpoint/index.svelte` |
| `show` | 得到 | /endpoint/:id | `/view/endpoint/show.svelte` |
| `new` | 得到 | /端点/新建 | `/view/endpoint/new.svelte` |
| `create` | 邮政 | /端点 | 没有模板 |
| `edit` | 得到 | /endpoint/:id/edit | `/view/endpoint/edit.svelte` |
| `update` | 放 | /endpoint/:id | 没有模板 |
| `delete` | 删除 | /endpoint/:id | 没有模板 |

## 向您的视图交付数据

呈现视图的方法的返回值将作为道具传递给 Svelte 模板:

*   如果动作返回一个结构，比如一个`Dog`结构，它将作为一个名为`dog`的属性被传递
*   如果动作返回一个`Dog`结构的数组，那么这个属性就叫做`dogs`
*   如果动作返回指定的返回值，那么每个返回值都是匹配名称的属性

让我们给我们的根视图发送一些道具并更新`controller/controller.go`:

```
package controller
// Controller Struct to house all Action methods
type Controller struct {}
// Define a Struct of data to send to template
type Alex struct {
    Name string
    Age int
}
// Index shows a list of users
// Will automatically render view/index.svelte
// Define our struct as the return value and return it
// GET /users
func (c *Controller) Index() Alex {
    return Alex{"Alex Merced", 37}
}

```

现在，让我们更新我们的视图来引入道具，并在我们单击按钮时使用它。相应地更新`view/index.svelte`:

```
<div>
    <h1>Here is the Main page of your app</h1>
    <section>
        <button on:click={handleClick}>Click This</button>
    </section>
</div>
<script>
    export let alex;
    const handleClick = (event) => {
        alert(`My name is ${alex.Name} and age is ${alex.Age}`)
    }
</script>
<style>
    h1, section {
        text-align: center;
    }
    button {
        border: none;
        padding: 10px;
        font-size: 1em;
        background-color: brown;
        color: white;
        border-radius: 40px;
    }
</style>

```

点击按钮。请注意，我们的 Go 方法返回的数据正在我们的 Svelte 模板中使用。整洁！

## Bud 与其他框架相比如何

还有许多功能 Bud 还没有，但考虑到它还处于早期阶段，这是相当令人印象深刻的。让我们将 Bud 与我们希望在未来看到的其他框架和功能进行比较。

### Bud vs. Ruby on Rails and Laravel

这些框架可以很容易地添加 web 套接字、通道等特性。巴德已经保留了一些未来的目录名称，暗示最终将包括这些功能。

### Bud vs. Next.js、Nuxt.js 和 SvelteKit

虽然允许某种程度的服务器端渲染，但 Next、Nuxt 和 SvelteKit 仍然分别严重依赖 React、Vue 和 Svelte 的客户端特性。这使得在它们内部使用像 Redux 这样的状态管理解决方案成为可能。与此同时，Bud 使用更传统的方法让后端管理状态，还没有一个清晰的路径来实现类似的状态管理模式。在对 RESTful 约定和 web 标准的依赖方面，它可能更类似于 Remix.js。

## 结论

Bud 采用了许多约定，比如我们在 Next 和 Ruby on Rails 等框架中看到的基于文件的路由和 RESTful 控制器方法，并将它们捆绑到一个框架中。虽然 Bud 仍在努力构建更多的功能和特性，以成为一个强大的框架，为 Laravel 等所有场景提供解决方案，但 Bud 提供了一个简单的基于约定的工作流程，使使用 Go 和 Svelte 并从中受益变得轻而易举。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)