# CSS 参考指南:margin - LogRocket 博客

> 原文：<https://blog.logrocket.com/css-reference-guide-margin/>

# CSS 参考指南:`margin`

## 

2020 年 10 月 30 日 1 分钟读完 452

CSS `margin`速记属性用于在任何已定义边界之外的元素周围创建空间。它定义了盒子模型的最外面部分。

#### *向前跳转:*

可以使用长度、百分比和关键字(如`auto`)来指定`margin`属性。它也可以接受负值。

* * *

## 演示

参见 Solomon Eseme([@ kaperskyguru](https://codepen.io/kaperskyguru))[CodePen](https://codepen.io)上的 Pen [CSS 边距示例](https://codepen.io/kaperskyguru/pen/XWKdVBb)。

句法

* * *

## `margin`是一个 CSS 简写属性，用于为`margin-top`、`margin-right`、`margin-left`和`margin-bottom`定义最多四个值。

```
div {
  margin: <length> | <percentage> | auto
}
```

等价的`margin`可以定义如下。

```
div {
  margin: 2px 3px 4px 5px;
}
```

价值观念

```
div {
  margin-top: 2px;
  margin-right: 3px;
  margin-left: 5px;
  margin-bottom: 4px;
}
```

* * *

## `margin`属性可以接受一到四个值。

一个值

### 当一个值被提供给`margin`速记属性时，它将相同的`margin`值应用于所有四个边。

两个值

```
div {
  margin: 5px;
}
 // SAME AS
div {
  margin-top: 5px;
  margin-right: 5px;
  margin-bottom: 5px;
  margin-left: 5px;
}
```

* * *

### 当向`margin`属性提供两个值时，第一个值应用于上边距和下边距，而另一个值应用于左边距和右边距。

三个价值观

```
div {
  margin: 5px 3px;
}
 // SAME AS
div {
  margin-top: 5px;
  margin-right: 3px;
  margin-left: 3px;
  margin-bottom: 5px;
}
```

* * *

### 当提供三个值时，第一个值应用于上边距；第二个值应用于左边距和右边距；第三个值应用于下边距。

四种价值观

```
div {
  margin: 5px 3px 1px;
}
 // SAME AS
div {
  margin-top: 5px;
  margin-right: 3px;
  margin-left: 3px;
  margin-bottom: 1px;
}
```

* * *

### 当提供四个值时，这些值按顺时针顺序应用。换句话说:第一个值应用于上边距；第二个值应用于右边距；第三个值应用于底部；第四个值应用于左侧。

定心元件

```
div {
  margin: 5px 3px 1px 6px;
}
 // SAME AS
div {
  margin-top: 5px;
  margin-right: 3px;
  margin-left: 6px;
  margin-bottom: 1px;
}
```

* * *

### 通过使用`auto`关键字，使用`margin`将元素放在容器的中心是非常容易的。

负值

```
div {
  margin: 2em auto;  /* top and bottom: 2em margin   */
                    /* Box is horizontally centered */
}

div {
  margin: auto;    /* top and bottom: 0 margin     */
                  /* Box is horizontally centered */
}
```

* * *

### 当负值被提供给`margin`时，它们将元素拉向特定的方向，而不是推它。

例如，下面的代码片段将元素拉向`top`5x，拉向`right`3x，拉向`left`6px，拉向`bottom`1px。

```
div {
  margin: -5px -3px -1px -6px;
}
```

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).