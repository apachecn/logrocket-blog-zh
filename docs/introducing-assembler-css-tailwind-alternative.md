# 介绍汇编 CSS，顺风 CSS 的新替代品

> 原文：<https://blog.logrocket.com/introducing-assembler-css-tailwind-alternative/>

CSS 框架是很棒的解决方案，旨在帮助开发人员轻松构建符合标准的 web 应用程序，几乎不需要额外的工作。多年来，我们已经看到数百个这样的框架被开发出来，从不同的灵感到设计方向到不同的 UI 模式。

有鉴于此，一个从其他流行框架(如 Tailwind 和 Bootstrap)获得灵感、用于快速前端设计实现的新框架是汇编器 CSS。

## 什么是汇编 CSS？

Assembler 的[文档](https://asmcss.com/docs/1.x/)将其描述为“一个现代的 UI 框架，设计为在桌面和移动设备上都具有很高的性能，同时提供了静态 CSS 框架难以比拟的全方位功能。”

汇编 CSS 是:

*   简单易学
*   快速和高性能
*   轻量级—大小约 8kB (gzip)
*   可扩展——您可以轻松地将汇编器 CSS 与其他 CSS 框架集成

在本教程中，我们将探索如何在你的项目中开始使用汇编 CSS，以及这个框架提供的特性。

## 装置

开始实现汇编 CSS 的最简单的方法之一是将其 CDN 包含在网页标记的`head`部分，如下所示:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://unpkg.com/@asmcss/assembler/dist/assembler.min.js"></script>
    <title>Learning Assembler CSS</title>
    <style></style>
  </head>
  <body></body>
</html>

```

您也可以使用以下命令安装带有`npm`或`yarn`的汇编器 CSS:

```
npm install @asmcss/assembler
# OR yarn
yarn add @asmcss/assembler

```

## 入门指南

汇编器 CSS 为 HTML 元素引入了一个新的`x-style`属性，它类似于默认 HTML `style`属性的工作方式，但更好。为了演示这一点，请考虑下面的代码:

```
<div
  style="
  width: 100px;
  height: 100px;
  background: #ff5722;
  border-radius: 50%;
  margin: auto;
"
></div>

```

它产生以下输出:

![x-style Assembler CSS Demo](img/786f0b63410cc48dd0758b3f0991986f.png)

可以使用相同的 CSS 属性和带有`x-style`属性的值重新创建该组件:

```
<div
  x-style="
  width: 100px;
  height: 100px;
  background: #ff5722;
  border-radius: 50%;
  margin: auto;
"
></div>

```

您会注意到，这两个代码块之间的唯一区别是将`style`属性更改为`x-style`。虽然这可能看起来没什么意思，但这是为了解释使用汇编 CSS 是多么容易，在接下来的章节中，我们将深入探索这个框架提供的强大功能。

## 虚拟财产

汇编器 CSS 围绕虚拟属性发展，虚拟属性是一个由几乎所有 CSS 属性组成的列表，灵感来自 Bootstrap 和 Tailwind 等流行的 CSS 框架。这类似于实用程序类的工作方式，但是这次使用自定义值。

使用虚拟属性，我们可以重写前面的示例，如下所示:

```
<div x-style="w:100px; h:100px; bg:#643296; radius:50%; m:auto;"></div>

```

我们可以看到，这不太麻烦，更容易阅读，运行代码将产生相同的输出:

![x-style Assembler CSS Demo](img/786f0b63410cc48dd0758b3f0991986f.png)

值得一提的是，一些虚拟属性有关联的默认值，其中一些可以根据它们的值映射到不同的属性。属于这一类别的一个例子是如果没有指定值，映射到`display: flex;`的`flex`属性。或者控制柔性项目上的柔性长度(如`flex: auto;`)，如果指定了值:

```
<div x-style="flex; flex:auto"></div>
<!-- is an equivalent of -->
<div x-style="display:flex; flex:auto"></div>

```

## CSS 变量

汇编器 CSS `x-style`也让你容易地访问 CSS 变量，使用`@`符号后跟变量名，或者使用标准的`var()`语法:

```
<style>
  :root {
    --primary: #3f51b5;
    --secondary: #673ab7;
  }
</style>

<p x-style="color:@primary">Hello World!</p>
<p x-style="color:var(--secondary)" lang="es">Hola Mundo!</p>

```

## 州

汇编器 CSS 的另一个很棒的特性是为不同的状态改变样式；也就是说，当用户悬停在按钮上时，你可以直接从`x-style`属性改变按钮的颜色。

尽管其他 CSS 框架如 Tailwind 已经提供了类似的特性，但是使用汇编 CSS，您可以为每个状态变化定义自定义值。

我们可以在汇编器 CSS 中使用这个特性，方法是将句点符号`.`附加到属性名，后面是您想要定位的状态名。

例如，在焦点上更改输入元素的边框将如下所示:

```
<input
x-style="border: 1px solid #ccc;border.focus: 2px solid #ccc;"
type="text"
/>

```

下面是一个更详细的例子，当用户将鼠标悬停在`div`上时，它的背景颜色变为黑色:

```
<div
x-style="
  bg: #ffcc00;
  bg.hover: #1f1f23;
  color: #fff;
  p: 40px;
  text: 20px;
  radius;
  shadow;
"
>
 HOVER YOUR MOUSE HERE
</div>

```

![Hover Black Assembler CSS](img/50886226dc6023d4c8a994c3c785feee.png)

使用该功能的一个缺点是，在每次状态更改期间，您需要将您的状态附加到要修改的属性上，这可能会使我们的代码更加健壮。

假设我们想在用户悬停在`div`上时改变背景颜色、文本颜色和文本大小。在前面的例子中，我们的代码看起来是这样的:

```
<div
  x-style="
  bg: #ffcc00;
  bg.hover: #1f1f23;
  color: #fff;
  color.hover: #ccc;
  text: 20px;
  text.hover: 30px;
"
>
  HOVER YOUR MOUSE HERE
</div>

```

## 媒体断点

用汇编 CSS 制作响应式设计很简单。

与 Bootstrap 和 Tailwind 等框架不同，它们只允许您修改桌面和移动设备上的边距、显示和填充等属性，Assembler CSS 允许您直接从`x-style`属性轻松修改它提供的所有虚拟属性，用于多个断点。

我们可以通过在虚拟属性名称前加上|符号和断点名称来定位特定的断点:

```
<div x-style="bg:red; sm|bg:green; md|bg:blue; w:150px; h:150px; mx:auto"></div>

```

运行上面的代码将在小型设备上显示一个绿色框，在中型台式机和平板电脑上显示一个蓝色框，在其他设备上显示一个红色框。

支持的断点包括:

*   `xs` —超小型设备
*   `sm` —小型设备
*   `md` —中型设备
*   `lg` —大型设备，以及
*   `xl` —超大型设备

## 混合蛋白

混合可能是汇编 CSS 中最有用的特性之一，因为它们允许你编写可重用的样式规则，这些规则可以应用于多个元素。

通过将单词`--mixin`附加到 CSS 变量来创建 mixin，我们可以通过在 mixin 的名称前加上`^`符号将它们应用到我们的元素(通过`x-style`)属性:

```
<style>
  :root {
    --btn--mixin: "px:10px; py:10px; rounded; font-weight:bold; outline:none; border:none;";
  }
</style>
<button x-style="^btn">Click Me</button>

```

### 混合参数

在汇编器 CSS 中，您也可以在应用参数时将参数传递给 mixin，然后在创建 mixin 时将这些参数作为参数进行访问。

mixin 内部的参数可以通过使用`${index}`来访问，其中`index`是参数的*从零开始的索引*:

```
<style>
  :root {
    --btn--mixin: "px:10px; py:10px; rounded; font-weight:bold; outline:none; border:none;";
    --btn-bg--mixin: "color:#fff;bg-color: ${0};
  }
</style>
<button x-style="^btn ^btn-bg:tomato">Click Me</button>

```

在上面的代码示例中，我们首先创建了一个 mixin ( `btn`)来帮助将基本样式应用于按钮，然后创建了一个新的 mixin `btn-bg`来修改按钮的颜色。在我们的按钮元素中，我们通过在 mixin 名称前加上冒号`:`，后跟参数值来传递参数。

## 结论

汇编器 CSS 帮助你不用安装和维护复杂的软件栈就能构建漂亮的 UI 组件。在本教程中，我们讨论了如何安装这个框架，并深入介绍了它的特性。

还值得一提的是，汇编器 CSS 是一个非常新的 CSS 框架，它于 2021 年 8 月 11 日首次发布，这意味着我们应该期待在不久的将来会有大量令人兴奋的更新。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。