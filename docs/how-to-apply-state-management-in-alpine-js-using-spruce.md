# 使用 Spruce - LogRocket 博客在 Alpine.js 中进行状态管理

> 原文：<https://blog.logrocket.com/how-to-apply-state-management-in-alpine-js-using-spruce/>

如果您使用过 React 和 Vue.js 等 JavaScript 框架，那么您可能已经熟悉了状态管理的概念，它创建了一种跨组件通信和共享数据的方式。

理想情况下，当用户的应用程序有两个或更多组件应该通信和共享数据，而不必在每个组件的范围内声明数据并手动传递它们时，用户需要状态管理。通常，用户有某种类型的存储，作为应用程序状态/数据的单一真实来源。

进入 [Alpine.js](https://github.com/alpinejs/alpine) ，这是一个相对较新的 JavaScript 框架，它借鉴了 React 和 Vue.js 的概念，并通过一个名为 [Spruce](https://github.com/ryangjchandler/spruce) 的库实现了自己的状态管理。Spruce 是 Alpine.js 的一个轻量级状态管理库，就像 Alpine.js 一样，Spruce 很简单，占用空间很小。

## 我们正在建造的东西

在本文中，我们将构建一个简单的待办事项应用程序，它包含两个组件:一个用于添加新待办事项的输入和一个显示待办事项列表的表格。这将使我们有机会从两个独立组件内部的全局存储中访问状态。

## 云杉入门

首先，让我们创建一个新的项目目录，我们称之为`alpine-spruce-todo`:

```
mkdir alpine-spruce-todo
```

接下来，在项目目录中创建一个`index.html`文件。

```
cd alpine-spruce-todo
touch index.html
```

就像 Alpine.js 一样，Spruce 既可以从 CDN 安装，也可以使用 npm 或 Yarn 安装。在本教程中，我们将使用 CDN。在`index.html`中添加以下代码:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Todo</title>

    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/[email protected]/css/bulma.min.css"
    />

    <script src="https://cdn.jsdelivr.net/npm/@ryangjchandler/[email protected]/dist/spruce.umd.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/[email protected]/dist/alpine.min.js"></script>
  </head>
  <body>
    <section class="section">
      <div class="container">
        <div class="columns">
          <div class="column is-three-fifths is-offset-one-fifth">

```

因为我们使用的是 CDN，所以我们需要在 Alpine.js 之前引入云杉。对于样式，我们使用布尔玛 CSS，它也将从 CDN 引入。

## 创建全球商店

要开始使用 Spruce，我们需要定义一个全局存储，它将作为我们应用程序所有组件的单一真实来源。让我们创建应用程序的全局存储。

将以下代码段添加到结束 body 标记之前:

```
<script>
  Spruce.store('todo', {
    todos: [],
  })
</script>

```

我们正在使用 CDN 构建，这意味着 Spruce 在窗口范围内可用。使用`Spruce`变量，我们调用`store`方法来创建商店。

该方法有两个参数:存储的名称和存储的状态(数据)。因为我们正在构建一个 todo 应用程序，所以将商店命名为`todo`是有意义的。商店只有一个状态(`todos`)，这是一个 todos 数组，默认情况下我们将它设置为空。

## 访问状态

有了全局存储之后，我们现在需要确定如何从我们的组件访问存储。对我们来说幸运的是，Spruce 通过公开一个`$store`魔法属性实现了无缝连接。

用以下代码替换**“component goes here”**文本:

```
<div x-data="{}">
  <template x-if="$store.todo.todos.length">
    <div class="box mt-5">
      <table class="table is-fullwidth">
        <tbody>
          <template
            x-for="(todo, index) in $store.todo.todos"
            :key="index"
          >
            <tr>
              <td x-text="todo.title"></td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
  </template>
</div>

```

这是一个典型的 Alpine.js 组件，但是您会注意到我们没有为该组件声明任何范围。这是因为我们希望利用来自全局存储的数据，这样我们就可以使用`$store.todo`访问`todo`存储，然后访问存储属性。

首先，我们将检查以确保`todos`数组包含一些 todos。然后我们将通过遍历`todos`数组在一个表中显示 todos。

## 修改存储状态

我们已经看到了如何访问存储中的状态。如果我们想修改状态呢？为此，我们遵循与之前类似的步骤。要修改一个状态，我们只需给该状态重新分配一个新值。但是因为我们使用一个数组作为我们的状态，我们将不得不把新的条目推送到数组中。

在前一个组件之前添加以下代码:

```
<div x-data="todoInput()">
  <div class="field">
    <div class="control">
      <input
        type="text"
        class="input"
        x-model="newTodo"
        placeholder="What needs to be done?"
        @keyup.enter="addTodo"
      />
    </div>
  </div>
</div>

```

这是一个典型的 Alpine.js 组件，但这一次，该组件有自己的范围，我们将把它提取到一个名为`todoInput()`的独立函数中。输入被绑定到一个`newTodo`数据，一旦按下回车键，我们就调用`addTodo`方法。

让我们创建函数。在定义云杉商店的代码后添加以下代码片段:

```
function todoInput() {
  return {
    newTodo: '',
    addTodo() {
      if (!this.newTodo) {
        return
      }

      this.$store.todo.todos.push({
        title: this.newTodo,
      })

      this.newTodo = ''
    }
  }
}

```

因为我们从一个函数访问商店，所以我们需要利用`this`。我们只需将新的 todo 添加到`todos`数组中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

在本教程中，我们讨论了状态管理的定义，以及为什么和什么时候使用它。我们还学习了如何使用 Spruce 在 Alpine.js 中应用状态管理，以及如何访问和修改商店的状态。

要了解关于 Spruce 的更多信息，请查看 GitHub repo 。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。