# 理解 CSS 中的背景尺寸

> 原文：<https://blog.logrocket.com/understanding-background-size-css/>

CSS `background-size`属性决定了元素背景图片的大小。图像可以保持其原始大小、拉伸或限制以适合可用空间；有许多变化和语法来设置这些行为。

`background-size`属性值可以是关键字值、单位值(如百分比或像素)或全局值(如下面代码块中的值):

```
.card-hero{
  background-size: cover| 30%| 300px 250px| inherit
}

```

本文将研究 CSS 中的`background-size`属性及其工作原理，并回顾其属性值的示例。

*向前跳转:*

让我们从查看`background-size`属性值开始，看看它们是如何工作的。

## 关键词值

使用关键字值`contain`和`cover`，我们可以改变背景图像的大小。让我们看看每个关键字值的一些例子。

### `contain`

当我们在`background-size`属性上设置`contain`值时，背景图像将调整大小以确保图像完全可见。此关键字值在不裁剪或拉伸的情况下，尽可能地缩放图像以适合其容器。

如果容器元素大于图像，这将导致图像平铺(其中多个副本并排显示)以填充负空间，除非将`background-repeat`属性设置为`no-repeat`:

参见 [CodePen](https://codepen.io) 上纳尔逊国王( [@D_kingnelson](https://codepen.io/D_kingnelson) )
的笔[-背景-大小-包含](https://codepen.io/D_kingnelson/pen/abGZPLP)。

在上面的例子中，容器元素比图像大。因此，如果我们没有将`background-repeat`属性设置为`no-repeat`，我们的图像将会平铺以覆盖整个容器。它这样做，而不是拉伸，以保持图像质量。

### `cover`

`cover`值，顾名思义，确保图片缩放到填满整个容器，不留空白，同时保持其比例。如果背景图像和元素的比例不同，图像的宽度或高度将被裁剪:

参见 [CodePen](https://codepen.io) 上纳尔逊国王( [@D_kingnelson](https://codepen.io/D_kingnelson) )
的笔[-背景-尺寸-封面](https://codepen.io/D_kingnelson/pen/OJZXdvZ)。

请注意上面的图像是如何被拉伸以完全填充容器的，即使其宽度或高度被裁剪。

***注意，*** `background-size` *可以设置为*`auto`*；这是该属性的默认值，将导致图像以其原始尺寸显示*

## 单位值

我们可以使用百分比或像素等单位值来设置背景图像的`background-size`。这里有几个例子:

```
.card-hero{
   background-size: 15%;
}

```

```
.card-hero{
  background-size: 400px;
}

```

```
.card-hero{
  background-size: 30vw;
}

```

现在，让我们看看为`background-size`设置了特定百分比值的代码笔:

参见 [CodePen](https://codepen.io) 上纳尔逊国王( [@D_kingnelson](https://codepen.io/D_kingnelson) )
的钢笔 [【背景-大小-百分比】](https://codepen.io/D_kingnelson/pen/JjvKzwv)。

在这个例子中，我们将`background-size`设置为`40%`。这意味着背景图像将占据容器大小的`40%`；在这种情况下，`240px` ( `40/100 * 600 = 240`)，因为我们的集装箱宽度是`600px`。

请注意，我们使用宽度来进行计算。这是因为当我们设置一个单独的值作为我们的`background-size`时，它只对宽度有效。我们将在[单值章节](#one-value)中进一步解释这一点。

## 全球价值观

`background-size`属性也接受以下 CSS 范围的关键字值作为其属性值:

### `inherit`

关键字`inherit`将一个元素的属性值更改为其父元素的相同属性值。换句话说，它迫使继承发生，即使它通常不会发生:

```
.card-header{
  background-size: inherit;
}

```

### `initial`

关键字`initial`将属性的值更改为指定的初始值，从而重置该值。例如，`font-weight`的默认设置是`normal`。因此，表达`font-weight: initial`相当于陈述`font-weight: normal`。

在我们的情况下，这意味着将`background-size`属性设置为`auto`，因为这是默认值:

```
.card-header{
  background-size: initial;
}

```

### `unset`

关键字`unset`可以用来替换`inherit`和`initial`。如果财产是继承的，那么`unset`与`inherit`具有相同的效力。但是，如果属性不是继承的，`unset`与使用`initial`具有相同的效果:

```
.card-header{
  background-size: unset;
}

```

## 一个值

每当我们在`background-size`属性上设置一个值时，如下所示，它只影响背景图像的宽度；高度自动设置为`auto`:

```
.card-header{
  background-size: 50%;
}

```

正如我们在前面的例子中看到的，我们可以使用任何 CSS 度量单位或关键字值。

## 两个值

每当我们用两个值设置`background-size`属性时，如下所示，它会影响背景图像的高度和宽度:

```
.card-header{
  background-size: 40% 50%
}

```

第一个值设置背景图像的宽度，第二个值设置高度。就像一个值一样，我们可以使用任何度量单位。

## 多个图像

CSS3 为创建引人注目的背景提供了许多[属性，包括一个奇妙的功能，允许我们有几个背景图像。语法很简单:用逗号分隔图像 URL。这里的技巧是理解图像的堆叠顺序。](https://blog.logrocket.com/advanced-effects-with-css-background-blend-modes-4b750198522a/)

这里有一个快速的心理图像来帮助理解堆叠顺序:我们声明的第一个图像将堆叠在其他图像之上。了解这是如何工作的，对于确定在使用大量照片时如何设置`background-size`至关重要。

让我们看一个例子:

```
.card-header{
  background-image: url('first.jpg'), url('second.jpg'), url('third.jpg');
}

```

在本例中，图像将按照图像文件名指定的顺序堆叠。我们将使用相同的顺序为这些图像设置`background-size`，用逗号分隔，如下所示:

```
.card-header{
  background-size: 40%, 20%, 30%;
}

```

我们可以在这里使用单值或双值语法；两者工作方式相同。

这里有一个例子:

参见 [CodePen](https://codepen.io) 上纳尔逊国王( [@D_kingnelson](https://codepen.io/D_kingnelson) )
的笔 [【背景-尺寸-多幅图像】](https://codepen.io/D_kingnelson/pen/RwyGYKz)。

当设置多个背景图像时，一定要添加`background-position`属性来控制图像在容器中的位置。

如果我们在图像上设置了`background-size`,并且它们比容器小，那么它们将互相堆叠，只留下第一个图像可见。这就是`background-position`派上用场的地方，就像我们上面的例子一样。

## 浏览器支持

`background-size`属性有非常坚实的浏览器支持，所以你不必担心它不能在一些浏览器或旧版本上工作:

| 浏览器 | 支持 |
| --- | --- |
| 铬 | 全力支持 |
| 边缘 | 全力支持 |
| 火狐浏览器 | 全力支持 |
| 歌剧 | 全力支持 |
| 旅行队 | 全力支持 |

## 结论

当你处理背景图片时，CSS 属性非常有用。它允许您控制设置为元素背景的图像的大小，这样您就不会损失图像的质量。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。