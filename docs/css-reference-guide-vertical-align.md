# CSS 参考指南:垂直对齐日志火箭博客

> 原文：<https://blog.logrocket.com/css-reference-guide-vertical-align/>

# CSS 参考指南:`vertical-align`

## 

2020 年 10 月 30 日 1 分钟读完 319

CSS `vertical-align`属性设置内联元素或显示为内联块的元素的垂直对齐方式。属性也适用于`table-cell`元素。

#### *向前跳转:*

其语法如下:

```
element {
  vertical-align: baseline|top|bottom|middle|text-top|text-bottom|sub|super|length units;
}
```

* * *

## 演示

参见 [CodePen](https://codepen.io) 上 Solomon Eseme([@ kaperskyguru](https://codepen.io/kaperskyguru))
的 Pen [CSS 垂直对齐示例](https://codepen.io/kaperskyguru/pen/BazKYEy)。

价值观念

* * *

## 关键词

### `baseline`

#### 这是`vertical-align`的默认值。顾名思义，它将元素与父元素的基线对齐。

`top`

```
img {
  vertical-align: baseline
}
```

#### 将元素与一行中最高元素的高度对齐。

`bottom`

```
img {
  vertical-align: top
}
```

#### 将元素与底部对齐，与下面最长的元素处于同一水平。

`middle`

```
img {
  vertical-align: bottom
}
```

#### 将元素与其父元素的中心对齐。

`text-top`

```
img {
  vertical-align: middle
}
```

#### 使用父元素行中最高字母的顶部对齐元素。

`text-bottom`

```
img {
  vertical-align: text-top
}
```

#### 使用父元素行中最长字母的底端对齐元素。

`sub`

```
img {
  vertical-align: text-bottom
}
```

#### 将元素与其父元素的基线对齐。它的行为更像是`<sub>`标签。

`super`

```
img {
  vertical-align: sub
}
```

#### 将元素在父元素的基线上方对齐。它的行为更像是`<sup>`标签。

长度和百分比值

```
img {
  vertical-align: super
}
```

* * *

### 将元素按给定单位对齐。正数表示元素在基线上方对齐，负数表示元素在基线下方对齐。

这些值可以是任何长度单位:`px`、`em`、`%`等。

全球价值观

```
img {
  vertical-align: 10px
}
img {
  vertical-align: 50%
}
img {
  vertical-align: 3em
}
```

* * *

### `initial`

#### 将元素的对齐设置为默认值`baseline`。

`inherit`

```
img {
  vertical-align: initial
}
```

#### 设置元素与其父元素值的对齐方式。

```
img {
  vertical-align: inherit
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