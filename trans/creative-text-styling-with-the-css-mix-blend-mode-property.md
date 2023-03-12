# 使用 CSS mix-blend-mode 属性的创造性文本样式

> 原文：<https://blog.logrocket.com/creative-text-styling-with-the-css-mix-blend-mode-property/>

`mix-blend-mode`是一个 CSS 属性，它定义了元素的内容应该如何与元素的父元素及其背景的内容混合。有了它，你可以根据元素的直接背景，为元素的部分内容创建漂亮的混合和颜色。

使 CSS 开发人员能够更加灵活地使用图像上的真实文本来创建原始设计，从而消除了使用 Photoshop 等工具编辑的臃肿图像的需要。

给我们带来的一大优势是更好的性能，因为需要的图像更少，文件大小也明显更小。最重要的是，使用 photoshopped 图像并不能提供真实文本所能提供的 SEO 优势。

使用`mix-blend-mode`而不是编辑图像的另一个重要好处是你可以使用 CSS 动画，让你有机会给你的网站和元素添加更多的生命。`mix-blend-mode`的其他实际使用案例包括从徽标中移除白色背景，并使动态文本与背景形成对比。

## 简单地说,`mix-blend-mode`是如何工作的

`mix-blend-mode`创建所谓的[堆叠环境](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)。堆叠上下文是一个假想的 z 轴，相对于面向 HTML 元素所在的视口的用户。它们根据元素的优先级来占据这个空间。这也是`position` CSS 属性使用的相同思想。

