# CSS 参考指南:属性选择器

> 原文：<https://blog.logrocket.com/css-reference-guide-attribute-selectors/>

在 HTML 中，我们使用属性向 DOM 元素传递信息或附加选项。可以在 CSS 中利用这些属性来针对特定元素进行快速样式化。

#### *向前跳转:*

* * *

考虑下面的例子:

```
<div class="flex-item" id="flexm" disabled>Hi</div>

```

`div`元素有三个属性:`class`、`id`和`disabled`，值分别为`"flex-item"`、`"flexm"`和`true`。

属性选择器基于元素的属性定位 HTML 中的特定选择器。

* * *

## `[attr]`

`[attr]`选择器选择具有`attr`属性的一个或多个元素。

```
div[test] {
    color: red;
}
```

这个例子将使用红色对带有`test`属性的 div 元素进行样式化。

```
p[target] {
    border-radius: 3px;
    padding: 10px;
}
```

该选择器将匹配所有具有属性`target`的`p`元素，并使它们的`border-radius`为 3px，而`padding`为 10px。

CSS 将匹配元素，不管它们是否定义了值。

* * *

## `[attr="value"]`

这个选择器对于属性及其值来说更加具体。它将匹配属性为`attr`且值为`value`的所有元素。

考虑下面的例子:

```
div[test="testing"] {
    color: tomato;
}
```

这将选择所有具有值为`"testing"`的`test`属性的`div`元素。

```
<div test>1</div>
<div test="testing">2</div>
<div test="testing">3</div>

```

这里，只有 divs 2 和 3 将被匹配和样式化，因为它们有一个值被设置为`"testing"`的`test`属性。尽管 Div 1 有一个`test`属性，但它不会被样式化，因为它没有值`"testing"`——因此，它没有被选中。

* * *

## `[attr^="value"]`

这个选择器是一个匹配模式选择器。它将选择属性为`attr`的元素，其值*以`"value"`开始*。

这里的关键词是*开头的*。属性必须以指定的值开头。

这里有一个例子:

```
div[test^="test"] {
    color: tomato;
}
```

这将选择具有属性`test`的 div 元素，其值以`"test"`开始。

```
<div test="lasttesting">1</div>
<div test="testing">2</div>
<div test="tester">3</div>
```

这里，只选择 divs 1 和 div 2，因为它们具有值以`"test"`开始的`test`属性。没有选择 Div 1，因为它的`test`属性值没有以`"test"`开头。

* * *

## `[attr$="value"]`

这也是一个匹配模式选择器。它选择属性为`attr`且值以`value`结尾的元素。

这里有一个例子:

```
div[test$="ing"] {
    color: green;
}
```

该选择器将匹配所有具有属性`test`的 div 元素，该属性的值以`"ing"`结尾。

```
<div test="lasttesting">1</div>
<div test="testing">2</div>
<div test="tester">3</div>
```

选择器只匹配 divs 1 和 div 2，因为它们有一个`test`属性，并且它们的值以`"ing"`结尾。Div 3 将不受影响。

* * *

## `[attr*="value"]`

另一个匹配模式选择器，它检查包含的属性值。该选择器将选择具有包含`value`的值的`attr`属性的元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
div[test*="val"] {
    color: turquoise;
}
```

在上面的例子中，我们选择了所有具有属性`test`的 div 元素，这些元素的值包含了`"val"`字符串。

```
<div test="valuetesting">1</div>
<div test="tester">2</div>
<div test="testingvalue">3</div>
<div test="testervalue">4</div>
```

我们有四个 div 元素，都有`"test"`属性。现在，在 CSS 选择器之后，只有 divs 1、3 和 4 会受到影响。它们的属性`"test"`值包含了`"val"`字符串；div 2 不会受到影响，因为它的`"test"`属性值不包含`"val"`字符串。

* * *

## `[attr~="value"]`

该选择器以空格分隔的属性值为目标。它选择其`attr`属性值列表与`"value"`匹配的元素。

```
div[test~="val"] {
    color: orangered;
}
```

在上面的例子中，选择器将影响值列表与`"val"`匹配的具有`"test"`属性的 div 元素。

```
<div test="tester value">1</div>
<div test="tester val">2</div>
<div test="testervalue">3</div>
<div test="testingvalue">4</div>
<div test="testing value">5</div>
<div test="val testing">6</div>
```

这里，只选择 divs 2 和 div 6。这是因为它们的离散`"test"`属性值之一是`"val"`。Divs 1、3、4 和 5 将不受影响，因为它们的`"test"`属性值没有与`"val"`匹配的空格分隔值。

* * *

## `[attr|="value"]`

这将选择属性为`attr`且值为`"value"`或值后紧跟一个连字符`-`的元素。

这个属性选择器主要用于国际化。

```
div[lang|="en"] {
    color: limegreen;
}
```

这将选择带有`lang`属性的 div 元素，其值或者是`"en"`或者是`"en-"`，带有一个连字符。

```
<div lang="en">English(en)</div>
<div lang="en-EN">English(en-EN)</div>
<div lang="es">Espana(es)</div>
<div lang="es-ES">Espana(es-ES)</div>
```

div`English(en)`和`English(en-EN)`将被选中，因为它们分别包含`"en"`和`"en-"`。

* * *

## 活生生的例子

您可以在下面的演示中探索各种选择器的实现。

参见 Chidume David([@ philipsz-davido](https://codepen.io/philipsz-davido))在 [CodePen](https://codepen.io) 上的 [css 属性](https://codepen.io/philipsz-davido/pen/GRZGVzy)。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。