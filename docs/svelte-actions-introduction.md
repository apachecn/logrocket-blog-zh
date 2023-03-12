# 苗条动作介绍-日志火箭博客

> 原文：<https://blog.logrocket.com/svelte-actions-introduction/>

动作是 Svelte 不常用的功能之一。当一个元素被添加到 DOM 时，一个动作允许你运行一个函数。虽然这听起来很简单，但一个正确使用的动作可以极大地简化您的代码，并允许您重用一些逻辑，而无需创建一个完全独立的组件。

在这篇文章中，我将给出两个苗条动作有用的例子，并展示为什么一个动作是这项工作的正确工具。

## 使用苗条的动作来集中输入

先说下面这个[苗条组件](https://blog.logrocket.com/build-your-own-component-library-svelte/)。我们有一些静态文本，旁边有一个编辑按钮。单击编辑按钮时，会显示一个文本字段。在字段中键入内容会更新文本，您可以确认保存更改。

```
<script>
    let name = 'world';    
    let editing = false;

    function toggleEdit() {
        editing = !editing
    }
</script>

<p>
    Name: {name}
</p>

{#if editing}
<label>
    Name
    <input type="text" bind:value={name}>
</label>
{/if}

<button on:click={toggleEdit}>
    {editing ? 'Confirm' : 'Edit'}
</button>

```

这个 UI 有点烦人，因为在单击编辑按钮后，您需要单击(或切换到)编辑字段。如果是自动对焦的话体验会更好，可以马上开始打字。我们如何做到这一点？

### 选项 1: `bind:this`

如果您熟悉在 Svelte 中绑定到 DOM 元素的[，您可能会想到这样做:](https://svelte.dev/docs#bind_element)

```
<script>
    let name = 'world';    
    let editing = false;
    let input;

    function toggleEdit() {
        editing = !editing
            if (editing) {
                    input.focus();
            }
    }
</script>

<p>
    Name: {name}
</p>

{#if editing}
<label>
    Name
    <input bind:this={input} type="text" bind:value={name}>
</label>
{/if}

<button on:click={toggleEdit}>
    {editing ? 'Confirm' : 'Edit'}
</button>

```

但是，如果您尝试运行该代码，您会在控制台中得到一个错误:

```
Uncaught TypeError: input is undefined

```

这是因为输入还没有被添加到 DOM 中，所以在将`editing`设置为`true`之后就不能聚焦了。

相反，我们需要调用 Svelte 的`[tick function](https://svelte.dev/docs#tick)`，它返回一个承诺，当 Svelte 完成应用任何未决的状态更改时，该承诺将得到解决。一旦`tick`被解析，DOM 将被更新，我们可以聚焦输入。

```
function toggleEdit() {
    editing = !editing
    if (editing) {
            tick().then(() => input.focus());
    }
}

```

那也行，但是感觉不是很直观。它也不太可重用——如果我们想将这种行为应用于其他输入，该怎么办？

### 选项 2:将输入移动到一个单独的组件中

另一种选择是将输入移动到它自己的组件中，并在安装该组件时聚焦输入。看起来是这样的:

```
<script>
    export let value;
    export let label;
    let input;

    import { onMount } from 'svelte';

    onMount(() => {
            input.focus();
    });
</script>

<label>
    {label}
    <input type="text" bind:this={input} bind:value>
</label>

```

然后可以在父组件中使用它，就像这样:

```
{#if editing}
<Input bind:value={name} label="name" />
{/if}

```

然而，使用这种方法，您必须承担创建新组件的成本，否则您不需要这样做。如果您想将这种行为应用到另一个 input 元素，您需要确保为每个不同的属性公开 props。

使用此方法还会限制输入元素，如果您想将此行为应用于另一个元素，则需要重新实现此行为。

### 选项 3:使用苗条的动作

虽然这些都是可行的解决方案，但感觉你不得不围绕苗条而不是与它一起工作。幸运的是，Svelte 有一个 API 来简化这种事情: [actions](https://svelte.dev/docs#use_action) 。

动作只是一个函数。它将一个对 DOM 节点的引用作为参数，并在该元素被添加到 DOM 时运行一些代码。

这里有一个简单的动作，它将调用节点上的焦点。这次我们不必调用`tick`,因为只有当节点已经存在时，这个函数才会运行。

```
function focusOnMount(node) {
    node.focus();
}

```

然后我们可以用`use:`指令将它应用到一个节点上。

```
{#if editing}
<label>
Name
<input use:focusOnMount type="text" bind:value={name}>
</label>
{/if}

```

这样干净多了！这只是几行代码来解决我们之前处理的同一个问题，它是可重用的，不需要创建一个单独的组件。它也更具可组合性，因为我们可以将这种行为应用于任何具有`focus`方法的 DOM 元素。

你可以在这件苗条的 REPL 里看到最终的演示。

## 示例 2:将苗条动作与 Tippy 相结合

当您希望与需要引用特定 DOM 节点的普通 JavaScript 库集成时，Actions 也很有用。这是苗条的另一个优势——虽然特定于苗条的生态系统仍在增长，但它仍然很容易与[大量香草 JS 包](https://blog.logrocket.com/tag/js-libraries)集成！

让我们以工具提示库 [Tippy.js](https://atomiks.github.io/tippyjs/) 为例。我们可以传递一个 DOM 元素来初始化那个节点上的 Tippy，还可以传递一个参数对象。

例如，下面是我们如何使用普通 JS 添加工具提示:

```
import tippy from 'tippy.js';

tippy(document.getElementById('tooltip'), { content: 'Hello!' });

```

我们可以使用一个简单的动作来运行这段代码，这样我们就可以在不调用`document.getElementById`的情况下引用这个节点。这可能是这样的:

```
function tooltip(node) {
    let tip = tippy(node, { content: 'Hello!' });
}

```

它可以用在这样的元素上:

```
<button use:tooltip>
    Hover me
</button>

```

但是我们如何定制用来初始化工具提示的属性呢？我们不希望动作的每次使用都是一样的。

### 向操作传递参数

动作也可以将参数作为第二个参数，这意味着我们可以轻松地定制工具提示，并允许消费者传入他们想要的参数。

```
function tooltip(node, params) {
    let tip = tippy(node, params);
}

```

下面是如何在元素上使用它:

```
<button use:tooltip={{
    content: 'New message'
}}>
    Hover me
</button>

```

请注意双花括号。您将想要传递给动作的参数放在花括号内。因为我们将一个对象传递给这个动作，所以有两组花括号:一组用于包装参数，另一组用于参数对象本身。

这是可行的，但是有几个问题:

1.  操作运行后，无法更新参数
2.  当元素被移除时，我们不会破坏工具提示

幸运的是，动作可以用处理这两个问题的`update`和`destroy`方法返回一个对象。

每当传递给动作的参数改变时，`update`方法就会运行，当动作所附加的 DOM 元素被移除时，`destroy`方法就会运行。我们可以使用 Tippy `setProps`函数更新参数，并在完成后使用`destroy`删除元素。

如果我们实现这些方法，下面是操作的样子:

```
function tooltip(node, params) {
    let tip = tippy(node, params);
    return {
     update: (newParams) => {
         tip.setProps(newParams);
     },
     destroy: () => {
         tip.destroy();
     }
    }
}

```

这允许我们编写一个更复杂的示例，在初始创建后更新工具提示的位置和消息:

```
<script>
    import tippy from 'tippy.js';

    function tooltip(node, params) {
     let tip = tippy(node, params);
     return {
         update: (newParams) => {
             tip.setProps(newParams);
         },
         destroy: () => {
             tip.destroy();
         }
     }
    }

    const placements = ['top', 'right', 'bottom', 'left'];
    let selectedPlacement = placements[0];
    let message = "I'm a tooltip!";
</script>

<label for="placement">Placement</label>
<select bind:value={selectedPlacement} id="placement">
    {#each placements as placement}
     <option>{placement}</option>
    {/each}
</select>

<label>Message <input bind:value={message} type="text"></label>

<button use:tooltip={{
    content: message,
    placement: selectedPlacement
}}>
    Hover me
</button>

```

你可以在这个苗条的 REPL 中找到最后一个例子。


## 不使用动作的替代方法

和前面的例子一样，我们不需要*动作来做到这一点。我们还可以在组件挂载时附加工具提示，并使用反应性语句更新参数。这可能是这样的:*

```
<script>
    import tippy from 'tippy.js';
    import { onMount, onDestroy } from 'svelte';

    let button;
    let tip;

    onMount(() => {
     tip = tippy(button, { content: message, placement: selectedPlacement});
    });

    $: if (tip) {
     tip.setProps({ content: message, placement: selectedPlacement });
    }

    onDestroy(() => {
     tip.destroy();
    });

    const placements = ['top', 'right', 'bottom', 'left'];
    let selectedPlacement = placements[0];
    let message = "I'm a tooltip!";
</script>

<label for="placement">Placement</label>
<select bind:value={selectedPlacement} id="placement">
    {#each placements as placement}
     <option>{placement}</option>
    {/each}
</select>

<label>Message <input bind:value={message} type="text"></label>

<button bind:this={button}>
    Hover me
</button>

```

这种方法是完全有效的。但是，它在多个组件之间的可重用性较差，如果工具提示元素是有条件呈现的或者在一个循环中，那么它就变得棘手了。

您可能还会考虑创建一个类似于`<TooltipButton>`的组件来封装逻辑。这也是可行的，尽管它将您限制为一种类型的元素。将它实现为一个动作可以让您将工具提示应用于任何元素，而不仅仅是按钮。

## 包扎

动作是一个非常强大的苗条特征。既然你已经熟悉了它们，请务必查看一下[官方教程](https://svelte.dev/tutorial/actions)和[文档](https://svelte.dev/docs#use_action)，看看动作的其他用法。它们并不总是正确的解决方案——很多时候，用其他方式封装行为更好，比如在一个单独的组件中或者用一个简单的事件处理程序。然而，有些时候，就像上面的例子一样，它们使你的组件代码更加干净，更加可重用。

还有一个[开放 RFC](https://github.com/sveltejs/rfcs/pull/24) 为 svelet 添加内置动作，类似于 svelet 如何包含内置过渡。作为 RFC 的一部分，社区创建了一个 [POC 库](https://github.com/sw-yx/svelte-actions)，其中包含一些常用的操作，如`longpress`、`clickOutside`和`lazyload`。

你可能也会对我去年在[上写的一篇文章感兴趣，这篇文章使用动作来检测一个粘着定位的元素何时粘在了视图](https://geoffrich.net/posts/svelte-action-sticky/)上。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)