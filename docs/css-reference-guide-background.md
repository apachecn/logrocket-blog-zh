# CSS 参考指南:背景- LogRocket 博客

> 原文：<https://blog.logrocket.com/css-reference-guide-background/>

CSS `background`速记属性设置元素的背景效果。这控制了在元素内容下面绘制的内容。

#### *向前跳转:*

下面的示例显示了可以指定为元素创建任何背景效果的属性的完整列表:

```
header {
  background: 
    url('folder/image.jpg') // image
    top center /  // position
    150px 150px   // size
    no-repeat     // repeat
    fixed         // attachment
    padding-box   // origin
    content-box   // clip
    blue;         // color
}
```

* * *

## 演示

参见 [CodePen](https://codepen.io) 上的 Pen [CSS 背景示例](https://codepen.io/kaperskyguru/pen/PozNbVY)by Solomon Eseme([@ kaperskyguru](https://codepen.io/kaperskyguru))
。

组件属性

* * *

## `background`简写由八个组件属性组成。让我们在下面逐一探讨。

`background-image`

### `background-image`是一个 CSS 属性，将一个或多个图像设置为元素的背景。当指定多个图像时，它们显示为一个堆栈，一个在另一个之上。

缺省值

#### `background-image`的默认值是`none`，简单来说就是不显示任何图像。

使用

```
header {
  background-image: none
}
```

#### 此示例仅指定一个图像。

该示例将在`second`图像的顶部显示`first`图像。

```
header {
  background-image: url('folder/image.jpg');
}
```

这个例子在`second`图像上指定了一个线性渐变。

```
header {
  background-image: 
    url('folder/first.jpg'), 
    url('folder/second.jpg')
}
```

`background-position`

```
header {
  background-image: 
    linear-gradient(to bottom, rgba(255,255,0,0.5), rgba(0,0,255,0.5)),
    url('folder/second.jpg')
}
```

* * *

### `background-position`属性设置背景图像的起始位置。它的值指定图像相对于元素框边缘放置的坐标。

`background-position`可以用关键字指定(`left`、`top`、`bottom`、`right`、`center`)；百分比(`0%`、`25%`等)。);或者长度(`0cm`、`3px`等)。).这些值可以组合在一起。

缺省值

#### `background-position`的默认值是`0% 0%`，表示元素所在盒子的左上角。

使用

```
header {
  background-position: 0% 0% // top left
}
```

#### `background-size`

```
header {
  background-position: top // same as top center
}

header {
  background-position: left // same as left center
}

header {
  background-position: center // center
}

header {
  background-position: 20% 50% // 20% from left and 50% from top
}

header {
  background-position: bottom 20px left 50px // 20px from bottom and 50px from left
}

header {
  background-position: left 2cm bottom 5cm; // 2cm from left and 5cm from bottom
}
```

* * *

### `background-size`属性指定背景图像的大小。它定义了图像是被拉伸，以其原始大小显示，还是填满整个可用空间。

同样值得注意的是，现在被图像大小覆盖的空间将被背景色填充。

`background-size`可以使用关键字、百分比或长度来指定。

关键词

#### `**cover**`:尽可能放大图像，但不会拉伸图像。它还可以垂直或水平裁剪图像，以覆盖任何空白区域

*   `**contain**`:尽可能放大图像，但不会裁剪或拉伸图像
*   `**auto**`:向相应的方向缩放图像，使图像保持原来的比例
*   缺省值

#### `background-size`的默认值是`auto`。

使用

```
header {
  background-size: auto;
}
```

#### `background-repeat`

```
header {
  background-size: auto; // default value
}

// stretches to fill the element's box
header {
  background-size: cover; 
}

// scale the image without cropping/scretching. Image can repeat to fill element's box
header {
  background-size: contain; 
}

// scale image by 20% width & auto for height. Image can repeat to fill element's box
header {
  background-size: 20%|em|cm|auto|px;
}

// scale image by 3em width and 25% height. Image can repeat to fill element's box
header {
  background-size: 3em 25%;
}

// You can specify more values for multiple backgrounds
header {
  background-size: 50%, 25%, 60%;
}
```

* * *

### 属性指定了背景图像是否和/或应该如何重复。该属性可以指定背景图像在水平方向和垂直方向重复显示，或者是否应该重复显示。

由于默认情况下图像被裁剪为其元素的大小，因此也可以使用`background-repeat`属性缩放图像以适合或填充元素的框。

使用关键字值来指定`background-repeat`属性。

更多来自 LogRocket 的精彩文章:

* * *

### 关键词

* * *

#### `**repeat**`:重复图像以覆盖整个背景

