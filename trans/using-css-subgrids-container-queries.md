# 在容器查询中使用 CSS 子网格

> 原文：<https://blog.logrocket.com/using-css-subgrids-container-queries/>

如果您经常使用 CSS，您可能会遇到这样的情况:当一侧的大小发生变化时，并排放置的布局会断开或错位。几乎每个网页设计师都面临这个问题，解决起来可能会令人沮丧。幸运的是，CSS 有一些特性允许你创建网页布局，当内容改变时保持对齐；这些被称为子网格和容器查询。

CSS 子网格和容器查询是两个可以极大地改进 web 布局设计和响应性 web 开发的特性。本文将通过提供一个教程来深入研究 CSS 子网格，在容器查询中无缝地使用它们。

### 内容

## 什么是 CSS 子网格？

CSS 子网格是 CSS 生态系统中相对较新的特性，被证明是对二级 CSS 网格 web 设计和布局规范的重要补充。

您在传统网格容器中构建和调整的元素(子组件)并不直接从其父组件继承行和列。这使得它们很难与父网格保持一致，因为每个元素都倾向于独立工作。

然后，您必须管理两个独立的网格，这可能会造成冗余。您可以通过添加`subgrid`作为`grid-template-rows`和`grid-template-columns`属性的值来快速解决这个问题，因为网格子组件现在可以完美地与其父组件同步。

CSS 子网格允许子组件继承它们的父行和父列设置，而不维护它们的网格设置。

考虑下面嵌套网格系统中的三张卡:

![Cards in a nested grid system](img/0f85bd1eb4de46523072252d785a011d.png)

看看当你增加卡片中心的内容时会发生什么:

![Nested grid cards increased center content](img/858704e1f42b8b53846d5b80607efd41.png)

您可以立即看到，随着内容的增加，标题和页脚的位置受到了影响。

处理这个问题有几种方法。尽管如此，它们最终都需要您手动管理嵌套网格系统中的各个网格，这在处理较大的代码库时可能会有问题。

子网格现在提供了另一种修复方法，允许行和列在网格系统中保持它们的初始位置，尽管它们的内容发生了变化。

在我上面的例子中，添加`subgrid`作为我的卡的`grid-template-row`的值将产生不同的输出:

```
.card {
  grid-template-rows: subgrid;
}

```

结果如下:

![subgrid cards](img/c5fda618ce0af2c65d659ee6e18848b8.png)

尽管第二张卡片的内容增加了，但是标题和页脚元素的位置仍然很好，并且与父网格同步。

子网格是 CSS 网格布局规范的重要补充，因为子组件能够继承其父组件和祖父组件的属性。在构建嵌套网格系统、完美对齐的表单布局和其他编辑性 web 对齐时，它非常方便。

> 目前只有 Firefox 浏览器支持 CSS 子网格，但其他流行的浏览器也不甘落后。你可以参考本指南来深入了解 CSS 子网格及其功能。

## 什么是 CSS 容器查询？

自从将媒体查询引入浏览器以来，响应式 web 设计已经向未来迈进了一大步，因为现在您可以为浏览器视窗设计特定的布局。

这很好，但是当创建更复杂的网站结构时，媒体查询也暴露了冗余问题。例如，包装在包含节中的组件需要调整它们的主要布局属性(如宽度或高度)以适应网页视窗的变化，这可能会影响和破坏设计。

要解决此问题，请保持组件的媒体查询与包含部分同步，以确保您的组件在所有视口大小下看起来都不错。使用容器查询可以很容易地解决这个问题，因为现在可以根据元素的实际属性来设计元素，而不是视口大小。容器查询允许任何组件响应指定的容器。

例如，在网格系统中，子组件可以与它们的容器保持一致，并决定它们的行为和响应。您可以根据它们在容器(而不是视口)中的位置来修改它们的样式。虽然您仍然可以使用响应式网格布局来构建 web 页面，但是使用容器查询是一种更直接的响应式 web 设计方法。

## CSS 容器查询是如何工作的？

关于 CSS 容器查询，首先要理解的是，虽然“容器”是被查询的元素，但是容器查询中的规则只影响容器的后代。例如，如果您查询一个`main`、`section`或者一个`div`作为您的目标容器，容器查询将允许您定义规则，以确定当容器大小改变时，其中的元素如何改变。

您可以使用`@container`创建一个容器查询。它寻找最接近的包容上下文:

```
.card {
  display: flex;
  flex-direction: column;
}

.container {
  background: #fff;
  container-type: layout inline-size
  container-name: something;
}

@container something (min-width: 450px) {
  .card {
    display: flex;
    flex-direction: row
  }
}

```

上面的代码演示了如何使用容器查询。如果容器的宽度至少为 450px，它只是指示浏览器将卡片的`flex-direction`列的值改为`row`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

容器查询被指定包含在`CSS containment`中。伴随它们的有三个属性:

### 1.`container-type`

此属性将查询容器分配给元素，以便其子组件可以查询其布局功能的几个方面。

