# CSS 参考指南:按钮样式

> 原文：<https://blog.logrocket.com/css-reference-guide-button-styling/>

普通按钮使用默认浏览器样式呈现。我们可以使用 CSS 覆盖这个默认样式。

#### *向前跳转:*

* * *

样式按钮的基本 CSS 属性有:`padding`、`margin`、`border-radius`和`background`。CSS 框架如 Material Design、Bootstrap、Tailwind CSS 等使用这些来设计按钮的样式，以生成自己的样式类。

在本指南中，我们将从纯 CSS 创建我们自己的按钮样式类。以下演示展示了我们实施的结果:

参见 [CodePen](https://codepen.io) 上 Chidume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的[笔按钮示例](https://codepen.io/philipsz-davido/pen/qBZoMXm)。

基本按钮

## 让我们创建一个具有基本样式的基本按钮样式类:

默认情况下，本指南中的所有按钮都采用上述样式。让我们来分解样式属性:

```
.button {
    padding: 6px 12px;
    margin-bottom: 0;
    font-weight: 400;
    cursor: pointer;
    text-align: center;
    white-space: nowrap;
    display: inline-block;
    background-image: none;

    border-radius: 3px;
    box-shadow: none;
    border: 1px solid transparent;
}
```

`padding`:将按钮内容上下填充 6px，左右填充 12px

*   `margin-bottom`:声明按钮底部没有边距
*   在这里，我们将文本内容设置得稍微加粗一些
*   `cursor`:悬停时，系统光标会变成手形图标
*   `text-align`:居中对齐按钮文本
*   `white-space`:按钮文本将保持在同一行；它不会打破有限的空间
*   `display`:将按钮设置为环绕其同级按钮，同时使其高度和宽度可调
*   `background-image`:关闭背景中设置的任何图像
*   `border-radius`:设定为 3px
*   `box-shadow`:设置按钮周围被删除
*   `border`:设置为 1px 宽，实心，透明
*   这构成了我们类的基础，并允许我们从中创建子按钮。只需将`.button`设置为按钮类属性，我们的样式就会接管:

按钮颜色

```
<button class="button">Button</button>
```

* * *

## 然后我们可以从`.button`基类中创建按钮的变体。这些变化将是不同颜色的按钮。

为了创建不同颜色的 CSS 按钮，我们使用`color`、`background-color`和`border-color`属性来创建变化。

红色按钮

#### 十六进制的`color`道具是`#fff`；`background-color`道具让按钮呈现出`red`的颜色。`border-color`设置为比`red`略暗，以更好地定义按钮边框。

```
.button .buttonRed {
    color: #fff;
    background-color: red;
    border-color: #ac2925;            
}
```

我们现在可以将`.buttonRed`类设置为`.button`按钮来创建番茄红色的按钮。

绿色按钮

```
<button class="button buttonRed">Red Button</button>
```

#### 这里的`background-color`道具设置为`green`。将`.buttonGreen`应用到按钮:

```
.button .buttonGreen {
    color: #fff;
    background-color: green;
    border-color: #4cae4c;
}
```

蓝色按钮

```
<button class="button buttonGreen">Green Button</button>
```

#### 这将使任何带有`.buttonBlue`类的按钮呈现蓝色。将`.buttonBlue`应用于按钮:

```
.button .buttonBlue {
    color: #fff;
    background-color: blue;
    border-color: #2e6da4;    
}
```

按钮尺寸

```
<button class="button buttonBlue">Blue Button</button>

```

* * *

## 我们可以使用`padding`和`font-size`属性来增加按钮的大小。

大按钮

#### 小按钮

```
.buttonLarge {
    padding: 10px 16px;
    font-size: 18px;
}
```

#### 超小按钮

```
.buttonSmall {
    padding: 5px 10px;
    font-size: 12px;    
}
```

#### 注意`padding`和`font-size`的值是如何从`.buttonLarge`减少到`.buttonXSmall`的——尤其是`font-size`，因为它设置了元素的文本大小。

```
.buttonXSmall {
    padding: 1px 5px;
    font-size: 12px;    
}
```

阻止按钮

* * *

## 块按钮扩展以填充其父容器的宽度。这是使用`width`属性完成的。

`width`被设置为`100%`，因此它会伸展以填充其父节点的整个长度。

```
.buttonBlock {
    width: 100%;
}
```

我们也可以使用`width`来定义我们的按钮长度；它可以设置为任何百分比或任何单位。

禁用的按钮

```
.buttonBlock {
    width: 50%;
}
```

```
.buttonBlock {
    width: 10px;
}
```

```
.buttonBlock {
    width: 10em;
}
```

* * *

## 我们可以通过使用`cursor`和`opacity`属性来模拟一个被禁用的按钮。

基于`cursor`属性中的`not-allowed`值，悬停时系统光标将变成停止图标。这将禁用按钮上的任何单击事件或任何其他与鼠标相关的事件。

```
.button.disabled {
    cursor: not-allowed;
    opacity: 0.65;
}
```

`opacity`定义按钮的可见性。这里，值`0.65`使它稍微变暗，表示按钮被禁用。

圆形按钮

* * *

## 我们通过使用`border-radius`、`height`和`width`属性在 CSS 中创建圆形按钮。

为了使按钮变圆，我们将`border-radius`设置为`50%`。`height`和`width`最终决定按钮的大小。如果它们不是相同的值，按钮将显示为椭圆形；为了使它成为一个圆，他们分享相同的`44px`值。

```
.buttonRound {
    border-radius: 50%;
    height: 44px;
    width: 44px;
}
```

高架按钮

* * *

## 创建提升或凸起的按钮是通过`box-shadow`属性完成的。该属性在元素的边框周围投射阴影。

这将使按钮具有 3D 外观。`box-shadow`属性的第一个值将顶部阴影设置为`0`，右侧为`3px`，底部为`8px`，左侧为`0`。我们还定义了一种`black`颜色。使用这些值，我们将看到黑色阴影更多地投射在底部而不是右侧。

```
.buttonRaised {
    box-shadow: 0 3px 8px 0 black;
}
```

更多来自 LogRocket 的精彩文章:

* * *

### 动画按钮

* * *

* * *

## 我们也可以使用 CSS 给按钮添加动画效果。

特别是，当鼠标悬停在按钮上时，当鼠标远离按钮时，以及当按钮被点击时，我们想要动画效果。

悬停效应

### 我们使用`:hover`伪类选择器来设置当鼠标移动到按钮上时按钮的样式。

让我们为每个不同的按钮颜色添加悬停效果:

我们为按钮的`color`、`border-color`和`background-color`属性选择的颜色分别具有比它们原始状态更亮的不透明度。使用悬停时的`box-shadow`属性，元素投射的阴影也被设置得更加清晰。

```
//red button
.buttonRed:hover {
    color: #fff;
    background-color: red;
    border-color: #ac2925;
    box-shadow: 1px 1px 1px 3px grey;
}

//green button
.buttonGreen:hover {
    color: #fff;
    background-color: green;
    border-color: #398439;
    box-shadow: 1px 1px 1px 3px grey;
}

//blue button
.buttonBlue:hover {
    color: #fff;
    background-color: blue;
    border-color: #269abc;
    box-shadow: 1px 1px 1px 3px grey;
}
```

这为用户悬停按钮时提供了视觉提示。他们将看到按钮的颜色、背景色变暗，并且在悬停时投下更深的阴影。

点击效果

### 为了创建点击效果，我们使用了`:active`伪类选择器。

就像在悬停效果中一样，当按钮被点击时，我们改变了边框、文本、背景的颜色。在 CSS 中，当点击按钮时，会触发`:active`伪选择器。

```
//red button
.buttonRed:active {
    color: #fff;
    background-color: #3b0404;
    border-color: #ac2925;
}

//green button
.buttonGreen:active {
    color: #fff;
    background-color: #022c02;
    border-color: #398439;
}

//blue button
.buttonBlue:active {
    color: #fff;
    background-color: #020221;
    border-color: #269abc;
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