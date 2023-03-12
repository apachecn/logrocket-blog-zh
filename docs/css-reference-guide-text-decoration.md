# CSS 参考指南:文本-装饰-日志火箭博客

> 原文：<https://blog.logrocket.com/css-reference-guide-text-decoration/>

# CSS 参考指南:`text-decoration`

## 

2020 年 10 月 30 日 1 分钟阅读 357

CSS `text-decoration`速记属性允许你在文本上添加装饰线和样式。

#### *向前跳转:*

该属性的语法如下:

```
text-decoration: line | style | color | thickness
```

* * *

## 演示

参见 [CodePen 上](https://codepen.io) [@kaperskyguru](https://codepen.io/kaperskyguru) )
的笔[CSS 文字修饰示例](https://codepen.io/kaperskyguru/pen/MWeyQzp) by Solomon Eseme。

组件属性

* * *

## CSS `text-decoration`简写由四个组件属性组成。考虑下面的例子:

我们将在下面深入探讨每个组件的属性。

```
p {
  text-decoration: underline solid red 0.1em;
}

// explodes to:

p {
  text-decoration-line: underline;
  text-decoration-style: solid;
  text-decoration-color: red;
  text-decoration-thickness: 0.1em
}
```

`text-decoration-line`

* * *

### 设置`text-decoration`的线条样式。

价值观念

#### `underline`:在文本下方显示一行

*   `line-through`:在文本中删除一行
*   `overline`:在文本上显示一条线
*   文本不断地在可见和不可见之间交替，换句话说，它在闪烁。为了支持 CSS 动画，该值已被弃用
*   `none`:不产生线条
*   使用

#### `text-decoration-style`

```
p {
  text-decoration-line: underline | line-through | overline | blink | none
}

```

* * *

### 设置要在文本上显示的线条样式。

价值观念

#### `solid`:画一条实线

*   `double`:画一条双线
*   `dotted`:画一条虚线
*   `dashed`:画一条虚线
*   `wavy`:画一条波浪线
*   使用

#### `text-decoration-color`

```
p { 
  text-decoration-style: solid | double | dotted | dashed | wavy;
}

```

* * *

### 设置要在文本上显示的线条颜色。

价值观念

#### 与所有 CSS 颜色一样，值可以由关键字、十六进制代码、RGB/RGBA 值或 HSL/HSLA 值提供。

使用

#### `text-decoration-thickness`

```
p {
  text-decoration-color: currentcolor | red | #00ff00 | rgba(255, 128, 128, 0.5) | transparent;
}

```

* * *

### 设置在文本上绘制的线条的线条粗细。

**注意:**，`text-decoration-thickness`目前仅在最新版本的 Firefox 和 Safari 中受支持。

> 价值观念

#### `auto`:允许浏览器为文本装饰线选择合适的宽度

*   `from-font`:允许线条的粗细由文本的字体文件决定。如果字体文件中没有提供厚度信息，该值默认为`auto`
*   `length`:允许开发者指定任意长度值(如`1px`、`2em`等)。)
*   `percentage`:允许开发者指定线条粗细的百分比值
*   使用

#### 你的前端是否占用了用户的 CPU？

```
p {
  text-decoration-thickness: auto | from-font | 0.2em | 3px | 10%;
}

```

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

## .

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).