*   `size`为块和内嵌轴维度查询创建查询容器。布局、样式和大小控制将应用于元素
*   `inline-size`为容器的内嵌轴上的维度查询创建一个查询容器。应用元素的布局、样式和内联大小包含
*   `block-size`为容器的块轴上的维度查询创建一个查询容器。布局、样式和块大小包含被应用到元素
*   `style`用于样式查询，并创建一个查询容器
*   `state`为状态查询创建查询容器

### 2.`container-name`

进一步指定容器名称的可选属性。当我们不想以最近的父容器为目标时，这个属性就像一个出口。

考虑下面的代码:

```
.sidebar {
  container-type: inline-size;
  container-name: sidebar;
}

```

上面的 CSS 生成了一个具有指定名称(`sidebar`)的容器，该容器只包含内联轴上的内容，这意味着内容可以根据需要在块轴上增长。然后，您可以用它的`container-name`查询这个特定的容器，如下所示:

```
@container sidebar (min-width: 450px){
  .card {
    display: flex;
    flex-direction: row
  }
}

```

### 3.`container`

这个属性通常被用作一个简写属性，可以用来在一个语句中同时设置`container-type`和`container-name`。

## CSS 容器查询的用例

容器查询适用于布局依赖于可用容器空间的高度可重用组件。它们可以在各种情况下使用，或者添加到页面上的多个容器中。

其他容器查询用例包括:

*   适应性布局
*   卡片、表单元素、横幅和其他模块化组件
*   CSS 调整大小实验
*   具有不同功能的移动和桌面分页

大多数浏览器仍然不支持容器查询，但是你可以在 Google Chrome Canary 上测试它们的特性。转至`chrome://flags`并启用`CSS Container` `Queries`:

![Enabling css container queries](img/9684056bbac4b026e2717811f5f378aa.png)

您还可以在 Mozilla Firefox 上试验容器查询，方法是转到`about:config`并启用`layout.css.container-queries`:

![enabling container queries firefox](img/a4582842d46e418e5b29e88fe6a3b1a1.png)

容器查询目前正处于第一个公开的工作草案中，但它是一个被证明很有帮助的特性，很可能会被集成到所有的浏览器版本中。

## 在容器查询中使用 CSS 子网格

CSS 子网格和容器查询是两个迷人的 CSS 特性，我认为它们将改变你的网页布局设计和响应样式，但是我们如何将它们结合起来呢？

假设我们有一个包含三篇文章的部分，格式如下:

```
<section class="container">
  <article class="article1">...</article>
  <article class="article2">...</article>
  <article class="article3">...</article>
</section>

```

现在让我们将这些文章放在嵌套网格系统中，子网格和容器查询可以在其中发挥作用:

```
/* this creates a container with containment on the inline axis only and gives it an optional name "main" */
html, section {
  container-type: inline-size;
  container-name: main;
}

/* targeting the section's minimum width at "60ch" */
@container main (min-width: 60ch) {
  section {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}

/* targeting the section's minimum width at "100ch" */
@container main (min-width: 100ch) {
  article {
    padding: 1rem;
    font-size: 1rem;
    border: 1px solid #000;
    margin: 10px 20px;
    display: grid;
    grid-template-rows: auto 1fr auto;
  }

  /* enabling a more responsive grid layout so elements don't break */
  .article1, .article2 {
    grid-template-columns: subgrid;
  }

  .article3 {
    display: grid;
    grid-column: span 2;
  }
}

/* targeting the section's containment context at a maximum width of "100ch" */
@container main (max-width: 100ch) {
  main {
    display: flex;
    flex-direction: column;
  }
}

```

从上面的 CSS 代码中，我们使前两列在两列网格系统中占用的空间与下面的第三列一样多。然后，我们在文章内部创建了一个子网格，以确保其中的元素是同步的，不会中断，即使文章的内容增加了。

![example card grid system](img/6f973d7d25c4e0fdcf447f32c730795b.png)

为了使这个系统响应性更好，我们必须将部分作为目标，并设置一些容器查询规则，这些规则应用于作为其后代的文章。您可能会注意到，我们查询了部分的`inline-size`属性，因为我们希望文章(子)组件与部分(容器)的宽度一起增长或收缩。

在这种情况下，当截面的宽度最大为`80ch`时，我们使文章在列方向上弯曲。这使得我们的文章不响应视窗大小，而是响应容器的宽度。

结果如下:

![container queries subgrids working together](img/1094dd034f29c536496e415113c3c99a.png)

## 结论

本文介绍了 CSS 子网格和容器查询、它们的工作方式和用例。我们也能够对这两个概念进行实验，并将它们结合起来，以实现一个响应式的布局。容器查询和 CSS 子网格目前正处于它们的第一个公开工作草案中，但它们是被证明很有帮助的特性，并且很可能被纳入大多数新的浏览器版本中。

我希望这篇文章对你有价值。编码快乐！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。