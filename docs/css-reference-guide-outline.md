# CSS 参考指南:大纲-日志火箭博客

> 原文：<https://blog.logrocket.com/css-reference-guide-outline/>

# CSS 参考指南:`outline`

## 

2020 年 10 月 30 日 1 分钟读完 371

CSS `outline`速记属性用于在元素外部画一条线。它与`a:focus`选择器结合使用特别有用，可以更加强调链接或其他元素。

#### *向前跳转:*

一开始可能看起来`outline`和`border`差不多。然而，不同之处在于`outline`在整个元素周围画了一条线；您不能指定要在其上画线的边。此外，与`border`不同的是，`outline`不是盒子模型的一部分，因为线条是在元素之外绘制的。

* * *

## 演示

参见 Solomon Eseme([@ kaperskyguru](https://codepen.io/kaperskyguru))
关于 [CodePen](https://codepen.io) 的[CSS 大纲示例](https://codepen.io/kaperskyguru/pen/LYZNevy)。

句法

* * *

## `outline`速记属性可以用一个、两个或三个值来声明。例如:

```
outline: color | style | width
```

值得注意的是，当只指定了一个或两个值时，其他值将解析为它们的默认值。大纲只需要设置它的`style`值就可以工作。

```
outline: solid; // This specifies only the STYLE value

outline: #eee dashed; // This specifies the COLOR value and STYLE value

outline: inset thick; // This specifies the STYLE value and WIDTH value

outline: green solid 2px; // This specifies all 3 values
```

组件属性

* * *

## `outline`属性是由三个单独属性组成的简写，用于定义轮廓的颜色、宽度和样式。我们将在下面逐一探讨。

`outline-color`

### 设置轮廓的文本和装饰部分的颜色。它可以通过关键字、十六进制值、RGB/RGBA 值和 HSL/HSLA 值来指定。

如果[浏览器支持](https://caniuse.com/?search=outline-color%3A%20invert)，其默认值为`invert`；否则，其默认值为`currentColor`。

`outline-style`

```
outline-color: currentColor | red | #eee | rgb(255, 255, 255) | hsl(0,0,0)
```

### 指定要绘制的线条类型。它的值可以是以下任何关键字:

`auto`

*   `dotted`
*   `dashed`
*   `solid`
*   `double`
*   `groove`
*   `ridge`
*   `inset`
*   `outset`
*   `none`(无轮廓)
*   其默认值为`none`。

`outline-width`

```
outline-style: auto | dotted | dashed | solid | double | groove | ridge | inset | outset
```

### 指定要绘制的线条的粗细。它的值可以是任何长度值，或者下列任何关键字:

其默认值为`medium`。

```
outline-width: 2(px, em, rem) | thin | medium | thick
```

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).