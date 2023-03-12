# CSS 参考指南:彩色日志博客

> 原文：<https://blog.logrocket.com/css-reference-guide-color/>

CSS `color`属性设置文本元素或文本装饰的前景色。

#### *向前跳转:*

* * *

## 演示

参见 [CodePen 上](https://codepen.io) [@kaperskyguru](https://codepen.io/kaperskyguru) )
的钢笔[CSS 颜色示例](https://codepen.io/kaperskyguru/pen/jOrqYYX) by Solomon Eseme。

句法

* * *

## 价值观念

```
p.text {
  color: <rgb()> | <rgba()> | <hsl()> | <hsla()> | <hex-color> | <named-color> | currentcolor
}
```

* * *

## CSS `color`属性的值可以用许多不同的方式指定。我们将在下面详细讨论它们。

关键词

### CSS `color`属性可以接受一个关键字作为它的值。它的缺省值是关键字`currentcolor`，它的值来自`color`属性的继承值。

我们还有`transparent`，它是一个`color`值，不是不透明，而是完全透明清晰。

```
p.text {
  color: currentcolor;
}
```

命名值

```
p.text {
  color: currentcolor | transparent
}
```

* * *

### 命名值有时也称为命名颜色。这些只是已知的命名颜色，如红色、绿色等。带有十六进制值的。

十六进制值

```
p.text {
  color: red | green | aqua | tan | rebeccapurple | etc
}
```

* * *

### 颜色的十六进制值表示为六个字符的字母数字字符串，前面是`#`字符。

十六进制值的第一对字母数字表示红色值，第二对表示绿色值，第三对表示 RGB 光谱中的蓝色值。

CSS 还允许使用三个字符的缩写来指定十六进制值。这些值的范围必须总是从`00`到`FF`(对于六个字符的字符串)或者从`0`到`F`(对于三个字符的字符串)。

RGB 和 RGBA 值

```
p.text {
  color: #00FFFF // 00 => RED, FF => GREEN and FF => BLUE
}

div.text {
  color #eee // hex value can also be 3 values only. E => RED, E => GREEN and E => BLUE
}
```

* * *

### RGB 颜色由三个值的逗号分隔列表表示，这三个值依次表示颜色的红色、绿色和蓝色值。这三个值的范围可以从`0`到`2``5``5`，或者从`0%`到`100%`。默认情况下，所有 RGB 颜色都是不透明的。

RGBA 颜色值允许您指定颜色的不透明度(或 alpha)值，其中`0.0`表示完全透明，`1`表示完全不透明。这样，您可以调整 RGB 颜色的不透明度。

```
p.text {
 color : rgb(0, 255, 255);
}

p.text {
  color : rgb(0%, 100%, 100%);
}
```

HSL 和 HSLA 价值观

```
p.text {
 color : rgb(0, 255, 255);
}

p.text {
  color : rgba(0%, 100%, 100%, 0.8);
}
```

* * *

### HSL 颜色由三个值表示，分别表示颜色的色相(范围从`0`到`360`)、饱和度(范围从`0%`到`100%`)和明度(范围从`0%`到`100%`)。默认情况下，所有 HSL 颜色都是不透明的。

HSLA 值允许您指定颜色的不透明度(或 alpha)值，其中`0.0`表示完全透明，`1`表示完全不透明。这样，您可以调整 HSL 颜色的不透明度。

```
p.text {
  color: hsl(180, 100%, 50%);
}
```

```
p.text {
  color: hsla(180, 100%, 50%, 0.5);
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