# CSS 参考指南:position 

> 原文：<https://blog.logrocket.com/css-reference-guide-position/>

# CSS 参考指南:`position`

## 

2020 年 10 月 30 日 1 分钟读完 487

CSS `position`属性用于操作或调整文档中元素的位置。它在指定用于元素的位置类型时也很有用。

#### *向前跳转:*

* * *

## 演示

参见 [CodePen 上](https://codepen.io) [@kaperskyguru](https://codepen.io/kaperskyguru) )
的 Pen[CSS 位置示例](https://codepen.io/kaperskyguru/pen/gOMrvoE) by Solomon Eseme。

价值观念

## 有五个主要的位置或值可以提供给`position`属性。我们在下面详细探讨每一个。

`static`

### `static`是 CSS `position`属性的默认值。它将元素固定在文档的正常流程中；即使提供了`top`、`bottom`、`left`和`right`值，它们也不会对静态定位的元素产生任何影响。

这将使`div`标签保持在正常位置。

```
div {
  border: 2px solid red;
  position: static;
}
```

`relative`

* * *

### 像`static`一样，将一个元素的位置设置为`relative`将导致该元素保留其在文档中的位置。但是，`top`、`bottom`、`left`和`right`等属性会对元素产生影响，并可用于移动元素在文档中的位置。

这将导致`div`元素从顶部移动 20 %,从左侧移动 20%。同样重要的是要注意，赋予`top`、`bottom`、`left`和`right`属性的值不会影响任何其他元素。

```
div {
  border: 2px solid red;
  position: relative
  top: 20%
  bottom: 0
  right: 0
  left: 20%
}
```

`absolute`

* * *

### 绝对定位的元素会将其`position`设置为`absolute`。该元素将从正常的文档流中移除，其他元素将填充所创建的空间，就像绝对定位的元素不在那里一样。

绝对定位的元素也可以通过使用`top`、`bottom`、`left`和`right`属性来控制，就像用`relative`值定位的元素一样。

同样重要的是要注意绝对定位的元素会受到滚动的影响。

`fixed`

```
div {
  border: 2px solid red;
  position: absolute
  top: 30%
  bottom: 0
  right: 0
  left: 40%
}
```

* * *

### 一个`fixed`元素的行为与一个绝对定位的元素完全一样:它将该元素从文档的正常流程中移除，并且它的位置可以通过使用`top`、`bottom`、`left`和`right`来影响。

不同之处在于，`fixed`元素不受滚动的影响，因为它是相对于视窗的，而不是像绝对定位的元素一样，是相对于其最近的定位祖先的。

`sticky`

```
div {
  border: 2px solid red;
  position: fixed
  top: 50%
  bottom: 0
  right: 0
  left: 10%
}
```

* * *

### 一个`sticky`定位的元素在滚动时表现得像一个相对定位的元素，直到它达到一个声明的阈值，在这一点上它将表现得像一个`fixed`元素；元素会粘在那个点上。

在上面的例子中，`div`元素将保持滚动，直到它到达视窗的 50 %,在这一点上，它将停止滚动并固定在那个点上。

```
div {
  border: 2px solid red;
  position: sticky;
  top: 50%
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