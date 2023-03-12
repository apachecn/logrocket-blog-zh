# 带插槽的苗条组件综合指南

> 原文：<https://blog.logrocket.com/comprehensive-guide-svelte-components-slots/>

## 介绍

创建可重用组件的一种方法是将子元素或组件传递给父组件。有了像 React 这样的 UI 库，我们可以用 React 的`children` prop 创建可重用的组件。但是我们如何在 Svelte 中将子数据传递给父组件呢？

在未来的细长插槽。我们可以使用细长插槽来创建接受和呈现任何子组件的组件。这样，我们将能够创建可以在我们的应用程序中多次使用的组件。插槽是有用的，因为它们有助于保持我们的代码库干燥。插槽还使得维护、调试和更新组件更加容易。

在本文中，我们将学习如何使用插槽来构建可重用的苗条组件，包括通过查看一些实际的代码示例来使用它们的不同方式。

## 使用细长插槽

让我们看看老虎机在实践中是如何工作的:

```
<div class="card">
  <h1>I am a reusable box</h1>
  <slot></slot>
</div>

<style>
  .card {
    width: 300px;
    border: 1px solid #aaa;
    border-radius: 2px;
    box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
    padding: 1em;
    margin: 0 0 1em 0;
  }
</style>

```

在上面的代码中，我们创建了一个`Card`组件。`slot`组件允许我们将子数据和内容传递给`Card`组件，从而使其可重用。

我们可以使用`App.svelte`中的`Card`组件，并传入我们的内容:

```
<script>
  import Card from './Card.svelte';
</script>

<Card>
  <h1>Hello!</h1>
  <p>This is a box. It can contain anything.</p>
</Card>

```

## 插槽回退

我们可以向槽添加后备内容，以便在槽为空时充当占位符。

假设我们创建了一个博客帖子卡片组件。我们可能希望在卡片收到实际数据之前给每篇文章添加一个后备标题。我们可以通过插槽回退来做到这一点。

我们在`slot`组件的开始和结束标记之间传递的任何数据都将是回退内容:

```
    <!-- Card.svelte -->
    <div class="card">
      <slot>
        <h1>Fallback Blog Title</h1>
      </slot>
    </div>

    <!-- App.svelte -->

    <script>
      import Card from "./Card.svelte";
    </script>
    <Card />

```

这样，在我们传入实际数据之前，我们拥有的每张博客卡都将有一个通用的“后备博客标题”标题。如果在开发过程中需要为组件设置虚拟数据，插槽回退也很有用。

## 命名插槽

通过使用`slot`组件上的 name 属性，我们可以在一个瘦组件中拥有多个插槽。

让我们假设我们想要扩展博客卡组件。大多数博客卡片不只有标题，它们也有日期和一个包含帖子内容细节的部分。

让我们在博客卡的不同部分进行设置:

```
<section>
  <slot name="title" />
  <slot name="date"/>
  <slot name="content" />
</section>

```

这里，我们使用插槽将`Card`组件组装到一个博客卡中。为此，我们设置了两个命名槽，`title`和`content`。

我们在`App.svelte`中使用了`Card`组件。然后，我们遍历`items`数组，将`title`、`date`和`content`数据传入它们各自的插槽，如下所示:

```
<script>
  import Card from "./Card.svelte";
  const items = [
    {title: "Title 1", date: '1-06-2000', content: "Some content content here"},
    {title: "Title 2", date: '1-06-2000', content: "Some more content content here"},
  ];
</script>

{#each items as item}
  <Card>
    <h1 slot="title">{item.title}</h1>
    <span slot="date">{item.date}</span>
    <p slot="content">{item.content}</p>
  </Card>
{/each}

```

## 命名插槽和细长片段

我们如何将多个组件传递到一个指定的插槽中呢？假设我们想创建一个带有标题和日期的卡片标题槽，我们该怎么做呢？

让我们看看如何处理下面的场景:

