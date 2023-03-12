# CSS 参考指南:padding 

> 原文：<https://blog.logrocket.com/css-reference-guide-padding/>

# CSS 参考指南:`padding`

## 

2020 年 10 月 30 日 1 分钟读完 452

CSS `padding`速记属性用于在元素中创建空格。它定义了盒子模型的内容部分。

#### *向前跳转:*

可以使用长度、百分比和关键字(如`auto`)来指定`padding`属性。它也可以接受负值。

* * *

## 演示

参见 [CodePen](https://codepen.io) 上 Solomon Eseme([@ kaperskyguru](https://codepen.io/kaperskyguru))
的 Pen[CSS 填充示例](https://codepen.io/kaperskyguru/pen/JjKXpKy)。

句法

* * *

## CSS `padding`速记属性用于为`padding-top`、`padding-right`、`padding-left`和`padding-bottom`定义最多四个值。

```
div {
  padding: <length> | <percentage> | auto
}
```

等效的`padding`可定义如下:

```
div {
  padding: 2px 3px 4px 5px;
}
```

价值观念

```
div {
 padding-top: 2px;
 padding-right: 3px;
 padding-left: 5px;
 padding-bottom: 4px;
}
```

* * *

## `padding`属性可以接受一到四个值。

一个值

### 当一个值被提供给 CSS `padding`速记属性时，它将相同的`padding`值应用于所有四个边。

两个值

```
div {
  padding: 5px;
}
 // SAME AS
div {
 padding-top: 5px;
 padding-right: 5px;
 padding-left: 5px;
 padding-bottom: 5px;
}
```

* * *

### 当两个值被指定给`padding`属性时，第一个值被应用于顶部和底部填充，而第二个值被应用于左侧和右侧。

三个价值观

```
div {
  padding: 5px 3px;
}
 // SAME AS
div {
 padding-top: 5px;
 padding-right: 3px;
 padding-left: 3px;
 padding-bottom: 5px;
}
```

* * *

### 当提供三个值时，第一个值应用于顶部填充；第二个值应用于左右填充；第三个值应用于底部填充。

四种价值观

```
div {
  padding: 5px 3px 1px;
}
 // SAME AS
div {
  padding-top: 5px;
  padding-right: 3px;
  padding-left: 3px;
  padding-bottom: 1px;
}
```

* * *

### 当提供四个值时，这些值按顺时针顺序应用。换句话说:第一个值应用于顶部填充；第二个值应用于右填充；第三个值应用于底部；第四个值应用于左填充。

定心元件

```
div {
  padding: 5px 3px 1px 6px;
}
 // SAME AS
div {
  padding-top: 5px;
  padding-right: 3px;
  padding-left: 6px;
  padding-bottom: 1px;
}
```

* * *

### 通过使用`auto`关键字，使用`padding`属性将元素放在容器的中心是非常容易的。

负值

```
div {
  padding: 2em auto;  /* top and bottom: 2em padding   */
                    /* Box is horizontally centered */
}

div {
  padding: auto;    /* top and bottom: 0 padding     */
                  /* Box is horizontally centered */
}
```

* * *

### 当负值被提供给`padding`时，它们将元素拉向特定的方向，而不是推它。

例如，下面的代码片段将元素拉向`top`5px，拉向`right`3px，拉向`left`6px，拉向`bottom`1px。

```
div {
  padding: -5px -3px -1px -6px;
}
```

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).