*   `**no-repeat**`:不会重复图像
*   `**space**`:重复图像而不剪裁，并在重复的图像之间均匀分布空白
*   `**round**`:重复图像，并填充重复图像之间的空白
*   `**repeat-x**`:仅沿 x 轴重复图像
*   `**repeat-y**`:仅沿 y 轴重复图像
*   缺省值

#### `background-repeat`的默认值是`repeat`，它重复图像，直到它填满整个元素背景。

使用

```
header {
  background-repeat: repeat;
}
```

#### `background-attachment`

```
header {
  background-repeat: repeat-x;  // equivalent `repeat no-repeat`
}

header {
  background-repeat: repeat-y; // equivalent `no-repeat repeat`
}

header {
  background-repeat: repeat; // equivalent `repeat repeat`
}

header {
  background-repeat: space; // equivalent `space space`
}

header {
  background-repeat: round; // equivalent `round round`
}

header {
  background-repeat: no-repeat; // equivalent `no-repeat no-repeat`
}
```

* * *

### 属性指定背景图像应该是可滚动的还是固定在视窗中。

仅用关键字指定了`background-attachment`属性。

关键词

#### `**scroll**`:将背景图像固定在其元素上。不随内容滚动背景图像

*   `**fixed**`:将背景图像固定到视口。即使内容可以滚动，背景图像也不会滚动
*   `**local**`:相对于元素的内容固定背景图像，从而允许背景图像随着内容的滚动而滚动
*   缺省值

#### 背景附件的默认值为`scroll`。

使用

```
header {
  background-attachment: scroll;
}
```

#### `background-origin`

```
header {
  background-attachment: scroll; // Fix image to element (can scrol only if the element is scrolling not the content of the element)
}

header {
  background-attachment: fixed; // Fix image to viewport
}

header {
  background-attachment: local; // Scrolls the image with content
}

// On multiple images we can specify double values
header {
  background-image: url("star1.gif"), url("star2.gif");
  background-attachment: fixed, scroll;
}
```

* * *

### `background-origin`属性指定在何处绘制背景图像——使用`content-box`值在整个元素上绘制，使用`border-box`值在边框内绘制，或者使用`padding-box`值在填充内绘制。

注意，当`background-attachment`被设置为`fixed`时，`background-origin`被忽略，并且`background-origin`与`background-clip`相同，除了它调整背景大小而不是剪辑。

使用关键字值来指定`background-origin`属性。

关键词

#### `**content-box**`:相对于内容框定位背景

*   `**border-box**`:相对于边框定位背景
*   `**padding-box**`:相对于填充框定位背景
*   缺省值

#### `background-origin`的默认值是`padding-box`关键字。

使用

```
header {
  background-origin: padding-box;
}
```

#### `background-clip`

```
header {
  background-origin: border-box;
}

header {
  background-origin: padding-box;
}

header {
  background-origin: content-box;
}

// We can specify double values for multiple images
header {
  background-image: url('logo.jpg'), url('main.png');
  background-origin: content-box, padding-box;
}
```

* * *

### 属性决定了一个元素的背景是否会扩展它的框。请注意，只有在指定了背景图像或背景颜色的情况下，该属性才会产生实际效果；否则只会在边框上产生视觉效果。

CSS `background-clip`属性是使用关键字值指定的。

关键词

#### `**border-box**`:将背景扩展到元素边框之外

*   `**content-box**`:裁剪元素内容的背景
*   **`padding-box`** :将背景扩展到元素填充之外
*   `**text**`:将背景裁剪到前景文本的实验值
*   缺省值

#### `background-clip`的默认值是`border-box`关键字。

使用

```
header {
  background-clip: border-box;
}
```

#### `background-color`

```
header {
  background-clip: border-box;
}

header {
  background-clip: padding-box;
}

header {
  background-clip: content-box;
}
```

* * *

### CSS `background-color`属性用于设置元素背景的颜色。如果图像加载需要时间，它可以与`background-image`属性结合作为后备选项。

`background-color`值被指定为颜色名称、十六进制值、RGB/RGBA 值或 HSL/HSLA 值。

缺省值

#### `background-color`的默认值是`transparent`关键字。

使用

```
header {
  background-color: transparent;
}
```

#### 你的前端是否占用了用户的 CPU？

```
/* Keyword values */
header {
  background-color: red;
}

/* Hexadecimal value */
header {
  background-color: #bbff00;
}

/* RGB value */
header {
  background-color: rgb(255, 255, 128);        /* Fully opaque */
}
header {
  background-color: rgba(117, 190, 218, 0.5);  /* 50% transparent */
}

/* HSL value */
header {
  background-color: hsl(50, 33%, 25%);         /* Fully opaque */
}
header {
  background-color: hsla(50, 33%, 25%, 0.75);  /* 75% transparent */
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