`mix-blend-mode`适用于包括 SVG 在内的所有元素，尽管在撰写本文时，这个[还不能在所有的浏览器](https://caniuse.com/mdn-css_properties_mix-blend-mode_svg)上使用。

### 语法和值

`mix-blend-mode`的语法是:

```
mix-blend-mode: <blend-mode>

```

其中`<blend-mode>`可以是以下任意值:

#### `normal`

```
mix-blend-mode:normal

```

这是属性的默认值，不添加任何混合模式。

#### `multiply`

```
mix-blend-mode:multiply

```

这会将元素的颜色与背景的颜色相乘。根据背景的不同，最终的颜色通常会更暗。

#### `screen`

```
mix-blend-mode:screen

```

使内容比背景亮得多。它将内容颜色与其背景相乘，并对结果进行补充。

#### `overlay`

```
mix-blend-mode:overlay

```

如果背景比内容暗，则`overlay`执行`multiply`操作，如果背景比内容亮，则执行`screen`操作。这类似于背景和内容颜色条件反转的`hard-light`。

#### `darken`

```
mix-blend-mode:darken

```

这将使背景中较暗的区域变暗，而其他部分保持不变。

#### `lighten`

```
mix-blend-mode:lighten

```

浅色背景部分周围的内容变得更亮。和`darken`是对立的。

#### `color-dodge`

```
mix-blend-mode:color-dodge

```

这会使背景颜色变亮，以反映元素内容的颜色。结果是用背景色除以内容颜色的倒数得到的。您会注意到内容的最终颜色通常非常“鲜艳”

#### `color-burn`

```
mix-blend-mode:color-burn

```

这是`color-dodge`的反义词。该值使背景颜色变暗，以反映元素的内容颜色。结果是通过反转`background-color`，用它除以内容的颜色，然后反转结果得到的。如果内容的颜色与背景颜色相反，那么结果就是黑色。

#### `hard-light`

```
mix-blend-mode:hard-light

```

如果内容颜色比背景颜色深，则`hard-light`将为`multiply`，如果内容颜色比背景颜色浅，则为`screen`。这和`overlay`类似，只是背景和前景对调了。

#### `soft-light`

```
mix-blend-mode:soft-light

```

几乎和`hard-light`相反。这将根据内容的颜色应用`lighten`或`darken`。它比`hard-light`产生的效果看起来更柔和。

#### `difference`

```
mix-blend-mode:difference

```

`difference`选择内容和背景之间较暗的颜色，并将其从另一种(较亮的)颜色中减去。如果内容和背景之间的任何一种颜色是白色，那么结果就是另一种颜色的反白。

#### `exclusion`

```
mix-blend-mode:exclusion

```

这个和`difference`差不多，但是对比度低很多。与`difference`一样，如果内容和背景之间的任何一种颜色是白色，那么结果是另一种颜色的反转。

#### `hue`

```
mix-blend-mode:hue

```

色调将内容的`hue`与背景的`saturation`和`luminosity`结合起来，为内容创建一种颜色。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

#### `saturation`

```
mix-blend-mode:saturation

```

另一方面，`saturation`将内容的`saturation`与背景的`hue`和`luminosity`结合起来，为内容创建一种颜色。

#### `color`

```
mix-blend-mode:color

```

`color`通过将内容的色调和饱和度与背景的亮度相结合，为内容创建颜色。

#### `luminosity`

```
mix-blend-mode:hue

```

`luminosity`反转`color`属性。它将内容的`luminosity`与背景的`hue`和`saturation`结合起来，为内容创建一种颜色。

### 演示

参见 [CodePen](https://codepen.io) 上 David ( [@Ajiva](https://codepen.io/Ajiva) )
的笔 [Mix 混合模式](https://codepen.io/Ajiva/pen/KKgBBKL)。

## `mix-blend-mode`的一些其他用例

### 切断效应

在这个例子中，我们能够通过给文本一个白色背景并使用`screen`混合模式来为文本创建一个剪切效果。

请看 [CodePen](https://codepen.io) 上大卫([@阿吉瓦](https://codepen.io/Ajiva) )
的笔 [剪纸设计](https://codepen.io/Ajiva/pen/JjRmgZL)。

聚光灯效应

### 下一个例子描绘了聚光灯效果。文字被隐藏起来，直到光线反射到上面。这是通过在文本上使用`darken`混合模式来实现的，所以只有当黄色的 div 在文本上时它才会显示。

请看 [CodePen](https://codepen.io) 上大卫([@阿吉瓦](https://codepen.io/Ajiva) )
的 [带 mix-blend-mode](https://codepen.io/Ajiva/pen/BaLGaEq) 的电筒灯。

See the Pen [Torch Light with mix-blend-mode](https://codepen.io/Ajiva/pen/BaLGaEq) by David ([@Ajiva](https://codepen.io/Ajiva))
on [CodePen](https://codepen.io).

使用`mix-blend-mode`和`filter`

### 将`mix-blend-mode`和`filter`属性结合起来可以帮助你用 CSS 实现真正吸引人的设计。你可以在内容和背景上使用`filter`来获得你想要的任何效果。

在下面的例子中，我们能够通过对背景和内容应用`invert`滤镜，然后对`mix-blend-mode`使用`difference`来实现一个看起来漂白的设计。

请看 [CodePen](https://codepen.io) 上大卫([@阿吉瓦](https://codepen.io/Ajiva) )
的笔 [带滤镜](https://codepen.io/Ajiva/pen/jOMegwP)的 mix-blend-mode。

See the Pen [mix-blend-mode with filters](https://codepen.io/Ajiva/pen/jOMegwP) by David ([@Ajiva](https://codepen.io/Ajiva))
on [CodePen](https://codepen.io).

这是我在自己的网站上使用的另一个类似的例子:

请看 [CodePen](https://codepen.io) 上大卫([@阿吉瓦](https://codepen.io/Ajiva) )
的笔 [负面效果 mix-blend-mode](https://codepen.io/Ajiva/pen/yLaQYaG) 。

See the Pen [Negative effect mix-blend-mode](https://codepen.io/Ajiva/pen/yLaQYaG) by David ([@Ajiva](https://codepen.io/Ajiva))
on [CodePen](https://codepen.io).

移除标志的白色背景

### 假设您有一个白色背景的徽标图像，而您放置该图像的背景不是白色的。当然，简单的解决方法是使用 Photoshop 之类的工具(甚至是 [remove.bg](https://www.remove.bg/) 之类的免费工具)来裁剪掉白色背景，但这也可以使用`mix-blend-mode`来实现。

用 [CodePen](https://codepen.io) 上的 [@Ajiva](https://codepen.io/Ajiva) )
用 mix-blend-mode 看笔[。](https://codepen.io/Ajiva/pen/YzpNdGe)

See the Pen [Remove white background from logo with mix-blend-mode](https://codepen.io/Ajiva/pen/YzpNdGe) by David ([@Ajiva](https://codepen.io/Ajiva))
on [CodePen](https://codepen.io).

`multiply`将背景色与内容色相乘，这样标志的白色部分就变成了背景色。我使用了`filter:contrast(1)`来使徽标更亮，因为`multiply`值使徽标变暗了一点。

虽然这可能不是这个问题的最佳解决方案，但在某些情况下，它仍然会派上用场。

结论

## 我敢说，`mix-blend-mode`的可能性是无穷无尽的。作为一名前端开发人员，它为您打开了一个全新的能力世界。你可以不断试验和尝试不同属性的`mix-blend-mode`，包括`background-blend-mode`。

我希望这篇文章能够给你一个使用`mix-blend-mode`的介绍，并向你展示你可能想要使用的相关例子。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).