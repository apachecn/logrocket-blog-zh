# 顺风 CSS v3.0 版的新功能- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-tailwind-css-v3-0/>

## 介绍

Tailwind 3.0 具有令人难以置信的性能增益和许多令人兴奋的新功能。

在这个版本中，Tailwind 解决了以前版本中的一些问题，例如有限的颜色支持和 Tailwind 设计约束的限制。

但是，在 3.0 版本中，Tailwind CSS 将新的实时(JIT)引擎作为默认引擎和唯一模式。这使它能够开箱即用，支持更多颜色，包括五种灰色阴影和任意属性。因此，这使得开发人员能够使用顺风设计约束中未定义的任意值和属性来应用样式。

在本文中，我们将了解 3.0 版的每个特性。

让我们开始下一部分。

## 新功能

以下是 Tailwind 3.0 的一些新增功能:

### 一直都是及时的

Tailwind 3.0 用新的 JIT 引擎取代了经典的 Tailwind 引擎，这给了 3.0 版本巨大的性能提升，并释放了几个令人兴奋的新功能，如闪电般的构建时间，可堆叠的变体，任意值支持，以及更多开箱即用的功能。

### 开箱即用的每种颜色

在以前的版本中， [Tailwind](https://blog.logrocket.com/tailwind-css-vs-bootstrap-ui-kits/) 附带了一组有限的颜色，因为颜色是文件变大的主要原因。然而，由于新的 JIT 引擎，这在 3.0 版中不再是一个问题。

Tailwind v3.0 提供了 22 种颜色，包括所有扩展的调色板颜色，如青色、玫瑰色、紫红色、石灰色和五种不同的灰色。

### 彩色方框阴影

Tailwind 3.0 增加了控制盒子阴影颜色的工具类，这对于创建发光和反射效果很有用。

要改变盒子的阴影，我们可以使用阴影工具`shadow-{color}`。我们也可以通过使用`shadow-{color}/{opacity}`工具来改变盒子阴影的不透明度。

考虑下面的代码:

```
<div class="min-h-screen grid place-items-center">
  <div class="space-x-8">
    <button 
      class="px-8 py-3 rounded-full bg-fuchsia-500 text-white font-semibold 
        shadow-xl shadow-fuchsia-500/30">
          Subscribe
    </button>
    <button 
      class="px-8 py-3 rounded-full bg-indigo-500 text-white font-semibold 
        shadow-xl shadow-indigo-500/30">
          Subscribe
    </button>
    <button class="px-8 py-3 rounded-full bg-pink-500 text-white font-semibold 
      shadow-xl shadow-pink-500/30">
        Subscribe
    </button>
  </div>
</div>

```

[在这里玩代码](https://play.tailwindcss.com/EcJRNvJ7I1)。

Tailwind 版提供了一个强大的滚动捕捉 API，其中包含 CSS 滚动捕捉模块的完整实用程序类列表，从而为开发人员提供了更多的灵活性和功能，以创建更好的滚动和滚动捕捉体验。

新 scroll API 中包含的一些实用程序有:

*   [滚动行为](https://tailwindcss.com/docs/scroll-behavior) —包含用于控制元素滚动行为的实用程序类
*   [Scroll margin](https://tailwindcss.com/docs/scroll-margin) —包含用于控制滚动容器中项目周围滚动偏移量的实用程序类
*   [Scroll padding](https://tailwindcss.com/docs/scroll-padding) —包含用于控制滚动容器内元素的滚动偏移量的实用程序类
*   [滚动对齐](https://tailwindcss.com/docs/scroll-snap-align) —包含用于控制元素滚动对齐的实用程序类
*   [滚动捕捉停止](https://tailwindcss.com/docs/scroll-snap-stop) —包含实用程序类，用于确定是否可以跳过可能的滚动位置
*   [滚动捕捉类型](https://tailwindcss.com/docs/scroll-snap-type) —包含实用程序类，用于确定在捕捉容器中强制捕捉点的严格程度

### 多栏布局

`[columns](https://developer.mozilla.org/en-US/docs/Web/CSS/columns)`是有用的 CSS 速记，Tailwind 在 3.0 中增加了对它们的支持。它们对于设置页脚的样式很有用，如下面的代码所示:

```
<footer class="columns-1 sm:columns-3 ...">
  ...
</footer>

```

同样在 3.0 版本中，Tailwind 添加了`[break-after](https://tailwindcss.com/docs/break-after)`实用程序类，用于控制一个列或页面在一个元素后应该如何换行。

### 本机窗体控件样式

Tailwind 添加了`accent-color`属性和`file`修饰符来简化本机表单的样式。

如下所示，在设置复选框样式时，可以使用强调色:

```
...
<label class="mt-6 flex items-center justify-center space-x-2 text-sm font-medium text-gray-600">
  <input type="checkbox" class="accent-blue-500" checked/>
  <span>I have read and agree to your terms and conditions</span>
</label>
...

```

虽然`file`修改器对样式文件输入很有用，如下所示:

```
...
<label class="block">
  <span class="sr-only">Choose profile photo</span>
  <input type="file" class="block w-full text-sm text-gray-500
    file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm 
    file:font-semibold file:bg-violet-50 file:text-violet-700 
    hover:file:bg-violet-100 "/>
</label>
...

```

### 打印修改器

这个特性添加了一个`print`修饰符，使我们能够添加样式来决定我们的文档在打印时的外观。

考虑下面的代码:

```
...
<section>
  <header class="print:hidden">
    <!-- Some naviagation markup -->
  </header>
  ...
</section>
...

```

上面的代码会在打印站点时隐藏导航。使用`print`修饰符将我们的代码包装在一个 [`[print]`媒体查询](https://developer.mozilla.org/en-US/docs/Web/CSS/@media)中，如下所示:

```
@media print { ... }

```

因此，上面的`print`修饰符应该是:

```
@media print {
  .print\:hidden {
    display: none;
  }
}

```

### 现代纵横比 API

随着浏览器支持的增加，Tailwind 增加了对`[aspect-ratio](https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio)`属性的支持。属性为一个用来计算自动尺寸和一些其他布局功能的框设置一个默认的纵横比。

考虑下面的代码:

```
<iframe 
  class="w-full aspect-video hover:aspect-square" 
    src="https://www.youtube.com/..."
>
</iframe>

```

顺风只给了我们三个可能的长宽比值:`aspect-auto`、`aspect-square`和`aspect-video`。然而，我们可以利用新的任意属性特性来应用我们想要的任意值。当我们讨论任意属性时，我们将看到这一点。

### 任意属性

任意属性是类固醇的内联样式。它们为 Tailwind CSS 增加了灵活性，使开发人员能够使用任何 CSS 属性，即使它没有在 Tailwind CSS 中定义。

在较低的顺风版本中，我们可以通过使用顺风的括号符号在[即时模式](https://v2.tailwindcss.com/docs/just-in-time-mode#arbitrary-value-support)中使用任意值:

```
<!-- Colors -->
<button class="bg-[#1da1f1]">Share on Twitter</button>

```

然而，由于 JIT 模式现在是 Tailwind v3 中的默认模式，我们可以简单地通过使用 Tailwind 的括号符号来使用没有在 Tailwind 设计约束中定义的任意属性。

考虑下面的代码:

```
<article>
  <img class="w-64 float-left aspect-[1/1] lg:aspect-[1/2] 
    rounded-lg shadow-lg object-cover object-center mb-0 mr-6 
    [clip-path:circle(70%_at_20%_30%)] 
    [shape-outside:circle(70%_at_20%_30%)]"

    src="https://images.unsplash.com/photo-1527980965255-d3b416303d12?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1760&q=80"

    alt="John Doe Avatar"
  />

  <p>
  Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
  </p>

</article>

```

同样，在上面的代码中，我们使用了 Tailwind 的括号符号来添加任意的`clip-path`和`shape-outside`值。以前在 Tailwind 中不支持这些，你可以[在这里](https://play.tailwindcss.com/NTVPCCmt7Z)看到运行中的代码。

任意值可以与 CSS 变量一起使用，并且可以与任何顺风 CSS 修改器结合使用。如下所示，我们重构了上面的示例，以提高响应速度:

```
<article>
  <img class="w-64 float-left aspect-[1/1] lg:aspect-[1/2]
  rounded-lg shadow-lg object-cover object-center mb-0 mr-6
  [clip-path:circle(70%_at_20%_30%)]
  [shape-outside:circle(70%_at_20%_30%)]
  md:[clip-path:polygon(0%_0%,100%_0%,75%_100%,0%_100%)]
  md:[shape-outside:polygon(0%_0%,100%_0%,75%_100%,0%_100%)]"

  src="https://images.unsplash.com/photo-1527980965255-d3b416303d12?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1760&q=80"

  alt="John Doe Avatar"
  />

  <p>
  Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
  </p>
</article>

```

注意任意属性没有空格，因此我们在上面的代码中使用了下划线。你可以在这里看到这一点。

### 花式下划线样式

Tailwind 3.0 增加了新的文本装饰工具类:`[text decoration color](https://tailwindcss.com/docs/text-decoration-color)`、`[text decoration style](https://tailwindcss.com/docs/text-decoration-style)`、`[text decoration thickness](https://tailwindcss.com/docs/text-decoration-thickness)`、[、`text underline offset`、](https://tailwindcss.com/docs/text-underline-offset)。这些用于扩展文本的装饰，如下面所示:

```
<p>
  I’m John Doe, a full-stack developer, writer, and instructor.
  <a href="#" class="underline decoration-blue-500 decoration-4">My Company, Inc</a>. 
  When not coding, I like to 
  <a href="#" class="underline decoration-orange-500 decoration-dotted decoration-2">watch Basket ball</a>
  or watch 
  <a href="#" class="underline decoration-green-500 decoration-wavy decoration-2">Soccer
  </a>
  fights.
</p>

```

你可以在这里看到这一点。

### RTL 和 LTR 修改器

这个特性增加了两个新的修饰符，`rtl`和`ltr`，增加了对多方向(从左到右和从右到左)布局的实验性支持。这些修饰符仅在构建支持多方向布局的站点时有用，最佳做法是在使用它们时显式设置方向:

```
<html dir="ltr">
  <!-- ... -->
</html>

```

### 肖像和风景修改器

Tailwind v3 添加了两个新的修饰符，`portrait`和`landscape`，使我们能够根据视口的方向有条件地将样式应用到我们的站点:

```
<section>
  <article class="portrait:hidden">
    ...
  </article>

  <article class="landscape:hidden">
   ...
  </article>
</section>

```

### 播放 CDN

这是一个创新的顺风 CDN，使我们能够通过 CDN 链接使用顺风 3.0 的所有强大功能:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Tailwind Demo</title>
    <script src="https://cdn.tailwindcss.com/"></script>
  </head>

  <body>
    ...
  </body>
</html>

```

这不是生产就绪，但它是在开发中构建顺风项目的好方法。你可以[在这里](https://tailwindcss.com/docs/installation/play-cdn)了解更多关于 CDN 的信息。

## 结论

Tailwind CSS v3 是一个游戏改变者，它提供了几个令人惊叹的新功能，如任意属性和扩展的颜色支持——开箱即用的 22 种颜色。

这些都是可能的，因为 Tailwind 3.0 使用新的 JIT 引擎作为默认引擎。其他有趣的功能是一个全面的滚动捕捉 API，现代的纵横比 API 和花哨的下划线样式。

我们已经在本文中详细介绍了这些特性，通过阅读本文，您应该准备好开始将 Tailwind 3.0 样式应用到您的项目中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。