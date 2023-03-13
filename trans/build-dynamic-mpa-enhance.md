# 使用增强功能构建动态 MPA

> 原文：<https://blog.logrocket.com/build-dynamic-mpa-enhance/>

在构建多页面应用程序(MPAs)时，有数不清的语言和框架可供选择。但是有一些专注于构建 MPAs 的最基本的语言——HTML。

有了大多数选项，您需要学习如何安装和配置许多包，以便为开发准备好最基本的 MPA。

如果有一个框架可以让你用一个简单的 HTML 文件构建你的应用程序，然后你可以向它添加更多的东西——比如样式、交互和数据——会怎么样？在任何时候，你都可以部署你的应用，甚至不需要 JavaScript。

增强是一个可以完成所有这些的框架。

在本教程中，您将学习什么是 Enhance 以及如何用它构建一个示例项目。我还将向你展示渐进增强的思维模式如何改进你构建网站的方式。

向前跳:

## 什么是增强？

它的官方文档将 Enhance 定义为“一个基于 web 标准的 HTML 框架”这意味着它允许我们使用网络平台提供的标准来建立网站。

出于多种原因，这是好的。首先，我们的应用程序包的大小可以更小，因为我们不需要安装额外的库来完成基本的事情。第二，我们的网站将更有性能，因为它将使用为浏览器运行而优化的东西。第三，它使团队协作变得容易得多，因为所有开发人员都知道标准，所以需要学习的东西会更少。

在 Enhance 中构建应用程序的用户界面很容易，因为它是基于 web 组件的。因此，我们得到了所有的好处，比如可重用性、可组合性和限定范围的样式。

更简单的是，Enhance 不要求我们直接使用 web 组件 API(尽管我们可以这样做)。相反，我们可以简单地从组件返回一个 HTML 字符串，它会按照预期的方式呈现它。

Enhance 处理让 web 组件在 SSR 中工作的所有必要工作，如果您需要自己做，这不是一个愉快的体验。

Enhance 还为我们提供了一种简单的方法，通过基于文件系统的路由器向我们的应用程序添加页面。它不仅用于为用户添加页面，还允许我们添加 API 路由来处理服务器请求，并提供我们的应用程序所需的数据。在本教程的后面部分，我将向您展示如何创建这两种路由类型。

## 渐进增强

增强是建立在渐进增强的思想上的。渐进式改进意味着您不需要对应用程序的每个方面(如 JavaScript、获取数据或样式)都进行工作，就可以让您的应用程序为测试甚至部署做好准备。

考虑到渐进增强，您可以逐步构建您的应用程序。因此，您从 HTML 开始，提取一些组件，向应用程序提供硬编码的数据，获取真实数据，并在浏览器中使用 JavaScript 向表单添加用户交互。

在上述步骤中的任何一点，您都应该能够测试甚至部署您的应用程序——并且您可以在以后增强它。

## 用渐进增强的思维方式创建一个示例项目

现在，我们对什么是增强以及为什么要使用它有了一个概念。让我们学习如何构建一个简单的应用程序来看看它的运行。

我们将建立的应用程序将非常简单，所以我们可以专注于概念。在这个应用程序中，我们将显示一个带有文本输入的表单，当它被提交时，服务器会将输入的文本转换为大写。

该应用的第一个版本不会在浏览器中使用 JavaScript。这意味着在用户提交表单后，它将重新加载页面以显示结果。

之后，我们将使用 JavaScript 增强它，这样我们就可以在页面上立即看到结果，而无需重新加载。

让我们从创建一个新的增强项目开始。首先，确保您的计算机上安装了 Node v16 或更高版本。然后，从您的终端运行以下命令:

```
npm create "@enhance" ./enhance-uppercase  -y
```

接下来，安装依赖项并启动项目:

```
cd ./enhance-uppercase

npm install

npm start
```

