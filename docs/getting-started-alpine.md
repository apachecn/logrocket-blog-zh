# Alpine - LogRocket 博客入门

> 原文：<https://blog.logrocket.com/getting-started-alpine/>

***编者按**:这篇文章更新于 2022 年 3 月 31 日，包含了 Alpine 最新发布的信息。*

Alpine 是一个坚固的、最小的前端开发框架，用于向 HTML 标记添加 JavaScript 行为。它使您能够以更低的成本利用流行的前端库和框架的反应性和声明性，如 [Angular](https://blog.logrocket.com/tag/angular/) 、 [React](https://blog.logrocket.com/tag/react/) 和 [Vue](https://blog.logrocket.com/tag/vue/) 。

没有构建步骤，库文件大小约为 4KB。Alpine 并不是要取代 Vue 和 React 等框架；如果你有一个高度互动的单页应用程序，最好坚持使用更强大的工具。当您的项目只需要最少的 JavaScript 时，最好使用它，比如当您只需要一两个组件，比如下拉菜单、侧栏、标签和图像选择。

Alpine 也非常适合服务器端渲染的应用，比如 Laravel、 [Rails](https://blog.logrocket.com/how-to-use-react-ruby-on-rails/) 和 AdonisJS，这些应用需要你切换一些 JavaScript 组件。由于它没有虚拟 DOM，所以更容易设置。

本质上，Alpine 就像 JavaScript 的 [Tailwind](https://blog.logrocket.com/whats-new-tailwind-css-v3-0/) 。DOM 和 behavior 没有分开；你可以保留你的 DOM，并在你认为合适的时候加入一些行为。您可以轻松地编写声明性代码，而不是过程性代码。最后，Alpine 在您的应用程序中占用的空间非常小。

现在，让我们继续安装步骤，用 Alpine 弄脏我们的手。

### 内容

## 安装和基本使用

将 Alpine 添加到项目中很容易。您可以通过 CDN 从一个`script`标签中包含它，或者将其作为一个模块导入。

### 来自`script`标签

使用一个`script`标签是将 Alpine 添加到您的项目中最简单直接的方法。您只需要将下面的代码片段添加到 HTML 文件的`<head>`部分的末尾:

```
<script defer src="https://unpkg.com/[email protected]/dist/cdn.min.js"></script>

```

> 在 CDN 中指定版本为`@3.x.x`会拉最新版本的 Alpine v3。但是，在生产中，建议在 CDN 链接中硬编码最新版本。

### 作为一个模块

使用这种方法，您首先需要通过 npm 安装 Alpine:

```
npm install alpinejs

```

然后，将 Alpine 导入到您的包中并初始化它:

```
import Alpine from 'alpinejs'

// optional
window.Alpine = Alpine

// initialize Alpine
Alpine.start()

```

### 阿尔卑斯山的基本成分

```
<html>
  <head>
    <script defer src="https://unpkg.com/[email protected]/dist/cdn.min.js"></script>
  </head>
  <body>
    <div x-data="{ isOpen: true }">
      <button x-on:click=" isOpen = !isOpen">Toggle</button>
      <h1 x-show="isOpen">Alpinjs</h1>
    </div>
  </body>
</html>

```

使用 Alpine 的第一步是定义一个州。状态会出现在任何需要的地方，并且与您放入的 HTML 选择器具有相同的范围。

在上面的代码中，我们通过传入一个对象作为状态值，使用`x-data`指令定义了一个范围。`x-on`指令监听事件。对于`button`元素，我们正在监听点击事件，该事件将`isOpen`的值更改为`true`或`false`。`x-show`指令根据状态对象的`isOpen`属性值显示或隐藏 DOM 中的元素。

## 阿尔卑斯山指令

Alpine 框架的核心是指令，它们通过添加和删除 DOM 元素来改变 DOM 布局，并改变 DOM 中元素的行为和外观。Alpine 指令以`x-`开头，后跟指令名。

让我们快速浏览一些指令，看看如何使用它们。

### `x-data`

用 HTML 元素中的对象初始化一个新的组件范围。所有子 HTML 元素都可以访问其父元素中的数据对象:

```
<div x-data="{ isOpen: false }">...</div>

```

### `x-init`

`x-init`用于在组件初始化时运行一个表达式，并可用于设置组件状态的初始值:

```
<div x-data="{ title: 'foo' }" x-init="title = 'bar'"></div>

```

此外，`x-init`可用于在组件初始化后通过传递回调函数来运行代码:

```
<div x-data="{ images: [] }"
  x-init="$nextTick(() => {
    fetch('https://pixabay.com/api/?key=15819227-ef2d84d1681b9442aaa9755b8&q=yellow+flowers&image_type=photo')
      .then(response => response.json())
      .then(response => { images = response.hits })
  })"
></div>

```

### `x-bind`

Alpine 提供了`x-bind`作为绑定值、布尔和类属性的机制。

值属性绑定:

```
<img x-bind:src="imgSrc">

```

这将属性的值设置为表达式的结果。

类属性绑定:

```
<div x-bind:class="{ 'hidden': isClosed }"></div>

```

对于类绑定，传递一个对象表达式。对象键是类名，值是布尔表达式。如果布尔表达式的计算结果为`true`，类名将被应用于该元素。

布尔属性绑定:

```
<input type="text" x-bind:hidden="true">

```

布尔绑定的工作方式与属性绑定相同，但是传递的表达式必须计算为`true`或`false`。

### `x-on`

将事件监听器添加到声明它的元素中。当元素发出该事件(例如，点击或输入事件)时，将执行一个表达式(或函数):

```
<button x-on:click="foo = 'bar'">Click me</button>

```

### `x-show`

`x-show`根据表达式的计算结果是`true`还是`false`来改变元素的 CSS 显示属性。如果表达式的计算结果为`false`，则元素的显示属性被设置为`none`。如果解析为`true`，则显示属性设置为`block`:

```
<div x-show="isOpen"></div>

```

### `x-if`

虽然`x-show`可以用来切换元素的显示属性，但元素实际上并没有从 DOM 中移除。`x-if`指令不隐藏 CSS 中的元素；它完全从 DOM 中添加和删除它们。

`x-if`的值是一个布尔表达式，可以计算为`true`或`false`。如果表达式的计算结果为`false`，`x-if`从 DOM 中删除它的宿主元素。`x-if`只在`template`元素中起作用，并且在`template`标签中必须有一个元素根:

```
<template x-if="true">
  <div>...</div>
</template>

```

### `x-for`

当您想要为集合中的每个项目创建新的 DOM 节点时,`x-for`会有所帮助。就像`x-if`指令一样，`x-for`指令需要存在于`template`标签上，而不是常规的 DOM 元素上:

```
<template x-for="item in items" :key="item">
    <div x-text="item"></div>
</template>

```

### `x-model`

`x-model`为元素增加双向数据绑定能力，同步输入元素的值和组件数据。它足够智能，可以检测文本输入、复选框、单选按钮、文本区域和多项选择的变化，并将它们的值绑定到组件数据:

```
<input type="search" x-model="search">

```

### `x-text`

`x-bind`用于属性绑定，`x-text`用于设置元素的`innerText`的值:

```
<span x-text="title"></span>

```

### `x-html`

`x-html`的工作方式与`x-text`类似，但是它不是设置`innerText`，而是设置一个元素的`innerHTML`的值:

```
<span x-html="title"></span>

```

要了解有关 Alpine 指令的更多信息，请务必查看[文档](https://alpinejs.dev/directives/data)。

## 用 Alpine 建立一个图库

为了演示如何一起使用这些指令，让我们构建一个简单的图片库:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <!-- Bootstrap CSS -->
    <link
      rel="stylesheet"
      href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css"
      integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm"
      crossorigin="anonymous"
    />
    <!-- Custom CSS -->
    <link rel="stylesheet" href="css/custom.css" />
    <!-- Fonts -->
    <link
      href="https://fonts.googleapis.com/css?family=Lora:400,700|Nunito:400,700"
      rel="stylesheet"
    />
    <script defer src="https://unpkg.com/[email protected]/dist/cdn.min.js"></script>
    <script
      defer
      src="https://use.fontawesome.com/releases/v5.0.7/js/all.js"
    ></script>
    <link
      href="images/favicon_32.ico"
      rel="shortcut icon"
      type="image/x-icon"
    />
    <link href="images/favicon_256.ico" rel="apple-touch-icon" />
  </head>
  <body
    x-data="images()"
    x-init="fetch('https://pixabay.com/api/?key=15819227-ef2d84d1681b9442aaa9755b8&q=yellow+flowers&image_type=photo')
.then(response => response.json())
.then(response => { images = response.hits })"
  >
    <!-- Header section -->
    <header class="navigation">
      <div class="container navigation-content">
        <nav class="navbar navbar-expand-lg navbar-light">
          <a class="navbar-brand" href="index.html"
            ><img
              src="https://godwinekuma.github.io/we-connect/images/logo-white.svg"
              alt="weconnect logo"
              height="50"
              class="navbar-brand-image"
            />
            PictureGram</a
          >
          <button
            class="navbar-toggler"
            type="button"
            data-toggle="collapse"
            data-target="#navbarSupportedContent"
            aria-controls="navbarSupportedContent"
            aria-expanded="false"
            aria-label="Toggle navigation"
          >
            <span class="navbar-toggler-icon"></span>
          </button>
        </nav>
      </div>
    </header>
    <!-- Header section /-->
    <!-- Hero Section -->
    <div>
      <section class="hero">
        <div class="container">
          <div class="d-flex flex-column align-items-center">
            <h1 class="display-4" style="text-align:center">
              Search for images.
            </h1>
            <h2 class="" style="text-align:center">
              Pixel perfect images can be found here.
            </h2>
            <div class="input-group">
              <input
                type="text"
                class="form-control"
                placeholder="search images"
                x-model="q"
                aria-label="Text input with segmented dropdown button"
              />
              <select class="custom-select" x-model="image_type">
                <option selected>choose image type</option>
                <option value="all">All</option>
                <option value="photo">Photo</option>
                <option value="illustration">Illustration</option>
                <option value="vector">Vector</option>
              </select>
              <div class="input-group-append">
                <button
                  class="btn btn-primary"
                  type="button"
                  x-on:click="getImages()"
                >
                  Search
                </button>
              </div>
            </div>
          </div>
        </div>
      </section>
      <section id="photos" class="my-5">
        <template x-for="image in images" :key="image.id">
          <img x-bind:src="image.webformatURL" alt="image.tags[0]" />
        </template>
      </section>
    </div>
    <script>
      function images() {
        return {
          images: [],
          q: "",
          image_type: "",
          getImages: async function() {
            console.log("params", this.q, this.image_type);
            const response = await fetch(
              `https://pixabay.com/api/?key=15819227-ef2d84d1681b9442aaa9755b8&q=${
                this.q
              }&image_type=${this.image_type}`
            );
            const data = await response.json();
            this.images = data.hits;
          }
        };
      }
    </script>
  </body>
</html>  

```

我们的 gallery 应用程序从 Pixabay 获取图像列表并显示它们。应用程序状态由`x-data`指令使用一个名为`images`的函数在`body`标签上设置。该函数返回一个包含`image`、`q`、`image-type`和`getImages`的对象。

使用`x-init`指令设置图像的初始值。`x-init`从 Pixabay 获取图像列表，并将其设置为`images`字段的值。`q`捕获`<input>`的值，并使用`x-model`指令进行设置。另一方面，`image_type`捕获`<select></select>`的值，也使用`x-model`指令进行设置。

我们在`<button>`上附加了一个点击事件监听器。点击按钮时，调用状态下的`getImages()`方法。`getImages()`方法根据`q`和`image_type`的值获取新图像。

> {框大小:边框-框；}正文{ margin:0；填充:0；} h1、h2、h3、h4、h5、h6 { font-family:“Nunito”，无衬线；} h1 { font-size:66px；颜色:# fff 边距-底部:26px} H2 { font-size:30px；颜色:rgba(255，255，255，0.8)；字母间距:0；行高:46px 边距-底部:54px} p，a，span

结论

## 在本教程中，我们介绍了如何使用 Alpine，并用该框架构建了一个示例图片库组件。虽然它可能不会完全取代其他框架，但它可以与 React 或 Vue 结合使用，无需编写太多 JavaScript 就能快速构建组件原型。要了解 Alpine 的更多信息，请务必查看该框架的[网站](https://alpinejs.dev/)。

您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

## 毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

更多来自 LogRocket 的精彩文章:

* * *

### LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

* * *

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

Build confidently — [Start monitoring for free](https://lp.logrocket.com/blg/javascript-signup).