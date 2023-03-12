# 回复:DOM vs. Svelte 

> 原文：<https://blog.logrocket.com/redom-vs-svelte/>

RE:DOM 是一个用于操作 DOM 的库，Svelte 是一个带有路由和转换的框架。在本帖中，我们将回顾它们的不同之处，并确定哪一个是不同类型应用的更好选择。

## 用 RE:DOM 和 Svelte 创建组件

回复:DOM 和 Svelte 都允许我们创建组件来呈现内容。

### 苗条的

在 Svelte 中，我们在单独的文件中创建组件。例如，我们可以写:

```
//App.svelte

<script>
    import Button from "./Button.svelte";
</script>

<style>
  main {
    font-family: sans-serif;
    text-align: center;
  }
</style>

<main>
    <h1>Hello</h1>
    <Button />
</main>
```

```
//Button.svelte

<script>
    let count = 0;

    function handleClick() {
      count += 1;
    }
</script>

<style>
    button {
      background: #ff3e00;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 2px;
    }
</style>

<button on:click={handleClick}>
  Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

我们在`Button.svelte`中创建了`Button`组件，它包含在`App.svelte`组件文件中。

`script`标签有逻辑，`style`有每个组件的样式。文件的其余部分是我们想要呈现的 HTML 内容。

我们将 JavaScript 表达式嵌入在括号中，它有自己的附加事件处理程序的语法。属性让我们附加一个点击处理程序。

标签中的函数可以在 HTML 模板中引用，就像我们对函数所做的一样。

在`script`标签中声明的任何变量都可以在模板中引用和呈现，就像在`button`元素中一样。

数据绑定是自动完成的，所以不需要我们自己来做。

### 回复:DOM

对于 RE:DOM，我们通过运行以下命令来安装`redom`包:

```
npm i redom
```

然后，我们可以通过编写以下代码来创建一个简单的组件:

```
//index.js

import { el, mount } from "redom";

const hello = el("h1", "Hello world");

mount(document.body, hello);
```

这假设我们将使用像 Parcel 这样的模块捆绑器来创建我们的项目。我们用`el`函数创建组件，并传入我们想要显示的标签和 HTML。然后，我们调用`mount`将`hello`组件安装到车身上。

我们可以用`text`函数创建一个文本组件:

```
import { text, mount } from "redom";

const hello = text("hello");

mount(document.body, hello);

hello.textContent = "hi!";
```

我们将初始的`textContent`传递给`text`函数，当我们重新分配它的值时，它会自动更新。

如果我们想添加子元素，我们写:

```
import { el, setChildren } from "redom";

const a = el("h1", "foo");
const b = el("h2", "bar");
const c = el("h3", "baz");

setChildren(document.body, [a, b, c]);
```

然后，我们按照与数组中相同的顺序添加元素。

我们也可以用类创建 RE:DOM 组件。这让我们可以设置生命周期方法，在组件生命周期的不同阶段运行代码。例如，我们可以写:

```
import { el, mount } from "redom";

class Hello {
  constructor() {
    this.el = el("h1", "Hello world");
  }
  onmount() {
    console.log("mounted Hello");
  }
  onremount() {
    console.log("remounted Hello");
  }
  onunmount() {
    console.log("unmounted Hello");
  }
}

class App {
  constructor() {
    this.el = el("app", (this.hello = new Hello()));
  }
  onmount() {
    console.log("mounted App");
  }
  onremount() {
    console.log("remounted App");
  }
  onunmount() {
    console.log("unmounted App");
  }
}

const app = new App();

mount(document.body, app);
```

上面，我们有一个嵌套在`App`组件中的`Hello`组件。为了进行嵌套，我们创建了它的一个新实例，并将其作为第二个参数传递给`el`。

总的来说，Svelte 有更多的选择来创建组件，因为它是一个应用程序开发框架。回复:DOM 的局限性更大，因为它应该被用作 DOM 操作库。

## 更新组件

### 苗条的

在 Svelte 中，我们可以用各种方式更新组件。

我们更新了`script`标签中的变量，新的值将被自动呈现。例如:

```
//Button.svelte

<script>
    let count = 0;

    function handleClick() {
      count += 1;
    }
</script>

<style>
    button {
      background: #ff3e00;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 2px;
    }
</style>

<button on:click={handleClick}>
  Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

如果我们单击该按钮，那么`handleClick`功能将运行，这将使`count`增加 1。`count`的最新值将会呈现在模板中。我们只是显示它，我们也用它来渲染别的东西，从表达式可以看出。

我们还可以用 Svelte 添加样式绑定。例如，我们可以写:

```
<script>
    let size = 42;
    let text = "edit me";
</script>

<input type=range bind:value={size}>

<div>
    <span style="font-size: {size}px">{text}</span>
</div>
```

我们有一个范围滑块，它绑定到`size`变量。所以，当我们改变滑块值时，`size`值也会随之改变。

在`span`元素中，我们使用`size`值来改变`font-size`样式。因此，滑块会改变**编辑我**文本的大小。

它还带有一个设置类的特殊语法。

例如，我们可以写:

```
<script>
 let active = true;
</script>

<style>
  .active {
    background-color: red;
  }
</style>

<div>
  <div
    class="{active ? 'active' : ''}"
    on:click="{() => active = !active}"
  >
    toggle
  </div>
</div>
```

当我们点击按钮时，我们通过在`true`和`false`之间切换`active`变量来打开和关闭`active`类。

我们也可以写类似这样的东西来做同样的事情:

```
<script>
 let active = true;
</script>

<style>
  .active {
    background-color: red;
  }
</style>

<div>
  <div
    class:active='{active}'
    on:click="{() => active = !active}"
  >
    toggle
  </div>
</div>
```

