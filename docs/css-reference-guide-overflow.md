# CSS 参考指南:溢出-日志火箭博客

> 原文：<https://blog.logrocket.com/css-reference-guide-overflow/>

CSS `overflow`属性指定元素的内容溢出或超出其父容器的方式。当元素的宽度或高度大于其封闭父元素时，元素的 x 和 y 方向都可能发生溢出。

#### *向前跳转:*

* * *

假设我们有一个宽度为 300px、高度为 700px 的父元素`div`，以及一个宽度为 500px、高度为 900px 的子元素。我们将看到子元素`div`在 x 和 y 方向上溢出了它的父元素。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;
}
```

查看下面的一个实例:

参见 [CodePen](https://codepen.io) 上 Chidume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的笔[CSS 溢出](https://codepen.io/philipsz-davido/pen/XWdYvBr)。

`.childEl`将溢出 x 轴和 y 轴 200 像素。

* * *

## 价值观念

`overflow`属性具有可以用来处理上述情况的值:`visible`、`hidden`、`auto`、`scroll`和`clip`。

### `visible`

`overflow`的默认值。该值使溢出可见。它不会剪裁任何溢出。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow: visible;
}
```

`.childEl`溢出的内容不会被裁剪。

* * *

### `hidden`

该值剪切或隐藏子元素的溢出区域/内容。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow: hidden;
}
```

上面`.childEl`溢出的内容会被隐藏。

* * *

### `auto`

这隐藏并提供滚动条以溢出元素中的内容。一旦内容溢出，滚动条就会出现。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow: auto;
}
```

滚动条将出现在. childEl 上。

如果我们设置宽度不溢出它的父级:

```
...
.childEl {
    width: 200px;
    ...

    overflow: auto;
}
```

然后，滚动条将不会出现在底部，因为那里没有溢出。滚动条仅在右侧可见。

* * *

这将隐藏或剪辑溢出的内容，但提供滚动条，以便可以滚动溢出的内容，使其可见或看到其余的内容。即使内容没有溢出，滚动条也会自动出现在元素中。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow: scroll;
}
```

`.childEl`溢出的内容将被隐藏，滚动条将出现在其右侧和底部。使用鼠标或按键，我们可以滚动显示溢出的内容。

* * *

### `clip`

与`hidden`类似，只是带有`hidden`的溢出内容可以编程滚动。而`clip`则不然，它禁止所有的滚动。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow: clip;
}
```

`.childEl`溢出的内容将被隐藏，无法使用 JS 滚动到。

> **注意:**，`overflow: clip;`在撰写本文时仍处于试验阶段，仍不受大多数主流浏览器的支持。

* * *

## `overflow`属性

`overflow`是`overflow-x`和`overflow-y`的简写，这意味着当我们使用`overflow`时，我们同时设置了`overflow-x`和`overflow-y`属性。

`overflow`可以有一个或两个值。如果它只有一个值，那么`overflow-x`和`overflow-y`都取这个值。

如果我们有`overflow: auto;`，它将等于:

```
overflow-x: auto;
overflow-y: auto;
```

如果有两个值，第一个值变成`overflow-x`，第二个值变成`overflow-y`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果我们有`overflow: auto hidden;`，它将等于:

```
overflow-x: auto;
overflow-y: hidden;
```

* * *

### `overflow-x`

这针对元素的左侧和右侧，并控制如何处理溢出的内容。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow-x: hidden;
}
```

上面的例子会隐藏右侧`.childEl`溢出的内容。其底部的溢出内容不受该规则的影响。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow-x: scroll;
}
```

上面的例子将只在`.childEl`的右侧添加一个滚动条

* * *

### `overflow-y`

这针对元素的顶部和底部，并控制如何处理溢出的内容。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow-y: hidden;
}
```

这只会隐藏`.childEl`溢出的底部

* * *

## 使用`overflow-x`和`overflow-y`

这两个属性可以一起使用来影响内容的顶部和底部。

```
.parentEl {
    width: 300px;
    height: 700px;
    border: 1px solid orangered;
}

.childEl {
    width: 500px;
    height: 900px;
    border: 1px solid black;

    overflow-x: hidden;
    overflow-y: scroll;
}
```

这将隐藏溢出的右侧，并在`.childEl`的底部添加一个滚动条。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。