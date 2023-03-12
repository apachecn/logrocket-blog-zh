# CSS 参考指南:过渡- LogRocket 博客

> 原文：<https://blog.logrocket.com/css-reference-guide-transitions/>

CSS `transition`属性是一个速记属性，允许我们在 CSS 中制作动画效果。`transition`在一段时间内逐渐将 CSS 属性从其原始值更改为新值。

#### *向前跳转:*

* * *

CSS 属性从一个值到另一个值的变化是瞬间的，就像突然眨眼一样；对于人眼来说，它们基本上是察觉不到的。允许我们放慢变化的速度，以便它能在一段时间内以人眼可察觉的方式逐渐发生。

示例:

```
div.banner {
    width: 100px;
    padding: 10px;
    background-color: seagreen;
    border: 1px solid deepskyblue;

    transition: width 2s;
}

div.banner:hover {
    width: 150px;
}
```

你可以在这里举一个例子:

参见 [CodePen](https://codepen.io) 上 Chidume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的笔 [css 转场](https://codepen.io/philipsz-davido/pen/QWNxezz)。

上面，当鼠标悬停在上面时，我们将元素的宽度增加到 150 像素。在`div.banner`中设置的`transition`属性使长度的增加在 2 秒内缓慢渲染。

我们将看到元素的宽度扩展到 150px，文本慢慢地重新对齐以补偿宽度的增加。当鼠标离开`div.banner`元素时，它再次以缓慢流畅的动作将其宽度更改为 100px，并且其文本换行恢复到其原始状态。

让我们看另一个例子:

```
div.banner {
    width: 100px;
    padding: 10px;
    background-color: seagreen;
    border: 1px solid;

    transition: background-color 4s ease-in 1s;
}

div.banner:hover {
    background-color: limegreen;
}
```

你可以在这里玩上面的例子:

参见 [CodePen](https://codepen.io) 上 chi dume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的笔[CSS _ transition _ background _ color](https://codepen.io/philipsz-davido/pen/ExKGmQJ)。

这里，当用户将鼠标悬停在`div.banner`元素上时，过渡会延迟 1 秒，然后在 4 秒的时间内`seagreen`的原始`background-color`变为`limegreen`。我们看到一个褪色的效果，`seagreen`颜色逐渐变暗，而`limegreen`颜色逐渐出现取代它的位置。

注意，我们使用了`:hover`伪类来触发转换。我们也可以使用其他方法来触发转换——例如，我们可以将一个元素的状态从有效变为无效，禁用或启用`:disabled`状态，激活`:active`状态，等等。

* * *

## 句法

`transition`属性采用空格分隔的值列表:

```
transition: <property> <duration> <timing-function> <delay>
```

*   `<property>`是可动画化的 CSS 属性，当属性改变时，我们希望将它从原始值动画化为新值
*   `<duration>`是动画将持续的时间段。我们可以用秒(`s`)或毫秒(`ms`)来设置持续时间
*   `<timing-function>`是一种控制动画速度的方法
*   `<delay>`是转换前的延迟时间

在我们前面的例子中:

```
div.banner {
    ...
    transition: background-color 4s ease-in 1s;
}
...
```

*   `background-color`是`div.banner`的 CSS 属性，当该属性改变时，我们希望制作动画
*   `4s`是属性变化动画的持续时间
*   `ease-in`是计时功能。这使得效果开始缓慢，然后加速
*   过渡在开始前会延迟`1s`

* * *

## 多重过渡

我们可以给`transition`设置多个属性。属性必须用逗号分隔。

```
transition: background-color 4s ease-in 1s, width 2s;
```

这里为过渡动画设置了两个属性:`background-color`和`width`。现在，`transition`监听任一或所有属性(`background-color`和`width`)的变化。

* * *

## 组件属性

`transition`属性是四种手写转换属性的简写:

*   `transition-property`
*   `transition-duration`
*   `transition-timing-function`
*   `transition-delay`

* * *

### `transition-property`

要转换的动画属性。

```
transition-property: border-radius
```

这将把`border-radius`设置为元素中的 CSS 属性，当它发生变化时，我们希望对其运行过渡效果。

```
transition-property: margin-right
```

当元素的右边距(`margin-right`)改变时，触发`transition`。

将转换限制在指定的属性上，让其余的属性瞬间改变。在逗号分隔的列表中，可以将多个属性分配给`transition-property`。

考虑下面的例子:

```
transition-property: margin-right, color, border-radius;
```

本示例仅选择右边距、文本颜色和边框半径属性，以便在它们的值发生变化时进行转换。

`transition-property`可以取以下值:

*   `none`:没有为过渡选择属性
*   `all`:元素中所有可动画化的属性都被选择用于过渡

* * *

### `transition-duration`

动画的持续时间，或完成动画所需的时间。单位可以设置为秒(`s`)或毫秒(`ms`)。

```
transition-duration: 1s
```

像`transition-property`一样，您可以在一个逗号分隔的列表中为`transition-duration`提供多个值。

```
transition-duration: 1s, 200ms;
```

列表中的每个值分别适用于`transition-property`中设置的每个值。

```
transition-property: width, color;
transition-duration: 1s, 200ms;
```

`1s`将应用于`width`属性，`200ms`将应用于`color`属性。这意味着元素中的`width`属性值改变的转变持续时间是 1 秒，元素中的`color`属性值改变的转变持续时间是 200 毫秒。

* * *

### `transition-timing-function`

过渡效果，或者说过渡将如何进行。它最终控制过渡的速度。

`transition-timing-function`可以取以下值:

*   `ease`
*   `linear`
*   `ease-in`
*   `ease-out`
*   `ease-inout`
*   `step-start`
*   `step-end`
*   `steps(n, start)`，其中`n`是`steps—steps(n, end)`的号码
*   `cubic-bezier(x1, y1, x2, y2)`

让我们来看看上面的主要价值观:

*   这使得过渡开始缓慢，然后逐渐加速，然后减速，非常缓慢地结束
*   `linear`:从开始到结束的过渡速度相同；它是线性的
*   `ease-in`:过渡开始缓慢，然后加速
*   过渡开始很快，然后变慢
*   过渡开始得很慢，中间移动得更快，然后慢下来，最后不要太慢

这里有一个例子:

```
transition-property: width;
transition-duration: 1s;
transition-timing-function: ease-in;
```

上面，转换是在`width`属性改变时触发的。设置为`ease-in`的`transition-timing-function`将使宽度变化缓慢开始，并在 1 秒的时间范围内逐渐加速。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

* * *

### `transition-delay`

属性状态更改和开始转换到新状态之间的时间。该值以秒(`s`)或毫秒(`ms`)表示。

```
transition-property: width;
transition-duration: 1s;
transition-timing-function: ease-in;

transition-delay: 1s;
```

上面，我们在过渡开始前设置了一个`1s`的延迟。

考虑另一个例子:

```
transition-property: border-radius;
transition-duration: 2s;
transition-timing-function: ease-in;

transition-delay: 10ms;
```

这里，当元素的`border-radius`状态改变时，过渡动画将延迟`10ms`。

如果该属性的值为`0s`，当目标属性改变状态时，转换将立即开始。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。