```
<Card>
  <slot="header">card title</slot>
  <slot="header">card date</slot>
</Card>
<!-- ❗ Duplicate slot name "header" in <Card> -->

```

上面的代码不起作用，因为不允许插槽名称重复。怎么才能修好？

解决方案在于使用一种特殊的细长元素`Svelte:fragment`。`svelte:fragment`允许您将内容放在一个命名的槽中，而不用将其包装在一个容器 DOM 元素中。这将保持文稿的流动布局不变。

让我们用`svelte:fragment`重构`Card`组件:

```
<Card>
  <svelte:fragment slot="header">
    <h1>Card title</h1>
    <p>Card date</p>
  </svelte:fragment>
</Card>

```

使用`svelte:fragment`，我们避免了添加不必要的 HTML 元素，这些元素会影响布局和样式。

## 有条件地显示插槽

有些时候，我们可能不想为插槽设置后备内容，但希望确保插槽只在有内容时才呈现。

我们可以用特殊的`$$slots`变量来实现。虽然这可能不是一个需要添加的关键特性，但如果组件在不应该呈现的时候呈现，它会影响应用程序的样式和布局。

让我们确保`Card`组件在呈现之前没有空槽:

```
<div class="card">
  {#if $$slots.title}
    <slot name="title" />
  {/if}

  {#if $$slots.content}
    <slot name="content">Content</slot>
  {/if}
</div>

```

除了有条件地呈现插槽组件，我们还可以使用`$$slots`变量有条件地将类应用于组件:

```
<div class="card">
  <slot name="title" class:title-style={$$slots.title} />

  {#if $$slots.content}
    <slot name="content">Content</slot>
  {/if}
</div>

<style>
  .title-style{
    color: red;
  }
</style>

```

`$$slots`变量是一个对象，它的键是父组件传入的插槽名称，我们可以用它来有条件地显示或样式化插槽组件。

## 通过 props 跨插槽传递数据

我们可以使用 slot props 通过 slots 的`let:`指令将数据从子进程传递到父进程。这有助于我们在父组件和子组件之间建立关注点的分离。

假设我们有一个雇员数组，我们想在我们的 UI 中呈现。我们设置了一个`Contacts.svelte`组件，其中将呈现雇员的详细信息，并在`App.svelte`中调用`Contacts.svelte`。

我们可以将雇员的数据存储在我们的`App.svelte`文件中，但是，我们希望避免用不需要的数据污染`App.svelte`,因为这将使将来更难维护。

让我们在代码中进行设置，看看它是如何工作的:

```
<!--Contacts.svelte -->
<script>
  const names = ["John", "Jane", "Mary"];
</script>

<div class="contact">
  <slot {names} />
</div>

<!--App.svelte -->
<script>
  import Card from "./Card.svelte";
</script>

<Contacts let:names>
  {#each names as name}
    <p>{name}</p>
  {/each}
</Contacts>

```

这样，我们可以将处理本地状态和数据的责任留给子组件`Contacts.svelte`，并保持我们的`App.svelte`更干净。

我们还可以通过指定的插槽传递数据，如下所示:

```
<!--Card.svelte -->
<script>
  let title = "I am the title";
  let content = "I am the content";
</script>

<div class="card">
  <slot name="title" {title} />
  <slot name="content" {content} />
</div>

<!--App.svelte -->
<script>
  import Card from "./Card.svelte";
</script>

<Card>
  <h1 slot="title" let:title>{title}</h1>
  <p slot="content" let:content>{content}</p>
</Card>

```

## 结论

在这篇文章中，我们已经学习了如何使用插槽来构建苗条的组件。我们学习了什么是槽，如何设置回退内容，以及命名槽。我们还学习了如何通过 props 将动态数据传递给插槽。除了插槽的特性之外，我们还看了一些场景以及如何在实际中使用它们。

既然你已经了解了细长插槽，我鼓励你深入研究文档，实践并构建一些令人敬畏的应用程序。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)