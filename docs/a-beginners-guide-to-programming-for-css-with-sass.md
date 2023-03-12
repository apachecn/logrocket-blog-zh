# 用 Sass - LogRocket 博客进行 CSS 编程的初学者指南

> 原文：<https://blog.logrocket.com/a-beginners-guide-to-programming-for-css-with-sass/>

近几年来，CSS 已经从编写简单的规则集转变为一个强大的引擎，用于创建漂亮的用户体验。但是随着项目变得越来越复杂，就像正在编写的 CSS 一样，管理变得越来越难，做出改变就更难了。原生 CSS 已经实现了一些新的工具来帮助解决这个问题，比如 Grid 和 variables，但是 CSS 预处理程序将 CSS 的编写和管理提升到了一个新的层次。

除了编写灵活的、可重用的规则集，像 Sass 这样的 CSS 预处理程序给 CSS 带来了强大的程序逻辑。没错——使用 Sass，您可以执行算术运算，编写函数(`@mixins`)，甚至可以使用像 if/else 语句和循环这样的控制结构。在本文中，我们将看看用 CSS 编写可编程逻辑的所有方法。正在使用的语法是 SCSS。

提示:如果你以前从未使用过 SCSS，我建议你看看这个指南开始使用。但是不要担心！如果你只是想看看 SCSS 在程序逻辑方面提供了什么，你可以在 [CodePen.io](https://codepen.io/) 中这样做

## 低垂的果实:算术运算符

在 SCSS，开始编程最简单的方法就是做数学。您可以执行五种算术运算:

*   与`+`相加
*   用`-`减法
*   与`*`相乘
*   用`/`除法
*   用`%`求余数

这允许你在字体大小中使用数学，甚至在样式中创建漂亮的渐变。这里有一个例子:

```
  $scale: 1.5;
    $heading-base: 65px;

    h1 {
      font-size: $heading-base;
    }

    h2 {
      font-size: $heading-base / $scale;
    }
```

多亏了一个简单的除数，你的标题字体大小现在可以相对缩放了。稍后，您将看到如何让它变得更好。

如果你只能在相似的单元上使用这些操作符，有一点需要注意。所以你*举例来说*不能乘以`px`和`em`。你*可以*对十六进制颜色使用操作符来制作一些有趣的颜色！一般来说，在 SCSS 的数学课上，我尽可能只用数字，不用单位。

但是在 CSS 中使用数学仅仅是个开始。让我们来看看 SCSS 更强大的编程功能。

## @ mixins:SCSS 的功能

可重复代码是任何编程语言的一个重要方面，SCSS 也不例外。使用`@mixin`，你可以创建一组可重用的代码。这里有一个简单的例子:

```
@mixin button() {
        background: #880000;
        color: #FFFFFF;
        border-radius: 10px;
        padding: 15px;
        text-align: center;
}

a.button {
  @include button;
}
```

在这个 mixin 中，我们有一套所有按钮的通用规则。然后，当我们声明应该有那些规则的东西时，我们可以使用`@include`指令来导入那个规则集。但事情会变得更好。您也可以将变量传递给 mixins。比方说，你想控制按钮的颜色:

```
@mixin button($color) {
        background: $color;
        color: #FFFFFF;
        border-radius: 10px;
        padding: 15px;
        text-align: center;
}

a.button {
  @include button(#008800);
}
```

最后，如果您想要一个默认的可变颜色，您可以设置一个可选变量:

```
@mixin button($color: #880000) {...}
```

然后如果你发一个颜色，那就是二手的。如果没有，则使用默认值`#880000`。

可以想象，这对于拥有、控制和修改一组通用样式来说是非常强大的。如果所有按钮的一般样式都改变了，你只需要在`@mixin`中改变它。

有了一般化代码并使其可重复的能力，您可能还想在您的工具集中添加另一个东西:决策。

## 控制流程:在 CSS 中使用 if 语句

有些人会说，编程很大程度上是基于某种条件做出特定的决定。虽然我将把哲学上的讨论留到另一篇文章中，但决策的确释放了代码中控制程序流程的巨大能量。

在 SCSS，这不是对实时交互做出反应(这就是媒体查询的目的)，而是能够整合和更好地管理大量规则集。让我们看一个例子:

```
@mixin shape($w, $h, $circle: false) {
        width: $w;
        height: $h;
        background: #FF0000;
        @if $circle {
                border-radius: 50%;
        }
}

.square {
  @include shape(100px, 100px);
}

.circle {
  @include shape(100px, 100px, true);
}
```

在这个 mixin 中，我们创建了一个普通的形状(嗯，实际上是一个正方形)，但是由于有了`@if`，我们也可以使用同一个 mixin 来创建一个圆形。在一个更实际的例子中，使用`@else`来管理“黑暗模式”的基本样式:

```
@mixin theme-styles($dark-mode: false) {
 @if $dark-mode {
    //Set styles based on dark mode variables
  } @else {
    //Set styles based on light mode variables
  }       
}
```

因此，math 让我们可以更好地计算我们的度量，mixins 让我们可以概括规则集，使它们更容易从高层次进行管理，if/else 语句让我们可以根据需要有条件地添加规则集和属性，从而使管理更进一步。

但也许在 SCSS 程序逻辑中最节省时间的是循环。

## 重复:用循环创造美丽的风格

只要某些条件为真，循环就可以让你重复一个任务。SCSS 有三种环路:

### 对于循环

`@for`循环是一个简单的计数循环。您可以执行特定次数的任务。这方面的一个很好的例子是轻松地创建我们前面看到的缩放标题大小:

```
$heading-size: 65px;

@for $i from 1 through 6 {
        h#{$i} {
                font-size: $base-size;
        }

        $heading-size: $heading-size / 1.25;
}
```

这里，我们有一个定义为`$i`的变量(通常在循环中使用；你可以把它想成“index”或者“interator”)，从 1 开始，递增到 6。然后我们在 SCSS 中有一点聪明的语法，让我们在选择器中使用变量名。首先，我们使用散列符号(`#`)来表示我们正在开始一个变量名，然后我们用花括号(`{ }`)将变量括起来。

所以这里我们使用`$i`来定义 6 个标题标签中的每一个。然后我们根据变量`$heading-size`设置字体大小。在循环的最后，我们将变量除以 1.25，使它变小，然后对下一个标题标签做同样的事情。所以渲染后的 CSS 看起来像这样:

```
h1 { font-size: 65px; }
h2 { font-size: 52px; }
h3 { font-size: 41.6px; }
h4 { font-size: 33.28px; }
h5 { font-size: 26.624px; }
h6 { font-size: 21.2992px; }
```

很好，精确的标题尺寸！

### While 循环

`@while`循环是相似的，但是我们并没有被归入一个单独的增量器。我们可以做一些事情，如创建一个缩放梯度，基于一些乘数:

```
$gradient: 1;
$i: 1;
@while $gradient >= 0.1 {
        main p:nth-child(#{$i}) {
                background: rgba(0,0,240, $gradient);
                min-height: 20px;
                margin: 0;
        }

        $gradient: $gradient - 0.1;
        $i: $i + 1;
}

```

在这个例子中，我们的目标是`<main>`元素中的`<p>`，并为每个子元素降低蓝色渐变。我们这里有两个计数器:控制不透明度的`$gradient`和为`nth-child`计数的`$i`。

这也可以用来创建一组渐变类，或者为长内容创建一个漂亮的视觉效果。

### 每个循环

也许 SCSS 最有趣的环路是`@each`环路。这让你可以遍历变量列表(SCSS 版本的数组)。通过正确的信息组合，你可以做一些非常漂亮的事情。假设您想要一系列警报。您可以像这样循环遍历颜色数组:

```
$alerts: red, blue, green, yellow;

@each $alert in $alerts {
        .alert-#{$alert} {
                background: $alert;
                color: #FFFFFF;
                padding: 10px;
                text-align: center;
        }
}
```

这意味着，对于`$alerts`列表中的每个条目，用条目的名称创建一个类，并将条目设置为背景色。

您可以在列表中使用映射-键→值对更进一步。如果您想要更具描述性的名称，可以这样做:

```
$alerts: ("error" : red,
    "neutral" : blue,
    "good" : green,
    "warning" : yellow
);

@each $name, $color in $alerts {
        .alert-#{$name} {
                background: $color;
                color: #FFFFFF;
                padding: 10px;
                text-align: center;
        }
}
```

这是快速处理大量重复 CSS 的有效方法。您可以想象一个想要使用的定制图标字体，其中有一个图标名称的映射，以及字形的 unicode 值。

## 向前迈进，编写 CSS！

这样，您就可以开始编写自己的 SCSS 了。现在，您已经有了进行数学运算、if 语句和样式循环所需的语法。你打算用这种新发现的力量做什么？请在评论中告诉我们！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。