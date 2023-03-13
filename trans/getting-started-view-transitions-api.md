# 视图转换 API 入门

> 原文：<https://blog.logrocket.com/getting-started-view-transitions-api/>

网络对我们呈现和显示内容的渠道、交互性的水平以及我们可以集成到应用程序中的独特体验没有任何限制。

作为开发人员，我们可以通过利用许多可用的工具和技术来定义用户如何体验 web，确保用户体验到 web 的独特性和美感。

给网络增添趣味的一种常见方式是给应用程序添加[动画和过渡](https://blog.logrocket.com/guide-to-css-animation-for-javascript-developers/)，比如当用户悬停在按钮上时背景颜色会发生变化，或者当用户向下滚动页面时博客卡会左右滑动。这些简单的过渡可以区分平淡的体验和充满活力的体验。

除了使用第三方工具，如 [Animate.css](https://animate.style/) 、 [Animsta](https://animista.net/) 、 [GSAP](https://greensock.com/gsap/) 或[普通的旧 CSS](https://blog.logrocket.com/tag/css/) ，我们还可以利用名为[视图过渡 API](https://github.com/WICG/view-transitions) 的浏览器 API 来创建令人惊叹和引人注目的动画和过渡。在本文中，我们将学习 API 是如何工作的，并探索一个演示。

*向前跳转:*

## 什么是视图转换 API？

[视图转换 API](https://drafts.csswg.org/css-view-transitions/) ，正式名称为共享元素转换 API，是一个在[单页动画](https://blog.logrocket.com/single-page-applications-css-transitions/) (SPA)中轻松创建转换和动画的 API。它仅限于 spa，因为这是它目前支持的唯一应用程序。

API 让我们控制如何定义动画和过渡。我们可以使用浏览器支持的默认动画属性或者[创建自定义效果](https://blog.logrocket.com/css-before-after-custom-animations-transitions/)。

此外，API 会自动为我们转换元素的大小和位置。它跟踪并计算这些属性在状态之前和之后的变化，并向它们添加适当的转换。

在撰写本文时，视图转换 API 仅在 Chrome v104+和 Canary 中可用。要激活它，首先导航至`chrome://flags/`。然后，启用`Experimental Web Platform features`和`viewTransition API for navigations`。

## 视图转换 API 如何工作

视图转换 API 的神奇工作方式发生在幕后。浏览器获取页面当前状态的屏幕截图，并在页面转换到另一个状态时处理转换。状态改变可以是元素从一个位置移动到另一个位置或者页面导航等等。

浏览器更新 DOM，并应用必要的逻辑在状态之间进行动画处理。然后，浏览器应用交叉淡入淡出作为先前和当前状态之间的默认过渡。

当 API 处于活动状态时，它会构建一个[伪元素](https://blog.logrocket.com/css-pseudo-elements-guide/)树，如下所示:

```
::view-transition
└─ ::view-transition-group(root)
   └─ ::view-transition-image-pair(root)
      ├─ ::view-transition-old(root)
      └─ ::view-transition-new(root)

```

让我们分解代码片段中的伪元素:

*   `::view-transition`:其他伪元素的容器元素
*   `::view-transition-group`:负责动画显示两个状态之间的大小和位置
*   `::view-transition-image-pair`:保存新旧状态的截图
*   `::view-transition-old(root)`:保存之前状态的截图
*   `::view-transition-new(root)`:保存新状态的表示

先前的状态从`opacity: 1`到`opacity: 0`是动画，而新的状态从`opacity: 0`到`opacity: 1`，创建交叉淡入淡出。动画是用 [CSS 动画](https://www.youtube.com/watch?v=93kLe0wJbJY)完成的，这意味着我们可以用 CSS 定制它们:

```
::view-transition-old(root),
::view-transition-new(root) {
  animation-duration: 5s;
}

We can also set up even more complex animation customizations if needed:
//language: CSS

@keyframes slide-from-right {
  from { transform: translateX(30px); }
}

@keyframes slide-to-left {
  to { transform: translateX(-30px); }
}

::view-transition-old(root) {
  animation: 300ms cubic-bezier(0.4, 0, 0.2, 1) both slide-to-left;
}

::view-transition-new(root) {
  animation: 300ms cubic-bezier(0.4, 0, 0.2, 1) both slide-from-right;
}

```

我们可以使用默认的交叉淡入淡出过渡，或者根据我们的喜好设置更复杂的过渡。

这个 API 的一个优点是，浏览器完成了跟踪和计算状态间变化的所有繁重工作，并以高性能的方式处理转换。

## 查看转换 API 演示

让我们创建一个简单的演示应用程序来看看视图转换 API 在实践中是如何工作的。我们将用基本的 [HTML](https://blog.logrocket.com/tag/html/) 、 [CSS](https://blog.logrocket.com/tag/css/) 和 [JavaScript](https://blog.logrocket.com/tag/vanilla-javascript/) 创建这个演示。

它是一个由任务的两个动作组成的待办 app:`Not Done`和`Done`。未完成的任务带有一个可点击的**按钮**来标记它们已经完成。

下面的 GIF 显示了该应用程序的用户界面和功能。注意当任务从一个部分转移到另一个部分时，[转换效果](https://blog.logrocket.com/css-reference-guide-transitions/)是如何发生的:

![Demo of a To-Do App Using the View Transitions API ](img/c606374ea1dbfb4c0577b868fb9b2088.png)

这是 HTML 的一个快照:

```
<div class="grid">
  <article class="col">
    <h2>Not Done</h2>
    <ul id="list-not-done">
      <li>
        <div class="title">Learn programming</div>
        <button onclick="isTaskComplete(true)">✅</button>
      </li>
      <li>
        <div class="title">Read a LogRocket article</div>
        <button onclick="isTaskComplete(true)">✅</button>
      </li>
      <li>
        <div class="title">Watch One Piece</div>
        <button onclick="isTaskComplete(true)">✅</button>
      </li>
    </ul>
  </article>
  <article class="col">
    <h2>Done</h2>
    <ul id="list-done">
      <li>Wash clothes</li>
      <li>Set an alarm</li>
    </ul>
  </article>
</div>

```

在上面的代码中，我们将一个`isTaskComplete`函数传递给按钮的`click`事件。我们将进一步了解`isTaskComplete`接下来会做什么。

### 设置 JavaScript 方面

现在，让我们设置演示的 JavaScript 方面，并探索一下`isTaskComplete`函数的作用:

```
async function isTaskComplete(taskComplete) {
  const task = this.window.event.target.closest("li");

  // Get the id of the target list
  const destination = document.getElementById(
    `list-${taskComplete ? "done" : "not-done"}`
  );

  // We'll use this class to hide the button when a task is done
  task.classList.add("task-done");

  if (document.createDocumentTransition) {
    const taskTransition = document.createDocumentTransition();
    // Activate the animation
    await taskTransition.start(() => destination.appendChild(task));
  } else {
    destination.appendChild(task);
  }
}

```

我们来分解一下上面的代码。首先，`isTaskComplete`函数接受一个布尔值作为参数。从那里，我们初始化了一个包含离事件发生地最近的`li`的`task`变量。

`destination`变量的值根据`taskComplete`的状态而变化。当`taskComplete`为`true`时，目的地为`list-done`。但是，当`taskComplete`为`false`时，目的地为`list-not-done`。

我们还在`task`中添加了一个`task-done`类，用于在任务被标记为完成时隐藏按钮。此外，我们检查了浏览器是否支持带有`if`语句的视图转换 API。如果没有，我们使用`createDocumentTransition`方法创建一个`taskTransition`实例，使用`start`方法触发转换。

如果浏览器不支持视图转换 API，我们直接添加任务，而不添加任何从一个位置到另一个位置的状态变化转换。

这里是我们添加的`task-done`类。它负责隐藏任务的`button`:

```
.task-done button {
  visibility: hidden;
}

```

这个实现揭示了视图转换 API 是如何工作的。

## 结论

视图转换 API 开辟了一个新的可能性边界，这就是我们可以带给 web 的独特而愉悦的体验。尽管它很棒，但重要的是要记住，在撰写本文时，它仍处于测试阶段。因此，API 随时都会发生变化。然而，这不会阻止我们去试验和了解它！

这里有一些很棒的例子、演示和视图转换 API 的实现，你应该看看: [Geoff Rich](https://twitter.com/geoffrich_) 用 [SvelteKit](https://blog.logrocket.com/exploring-sveltekit-the-newest-svelte-based-framework/) 构建了这个[水果数据应用](https://sveltekit-shared-element-transitions-codelab.vercel.app/fruits)并写了一篇关于它的文章， [Maxi Ferreira](https://twitter.com/charca) 用 [Astro](https://blog.logrocket.com/astro-build-faster-apps-less-javascript/) 和[制作了一个](https://www.maxiferreira.com/blog/astro-page-transitions/)[电影应用](https://astro-movies.netlify.app/)解释了他是如何实现的，以及 [Jake Archibald](https://twitter.com/jaffathecake)

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。