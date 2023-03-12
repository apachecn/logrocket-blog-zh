# 提高 CSS 性能的最佳实践

> 原文：<https://blog.logrocket.com/best-practices-improving-css-performance/>

***编者按**:这篇文章最后一次更新是在 2022 年 10 月 20 日，包含了关于压缩 CSS 文件和 CSS 中的惰性加载的信息。*

CSS 位于网站设计的表示层。如果做得好，它会为你的用户增加美感，并给下面的 HTML 标记带来正确的感觉。如果不是这样，它会导致糟糕的用户体验，并影响你网站的速度和性能。

不管你的网站是什么性质，也不管它是否能在各种浏览器上运行良好，它都必须快速加载。如果不快，你的用户就会离开，去别的地方。

如果你碰巧在你的网站上销售服务或商品，这意味着销售额减少，因为网上有很多选择，你已经给了用户一个明确的指示去别处看看。他们甚至可能会阻止其他人在将来使用你的网站，这可能会导致页面浏览量减少。

在本文中，您将了解如何避免一些常见的 CSS 编码决策，这些决策将会使您的网站陷入困境。到最后，你将能够加快你的网站和改善 UX。

我们将讨论这八个 CSS 主题，以改进您的代码并提高您网站的性能:

1.  [编写简单的选择器](#write-simple-selectors)
2.  [避免过多的动画](#avoid-excessive-animations)
3.  [知道何时激活昂贵的属性](#know-when-to-animate-expensive-properties)
4.  [避开`@import`语句](#avoid-import-statement)
5.  [优化您的文件大小](#optimize-file-sizes)
6.  [避免 base64 位图图像](#avoid-base64-bitmap-images)
7.  [压缩你的 CSS](#compress-css)
8.  [懒惰加载你的 CSS](#lazy-load-css)

## 为什么 CSS 会影响网站性能

简单来说，CSS 影响站点渲染。CSS 是网站关键渲染路径的一部分，即网站渲染第一个像素所需的最小信息量。这意味着当 CSS 解析时，它将阻止所有其他资源的加载，包括 HTML 和 CSS 功能。

事实上，当 CSS 解析时，你可能看不到网站的一个像素，也不能与页面交互，所以优化 CSS 以获得最佳性能是很重要的。你可以通过下面的八个步骤做到这一点。

## 1.编写简单的选择器

CSS 有一系列广泛、灵活的编码选项，您可以使用它们来确定 HTML 元素的样式。多年来，专家们建议开发人员编写简单的选择器，以减少浏览器的负载，并保持代码的整洁和简单。

下一个代码块演示了简单性的含义:

```
.hero-image {
        width: 70%
}

```

但是，CSS 不会阻止您编写以下内容:

```
main > div.blog-section + article > * {
        /* Code here */
}

```

在这个例子中，在后台，浏览器将从右到左解析选择器，从通用选择器(`*`)开始，一直读到`main`选择器。这对于浏览器来说是比平常更多的工作。尽管我们讨论的是解析这个选择器的毫秒差，但是当这个选择器方法在样式表中出现多次时，这些毫秒就会累加起来。

此外，选择器越长，它们给样式表增加的字节就越多。

## 2.避免过多的动画

既然动画在原生 CSS 中可用，那么就没有必要使用 JavaScript 来将动画添加到网页中。这使得在你的网站上添加动画更容易，如果做得好，你可以利用它们来创造更好的用户体验。

然而，当你过度使用动画时，你可能会分散用户的注意力，使他们在访问你的网站时无法完成他们想要完成的任务。此外，请记住，您添加的每个动画都需要时间来解析，因此过多的动画会减慢或停止 web 浏览器。

## 3.知道何时激活昂贵的属性

导致整个页面重新布局的属性不应该是动画。这些属性通常被称为“昂贵的”，因为它们会在您的网站上创建大量的加载时间。其中一些是:

*   `margin`
*   `padding`
*   `height`
*   `width`

这样做的原因是，当您更改单个 DOM 元素的属性(如`margin`和其他维度)时，会导致所有其他元素的更改。

其他一些属性，比如`opacity`和`transform`，可以被动画化，因为它们不影响其他元素的布局。这使得网络浏览器能够将这些计算转移到 GPU 上，从而加快速度。

其他 CSS 属性更常用，但仍然需要更长的时间来绘制。其中一些包括:

*   `:nth-child`
*   `box-shadow`
*   `border-radius`
*   `position: fixed`

当这些出现在你的样式表中时，它们会影响你的网站的性能。谨慎使用它们。

## 4.避免使用`@import`语句

`@import`语句主要用于包含资源，比如字体，尽管它也可以包含其他 CSS 文件。

CSS 是渲染阻塞的，这意味着当您在 CSS 文件中使用`@import`语句获取字体或其他 CSS 文件时，浏览器将在继续处理剩余的 CSS 代码之前获取资源:

```
/* styles.css */
/**
 * The browser would fetch base.css before
 * processing the remaining code in styles.css
 */
@import url("base.css");

```

当资源是字体文件时，浏览器将使用系统中可用的字体，同时等待下载另一种字体。下载后，它会用下载的字体替换系统字体。因此，您的用户可能正在以一种字体阅读您的内容，突然字体发生了变化。这对用户体验不好。

下面是一个用`@import`语句加载字体的例子:

```
/**
 * Example of loading a font with the
 * @import statement.
 * The font is only available after it downloads.
*/
@import url('https://fonts.googleapis.com/css2?family=Roboto:[email protected]&display=swap');

```

相反，我们建议使用 HTML 的`head`中的`link`标签来加载字体:

```
<link rel="preload" as="font" href="https://fonts.googleapis.com/css?family=Open+Sans" crossorigin="anonymous">

```

这里的`rel="preload"`和`as="font"`告诉浏览器尽快下载字体。你也可以采取措施来确保你预加载的字体文件与你的 CSS 中的相匹配，以防止你的用户下载相同字体的两个版本，浪费他们的带宽。

## 5.优化您的文件大小

在网页设计和开发中，尺寸很重要。无论你处理的是图像、HTML 或 JavaScript 文件，还是其他媒体资源，都有一条黄金法则:始终压缩。

通过缩小来减少你的 CSS 文件大小。你网站上的图片应该进行优化，以降低它们的加载速度，这意味着使用在线工具，如[TinyPNG](https://tinypng.com/)——或者，如果你正在创建自己的图片，利用 Photoshop 中的 Save for Web 等工具。

## 6.避免 base64 位图图像

Base64 图像是在网页上嵌入图像的选项之一。多年来，哈里·罗伯茨等专家已经证明了 base64 图像不利于性能的原因有很多:

*   它们显著增加了 CSS 文件的整体大小
*   不管它们是否被使用或被浏览，它们都会被下载
*   Base64 编码导致图像文件大于正常大小
*   浏览器必须解析整个 base64 字符串才能使用

在下图中，观察转换为 base64 前后每个图像的大小[的增加:](https://www.base64-image.de/)

![Before And After Of The Base64 Image Conversion](img/5c63c3480340152ab2f2f33121505f2d.png)

看看当我们将这三个 base64 图像添加到一个只有 14 行代码的 CSS 文件时会发生什么:

```
/**
 * Base64 code truncated.
*/
@media screen and (min-width: 20em) {

    html {
            background-image:  url('data:image/png;base64,iVBO ...');
    }

    footer {
            background-image:  url('data:image/png;base64,iVBO ...');
    }

    .non-existence-class {
            background-image:  url('data:image/png;base64,iVBO ...');
    }

}

```

文件大小增加到 500KB 以上。不仅如此，无论是否使用该图像，用户的浏览器都会花时间下载该文件:

![Base64 Image File Size Increase](img/3933e278a664632a21c0932b0d7a7ce1.png)

同时，在下面的代码中，浏览器将根据浏览器的视区按需下载图像:

```
html {
    padding: 2em;
    background-image: url("images/asnim_mobile.jpg");
}

@media screen and (min-width: 20em) {
    html {
            background-image: url("images/asnim_tablet.jpg");
    }
}

@media screen and (min-width: 48em) {
    html {
            background-image: url("images/asnim.jpg");
            background-size: cover;
    }
}

```

您可以通过执行以下步骤来验证这一点:

1.  为不同的断点创建三个不同大小的图像，如前面的代码块所示
2.  使用您的图像作为背景图像复制代码块
3.  启动浏览器，在开发者工具中打开**网络**标签
4.  调整浏览器视窗的大小并观察

## 7.压缩你的 CSS

未压缩的 CSS 文件通常比压缩的 CSS 文件花费两倍的时间来加载，所以压缩你的文件应该是一个容易的决定——特别是如果你的站点有许多 CSS 文件。

CSS 压缩器通过移除空白、换行符、块分隔符、注释等来工作。减小应用程序文件的大小。他们还找到并修复任何与自己重叠的属性，并寻找机会使用 CSS 速记。

虽然压缩 CSS 文件使它们对浏览器更加友好，但是它的一个缺点是降低了代码的可读性。

一些压缩器还允许您在可读性和大小之间进行选择，因此如果您与团队合作或者您知道其他人会阅读您的代码，您仍然可以在保持代码可读性的同时压缩 CSS:

![Compressing CSS And Keeping Code Readable](img/39798174c59732278b8e8f7049ae81ce.png)

虽然市场上有许多 CSS 压缩器，但你必须花时间找到适合自己的。看看[这个列表](https://w3bits.com/tools-optimize-css-online/)就可以开始了。

## 8.延迟加载你的 CSS

惰性加载是一种用来提高网站性能的策略，它通过阻止非关键资源的渲染，直到需要它们的时候。延迟加载 CSS 是一种减少页面关键渲染路径的好方法，方法是拆分 CSS，只加载所需的代码，而所有其他代码(例如，图像、动画等)。)是按需加载的。

延迟加载的主要目标是图像和视频。延迟加载图像和视频最简单的方法是将`loading`属性添加到`<img>`和`<iframe>`元素中，并将其设置为`lazy`。这确保了图像仅在进入视口时加载:

```
<img src="myimage.jpg" loading="lazy" alt="..." />
<iframe src="content.html" loading="lazy"></iframe>

```

对于视频，您可以使用`preload`属性来延迟加载不自动播放的视频:

```
<video controls preload="none">
  <source src="Fluffydogs.mp4" type="video/mp4">
  A video about fluffy dogs
</video>

```

## 结论

当你从这篇文章中学到一些东西，并将它们应用到你未来的 web 项目中时，你会发现你正在为一个更好的 web 做出贡献！如果你有一个你觉得需要改进性能的网站，用这篇文章中学到的经验重构你的代码，并在评论中告诉我们进展如何。

浏览一些关于您可以进行的其他 CSS 和一般 web 性能更新的提示:

编码快乐！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。