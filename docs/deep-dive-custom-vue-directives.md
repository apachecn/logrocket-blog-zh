# 深入了解自定义 Vue 指令

> 原文：<https://blog.logrocket.com/deep-dive-custom-vue-directives/>

Vue 开发人员经常使用指令在他们的应用程序中快速操作 DOM 元素。指令使代码更加清晰易读，并鼓励代码重用。创建自定义指令的能力也让 Vue 比 React 略胜一筹。

本教程将带您浏览 Vue 的自定义指令，并说明如何在您的项目中实现它们。您还将了解它们如何简化您的开发过程，并帮助构建一些方便的特性。

本指南的唯一先决条件是 JavaScript、CSS 和 [Vue](https://blog.logrocket.com/tag/vue/) 的工作知识。

## 什么是 Vue 指令？

要学习自定义指令，最好先熟悉 Vue 指令的基本概念。

Vue 指令是用于与 DOM 交互的特殊属性。换句话说，在使用时，指令会对 DOM 施加副作用或修改。指令的可选属性使您能够定义可能的副作用。

Vue 中的指令总是有一个“v-”前缀。默认指令的一些例子有`v-if`、`v-else`、`v-show`和`v-model`。你可以在这里看到[的完整名单](https://v3.vuejs.org/api/directives.html)。

## 在 Vue 中使用指令

下图说明了 Vue 指令的结构:

![Defining a directive in Vue](img/d1316308ca70bd732e1cbfb05841e59c.png)

表达式、参数和修饰符是可选的；它们的实现可能因手头的任务而异。一个指令一次只能接受一个表达式。

让我们看一个基本的例子来理解这意味着什么。我假设您知道如何使用 Vue CLI 创建项目。如果你现在没有使用 Vue CLI，你也可以像我在本教程中演示的那样使用 CodePen。

看看下面例子中的`v-if`和`v-else`指令。这些分别用于有条件地显示假的`logged-in`和`logged-out`状态的消息和动作。

参见 Pen [Vue 指令示例:v-if，v-else](https://codepen.io/_rahul/pen/OJxYWJj)by Rahul([@ _ Rahul](https://codepen.io/_rahul))
on[code Pen](https://codepen.io)。

双引号中的`v-if`指令的值就是我们这里的表达式。`isUserLoggedIn`的值是`true`，因此应用程序将显示欢迎消息。同样，将其设置为`false`会在浏览器中弹出一个登录按钮。

如您所见，`v-else`指令没有附加表达式值、参数或修饰符。这表明指令的所有属性都是完全可选的。

现在，如果您仔细观察 DOM，您会看到只有一个元素是基于`isUserLoggedIn`变量的值呈现的。

### 指示性参数

一个指令一次只能有一个参数，并且总是在指令名后以冒号开头。让我们继续我们的登录/注销示例，并使用`v-on`指令了解指令参数。

以前，我们在代码中手动更改了`isUserLoggedIn`的值，以显示条件指令。这个例子展示了我们如何通过点击一个按钮来控制它。

参见 Pen [Vue 指令示例:带 v-on](https://codepen.io/_rahul/pen/GRMaVeL) 的参数由 Rahul([@ _ Rahul](https://codepen.io/_rahul))
on[code Pen](https://codepen.io)。

这里，`click`是`v-on`指令的一个参数。现在，如果我们单击登录按钮，我们会收到一条登录成功的消息。

### 参数修饰符

理想的做法是在 HTML 表单元素中使用登录输入元素，然后在 HTML 表单中包装我们的消息和按钮。此外，让我们将`v-on`提升到表单的一个级别，并添加一个`submit`参数，而不是向按钮添加一个事件。

向`v-on`添加`submit`参数将为我们的表单提供`onSubmit`功能。我们不再需要按钮的`v-on:click`事件，因为它现在将自己作为提交按钮。

但是具有`onSubmit`属性的表单在提交时会重新加载整个页面。我们现在不需要这个。为了阻止表单重新加载，我们可以使用一个`prevent`修饰符。

参见 Pen [Vue 指令示例:带 v-on](https://codepen.io/_rahul/pen/oNGOdOm) 的参数修饰符由 Rahul([@ _ Rahul](https://codepen.io/_rahul))
on[code Pen](https://codepen.io)。

在上面的例子中，`prevent`修饰符的工作方式就像`e.preventDefault()` JavaScript 方法一样。它只是阻止表单重新加载页面。

修改器总是以点开始，并且可以与其他修改器链接在一起。我们将在教程的后面看到修改器链接的例子。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 自订检视指示

使用指令 API 添加的指令是自定义指令。作为开发人员，您可以完全控制自定义指令的名称、参数、修饰符和功能。

有两种方法可以在 Vue 中定义自定义指令。第一个是标准，它使指令在应用程序中全局可用:

```
const app = Vue.createApp({})
app.directive("test", {
  // TODO
})

```

你可以在应用程序的任何地方使用这个`v-test`指令，但是它现在不做任何事情，所以我们将为它添加一些功能。

或者，您可以对组件使用`directives`选项，这使得该指令在该组件的本地上下文中可用。

由于 Vue 自己处理指令前缀，您应该避免在定义指令时添加`v-`。

### 创建自定义 Vue 指令

让我们定义一个指令，将任何给定元素的文本颜色更改为蓝色。

参见笔 [自定义指令示例:v-blue](https://codepen.io/_rahul/pen/JjrVLeN)by Rahul([@ _ Rahul](https://codepen.io/_rahul))
on[code Pen](https://codepen.io)。

然而，这不是那么有效，因为它只能使文本变蓝。除了在指令中硬编码颜色值，我们还可以通过引入自定义颜色来改进它。

参见笔 [自定义指令示例:v-color](https://codepen.io/_rahul/pen/jOGRxGq)by Rahul([@ _ Rahul](https://codepen.io/_rahul))
on[code Pen](https://codepen.io)。

现在看起来好多了。我们在指令中使用了`mounted`生命周期钩子，它适合在我们的元素上添加常规的 DOM 操作。我们将在下一节讨论更多关于生命周期挂钩的内容。

需要注意的是，我们已经将颜色值用单引号括起来了。这是因为 Vue 指令接受值的表达式，而不仅仅是普通的值(在本例中是字符串)。

我们还将颜色的值用单引号括起来，使它看起来更像一个表达式。指令的值可以是被接受为有效 JavaScript 表达式的任何值。

### JavaScript 对象文字作为指令表达式

可以同时向指令传递多个颜色值，然后稍后将它们应用于绑定元素。例如，我们可以同时为文本颜色和背景颜色传递不同的值。

让我们在指令中传递一个带有颜色值的对象文字，然后在挂载的钩子中进行相应的设置。

参见笔 [自定义指令示例:高级 v 色](https://codepen.io/_rahul/pen/NWamzdj)由拉胡尔([@ _ 拉胡尔](https://codepen.io/_rahul) )
在 [CodePen](https://codepen.io) 上。

在上面的例子中，我们使用了 JavaScript 中的析构赋值来简化对象文字的赋值。

回到钩子，安装钩子和其他类似的东西的目的是什么？接下来让我们学习更多关于这些指令钩子的知识。

### 生命周期挂钩

指令 API 提供了一组生命周期钩子:`created`、`mounted`、`beforeUpdate`、`updated`、`beforeUnmount`和`unmounted`。

顾名思义，每个挂钩都有不同的功能。让我们逐一检查它们:

在应用绑定元素的属性或事件侦听器之前，调用`created`钩子。如果你有事件监听器，你必须在正常的`v-on`事件监听器被附加之前调用，这是非常有用的。

在安装绑定元素的父组件之前，调用`mounted`钩子。它是我们执行 DOM 操作的地方，定义了我们指令的功能。

在更新父元素的`vNode`之前，调用`beforeUpdate`钩子。假设我有一个点击监听器连接到一个改变当前值的元素。现在，在那个值更新之前，`beforeUpdate`被解雇了。

在包含元素及其子元素的 vNodes 更新后，立即调用`updated`钩子。从上一个例子来看，更新后的钩子将在`beforeUpdate`之后执行。

在卸载绑定元素的父元素之前调用`beforeUnmount`。

当父元素被卸载时,`unmounted`钩子只被调用一次。在这种情况下，指令与元素解除绑定。

每个钩子都接受三个参数:命名、绑定和 vNode。在我们讨论钩子参数之前，我想向你展示钩子在 Vue 中是如何工作的。建议使用完整视图来查看钩子日志中的更新。

参见笔 [自定义 Vue 指令示例:生命周期挂钩](https://codepen.io/_rahul/pen/WNZWyRO)由 Rahul ( [@_rahul](https://codepen.io/_rahul) )
在 [CodePen](https://codepen.io) 上。

只需在文本框中输入一些内容，就能看到它们的实际效果。

### 挂钩参数

正如上一节所讨论的，指令钩子也有一些参数。

指定我们的指令所绑定的元素。我们用它来定位我们的元素并对其进行修改。

绑定参数包含指令属性，如当前的`value`、`oldValue`、`arg`和`modifiers`。随着教程的深入，我们会看到`arg`和`modifiers`的用法。没有这两者，就不可能在 Vue 中为指令创建参数和修饰符。

`vNode`参数基本上是`el`的真实 DOM 对象的副本。`prevNode`参数是`el`之前的状态，仅在`beforeUpdate`和`updated`挂钩中可用。

我鼓励你去看看指令钩子和它们的参数在[这个开放代码](https://codepen.io/_rahul/pen/WNZWyRO)中的作用。在控制台中观察这些参数的性质；这将有助于您将来使用 Vue 指令做高级的事情。

现在我们已经熟悉了自定义指令，是时候创建一些更高级的东西了，比如引用。

## 使用自定义指令提取报价

[引用的概念](https://marcomm.nd.edu/blog/the-difference-between-blockquotes-and-pull-quotes)受到杂志和报纸设计的启发，由一篇文章现有文本中的小引用组成。

因此，我们可以选择一个带有标签的文本片段(例如，span)，用 JavaScript 克隆它，然后用 CSS 样式化克隆以创建一个引用。

在我们进入 JavaScript 部分之前，让我们先讨论一下我们需要的 CSS 来搭建我们的引用并对其进行样式化。

标准是使用 CSS 属性。我们可以将 CSS 属性直接注入到相应的元素中，就像我们在上面的一些例子中所做的那样，或者声明 CSS 类并在`classList` Web API 中使用它们。

后一种方法更好，因为它允许我们保持事情井井有条。

### 关于 CSS 的更多信息

为了保持简单，我选择使用简单的 CSS 样式作为我们的引用示例。下面是我们将使用的所有主要 CSS 属性的摘要。

CSS 浮动和文本对齐将帮助我们左右对齐引用。一些基本的边距和填充属性将用于创建空间和大小。

为了让我们的示例保持移动优先，我们还将添加一个简短的媒体查询。

### CSS 设置

从头开始，在我们的新项目中放置一些 HTML 虚拟数据，作为我们的文章。我们将使用这些数据的片段来制作拉动式报价:

```
<div id="app">
    <article class="content">
        <p>...</p>
        <p>...</p>
        <p>...</p>
        …
    </article>
</div>

```

我们现在将添加正常化。CSS 到我们的项目。如果您在 CodePen 上，只需单击 CSS 面板中的齿轮图标，并选择**规格化**选项。

接下来，定义一个内容宽度将是一个好主意，因为它将为我们提供空间，可以舒适地将我们的引用放在左边或右边:

```
.content {
margin-inline: auto;
max-width: 640px;
}

```

上面的 CSS 将内容的宽度设为 640px，并将其与浏览器的中心对齐。我还在应用程序中添加了一些填充，使其远离边缘。

现在，让我们为引用的默认状态编写一些 CSS:

```
.pulled-quote {
    font-size: 1.1em;
    font-weight: bold;
    display: block;
    text-align: center;
    color: #999;
    padding-block: 1em;
    margin-block: 1.5em;
}

```

这是我们的拉报价在较小设备上的外观。如你所见，无非是一些对齐和字体属性来装饰。让我们也写同样的左和右变量，只有当足够的屏幕空间可用时才起作用:

```
@media only screen and (min-width: 1024px) {
.pulled-quote:not(.pulled-quote--center) {
width: 300px;
}

.pulled-quote--right {
float: right;
margin: 0 -200px 0 0;
padding: 0 0 1.5em 1.5em;
text-align: left;
}
.pulled-quote--left {
        float: left;
        margin: 0 0 0 -200px;
        padding: 0 1.5em 1.5em 0;
        text-align: right;
    }
}

```

我们使用 float 属性将元素从常规流向移动到指定的方向。应用文本对齐和填充来支持浮动。我们还使用负边距将元素从容器中分离出来。

### 指令定义

现在我们有足够的 CSS 来支持我们的引用，让我们写一个指令，看看如何创建有界元素的副本来构造它:

```
const app = Vue.createApp({});
app.directive("pq", {
mounted(el, binding) {
var elParent = el.parentNode
var elClone = el.cloneNode(true)
elParent.prepend(elClone)
elClone.classList.add("pulled-quote")
}
}
```

使用 Web API 的`cloneNode`和`prepend` JavaScript 函数，很容易复制一个节点并将其添加到元素的父元素中。然后，`clasList.add`函数可以添加`.pull-quote` CSS 类。

如果没有提供指令参数，或者默认情况下，我们的引用应该靠左对齐。记住这一点，让我们添加一些指示性参数:

```
const app = Vue.createApp({});
app.directive("pq", {
mounted(el, binding) {
var elParent = el.parentNode
var elClone = el.cloneNode(true)
elParent.prepend(elClone)
elClone.classList.add("pulled-quote")

switch(binding.arg) {
case 'right':
elClone.classList.add("pulled-quote--right")
break
case 'center':
elClone.classList.add("pulled-quote--center")
break
default:
elClone.classList.add("pulled-quote--left")
break
}
}
}

```

因为一个指令一次只接受一个参数，所以我们在每个案例中都使用了 switch 块。您可以看到默认情况下将`"pulled-quote--left"` CSS 添加到绑定元素中。

我们现在将把这个指令应用到一些虚拟数据中，看看它是如何工作的。

参见笔 [自定义指令示例:拉引号](https://codepen.io/_rahul/pen/mdBYREm)由拉胡尔([@ _ 拉胡尔](https://codepen.io/_rahul) )
在 [CodePen](https://codepen.io) 上。

如您所见，我们的拉动式报价现在可以使用了。类似地，我们还可以在指令中添加一些修饰符，以添加一些小细节，如斜体、突出显示和引用文本:

```
const app = Vue.createApp({});
app.directive("pq", {
mounted(el, binding) {
var elParent = el.parentNode
var elClone = el.cloneNode(true)
elParent.prepend(elClone)
elClone.classList.add("pulled-quote")

switch(binding.arg) {
…
}

if(binding.modifiers.italic) {
elClone.style.fontStyle = "italic"
}
        if(binding.modifiers.quoted) {
            elClone.classList.add("pulled-quote--quoted")
        }
        if(binding.modifiers.highlighted) {
            elClone.classList.add("pulled-quote--highlighted")
        }
    }
}

```

此外，我想把缩写较长的文本片段的功能融入到引用中。现在，如果你传递一个字符串作为表达式，它可以作为一个缩写。

这是我们上面所做的所有工作的例子。我添加了一些字体和化妆品，使它看起来更整洁，更有吸引力。

参见笔 [自定义指令示例:拉引号](https://codepen.io/_rahul/pen/eYGoreR)由拉胡尔([@ _ 拉胡尔](https://codepen.io/_rahul) )
在 [CodePen](https://codepen.io) 上。

使用`v-pq`指令，我们现在可以将任何文本块转换成具有不同对齐和样式选项的引用。我也希望看到你对此的补充！

## **总之**

在本教程中，我们学习了 Vue 中的指令是什么，它们为什么有用，以及如何使用它们。然后，我们继续查看自定义指令的示例，以及 Vue 指令 API 中的生命周期挂钩和挂钩参数。

有了这些知识，我们最终创建了一个指令，在我们的 Vue 应用程序中为任何文本片段添加一个引用行为。

我希望您通过本教程学到了一些关于自定义 Vue 指令的新知识。我是 Rahul，我很感激你一直读到最后。欢迎在下面的评论区问我任何问题或提出建议。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。