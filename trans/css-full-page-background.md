# 用 CSS 创建整页背景

> 原文：<https://blog.logrocket.com/css-full-page-background/>

通常，您可能会遇到背景横跨整个页面的网页。这种整页背景可以用来为网站创建一个有凝聚力的外观或感觉，或者为特定的页面或部分添加视觉趣味和背景。如果使用得当，整页背景可以提升用户体验，让网站脱颖而出。

在本教程中，您将学习如何设置整页背景图像，并为用户创建视觉上引人注目的沉浸式体验。我们将探索使用 CSS 设置整个页面背景的不同技术，包括使用`background-image`属性、`image-set()`函数，以及使用多个背景图像和调整`background-repeat`、`background-position`和`background-size`属性等高级技术。

*向前跳跃*:

## 设置背景图像

设置整页背景最基本的方法是在 CSS 中使用`background-image`属性。该属性接受一个值，该值是要用作背景的图像的 URL。

下面是一个如何使用`background-image`属性设置整页背景的例子:

```
body {
  background-image: url('/path/to/image.jpg');
}
```

这将把 body 元素设置为背景图片的容器，背景图片将覆盖整个网页。

选择背景图片时，考虑图片的尺寸与屏幕大小的关系是很重要的。如果图像太小，在大屏幕上显示时可能会出现像素化或拉伸。另一方面，使用太大的图像会对性能产生负面影响，因为它需要更长的加载时间。

优化背景图像大小和分辨率的一种方法是使用图像编辑工具来调整图像大小和裁剪图像，以适合网页的尺寸。您还可以使用`background-size`属性来指定如何缩放图像以适合网页:

```
body {
  background-image: url('/path/to/image.jpg');
  background-size: cover;
}
```

`cover`值告诉浏览器根据需要放大或缩小图像以适合整个网页，同时保持图像的纵横比。如果您希望图像充满整个网页，但不希望它被拉伸或扭曲，这可能是一个不错的选择。

下面是一个 CSS 示例，它将 body 标记设置为可视宽度和高度的大小，并将背景设置为大小`cover`:

```
body {
  width: 100vw;
  height: 100vh;
  background: url('https://i.imgur.com/vHHl8Xk.jpg');
  background-size: cover;
}

```

这是输出的样子:

![Setting A Background Image Using The Cover Property](img/d3221908eadfec4d324344d7e5a679e2.png)

它看起来很好，但是如果我们调整页面大小，图像将完美地缩放，同时保持其纵横比以覆盖整个页面:

![Resizing The Background Image To Perfectly Scale ](img/ebd8ee2fd4e367ef96ffa60d861ecc69.png)

注意这里没有滚动条。图像简单地覆盖了身体的整个背景。

## 了解`background-size`属性

`background-size`属性具有以下值:

*   `auto`:这是默认值。背景图像的大小由图像文件的实际大小决定
*   `length`:您可以使用长度值来指定背景图像的大小，例如“20px”或“10em”。这将把图像的宽度和高度设置为指定值
*   `percentage`:您可以使用百分比来指定背景图像的大小，例如“50%”或“75%”。这将把图像的宽度和高度设置为元素大小的百分比
*   `cover`:该值将背景图像缩放到尽可能大，以便背景图像覆盖整个元素，同时保持其纵横比
*   `contain`:该值将背景图像缩放到最大尺寸，使其宽度和高度都适合元素
*   `initial`:将属性设置为默认值
*   `inherit`:继承父元素的值

## `image-set()`功能

`image-set()`功能允许您为不同的设备分辨率提供不同版本的图像。如果您希望对高像素密度的设备(如智能手机和平板电脑)使用较高分辨率的图像，而对低像素密度的设备使用较低分辨率的图像，这将非常有用。

下面是一个如何在`background-image`声明中使用`image-set()`函数的例子:

```
body {
  background-image: image-set(
    '/path/to/image-lowres.jpg' 1x,
    '/path/to/image-highres.jpg' 2x
  );
}
```

`image-set()`函数将一系列图像 URL 和分辨率作为参数。

在本例中，我们提供了同一图像的两个版本:像素密度为 1x 的低分辨率版本和像素密度为 2x 的高分辨率版本。浏览器将根据设备的像素密度选择合适的图像版本。

### 整页背景的高级技术

除了`background-image`和`background-size`属性，还有其他几个 CSS 属性可以用来微调整页背景的外观。

一个有用的属性是`background-repeat`，它决定了如果背景图像小于容器元素，它将如何重复。默认值是`repeat`，这意味着图像将水平和垂直平铺以填充容器。您也可以将该值设置为`repeat-x`、`repeat-y`或`no-repeat`，以指定图像应该如何重复。

让我们来看一下与之前相同的演示，但这次我手动定义了背景的大小，并使其在两个轴上重复:

```
body {
  font-weight: bold;
  width: 100vw;
  height: 100vh;
  background: url('https://i.imgur.com/vHHl8Xk.jpg');
  background-size: 250px;
  background-repeat: repeat-x repeat-y;
}

```

![Background-Repeat Property](img/e93d8aa362d5a6a6f6eb5c0cda2b38f9.png)

*注意，*如果你设置了`background-size`到`cover`，那么`repeat`不会有任何影响。

属性允许你指定背景图片在容器元素中的位置。您可以使用此属性将图像居中或将其位置调整到容器的左侧、右侧、顶部或底部。

除了这些属性，您还可以使用`background-blend-mode`属性来覆盖多个背景图像，并创建更复杂的效果。此属性允许您使用`multiply`、`screen`和`overlay`等选项来指定背景图像的颜色应该如何组合。

让我们看一个例子，看看如何使用这些属性创建一个包含多个图像和叠加效果的整页背景。这是我们将在演示中使用的两张图像:

这是相关的 CSS:

```
body {
    background-image: url('https://imgur.com/Fn9FQwT.jpg'), url('https://imgur.com/VfcgZZ9.jpg');
  background-size: cover;
  background-blend-mode: difference;
}
```

这是输出结果:

![Background-Blend mode](img/04e34a1d8b965a90e0cd200838ea758d.png)

在这个例子中，我们使用两个图像作为 body 元素的背景，第一个图像位于容器的中心，第二个图像位于容器的底部。我们还使用`difference`混合模式来叠加两幅图像，创建一个更加复杂和视觉上有趣的效果。

你可以在 jsdild 上看一个稍微不同的例子[。请随意摆弄和试验它。](http://jsfiddle.net/0u91L64y/2/)

## 结论

在本文中，我们介绍了使用 CSS 创建整页背景的不同技术。我们研究了`background-image`属性和`image-set()`函数，以及高级技术，比如使用多个背景图像和调整`background-repeat`、`background-position`和`background-size`属性。

重要的是要考虑背景图片对网站性能的影响，并适当地选择和优化图片，以确保快速流畅的用户体验。通过使用这些技术并考虑用户的设备和连接速度，您可以创建视觉上引人注目和身临其境的整页背景，以增强网站上的用户体验。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。