我们有一个表达式来决定`active`类是否应用了`class:active`属性。

### 回复:DOM

使用 RE:DOM，更新组件会受到更多限制。我们可以设置元素的属性或者样式。例如，我们可以编写这样的代码来创建一个`h1`元素，并将其颜色设置为红色:

```
import { el, setAttr, mount } from "redom";

const hello = el("h1", "Hello world!");

setAttr(hello, {
  style: { color: "red" },
  className: "hello"
});

mount(document.body, hello);
```

`setAttr`函数有两个参数:第一个是我们创建的元素，第二个是对象，键是属性名，值是属性值。

RE:DOM 还附带了一个`setStyle`方法，让我们设置元素的样式。例如，我们可以这样使用它:

```
import { el, setStyle, mount } from "redom";

const hello = el("h1", "Hello world!");

setStyle(hello, { color: "green" });

mount(document.body, hello);
```

这会将`h1`元素设置为绿色。

这些是更新组件的主要方式。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 开发者体验

Svelte 是一个完整的 app 开发框架，而 RE:DOM 是一个用于 DOM 操作的库。它们都使得操作 DOM 变得容易。

苗条让我们有表现力地编写代码，消除样板文件。为了快速创建一个苗条的项目，我们必须克隆[苗条模板 repo](https://github.com/sveltejs/template) ，这不是非常方便。

另一方面，使用 RE:DOM，我们只需安装带有`npm i redom`的`redom`包。

我们也可以使用 https://redom.js.org/redom.min.js 的脚本将其添加到我们的应用程序中。这意味着我们不需要构建工具来将 RE:DOM 添加到我们的应用程序项目中。

这两个工具都支持最新的 JavaScript 语法。因为 RE:DOM 是一个库，它没有一个 CLI 程序让我们创建应用程序。另一方面，没有 CLI 程序从头开始创建一个苗条的项目并构建它，这不是很方便。

## 表演

根据很多测试，RE:DOM 比 Svelte 快。从 [AJ Meyghani 的基准](https://medium.com/@ajmeyghani/javascript-frameworks-performance-comparison-c566d19ab65b)可以看出，Svelte 在很多方面都比 RE:DOM 慢。这在[最近的基准](https://krausest.github.io/js-framework-benchmark/current.html)中也有类似的表现。

然而，这种差异只有几毫秒，在大多数情况下可能不太明显。值得重申的是，RE:DOM 是一个轻量级的库，而 Svelte 是一个完整的 app 开发框架，所以这是有意义的。

## 按指定路线发送

RE:DOM 自带路由器，用于将 URL 映射到要呈现的组件，而 Svelte 没有自带路由器。

### 苗条的

要将路由添加到我们的瘦应用程序中，我们需要通过运行以下命令来安装`svelte-routing`包:

```
npm i svelte-routing
```

然后，我们写道:

```
//routes/About.svelte

<p>about</p>
```

```
//routes/Blog.svelte

<p>blog</p>
```

```
//routes/BlogPost.svelte

<script>
  export let id;
</script>

<p>blog post {id}</p>
```

```
//routes/Home.svelte

<p>home</p>
```

```
//App.svelte

<script>
  import { Router, Link, Route } from "svelte-routing";
  import Home from "./routes/Home.svelte";
  import About from "./routes/About.svelte";
  import Blog from "./routes/Blog.svelte";
  import BlogPost from "./routes/BlogPost.svelte";

  export let url = "";
</script>

<Router url="{url}">
  <nav>
    <Link to="/">Home</Link>
    <Link to="about">About</Link>
    <Link to="blog">Blog</Link>
    <Link to="blog/1">Blog Post</Link>
  </nav>
  <div>
    <Route path="blog/:id" let:params>
      <BlogPost id="{params.id}" />
    </Route>
    <Route path="blog" component="{Blog}" />
    <Route path="about" component="{About}" />
    <Route path="/"><Home /></Route>
  </div>
</Router>
```

我们添加`Router`组件来添加路由器，`Route`添加路由。

我们传递带有`let:params`属性和`id`属性的 URL 参数来接受`id` URL 参数。

`:id`是 URL 参数占位符。如果有什么参数，我们可以把它们作为道具获取，就像我们在`BlogPost.svelte`中做的那样。如果变量声明前面有`export`关键字，那么它就是一个道具。

### 回复:DOM

要使用 RE:DOM 的路由器，我们可以编写:

```
//index.js

import { el, router, mount } from "redom";

class Home {
  constructor() {
    this.el = el("h1");
  }
  update(data) {
    this.el.textContent = `Hello ${data}`;
  }
}

class About {
  constructor() {
    this.el = el("about");
  }
  update(data) {
    this.el.textContent = `About ${data}`;
  }
}

class Contact {
  constructor() {
    this.el = el("contact");
  }
  update(data) {
    this.el.textContent = `Contact ${data}`;
  }
}

const app = router(".app", {
  home: Home,
  about: About,
  contact: Contact
});

mount(document.body, app);

const data = "world";

app.update("home", data);
app.update("about", data);
app.update("contact", data);
```

我们通过使用`router`功能创建路线。

对象中的键是路由的 URL。前往我们想要的路线。我们将从`update`方法的参数中获得的`data`传递给路由。

总的来说，RE:DOM 的路由器肯定不如 Svelte 的灵活。

## 结论

RE:DOM 是一个轻量级的 DOM 操作库。尽管我们可以用它来创建组件，但对于创建复杂的组件来说，它的表达能力不是很强。因此，我们应该坚持用它来操纵 DOM。

另一方面，Svelte 是一个完整的应用程序框架，只要我们将`svelte-routing`包添加到其中进行路由。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。