之后，你应该会看到你的应用在 [http://localhost:3333/](http://localhost:3333/) 运行。

## 路由在增强中的工作原理

Enhance 中的路由基于文件系统，这意味着您添加的页面的 URL 将基于您定义页面文件的位置以及您对该文件的命名。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

所有页面路由都应该位于`app/pages/`下。如果它被命名为`index.html`，那么它将是根页面——即`/`。所以，以添加`/about`页面为例，你需要添加`app/pages/about.html`。

你可以从[增强文档](https://enhance.dev/docs/learn/concepts/routing/)中了解更多关于路由的信息。在本例中，我们将在主页上工作，`app/pages/index.html`。

因此，页面路由是针对用户看到的页面的。如果我们想要定义 API 路由，那么我们可以遵循相同的约定，除了我们应该在`app/api/`中定义它们。

## API 路由有什么用？

我们知道在哪里定义 API 路由，但不知道它们的用途。我们在这里解释一下。

当用户在 Enhance 中请求页面时，路由器会将用户请求的 URL 与开发人员在应用程序中定义的路由进行匹配。

因此，如果用户请求`/about`页面，那么路由器寻找`app/pages/about.html`来为用户服务。这时我们不需要为页面定义 API 路由。如果应用程序也为该请求定义了 API 路由，那么它将在`app/pages/about.html`被服务之前执行`app/api/about.mjs`。

这使我们有机会在将页面提供给用户之前获取和处理任何数据。因此，在 Enhance 中，我们可以从被请求页面的 API 路径向被请求页面返回任何我们想要的数据。

为了更好地理解这一点，让我们看一个代码示例。

如果我们以 About 页面为例，我们会有`app/api/about.mjs`:

```
export async function get (request) {
  return {
    json: {
      userBio: 'example bio'
    }
  }
}
```

所以，当用户请求`/about`时，我们会从`get`返回 JSON 数据(因为是 GET 请求)。

为了使用这些数据，我们可以从传递给页面的`state`对象中访问它。因此，应该在`app/pages/about.mjs`中定义页面路由。注意我们是如何定义它为`.mjs`而不是`.html`来访问数据的:

```
export default function about ({ html, state }) {
  return html
    User bio: ${state.userBio}

```

## 将表单添加到主页

回到我们的项目，我们需要添加一个带有文本输入的表单。因此，将`app/pages/index.html`中的所有内容替换为:

```
<style>
  main {
    width: 500px;
    margin: 100px auto;
    font-family: sans-serif;
  }

  form {
    display: flex;
    align-items: center;
    margin-bottom: 10px;;
  }

  input {
    border: 1px solid #888;
    border-radius: 4px;
    margin-right: 10px;
    padding: 3px 10px;
  }

  button {
    background: #eee;
    padding: 5px 10px;
    border-radius: 4px;
  }

</style>

<main>
  <form action="/">
    <input type="text" name="text" />
    <button>Convert</button>
  </form>

  <div class="result">
    Result: <span class="output"></span>
  </div>
</main>

```

如果您检查您的浏览器，您会看到文本输入，提交按钮，以及下面的结果部分。

## 添加自定义元素

这是一个简单的例子，但是如果你想在多个地方使用这个表单呢？然后，最好将其提取为自定义元素。

在 Enhance 中，可以在`app/elements/`中定义自定义元素。那么，让我们在`app/`中创建一个`elements`目录，然后将`uppercase-form.mjs`添加到`app/elements/`中。

自定义元素被定义为纯函数。因此，让我们这样定义元素:

```
export default function UppercaseForm({ html }) {
  return html`
    <style>
      main {
        width: 500px;
        margin: 100px auto;
        font-family: sans-serif;
      }

      form {
        display: flex;
        align-items: center;
        margin-bottom: 10px;;
      }

      input {
        border: 1px solid #888;
        border-radius: 4px;
        margin-right: 10px;
        padding: 3px 10px;
      }

      button {
        background: #eee;
        padding: 5px 10px;
        border-radius: 4px;
      }

    </style>

    <main>
      <form action="/">
        <input type="text" name="text" />
        <button>Convert</button>
      </form>

      <div class="result">
        Result: <span class="output"></span>
      </div>
    </main>
  `
}
```

因此，它类似于我们在`pages/index.html`中的内容，除了 HTML 是使用参数中提供的`html`函数呈现的。

要在`pages/index.html`中使用它，只需删除所有内容并添加:

```
<uppercase-form />
```

请注意，我们不需要导入任何东西。增强功能会自动导入页面中的所有自定义元素。

## 定义获取 API 路由

我们创建的表单将`action`设置为`/`。这意味着当提交表单时，它会向`[http://localhost:3333/](http://localhost:3333/)`发送一个 GET 请求。因为我们已经在`pages/index.html`定义了一个页面，所以响应将显示该页面。但是，我们还需要返回大写版本的响应文本。为此，我们需要为`/`定义一个 GET API 路径。

遵循 API 路由约定，我们应该在`app/api/index.mjs`中定义它。

因此，添加该文件，并将以下内容放入其中:

```
export function get (req) {
  const text = req.query.text || ''
  const transformedText = text.toUpperCase()

  return {
    json: { transformedText }
  }
}
```

在上面的代码中，我们定义了一个名为`get`的函数，因为它应该处理该页面的 GET 请求。

因为这是一个 GET 请求，所以我们通过请求查询字符串来访问表单数据，这个字符串就是`req.query`。文本值作为`text`可用，因为输入有`name="text"`。

如果请求中没有文本(这意味着表单还没有提交)，那么我们默认返回一个空字符串`const text = req.query.text || ''`。

在我们将文本转换成大写后，我们将其作为 JSON 响应返回。这样我们可以通过`state.store.transformedText`从我们的定制元素中访问它。让我在下一节向您展示如何做。

## 显示结果

正如我在上一节中提到的，我们可以通过参数中的`store`来访问自定义元素中的 JSON 数据。

因此，将自定义元素的参数更新为:

```
export default function UppercaseForm({ html, state }) {
```

然后，在`<span class="output">`中显示。

```
<div class="result">
Result: <span class="output">${state.store.transformedText}</span>
</div>
```

现在，如果您提交表单，您应该看到文本被转换为大写并显示在结果部分。

该应用程序正在按预期工作，可以部署。但是，我们可以通过显示结果而不重新加载页面来改进它。为此，让我们在客户端用一些 JavaScript 来增强我们的应用程序。

## 添加 JavaScript 以改善用户体验

为了使定制元素在客户端可用(即浏览器中的 JavaScript)，我们需要为浏览器重新定义元素。我们将在一个`<script>`标签中这样做。

为了简单起见，让我们将其定义为`app/elements/uppercase-form.mjs`中自定义元素 HTML 中的内联 JavaScript。

所以，将这个`<script>`添加到`</main>`下面的自定义元素 HTML 中。

```
<script type="module">
  class UppercaseForm extends HTMLElement {
    constructor () {
      super()
      this.form = this.querySelector('form')
      this.output = this.querySelector('.output')
      this.form.addEventListener('submit', this.onSubmit.bind(this))
    }

    async onSubmit (e) {
      e.preventDefault()

      const formData = new FormData(e.target)
      const queryString = new URLSearchParams(formData).toString()

      const result = await fetch('/?' + queryString, {
        headers: { 'accept': 'application/json' },
        method: 'get'
      })

      const json = await result.json()
      this.output.textContent = json.transformedText
    }
  }

  customElements.define('uppercase-form', UppercaseForm)
</script>
```

添加这个之后，`uppercase-form`元素在浏览器端会有点“水化”。

现在，当用户提交表单时，客户端 JavaScript 将通过使用浏览器的本地[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch#:~:text=The%20Fetch%20API%20provides%20a,resources%20asynchronously%20across%20the%20network) 发送请求来处理表单。但是请注意，我们如何需要将 headers 设置为`{ 'accept': 'application/json' }`,以使我们的 API route 将其作为 JSON 响应而不是常规的 HTML 响应返回。

如果您现在测试应用程序，它应该会转换文本并显示它，而无需重新加载页面。

这种方法的好处是，即使浏览器禁用了 JavaScript，我们的应用程序仍然可以工作，因为它是逐步增强的。

## 结论

关于 Enhance 还有更多需要学习的地方。我鼓励你查看文档来了解它的所有特性。

我们在本教程中探索的重要的事情是，当我们想用渐进式增强来构建我们的应用程序时，增强是多么强大。

做完本教程的项目，我推荐尝试不用 JavaScript 搭建一个计数器 app，然后用 JavaScript 完善。如果您需要这方面的帮助，请查看文档中的[示例。](https://www.google.com/url?q=https://enhance.dev/docs/learn/concepts/routing/api-routes&sa=D&source=docs&ust=1671735801637107&usg=AOvVaw0GQBcQntXl2HE_-yndI6BL)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)